---
title: Production readiness and best practices
description: This article provides guidance on how to configure and deploy the Azure Video Analyzer on IoT Edge module in production environments.
ms.topic: reference
ms.date: 04/26/2021

---
# Production readiness and best practices

This article provides guidance on how to configure and deploy the Azure Video Analyzer on IoT Edge module in production environments. You should also review [Prepare to deploy your IoT Edge solution in production](https://docs.microsoft.com/en-us/azure/iot-edge/production-checklist) article on preparing your IoT Edge solution.

> [!NOTE]
> You should consult your organizations’ IT departments on aspects related to security.

## Running the module as a local user

When you deploy the Video Analyzer module to an edge device, by default it runs with elevated privileges. When you do this, if you check the logs on the module (`sudo iotedge logs {name-of-module}`), you will see the following:

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
```

The sections below discuss how you can address the above warning.

### Creating and using a local user account

You can and should run the Video Analyzer module in production using an account with as few privileges as possible. The following commands, for example, show how you can create a local user account on a Linux VM:

```
sudo groupadd -g 1010 localedgegroup
sudo useradd --home-dir /home/localedgeuser --uid 1010 --gid 1010 localedgeuser
```

Next, in the deployment manifest, you can set the LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to that non-root user and group:

```
"avaedge": {
"version": "1.0",
…
"env": {
    "LOCAL_USER_ID": 
    {
        "value": "1010"
    },
    "LOCAL_GROUP_ID": {
        "value": "1010"
    }
}
},
…
```

### Granting permissions to device storage

The Video Analyzer module requires the ability to write files to the local file system when:

- Using a module twin property [`ApplicationDataDirectory`](https://review.docs.microsoft.com/en-us/azure/azure-video-analyzer/video-analyzer-docs/module-twin-configuration-schema.md?branch=release-azure-video-analyzer), where you should specify a directory on the local file system for storing configuration data.
- Using a pipeline to record video to the cloud, the module requires the use of a directory on the edge device as a cache (see [Continuous video recording](https://review.docs.microsoft.com/en-us/azure/azure-video-analyzer/video-analyzer-docs/continuous-video-recording?branch=release-azure-video-analyzer) article for more information).
- [Recording to a local file](https://review.docs.microsoft.com/en-us/azure/azure-video-analyzer/video-analyzer-docs/event-based-video-recording-concept?branch=release-azure-video-analyzer), where you should specify a file path for the recorded video.

If you intend to make use of any of the above, you should ensure that the above user account has access to the relevant directory. Consider applicationDataDirectory for example. You can create a directory on the edge device and link device storage to module storage.

```
sudo mkdir /var/lib/videoanalyzer
sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer
```

Next, in the create options for the edge module in the deployment manifest, you can add a binds setting mapping the directory (“/var/lib/videoanalyzer”) above to a directory in the module (such as “/var/lib/videoanalyzer”). And you would use the latter directory as the value for the applicationDataDirectory.

```
"avaedge": {
    "version": "1.0",
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
        "image": "mcr.microsoft.com/media/azure-video-analyzer:1.0",
    "createOptions": "{\"HostConfig\":{\"LogConfig\":{\"Type\":\"\",\"Config\":{\"max-size\":\"10m\",\"max-file\":\"10\"}},\"Binds\":[\"/var/media:/var/media/\",\"/var/lib/videoanalyzer:/var/lib/videoanalyzer/\"]}}",
    
    "env": {
        "LOCAL_USER_ID": 
        {
            "value": "1010"
        },
        "LOCAL_GROUP_ID": {
            "value": "1010"
        }
    }
    },
    …
    
    "avaedge": {
    "properties.desired": {
    "applicationDataDirectory": "/var/lib/videoanalyzer",
    …
    }
}
```

If you look at the sample pipelines for the quickstart and tutorials, such as [continuous video recording](https://review.docs.microsoft.com/en-us/azure/azure-video-analyzer/video-analyzer-docs/use-continuous-video-recording?branch=release-azure-video-analyzer), you will note that the media cache directory (localMediaCachePath) uses a subdirectory under applicationDataDirectory. This is the recommended approach, since the cache contains transient data.

### Naming video or files

Pipelines allows for creation of videos in the cloud or as .mp4 files on the edge device. These can be generated by [continuous video recording](https://review.docs.microsoft.com/en-us/azure/azure-video-analyzer/video-analyzer-docs/use-continuous-video-recording?branch=release-azure-video-analyzer) or by [event-based video recording](https://review.docs.microsoft.com/en-us/azure/azure-video-analyzer/video-analyzer-docs/record-event-based-live-video?branch=release-azure-video-analyzer). While these can be named as you want, the recommended naming structure for continuous video recording-based video is "<anytext>-${System.TopologyName}-${System.PipelineName}".

Substitution pattern is defined by the $ sign followed by braces: **${variableName}**.

As an example, you can set the video name on the Video Sink  as follows:

```
"VideoName": "sampleVideo-${System.TopologyName}-${System.PipelineName}
```

Or on the File Sink  as follows:

```
"fileNamePattern": "sampleFilesFromEVR-${fileSinkOutputName}--${System.TopologyName}-${System.PipelineName} ${System.Runtime.DateTime}"
```

> [!Note]
> In the example above, the variable **fileSinkOutputName** is a sample variable name that you define in the pipeline instance. This is **not** a system variable.

#### System variables

Some system defined variables that you can use are:

| System Variable        | Description                                                  | Example              |
| :--------------------- | :----------------------------------------------------------- | :------------------- |
| System.Runtime.DateTime        | UTC date time in ISO8601 file compliant format (basic representation YYYYMMDDThhmmss). | 20200222T173200Z     |
| System.Runtime.PreciseDateTime | UTC date time in ISO8601 file compliant format with milliseconds (basic representation YYYYMMDDThhmmss.sss). | 20200222T173200.123Z |
| System.TopologyName    | User provided name of the executing graph topology.          | IngestAndRecord      |
| System.PipelineName    | User provided name of the executing graph instance.          | camera001            |

> [!Tip]
> System.Runtime.PreciseDateTime and System.Runtime.DateTime cannot be used when naming videos in the cloud

### Keeping your VM clean

The Linux VM that you are using as an edge device can become unresponsive if it is not managed on a periodic basis. It is essential to keep the caches clean, eliminate unnecessary packages and remove unused containers from the VM as well. To do this here is a set of recommended commands, you can use on your edge VM.

`sudo apt-get clean`

The apt-get clean command clears the local repository of retrieved package files that are left in /var/cache. The directories it cleans out are /var/cache/apt/archives/ and /var/cache/apt/archives/partial/. The only files it leaves in /var/cache/apt/archives are the lock file and the partial subdirectory. The apt-get clean command is generally used to clear disk space as needed, generally as part of regularly scheduled maintenance. For more information, see [Cleaning up with apt-get](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html).

- `sudo apt-get autoclean`

The apt-get autoclean option, like apt-get clean, clears the local repository of retrieved package files, but it only removes files that can no longer be downloaded and are not useful. It helps to keep your cache from growing too large.

- `sudo apt-get autoremove1`

The auto remove option removes packages that were automatically installed because some other package required them but, with those other packages removed, they are no longer needed

- `sudo docker image ls` – Provides a list of Docker images on your edge system

- `sudo docker system prune`

Docker takes a conservative approach to cleaning up unused objects (often referred to as “garbage collection”), such as images, containers, volumes, and networks: these objects are generally not removed unless you explicitly ask Docker to do so. This can cause Docker to use extra disk space. For each type of object, Docker provides a prune command. In addition, you can use docker system prune to clean up multiple types of objects at once. For more information, see [Prune unused Docker objects](https://docs.docker.com/config/pruning/).

- `sudo docker rmi REPOSITORY:TAG`

As updates happen on the edge module, your docker can have older versions of the edge module still present. In such a case, it is advisable to use the docker rmi command to remove specific images identified by the image version tag.

## Next steps

[Quickstart: Get started - Azure Video analyzer on IoT Edge](https://review.docs.microsoft.com/en-us/azure/azure-video-analyzer/video-analyzer-docs/get-started-detect-motion-emit-events?branch=release-azure-video-analyzer)