---
title: Tutorial to unpack, connect to, unlock Azure Data Box Disk| Microsoft Docs
description: In this tutorial, learn how to unpack your Azure Data Box Disk, connect to disks, get the passkey, and unlock disks on Windows and Linux clients.
services: databox
author: stevenmatthew

ms.service: databox
ms.subservice: disk
ms.custom: linux-related-content
ms.topic: tutorial
ms.date: 04/09/2024
ms.author: shaas
# Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
---

::: zone target="docs"

# Tutorial: Unpack, connect, and unlock Azure Data Box Disk

> [!IMPORTANT]
> Hardware encryption support for Data Box Disk is currently available for regions within the US, Europe, and Japan.
>
> Azure Data Box disk with hardware encryption requires a SATA III connection. All other connections, including USB, are not supported.

> [!CAUTION]
> This article references CentOS, a Linux distribution that is nearing End Of Life (EOL) status. Please consider your use and planning accordingly. For more information, see the [CentOS End Of Life guidance](~/articles/virtual-machines/workloads/centos/centos-end-of-life.md).

This tutorial describes how to unpack, connect, and unlock your Azure Data Box Disk.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Unpack your Data Box Disk
> * Connect to disks and get the passkey
> * Unlock disks on Windows client
> * Unlock disks on Linux client

::: zone-end

::: zone target="chromeless"

## Unpack, connect, and unlock Azure Data Box Disk

::: zone-end

::: zone target="docs"

## Prerequisites

Before you begin, make sure that:

