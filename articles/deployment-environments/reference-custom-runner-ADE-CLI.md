---
title: ADE CLI Custom Runner Image reference
titleSuffix: Azure Deployment Environments
description: <!-- @jack can you add a description here -->
ms.service: deployment-environments
author: RoseHJM
ms.author: rosemalcolm
ms.date: 02/16/2024
ms.topic: reference
---

# Azure Deployment Environment CLI Custom Runner Image reference

This article describes the commands available for building custom images using Azure Deployment Environment (ADE) base images.

By using the ADE CLI, you can interact with information about your environment and specified environment definition, upload, and access previously uploaded files related to the environment, record more logging regarding their executing operation, and upload and access outputs of an environment's deployment.

## What commands can I use?
The ADE CLI currently supports the following commands:
- [ade environment](#ade-environment-command)
- [ade definitions](#ade-definitions-command-set)
- [ade files](#ade-files-command-set)
- [ade log](#ade-log-command-set)
- [ade operation-result](#ade-operation-result-command)
- [ade outputs](#ade-outputs-command-set)

Additional information on how to invoke the ADE CLI commands can be found in the linked documentation. 

## License
- Legal Notice: [Container License Information](https://aka.ms/mcr/osslegalnotice)

See license terms [here](https://github.com/Azure/deployment-environments/blob/main/LICENSE).

## ade environment command
The `ade environment` command allows the user to see information related to their environment the operation is being performed on.

The command is invoked as follows:

```environmentValue=$(ade environment)```

This command returns a data object describing the various properties of the environment.

### Return type
This command returns a JSON object describing the environment. Here's an example of the return object:
```
{
    "uri": "https://TENANT_ID-DEVCENTER_NAME.DEVCENTER_REGION.devcenter.azure.com/projects/PROJECT_NAME/users/USER_ID/environments/ENVIRONMENT_NAME",
    "name": "ENVIRONMENT_NAME",
    "environmentType": "ENVIRONMENT_TYPE",
    "user": "USER_ID",
    "provisioningState": "PROVISIONING_STATE",
    "resourceGroupId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME",
    "catalogName": "CATALOG_NAME",
    "environmentDefinitionName": "ENVIRONMENT_DEFINITION_NAME",
    "parameters": {
        "location": "locationInput",
        "name": "nameInput"
    },
    "location": "regionForDeployment"
}
```

### Utilizing returned property values

You can assign environment variables to certain properties of the returned definition JSON object by utilizing the JQ library (preinstalled on ADE-authored images), using the following format:\
```environment_name=$(echo $environment | jq -r ".Name")```

You can learn more about advanced filtering and other uses for the JQ library [here](https://devdocs.io/jq/).


## ade definitions command set
The `ade definitions` command allows the user to see information related to the definition chosen for the environment being operated on, and download the related files, such as the primary and linked Infrastructure-as-Code (IaC) templates, to a specified file location. 

The following commands are within this command set:

- [ade definitions list](#ade-definitions-list)
- [ade definitions download](#ade-definitions-download)

### ade definitions list
The list command is invoked as follows:

```definitionValue=$(ade definitions list)```

This command returns a data object describing the various properties of the environment's definition.

#### Return type
This command returns a JSON object describing the environment definition. Here's an example of the return object, based on one of our sample environment definitions:
```
{
    "id": "/projects/PROJECT_NAME/catalogs/CATALOG_NAME/environmentDefinitions/appconfig",
    "name": "AppConfig",
    "catalogName": "CATALOG_NAME",
    "description": "Deploys an App Config.",
    "parameters": [
        {
            "id": "name",
            "name": "name",
            "description": "Name of the App Config",
            "type": "string",
            "readOnly": false,
            "required": true,
            "allowed": []
        },
        {
            "id": "location",
            "name": "location",
            "description": "Location to deploy the environment resources",
            "default": "westus3",
            "type": "string",
            "readOnly": false,
            "required": false,
            "allowed": []
        }
    ],
    "parametersSchema": "{\"type\":\"object\",\"properties\":{\"name\":{\"title\":\"name\",\"description\":\"Name of the App Config\"},\"location\":{\"title\":\"location\",\"description\":\"Location to deploy the environment resources\",\"default\":\"westus3\"}},\"required\":[\"name\"]}",
    "templatePath": "CATALOG_NAME/AppConfig/appconfig.bicep",
    "contentSourcePath": "CATALOG_NAME/AppConfig"
}
```

#### Utilizing returned property values

You can assign environment variables to certain properties of the returned definition JSON object by utilizing the JQ library (preinstalled on ADE-authored images), using the following format:\
```environment_name=$(echo $definitionValue | jq -r ".Name")```

You can learn more about advanced filtering and other uses for the JQ library [here](https://devdocs.io/jq/).

### ade definitions download
This command is invoked as follows:\
```ade definitions download --folder-path EnvironmentDefinition```

This command downloads the main and linked Infrastructure-as-Code (IaC) templates and any other associated files with the provided template.

#### Options

**--folder-path**: The folder path to download the environment definition files to. If not specified, the command stores the files in a folder named EnvironmentDefinition at the current directory level at execution time.

#### What Files Do I Have Access To?
Any files stored at or below the level of the environment definition's manifest file (environment.yaml or manifest.yaml) within the catalog repository are accessible when invoking this command. 

You can learn more about curating environment definitions and the catalog repository structure through the following links:

- [Add and Configure a Catalog in ADE](/azure/deployment-environments/how-to-configure-catalog?tabs=DevOpsRepoMSI)
- [Add and Configure an Environment Definition in ADE](/azure/deployment-environments/configure-environment-definition)
- [Best Practices For Designing Catalogs](/azure/deployment-environments/best-practice-catalog-structure)

## ade files command set
The `ade files` command set allows a customer to upload and download files within the executing operation container for a certain environment to be used later in the container, or in later operation executions. This command set is also used to upload state files generated for certain Infrastructure-as-Code (IaC) providers.

The following commands are within this command set:
* [ade files list](#ade-files-list)
* [ade files download](#ade-files-download)
* [ade files upload](#ade-files-upload)

###  ade files list
This command lists the available files for download while within the environment container.

#### Return type
This command returns available files for download as an array of strings. Here's an example:
```
[
    "file1.txt",
    "file2.sh",
    "file3.zip"
]
```

### ade files download
This command downloads a selected file to a specified file location within the executing container. 

#### Options
**--file-name**: The name of the file to download. This file name should be present within the list of available files returned from the `ade files list` command. This option is required.

**--folder-path**: The folder path to download the file to within the container. This path isn't required, and the CLI by default downloads the file to the current directory when the command is executed.

**--unzip**: Set this flag if you want to download a zip file from the list of available files, and want the contents unzipped to the specified folder location. 

#### Examples

The following command downloads a file to the current directory:
```
ade files download --file-name file1.txt
```

The following command downloads a file to a lower-level folder titled *folder1*.
```
ade files download --file-name file1.txt --folder-path folder1
```

The last command downloads a zip file, and unzips the file contents into the current directory:
```
ade files download --file-name file3.zip --unzip
```

### ade files upload
This command uploads either a singular file specified, or a zip folder specified as a folder path to the list of available files for the environment to access.

#### Options
**--file-path**: The path of where the file exists from the current directory to upload. Either this option or the `--folder-path` option is required to execute this command.

**--folder-path**: The path of where the folder exists from the current directory to upload as a zip file. The resulting accessible file is accessible under the name of the lowest folder. Either this option or the `--file-path` option is required to execute this command. 

**NOTE**: Specifying a file or folder with the same name as an existing accessible file for the environment for this command overwrites the previously saved file (that is, if file1.txt is an existing accessible file, executing `ade files --file-path file1.txt` overwrites the previously saved file).

#### Examples
The following command uploads a file from the current directory named *file1.txt*:
```
ade files upload --file-path "file1.txt"
```

This file is later accessible by running:
```
ade files download --file-name "file1.txt"
```
The following command uploads a folder one level lower than the current directory named *folder1* as a zip file named *folder1.zip*:
```
ade files upload --folder-path "folder1"
```

Finally, the following command uploads a folder two levels lower than the current directory at *folder1/folder2* as a zip file named *folder2.zip*:
```
ade files upload --folder-path "folder1/folder2"
```

## ade log command set
The `ade log` commands are used to record details regarding the execution of the operation on the environment while within the container. This command offers many different logging levels, which can be then accessed after the operation finishes to analyze, and a customer can specify different files to log to for different logging scenarios.

### Options
**--content**: A string input containing the information to log. This option is required for this command.

**--type**: The level of log (verbose, log, or error) to log the content under. If not specified, the CLI logs the content at the log level.

**--file**: The file to log the content to. If not specified, the CLI logs to a .log file specified by the unique Operation ID of the executing operation.

### Examples

This command logs a string to the default log file:
```
ade log --content "This is a log"
```

This command logs an error to the default log file:
```
ade log --type error --content "This is an error."
```

This command logs a string to a specified file named *specialLogFile.txt*:
```
ade log --content "This is a special log." --file "specialLogFile.txt"
```

## ade operation-result command
The `ade operation-result` command allows error details to be added to the environment being operated on in the event of an operation failure, and updates the ongoing operation.

The command is invoked as follows:
```
ade operation-result --code "ExitCode" --message "The operation failed!"
```

### Options
**--code**: A string detailing the exit code causing the failure of the operation

**--message**: A string detailing the error message for the operation failure.

**NOTE**: This operation should only be used just before exiting the container, as setting the operation in a Failed state doesn't permit other CLI commands to successfully complete.

## ade outputs command set
The `ade outputs` command allows a customer to upload outputs from the deployment of an Infrastructure-as-Code (IaC) template to be accessed from the Outputs API for ADE. 

### ade outputs upload 
This command uploads the contents of a JSON file specified in the ADE EnvironmentOutput format to the environment, to be accessed later using the Outputs API for ADE.

#### Options
**--file**: A file location containing a JSON object to upload.

#### Examples

This command uploads a .json file named *outputs.json* to the environment to serve as the outputs for the successful deployment:
```
ade outputs upload --file outputs.json
```

#### EnvironmentOutputs Format
In order for, the incoming JSON file to be serialized properly and accepted as the environments deployment outputs, the object submitted must follow the below structure:
```
{
    "outputs": {
        "output1": {
            "type": "string",
            "value": "This is output 1!",
            "sensitive": false
        },
        "output2": {
            "type": "int",
            "value": 22,
            "sensitive": false
        },
        "output3": {
            "type": "string",
            "value": "This is a sensitive output",
            "sensitive" true
        }
    }
}
```

This format is adapted from how ARM template deployments report outputs of a deployment, along with an additional property of "sensitive". The "sensitive" property is optional to provide, but restricts viewing the output to users with privileged access, such as the creator of the environment.

Acceptable types for outputs are "string", "int", "boolean", "array", and "object".

### How to Access Outputs

To access outputs either while within the container or post-execution, a customer can use the Outputs API for ADE, accessible either by calling the API endpoint or using the AZ CLI.

In order to access the outputs within the container, a customer needs to install the Azure CLI to their image (preinstalled on ADE-authored images), and run the following commands: 
```
az login
az devcenter dev environment show-outputs --dev-center-name DEV_CENTER_NAME --project-name PROJECT_NAME --environment-name ENVIRONMENT_NAME
```

## Support

[File an issue.](https://github.com/Azure/deployment-environments/issues)

[Additional Documentation about ADE](/azure/deployment-environments/)

## Related content
- [ADE Custom Image Support](./how-to-configure-custom-runner.md)