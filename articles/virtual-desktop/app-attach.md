---
title: Configure Azure Virtual Desktop MSIX app attach PowerShell scripts - Azure
description: How to create PowerShell scripts for MSIX app attach for Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 01/18/2023
ms.author: helohr
manager: femila
---
# PowerShell scripts for MSIX app attach

This topic will walk you through how to set up PowerShell scripts for testing and troubleshooting MSIX app attach disk images outside of Azure Virtual Desktop. If you want to use app attach with Azure Virtual Desktop, we recommend [using the Azure portal to manage app attach](app-attach-azure-portal.md).

## Prepare PowerShell scripts for MSIX app attach

The MSIX app attach setup process has four distinct phases that you must perform in the following order, otherwise it won't work:

1. Stage
2. Register
3. Deregister
4. Destage

Staging and Destaging are machine-level operations, while registering and deregistering are user-level operations. The commands you'll need to use will vary based on which version of PowerShell you're using and whether your disk images are in VHD(X) or cim format.

>[!NOTE]
>All MSIX application packages include a certificate. You're responsible for making sure the certificates for MSIX applications are trusted in your environment.

## Stage PowerShell script

The first part of setting up app attach with PowerShell involves staging the script. This involves installing the relevant MSIX package from teh NuGet repository, and you'll only need to run the following commands once per machine during image creation.

However, if you're using an image in cim format or a version of PowerShell greater than 5.1, the instructions will look a bit different. Later versions of PowerShell are multi-platform, which means the Windows application parts are split off into their own package called [Windows Runtime](/windows/uwp/winrt-components/). You'll need to use a slightly different version of the commands to install a package with a multi-platform version of PowerShell.

Before you stage your PowerShell script, you'll need to get elevated privileges by running the following command:

```powershell
#Required for PowerShell versions greater than 5.1
$nuGetPackageName = 'Microsoft.Windows.SDK.NET.Ref'
Register-PackageSource -Name MyNuGet -Location https://www.nuget.org/api/v2 -ProviderName NuGet
Find-Package $nuGetPackageName | Install-Package
```