1. You have completed the [Tutorial: Order Azure Data Box Disk](data-box-disk-deploy-ordered.md).
2. You have received your disks and the job status in the portal is updated to **Delivered**.
3. You have a client computer on which you can install the Data Box Disk unlock tool. Your client computer must:
    - Run a [Supported operating system](data-box-disk-system-requirements.md#supported-operating-systems-for-clients).
    - Have other [required software](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) installed if it is a Windows client.

## Unpack disks

 Perform the following steps to unpack your disks.

1. The Data Box Disks are mailed in a small shipping Box. Open the box and remove its contents. Check that the box has 1 to 5 solid-state disks (SSDs) and a USB connecting cable per disk. Inspect the box for any evidence of tampering, or any other obvious damage.

    ![Data Box Disk shipping package](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. If the shipping box is tampered or severely damaged, do not open the box. Contact Microsoft Support to help you assess whether the disks are in good working order and if they need to ship you a replacement.
3. Verify that the box has a clear sleeve containing a shipping label (under the current label) for return shipment. If this label is lost or damaged, you can always download and print a new one from the Azure portal.

    ![Data Box Disk shipping label](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. Save the box and packaging foam for return shipment of the disks.

## Connect disks

> [!IMPORTANT]
> Azure Data Box disk with hardware encryption is only supported and tested for Linux-based operating systems. To access disks using a Windows OS-based device, download the [Data Box Disk toolset](https://aka.ms/databoxdisktoolswin) and run the **Data Box Disk Unlock tool**.

### [Software encryption](#tab/bitlocker)

Use the included USB cable to connect the disk to a Windows or Linux machine running a supported version. For more information on supported OS versions, go to [Azure Data Box Disk system requirements](data-box-disk-system-requirements.md). 

![Data Box Disk connect](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)

### [Hardware encryption](#tab/sed)

Only use the included SATA 3 cable to connect the disk to a Linux machine running a supported version. For more information on supported OS versions, go to [Azure Data Box Disk system requirements](data-box-disk-system-requirements.md). 

Insert image of disk and cable here.

---

## Retrieve your passkey

### [Azure Portal](#tab/portal)

In the Azure portal, navigate to your Data Box Disk Order. Search for it by navigating to **General > All resources**, then select your Data Box Disk Order. Use the copy icon to copy the passkey. This passkey will be used to unlock the disks.

[Data Box Disk unlock passkey](media/data-box-disk-deploy-set-up/data-box-disk-get-passkey.png)

Depending on whether you are connected to a Windows or Linux client, the steps to unlock the disks are different.

### [Azure CLI](#tab/cli)

Azure CLI instructions to retrieve your passkey

---

## Unlock disks

Perform the following steps to connect and unlock your disks.

### [Windows](#tab/windows)

Perform the following steps to connect and unlock your disks.

1. In the Azure portal, navigate to your Data Box Disk Order. Search for it by navigating to **General > All resources**, then select your Data Box Disk Order.
2. Download the Data Box Disk toolset corresponding to the Windows client. This toolset contains 3 tools: Data Box Disk Unlock tool, Data Box Disk Validation tool, and Data Box Disk Split Copy tool.

    In this procedure, you will use only the Data Box Disk Unlock tool. The other two tools will be used later.

    > [!div class="nextstepaction"]
    > [Download Data Box Disk toolset for Windows](https://aka.ms/databoxdisktoolswin)

3. Extract the toolset on the same computer that you will use to copy the data.
4. Open a Command Prompt window or run Windows PowerShell as administrator on the same computer.
5. Verify that your client computer meets the operating system requirements for the **Data Box Unlock tool**. Run a system check in the folder containing the extracted **Data Box Disk toolset** as shown in the following example.

    ```powershell
    .\DataBoxDiskUnlock.exe /SystemCheck
    ```

    The following sample output confirms that your client computer meets the operating system requirements.

    :::image type="content" source="media/data-box-disk-deploy-set-up/system-check.png" alt-text="Screen capture showing the results of a successful system check using the Data Box Disk Unlock tool." lightbox="media/data-box-disk-deploy-set-up/system-check-lrg.png":::

6. Run	`DataBoxDiskUnlock.exe`, providing the passkey obtained in the [Retrieve your passkey](#retrieve-your-passkey) section. The passkey is submitted as the `Passkey` parameter value as shown in the following example. 

    ```powershell
    .\DataBoxDiskUnlock.exe /Passkey:<testPasskey>
    ```

    A successful response includes the drive letter assigned to the disk as shown in the following example output.

    :::image type="content" source="media/data-box-disk-deploy-set-up/disk-unlocked-win.png" alt-text="Screen capture showing a successful response from the Data Box Disk Unlock tool containing the drive letter assigned." lightbox="media/data-box-disk-deploy-set-up/disk-unlocked-win-lrg.png":::

7. Repeat the unlock steps for any future disk reinserts. If you need help with the Data Box Disk unlock tool, use the `help` command as shown in the following sample code and example output.

    ```powershell
    .\DataBoxDiskUnlock.exe /help
    ```

    :::image type="content" source="media/data-box-disk-deploy-set-up/disk-unlock-help.png" alt-text="Screenshot showing the output fo the Data Box Unlock tool's Help sommand." lightbox="media/data-box-disk-deploy-set-up/disk-unlock-help-lrg.png":::

8. After the disk is unlocked, you can view the contents of the disk.

    :::image type="content" source="media/data-box-disk-deploy-set-up/data-box-disk-content.png" alt-text="Screenshot showing the contents of the unlocked Data Box Disk." lightbox="media/data-box-disk-deploy-set-up/data-box-disk-content-lrg.png":::

    > [!NOTE]
    > Don't format or modify the contents or existing file structure of the disk.

If you run into any issues while unlocking the disks, see how to [troubleshoot unlock issues](data-box-disk-troubleshoot-unlock.md).

<!--
This is the end.



Perform the following steps to connect to and unlock your self-encrypted Data Box Disks on a Windows client.

1.	Using the [Azure portal](https://portal.azure.com), navigate to your Data Box Disk order. You might find it helpful to search for your order by navigating to *General* > *All resources*, and then selecting your Data Box Disk order.
1.	Download the [Data Box Disk toolset](https://aka.ms/databoxdisktoolswin) for the Windows client. This toolset contains four tools: the **Data Box Disk Unlock tool**, the **Data Box SED Unlock tool**, the **Data Box Disk Validation tool**, and the **Data Box Disk Split Copy tool**.

    > [!IMPORTANT]
    > Only the **Data Box SED Unlock tool** is used with self-encrypted disks. Neither the **Data Box Disk Validation tool** nor **Data Box Disk Split Copy tool** are supported for hardware encryption-enabled disks.

    In this procedure, you use the **Data Box SED Unlock tool** only.
1.	Extract the toolset to a location on the same computer you use to copy your data.
1.	Open a **Command Prompt** window or run **Windows PowerShell** with elevated privileges on the same computer.
1.	Verify that your client computer meets the operating system requirements for the **Data Box SED Unlock tool**. Run a system check in the folder containing the extracted **Data Box Disk toolset** as shown in the following example.

    ```powershell
    .\DataBoxDiskUnlock.exe /SystemCheck /SED
    ```

    The following sample output confirms that your client computer meets the operating system requirements.

    :::image type="content" source="media/data-box-disk-deploy-set-up/system-check.png" alt-text="Screen capture showing the results of a successful system check using the Data Box Disk Unlock tool." lightbox="media/data-box-disk-deploy-set-up/system-check-lrg.png":::

1.	Run	`DataBoxDiskUnlock.exe`. Use the passkey obtained in the **Connect to disks and get the passkey** section as the `Passkey` parameter value as shown in the following example. 

    ```powershell
    .\DataBoxDiskUnlock.exe /Passkey:<testPasskey> /SED
    ```

    A successful response includes the drive letter assigned to the disk as shown in the following example.

    :::image type="content" source="media/data-box-disk-deploy-set-up/disk-unlocked-win.png" alt-text="Screen capture showing a successful response from the Data Box Disk Unlock tool containing the drive letter assigned." lightbox="media/data-box-disk-deploy-set-up/disk-unlocked-win-lrg.png":::

1.	Repeat the unlock steps for any future disk reinserts. Use the `help` command if you need help with the Data Box Disk unlock tool.

    > [!NOTE]
    > Don't format or modify the contents or existing file structure of the disk.

If you run into any issues while unlocking the disks, see how to [troubleshoot unlock issues](data-box-disk-troubleshoot-unlock.md).

-->

### [Linux - hardware encryption](#tab/linux-hardware)


<!--
1. In the Azure portal, go to **General > Device details**.
1. Download the Data Box Disk toolset corresponding to the Linux client.

    > [!div class="nextstepaction"]
    > [Download Data Box Disk toolset for Linux](https://aka.ms/databoxdisktoolslinux)

1. On your Linux client, open a terminal. Navigate to the folder where you downloaded the software. Change the file permissions so that you can execute these files. Type the following command:

    `chmod +x DataBoxDiskUnlock`

    `chmod +x DataBoxDiskUnlock_Prep.sh`

    A sample output is shown below. Once the chmod command is run, you can verify that the file permissions are changed by running the `ls` command.

    ```
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_Prep.sh
        [user@localhost Downloads]$ ls -l
        -rwxrwxr-x. 1 user user 1152664 Aug 10 17:26 DataBoxDiskUnlock
        -rwxrwxr-x. 1 user user 795 Aug 5 23:26 DataBoxDiskUnlock_Prep.sh
    ```

1. Execute the script so that it installs all the binaries needed for the Data Box Disk Unlock software. Use `sudo` to run the command as root. Once the binaries are successfully installed, you will see a note to that effect on the terminal.

    `sudo ./DataBoxDiskUnlock_Prep.sh`

    The script will first check whether your client computer is running a supported operating system. A sample output is shown below.

    ```
    [user@localhost Documents]$ sudo ./DataBoxDiskUnlock_Prep.sh
        OS = CentOS Version = 6.9
        Release = CentOS release 6.9 (Final)
        Architecture = x64

        The script will install the following packages and dependencies.
        epel-release
        dislocker
        ntfs-3g
        fuse-dislocker
        Do you wish to continue? y|n :|
    ```

-->
<!--1. Type `y` to continue the install. The packages that the script installs are:
   - **epel-release** - Repository that contains the following three packages.
   - **dislocker and fuse-dislocker** - These utilities helps decrypting BitLocker encrypted disks.
   - **ntfs-3g** - Package that helps mount NTFS volumes.

     Once the packages are successfully installed, the terminal will display a notification to that effect.
     ```
     Dependency Installed: compat-readline5.x86 64 0:5.2-17.I.el6 dislocker-libs.x86 64 0:0.7.1-8.el6 mbedtls.x86 64 0:2.7.4-l.el6        ruby.x86 64 0:1.8.7.374-5.el6
     ruby-libs.x86 64 0:1.8.7.374-5.el6
     Complete!
     Loaded plugins: fastestmirror, refresh-packagekit, security
     Setting up Remove Process
     Resolving Dependencies

     Running transaction check
     Package epel-release.noarch 0:6-8 will be erased  Finished Dependency Resolution

     Dependencies Resolved
     Package        Architecture        Version        Repository        Size
     Removing:  epel-release        noarch         6-8        @extras        22 k
     Transaction Summary                                
     Remove        1 Package(s)
     Installed size: 22 k
     Downloading Packages:
     Running rpmcheckdebug
     Running Transaction Test
     Transaction Test Succeeded
     Running Transaction
     Erasing : epel-release-6-8.noarch
     Verifying : epel-release-6-8.noarch
     Removed:
     epel-release.noarch 0:6-8
     Complete!
     Dislocker is installed by the script.
     OpenSSL is already installed.
     ```

1. Run the Data Box Disk Unlock tool. Supply the passkey from the Azure portal you obtained in [Connect to disks and get the passkey](#connect-to-disks-and-get-the-passkey). Optionally specify a list of BitLocker encrypted volumes to unlock. The passkey and volume list should be specified within single quotes.

   Type the following command.

   ```bash
   sudo ./DataBoxDiskUnlock /PassKey:'<Your passkey from Azure portal>'
   ```

   The sample output is shown below.

   ```output
   [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock /Passkey:'qwerqwerqwer'

   START: Mon Aug 13 14:25:49 2018
   Volumes: /dev/sdbl
   Passkey: qwerqwerqwer

   Volumes for data copy :
   /dev/sdbl: /mnt/DataBoxDisk/mountVoll/
   END: Mon Aug 13 14:26:02 2018
   ```
   The mount point for the volume that you can copy your data to is displayed.

1. Repeat unlock steps for any future disk reinserts. Use the `help` command if you need help with the Data Box Disk unlock tool.

    `sudo ./DataBoxDiskUnlock /Help`

    The sample output is shown below.

    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock /Help
    START: Mon Aug 13 14:29:20 2018
    USAGE:
    sudo DataBoxDiskUnlock /PassKey:'<passkey from Azure_portal>'

    Example: sudo DataBoxDiskUnlock /PassKey:'passkey'
    Example: sudo DataBoxDiskUnlock /PassKey:'passkey' /Volumes:'/dev/sdbl'
    Example: sudo DataBoxDiskUnlock /Help Example: sudo DataBoxDiskUnlock /Clean

    /PassKey: This option takes a passkey as input and unlocks all of your disks.
    Get the passkey from your Data Box Disk order in Azure portal.
    /Volumes: This option is used to input a list of BitLocker encrypted volumes.
    /Help: This option provides help on the tool usage and examples.
    /Unmount: This option unmounts all the volumes mounted by this tool.

    END: Mon Aug 13 14:29:20 2018 [user@localhost Downloads]$
    ```

1. Once the disk is unlocked, you can go to the mount point and view the contents of the disk. You are now ready to copy the data to *BlockBlob* or *PageBlob* folders.

    ![Data Box Disk contents 2](media/data-box-disk-deploy-set-up/data-box-disk-content-linux.png)

    > [!NOTE]
    > Don't format or modify the contents or existing file structure of the disk.
-->

Perform the following steps to connect and unlock hardware encrypted Data Box disks on a Linux-based machine.

> [!WARNING]
> Enabling the TPM on a device might require a reboot.
>
> The following example contains the `reboot` command. Ensure that no data will be lost before running the script.

1.	The Trusted Platofrm Module (TPM) must be enabled on Linux systems for SATA-based drives. To enable TPM, set `libata.allow_tpm` to `1` by editing the GRUB config as shown in the following example. More details can be found on the Drive-Trust-Alliance public Wiki located at [https://github.com/Drive-Trust-Alliance/sedutil/wiki](https://github.com/Drive-Trust-Alliance/sedutil/wiki).

    ```bash
    sudo nano /etc/default/grub
    GRUB_CMDLINE_LINUX_DEFAULT="quiet splash libata.allow_tpm=1"
    sudo update-grub
    reboot
    ```

1.  Download the [Data Box Disk toolset](https://aka.ms/databoxdisktoolswin), copy the **Data Box Disk Unlock Utility** and extract it to a local path on the machine.
1.	Download the [SEDUtil](https://github.com/Drive-Trust-Alliance/sedutil/wiki/Executable-Distributions). For more information, visit the [Drive-Trust-Alliance public Wiki](https://github.com/Drive-Trust-Alliance/sedutil/wiki).

    > [!IMPORTANT]
    > SEDUtil is an external utility for Self-Encrypting Drives. This is not managed by Microsoft. More information, including license information for this utility, can be found at [https://sedutil.com/](https://sedutil.com/).

1.	Extract `SEDUtil` to a local path on the machine and add the extracted tool path to the `PATH` environment variable. This is required in a later step when the utility utilizes `SEDUtil` to unlock the disk.

    ### [CentOS](#tab/centos)

    CentOS steps

    ### [Debian](#tab/debian)

    Debian steps

    ### [Red Hat Enterprise Linux](#tab/rhel)

    RHEL steps

    ### [Ubuntu](#tab/ubuntu)

    Ubuntu steps

    ### [Another distro](#tab/another)

    Steps for another distro go here.

    ---

1. Validate that `SEDUtil` has been extracted to a local path and that the `PATH` environment variable contains the it's path. Use the following command to perform the Validation.

    ```bash
    sudo sedutil-cli --scan
    ```

    The following example output confirms that the validation completed successfully.
   
    :::image type="content" source="media/data-box-disk-deploy-set-up/scan-results.png" alt-text="Screen capture showing the successful results when scanning a system for Data Box Disks." lightbox="media/data-box-disk-deploy-set-up/scan-results-lrg.png":::

1.  Azure disks can be identified using the following command. Disk serial numbers can be verified for a volume using the following command.

     ```bash
     sedutil-cli --query <volume> 
     ```

    :::image type="content" source="media/data-box-disk-deploy-set-up/disk-serial.png" alt-text="Screen capture of example output of the sedutil tool showing identified volumes." lightbox="media/data-box-disk-deploy-set-up/disk-serial-lrg.png":::
   
1.	Run the **Data Box Disk Unlock Utility** from the Linux toolset extracted in a previous step. Supply the passkey from the Azure portal you obtained from the **Connect to disks** section. Optionally, you can specify a list of Bitlocker encrypted volumes to unlock. The passkey and volume list should be specified within single quotes as shown in the following example.

    ```bash
    chmod +x DataBoxDiskUnlock
    sudo ./DataBoxDiskUnlock /Volumes:'/dev/sdb' /PassKey:'<Your passkey from Azure portal>' /sed
    ```
   
    The following example output indicates that the volume was successfully unlocked. The mount point is also displayed for the volume in whick your data can be copied.

    :::image type="content" source="media/data-box-disk-deploy-set-up/disk-unlocked.png" alt-text="Screen capture showing showing a successfully unlocked data box disk.":::

    > [!IMPORTANT]
    > Repeat the steps to unlock the disk for any future disk reinserts.

    You can use the help switch if you need additional assistance with the Data Box Disk Unlock Utility as shown in the following example.

    ```bash
    sudo ./DataBoxDiskUnlock /Help /SED
    ```

    The following image shows the sample output.

    :::image type="content" source="media/data-box-disk-deploy-set-up/help-output.png" alt-text="Screen capture displaying sample output from the Data Box Disk Unlock Utility help command." lightbox="media/data-box-disk-deploy-set-up/help-output-lrg.png":::

1. After the disk is unlocked, you can go to the mount point and view the contents of the disk. You are now ready to copy the data to folders based on the desired destination data type.
1. After the required data is copied to the disk, make sure to unmount and remove the disk safely using the following command.
    ```bash
    sudo ./DataBoxDiskUnlock /Unmount  /sed
    ```

    The following example output confirms that the volume unmounted successfully.
1. Waiting additional step 10 data from Shashank
1. Waiting additional step 11 data from Shashank

If you run into any issues while unlocking the disks, see how to [troubleshoot unlock issues](data-box-disk-troubleshoot-unlock.md).


### [Linux - software encryption](#tab/linux-software)

Perform the following steps to connect and unlock software encrypted Data Box disks on a Linux-based machine.

1. In the Azure portal, go to **General > Device details**.
1. Download the Data Box Disk toolset corresponding to the Linux client.

    > [!div class="nextstepaction"]
    > [Download Data Box Disk toolset for Linux](https://aka.ms/databoxdisktoolslinux)

1. On your Linux client, open a terminal. Navigate to the folder containing . Change the file permissions so that you can execute these files. Type the following command:

    `chmod +x DataBoxDiskUnlock`

    `chmod +x DataBoxDiskUnlock_Prep.sh`

    A sample output is shown below. Once the chmod command is run, you can verify that the file permissions are changed by running the `ls` command.

    ```
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_Prep.sh
        [user@localhost Downloads]$ ls -l
        -rwxrwxr-x. 1 user user 1152664 Aug 10 17:26 DataBoxDiskUnlock
        -rwxrwxr-x. 1 user user 795 Aug 5 23:26 DataBoxDiskUnlock_Prep.sh
    ```

1. Execute the script so that it installs all the binaries needed for the Data Box Disk Unlock software. Use `sudo` to run the command as root. Once the binaries are successfully installed, you will see a note to that effect on the terminal.

    `sudo ./DataBoxDiskUnlock_Prep.sh`

    The script will first check whether your client computer is running a supported operating system. A sample output is shown below.

    ```
    [user@localhost Documents]$ sudo ./DataBoxDiskUnlock_Prep.sh
        OS = CentOS Version = 6.9
        Release = CentOS release 6.9 (Final)
        Architecture = x64

        The script will install the following packages and dependencies.
        epel-release
        dislocker
        ntfs-3g
        fuse-dislocker
        Do you wish to continue? y|n :|
    ```

1. Type `y` to continue the install. The packages that the script installs are:
   - **epel-release** - Repository that contains the following three packages.
   - **dislocker and fuse-dislocker** - These utilities helps decrypting BitLocker encrypted disks.
   - **ntfs-3g** - Package that helps mount NTFS volumes.

     Once the packages are successfully installed, the terminal will display a notification to that effect.
     ```
     Dependency Installed: compat-readline5.x86 64 0:5.2-17.I.el6 dislocker-libs.x86 64 0:0.7.1-8.el6 mbedtls.x86 64 0:2.7.4-l.el6        ruby.x86 64 0:1.8.7.374-5.el6
     ruby-libs.x86 64 0:1.8.7.374-5.el6
     Complete!
     Loaded plugins: fastestmirror, refresh-packagekit, security
     Setting up Remove Process
     Resolving Dependencies

     Running transaction check
     Package epel-release.noarch 0:6-8 will be erased  Finished Dependency Resolution

     Dependencies Resolved
     Package        Architecture        Version        Repository        Size
     Removing:  epel-release        noarch         6-8        @extras        22 k
     Transaction Summary                                
     Remove        1 Package(s)
     Installed size: 22 k
     Downloading Packages:
     Running rpmcheckdebug
     Running Transaction Test
     Transaction Test Succeeded
     Running Transaction
     Erasing : epel-release-6-8.noarch
     Verifying : epel-release-6-8.noarch
     Removed:
     epel-release.noarch 0:6-8
     Complete!
     Dislocker is installed by the script.
     OpenSSL is already installed.
     ```

1. Run the Data Box Disk Unlock tool. Supply the passkey from the Azure portal you obtained in [Connect to disks and get the passkey](#connect-to-disks-and-get-the-passkey). Optionally specify a list of BitLocker encrypted volumes to unlock. The passkey and volume list should be specified within single quotes.

   Type the following command.

    ```bash
    sudo ./DataBoxDiskUnlock /PassKey:'<Passkey from Azure portal>' 
        /SerialNumbers: '22183820683A;221838206839'
    ```

   The sample output is shown below.

    :::image type="content" source="/media/data-box-disk-deploy-setup/bitlocker-unlock-linux.png" alt-text="Screenshot of output showing successfully unlocked Data Box disks.":::
   
    ```output
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock /Passkey:'qwerqwerqwer'

    START: Mon Aug 13 14:25:49 2018
    Volumes: /dev/sdbl
    Passkey: qwerqwerqwer

    Volumes for data copy :
    /dev/sdbl: /mnt/DataBoxDisk/mountVoll/
    END: Mon Aug 13 14:26:02 2018
    ```
    The mount point for the volume that you can copy your data to is displayed.

1. Repeat unlock steps for any future disk reinserts. Use the `help` command if you need help with the Data Box Disk unlock tool.

    `sudo ./DataBoxDiskUnlock /Help`

    The sample output is shown below.

    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock /Help
    START: Mon Aug 13 14:29:20 2018
    USAGE:
    sudo DataBoxDiskUnlock /PassKey:'<passkey from Azure_portal>'

    Example: sudo DataBoxDiskUnlock /PassKey:'passkey'
    Example: sudo DataBoxDiskUnlock /PassKey:'passkey' /Volumes:'/dev/sdbl'
    Example: sudo DataBoxDiskUnlock /Help Example: sudo DataBoxDiskUnlock /Clean

    /PassKey: This option takes a passkey as input and unlocks all of your disks.
    Get the passkey from your Data Box Disk order in Azure portal.
    /Volumes: This option is used to input a list of BitLocker encrypted volumes.
    /Help: This option provides help on the tool usage and examples.
    /Unmount: This option unmounts all the volumes mounted by this tool.

    END: Mon Aug 13 14:29:20 2018 [user@localhost Downloads]$
    ```

1. Once the disk is unlocked, you can go to the mount point and view the contents of the disk. You are now ready to copy the data to *BlockBlob* or *PageBlob* folders.

    ![Data Box Disk contents 2](media/data-box-disk-deploy-set-up/data-box-disk-content-linux.png)

    > [!NOTE]
    > Don't format or modify the contents or existing file structure of the disk.

---

::: zone-end

::: zone target="chromeless"

1. Unpack disks and use the included cable to connect the disk to the client computer.
2. Download and extract the Data Box Disk toolset on the same computer that you will use to copy the data.

    > [!div class="nextstepaction"]
    > [Download Data Box Disk toolset for Windows](https://aka.ms/databoxdisktoolswin)

    or
    > [!div class="nextstepaction"]
    > [Download Data Box Disk toolset for Linux](https://aka.ms/databoxdisktoolslinux)

3. To unlock the disks on a Windows client, open a Command Prompt window or run Windows PowerShell as administrator on the same computer:

    - Type the following command in the same folder where Data Box Disk Unlock tool is installed.

        ```
        .\DataBoxDiskUnlock.exe
        ```
    -  Get the passkey from **General > Device details** in the Azure portal and provide it here. The drive letter assigned to the disk is displayed.
4. To unlock the disks on a Linux client, open a terminal. Go to the folder where you downloaded the software. Type the following commands to change the file permissions so that you can execute these files:

    ```
    chmod +x DataBoxDiskUnlock
    chmod +x DataBoxDiskUnlock_Prep.sh
    ```
    Execute the script to install all the required binaries.

    ```
    sudo ./DataBoxDiskUnlock_Prep.sh
    ```
    Run the Data Box Disk Unlock tool. Get the passkey from **General > Device details** in the Azure portal and provide it here. Optionally specify a list of BitLocker encrypted volumes within single quotes to unlock.

    ```
    sudo ./DataBoxDiskUnlock /PassKey:'<Your passkey from Azure portal>'
    ```
5. Repeat the unlock steps for any future disk reinserts. Use the help command if you need help with the Data Box Disk unlock tool.

After the disk is unlocked, you can view the contents of the disk.

For more information on how to set up and unlock disks, go to [Set up Data Box Disk](data-box-disk-deploy-set-up.md).

::: zone-end

::: zone target="docs"

## Next steps

In this tutorial, you learned about Azure Data Box Disk topics such as:

> [!div class="checklist"]
> * Unpack your Data Box Disk
> * Connect to disks and get the passkey
> * Unlock disks on Windows client
> * Unlock disks on Linux client


Advance to the next tutorial to learn how to copy data on your Data Box Disk.

> [!div class="nextstepaction"]
> [Copy data on your Data Box Disk](./data-box-disk-deploy-copy-data.md)

::: zone-end
