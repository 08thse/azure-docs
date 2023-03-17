---
title: Troubleshoot Multimedia redirection on Azure Virtual Desktop - Azure
description: Known issues and troubleshooting instructions for multimedia redirection for Azure Virtual Desktop.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 02/07/2023
ms.author: helohr
manager: femila
---
# Troubleshoot multimedia redirection for Azure Virtual Desktop

> [!IMPORTANT]
> Azure Virtual Desktop webRTC-based calls are currently in PREVIEW.
> See the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

>[!NOTE]
>Azure Virtual Desktop doesn't currently support multimedia redirection on Azure Virtual Desktop for Microsoft 365 Government (GCC), GCC-High environments, and Microsoft 365 DoD.
>
>Multimedia redirection on Azure Virtual Desktop is only available for the Windows Desktop client on Windows 11, Windows 10, or Windows 10 IoT Enterprise devices.
>
>Multimedia redirection video playback redirection on Azure Virtual Desktop is only available for the [Windows Desktop client, version 1.2.3916 or later](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew).
> 
>Multimedia redirection WebRTC calling redirection (preview) on Azure Virtual Deskto pis only available for the Windows Desktop client, version x.x.xxxx or later with Insider releases enabled.

This article describes known issues and troubleshooting instructions for multimedia redirection (MMR) for Azure Virtual Desktop.

## Known issues and limitations

The following issues are ones we're already aware of, so you won't need to report them:

- In the first browser tab a user opens, the extension pop-up might show a message that says, "The extension is not loaded", or a message that says video playback or calling redirection isn't supported while redirection is working correctly in the tab. You can resolve this issue by opening a second tab. 

- Multimedia redirection only works on the [Windows Desktop client](users/connect-windows.md). Any other clients, such as the web client, don't support multimedia redirection. Clients on any other non-Windows platforms, such as the macOS, iOS, Android, or Linux clients, don't support multimedia redirection.

- Multimedia redirection won't work as expected if the VMs in your deployment are blocking cmd.exe.
  
- Multimedia redirection is disabled by default on all sites except for the ones listed in [Websites that work with multimedia redirection](multimedia-redirection-intro.md#websites-that-work-with-multimedia-redirection). However, you can enable multimedia redirection features for all websites by following the directions in [Enable video playback for all sites](multimedia-redirection.md#enable-video-playback-for-all-sites) and [Enable calling redirection for all sites](multimedia-redirection.md#enable-calling-redirection-for-all-sites). We added the option to enable multimedia redirection on sites that aren't officially supported so organizations can test the feature on their company websites.

### The MSI installer doesn't work

- There's a small chance that the MSI installer won't be able to install the extension during internal testing. If you run into this issue, you'll need to install the multimedia redirection extension from the Microsoft Edge Store or Google Chrome Store.

  - [Multimedia redirection browser extension (Microsoft Edge)](https://microsoftedge.microsoft.com/addons/detail/wvd-multimedia-redirectio/joeclbldhdmoijbaagobkhlpfjglcihd)
  - [Multimedia browser extension (Google Chrome)](https://chrome.google.com/webstore/detail/wvd-multimedia-redirectio/lfmemoeeciijgkjkgbgikoonlkabmlno)

- Installing the extension on host machines with the MSI installer will either prompt users to accept the extension the first time they open the browser or display a warning or error message. If users deny this prompt, it can cause the extension to not load. To avoid this issue, install the extensions by [editing the group policy](multimedia-redirection.md#install-the-browser-extension-using-group-policy).

- Sometimes the host and client version number disappears from the extension status message, which prevents the extension from loading on websites that support it. If you've installed the extension correctly, this issue is because your host machine doesn't have the latest C++ Redistributable installed. To fix this issue, install the [latest supported Visual C++ Redistributable downloads](/cpp/windows/latest-supported-vc-redist).

### Video playback redirection

- Video playback redirection doesn't currently support protected content, so videos from Pluralsight and Netflix won't work.

- When you resize the video window, the window's size will adjust faster than the video itself. You'll also see this issue when minimizing and maximizing the window.

- If you access a video site, sometimes the video will remain in a loading or buffering state but never actually start playing. We're aware of this issue and are currently investigating it. For now, you can make videos load again by signing out of Azure Virtual Desktop and restarting your session.

### WebRTC call redirection

- Calling redirection only works for WebRTC-based audio calls on the sites listed in [WebRTC call redirection](multimedia-redirection-intro#webrtc-call-redirection).

- When disconnecting from a remote session, calling redirection might stop working. You can make redirection start working again by refreshing the webpage.

## Log collection

If you encounter any issues, you can collect logs from the extension and provide them to your IT admin or support.

To enable log collection:

1. Select the multimedia redirection extension icon in your browser.

1. Select **Show Advanced Settings**.

2. For **Collect logs**, select **Start**.

## Next steps

For more information about this feature and how it works, see [What is multimedia redirection for Azure Virtual Desktop?](multimedia-redirection-intro.md).

To learn how to use this feature, see [Multimedia redirection for Azure Virtual Desktop](multimedia-redirection.md).