Next, you'll need to install the package based on whether you're using [a PowerShell version later than 5.1](#install-an-package-using-a-later-powershell-version), [PowerShell version 5.1 or earlier](#install-a-package-using-powershell-51-and-earlier), or are [using a cim disk image](#install-a-package-on-a-cimfs-disk-image). After you complete those instructions, proceed to [mount your disk image](#mount-your-disk-image).

### Install an package using a later PowerShell version

To stage packages at boot using a PowerShell version greater than 5.1 you will need the following commands before the staging operations to bring the capabilities of the Windows Runtime package you previously installed into the PowerShell session.

```powershell
#Required for PowerShell versions greater than 5.1
$nuGetPackageName = 'Microsoft.Windows.SDK.NET.Ref'
$winRT = Get-Package $nuGetPackageName
$dllWinRT = Get-Childitem (Split-Path -Parent $winRT.Source) -Recurse -File WinRT.Runtime.dll
$dllSdkNet = Get-Childitem (Split-Path -Parent $winRT.Source) -Recurse -File Microsoft.Windows.SDK.NET.dll
Add-Type -AssemblyName $dllWinRT.FullName
Add-Type -AssemblyName $dllSdkNet.FullName
```

### Install a package using Powershell 5.1 and earlier

To stage packages at boot with PowerShell version 5.1 or earlier, run this command:

```powershell
#Required for PowerShell versions less than or equal to 5.1
[Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime] | Out-Null
Add-Type -AssemblyName System.Runtime.WindowsRuntime
```

### Install a package on a CimFS disk image

If your disk image is in the [CimFS](/windows/win32/api/_cimfs/) format, you'll need run the following cmdlets to install a PowerShell module from the PowerShell image gallery in order to use the commands in this article.

```powershell
Install-Module CimDiskImage
Import-Module CimDiskImage
```

>[!NOTE]
>Microsoft Support doesn't currently support this module, so if you run into any problems, you'll need to submit a request on [the module's Github repository](https://github.com/JimMoyle/CimDiskImage-PowerShell/).

## Mount your disk image

Now that you've prepared your machine to stage MSIX app attach packages, you'll need to mount your disk image. This process will vary depending on which format you're using for your disk image.

>[!NOTE]
>Make sure to record the full name of the MSIX package for each application the commands output. You'll need those names to follow directions later in this article.

To mount a VHD(X) disk image, run this command:

```powershell
$diskImage = "<UNC path to the Disk Image>"

$mount = Mount-Diskimage -ImagePath $diskImage -PassThru -NoDriveLetter -Access ReadOnly

#We can now get the Device Id for the mounted volume, this will be useful for the destage step. This 
$partition = Get-Partition -DiskNumber $mount.Number
$DeviceId = $partition.AccessPaths
Write-OutPut $DeviceId
```

To mount a CimFS disk image, run this command:

```powershell
$diskImage = "<UNC path to the Disk Image>"

$mount = Mount-CimDiskimage -ImagePath $diskImage -PassThru -NoMountPath

#We can now get the Device Id for the mounted volume, this will be useful for the destage step.
Write-Output $mount.DeviceId
```

Finally, you'll need to run the following command for all image types. This command will use the $mount variable you created when you mounted your disk image in the previous section.

```powershell
#Once the volume is mounted we can retrieve the application information
$manifest = Get-Childitem -LiteralPath $mount.DeviceId -Recurse -File AppxManifest.xml
$manifestFolder = $manifest.DirectoryName

#We can now get the MSIX package full name, this will be needed for later steps.
$msixPackageFullName = $manifestFolder.Split('\')[-1]
Write-Output $msixPackageFullName

#We need to create an absolute uri for the manifest folder for the Package Manager API
$folderUri = $maniFestFolder.Replace('\\?\','file:\\\')
$folderAbsoluteUri = ([Uri]$folderUri).AbsoluteUri

#Package Manager will now use the absolute uri to stage the application package
$asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where-Object { $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult] AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])' })[0]
$asTaskAsyncOperation = $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult], [Windows.Management.Deployment.DeploymentProgress])

$packageManager = New-Object -TypeName Windows.Management.Deployment.PackageManager

$asyncOperation = $packageManager.StagePackageAsync($folderAbsoluteUri, $null, "StageInPlace")
$stagingResult = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))

#You can check the $stagingResult variable to  monitor the staging progress for the appliocation package
Write-Output $stagingResult
```

Your application is now ready to be registered.

## Register PowerShell script

To run the register script, run the following PowerShell cmdlets with the placeholder values replaced with values that apply to your environment.

```powershell
$msixPackageFullName = "<package full name>"

$manifestPath = Join-Path (Join-Path $Env:ProgramFiles 'WindowsApps') (Join-Path $msixPackageFullName AppxManifest.xml)
Add-AppxPackage -Path $manifestPath -DisableDevelopmentMode -Register
```

The *$msixPackageFullName* parameter should be the full name of the package from the previous section, but you should format it similar to the following example:

```powershell
Publisher.Application_version_Platform__HashCode
```

If you didn't retrieve the parameter after staging your app, you can also find it as the folder name for the app itself in **C:\Program Files\WindowsApps**.

## Deregister PowerShell script

Now it's time to Deregister your package. For this, you'll need the *$msixPackageFullName* parameter again.

To deregister, run the following command after replacing the placeholder text with the relevant values:

```powershell
$msixPackageFullName = "<package full name>"

Remove-AppxPackage $msixPackageFullName -PreserveRoamableApplicationData
```

## Destage PowerShell script

To destage your PowerShell script, make sure you're running an elevated PowerShell prompt. You'll need to run the following PowerShell command to get the disk's DeviceId parameter. Replace the placeholder for **$packageFullName** with the name of the package you're testing. In a production deployment, we recommend only running this command when shutting down your deployment.

```powershell
$msixPackageFullName = "<package full name>"

#If you don't know the DeviceId, you can find it using this code.
$appPath = Join-Path (Join-Path $Env:ProgramFiles 'WindowsApps') $msixPackageFullName
$folderInfo = Get-Item $appPath
$DeviceId = '\\?\' + $folderInfo.LinkTarget.Split('\')[0] +'\'
Write-Output $DeviceId #Save this for later

Remove-AppxPackage -AllUsers -Package $msixPackageFullName
Remove-AppxPackage -Package $msixPackageFullName
```

### Dismount the disks from the system

To finish the destaging process, you'll need to dismount the disks from the system. The command you'll need to use depends on which format your disk images are in.

If your image is in CimFS format, run this cmdlet:

```powershell
DisMount-CimDiskimage -DeviceId $DeviceId
```

If your image is in VHD(X) format, run this cmdlet:

```powershell
DisMount-DiskImage -DevicePath $DeviceId.TrimEnd('\')
```

Once you finish dismounting your disks, your scripts are all set!

## Set up simulation scripts for the MSIX app attach agent

After you create the scripts, users can manually run them or set them up to run automatically as startup, logon, logoff, and shutdown scripts. To learn more about these types of scripts, see [Using startup, shutdown, logon, and logoff scripts in Group Policy](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/).

Each of these automatic scripts runs one phase of the app attach scripts:

- The startup script runs the stage script.
- The logon script runs the register script.
- The logoff script runs the deregister script.
- The shutdown script runs the destage script.

>[!NOTE]
>You can run the task scheduler with the stage script. To run the script, set the task trigger to **When the computer starts**, then enable **Run with highest privileges**.

## Use packages offline

If you're using packages from the [Microsoft Store for Business](https://businessstore.microsoft.com/) or the [Microsoft Store for Education](https://educationstore.microsoft.com/) within your network or on devices that aren't connected to the internet, you need to get the package licenses from the Microsoft Store and install them on your device to successfully run the app. If your device is online and can connect to the Microsoft Store for Business, the required licenses should download automatically, but if you're offline, you'll need to set up the licenses manually.

To install the license files, you'll need to use a PowerShell script that calls the MDM_EnterpriseModernAppManagement_StoreLicenses02_01 class in the WMI Bridge Provider.

Here's how to set up the licenses for offline use:

1. Download the app package, licenses, and required frameworks from the Microsoft Store for Business. You need both the encoded and unencoded license files. Detailed download instructions can be found [here](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Update the following variables in the script for step 3:
      - `$contentID` is the ContentID value from the Unencoded license file (.xml). You can open the license file in a text editor of your choice.
      - `$licenseBlob` is the entire string for the license blob in the Encoded license file (.bin). You can open the encoded license file in a text editor of your choice.
3. Run the following script from an Admin PowerShell prompt. A good place to perform license installation is at the end of the [staging script](#stage-powershell-script) that also needs to be run from an Admin prompt.

      ```powershell
      $namespaceName = "root\cimv2\mdm\dmmap"
      $className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
      $methodName = "AddLicenseMethod"
      $parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"
      
      #TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
      $contentID = "{'ContentID'_in_unencoded_license_file}"
      
      #TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
      $licenseBlob = "{Entire_String_in_encoded_license_file}"
      
      $session = New-CimSession
      
      #The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
      $licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />'
      
      $params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
      $param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
      $params.Add($param)


      try
      {
         $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
         $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

      }
      catch [Exception]
      {
           write-host $_ | out-string
      }
      ```

## Next steps

If you have any questions, you can ask them at the [Azure Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

You can also leave feedback for Azure Virtual Desktop at the [Azure Virtual Desktop feedback hub](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).