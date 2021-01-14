---
title: Configure Windows Virtual Desktop AD FS single sign-on - Azure
description: How to configure AD FS single sign-on for a Windows Virtual Desktop environment.
services: virtual-desktop
author: Heidilohr
manager: lizross

ms.service: virtual-desktop
ms.topic: how-to
ms.date: 11/01/2020
ms.author: helohr
---
# Configure AD FS single sign-on for Windows Virtual Desktop
This article will walk you through the process of configuring Active Directory Federation Service (AD FS) single sign-on (SSO) for Windows Virtual Desktop.

> [!IMPORTANT]
> AD FS single sign-on is currently in public preview.
> This preview version is provided without a service level agreement, and we don't recommend using it for production workloads. Certain features might not be supported or might have constrained capabilities.
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Prerequisites
Before configuring AD FS single sign-on, you must have the following setup and running in your environment:

- **Active Directory Certificate Services** - For the Active Directory Certificate Services role, make sure the servers running the role are domain-joined, have the latest Windows updates installed, and are configured as [enterprise certificate authorities](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731183%28v%3dws.10%29).
- **Active Directory Federation Services (AD FS)** - The servers running this role must be domain-joined, have the latest Windows Updates installed, and running Windows Server 2016 or later. See this [federation tutorial](https://docs.microsoft.com/azure/active-directory/hybrid/tutorial-federation) to get started.
- **Web Application Proxy** - Recommended to secure the environment as a frontend to the AD FS servers. The servers running this role must have the latest Windows Updates installed, and running Windows Server 2016 or later. See this [Web Application Proxy guide](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383662(v=ws.11)) to get started.
- **Azure AD Connect** - For the Azure Active Directory (AD) Connect role, your service must be configured in [federation mode](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom).

## Supported clients

This feature is currently supported on the following Windows Virtual Desktop Clients.

- [Windows Desktop client](connect-windows-7-10.md)
- [Windows Store client](connect-microsoft-store.md)
- [Web client](connect-web.md)

## Configure the certificate authority to issue smart cards

You must properly configure the following certificate templates so that AD FS can use SSO:

* Exchange Enrollment Agent (Offline Request) – AD FS uses the Exchange Enrollment Agent certificate template to request smart card logon certificates on the user's behalf. The smart card logon certificate template is the foundation of the smart card logon certificate.
* Smart card logon – AD FS will use this certificate template as the basis of the smart card logon certificate.

Once you create these certificate templates, you must enable them on the certificate authority so AD FS can request them.

### Configure the enrollment agent certificate template

1. On the certificate authority, run **mmc.exe** from the Start menu to launch the **Microsoft Management Console**.
2. Select **File...** > **Add/Remote Snap-in...** > **Certificate Templates** > **Add >** > **OK** to view the list of certificate templates.
3. Expand the **Certificate Templates**, right-click **Exchange Enrollment Agent (Offline Request)** and select **Duplicate Template**.
4. Select the **General** tab, then enter "ADFS Enrollment Agent" into the **Template display name** field. This will automatically set the template name to "ADFSEnrollmentAgent".
5. Select the **Security** tab, then select **Add...**.
6. Next, select **Object Types...**, then **Service Accounts**, and then **OK**.
7. Enter the service account name for AD FS and select **OK**.
     * In an isolated AD FS setup, the service account will be named `adfssvc$`.
     * If you setup AD FS via Azure AD Connect, the service account will be named `aadcsvc$`.
8. After the service account is added and is visible in the **Security** tab, select it in the **Group or user names** pane, select **Allow** for both "Enroll" and "Autoenroll", then select **OK** to save.

    ![A screenshot showing the security tab of the Enrollment Agent certificate template after it is properly configured](media/adfs-enrollment-properties-security.png)

### Configure the smart card logon certificate template for interactive logon

1. On the certificate authority, run **mmc.exe** from the Start menu to launch the **Microsoft Management Console**.
2. Select **File...** > **Add/Remote Snap-in...** > **Certificate Templates** > **Add >** > **OK** to view the list of certificate templates.
3. Expand the **Certificate Templates**, right-click **Smartcard Logon** and select **Duplicate Template**.
4. Select the **General** tab, then enter "ADFS SSO" into the **Template display name** field. This will automatically set the template name to "ADFSSSO".
   > [!NOTE]
   > Since this certificate is requested on-demand, we recommend shortening the validity period to 8 hours and the renewal period to 1 hour.

5. Select the **Subject name** tab and then select **Supply in the request**. Accept the warning by clicking OK.
  
    [ ![A screenshot showing  the subject name tab of the SSO certificate template and what it should look like when properly configured](media/adfs-sso-properties-subject-inline.png) ](media/adfs-sso-properties-subject-expanded.png#lightbox)

6. Select the **Issuance Requirements** tab.
7. Select **This number of authorized signatures** and enter the value of **1**.
   
    [ ![A screenshot showing the issuance requirements tab of the SSO certificate template and what it should look like when properly configured](media/adfs-sso-properties-issuance-inline.png) ](media/adfs-sso-properties-issuance-expanded.png#lightbox)

8. For **Application policy**, select **Certificate Request Agent**.
9.  Select the **Security** tab, then select **Add...**.
10. Select **Object Types...**, **Service Accounts**, and **OK**.
11. Enter the service account name for AD FS just like you did in the [Configure the enrollment agent certificate template](#configure-the-enrollment-agent-certificate-template) section.
     * In an isolated AD FS setup, the service account will be named `adfssvc$`.
     * If you setup AD FS via Azure AD Connect, the service account will be named `aadcsvc$`.

12. After the service account is added and is visible in the **Security** tab, select it in the **Group or user names** pane, select **Allow** for both "Enroll" and "Autoenroll", then select **OK** to save.

     ![A screenshot showing the security tab of the SSO certificate template after it is properly configured](media/adfs-sso-properties-security.png)

### Enable the new certificate templates on the certificate authority

1. On the certificate authority, launch **MMC** from the Start menu.
2. Select **File...** > **Add/Remove Snap-in...** > **Certification Authority** > **Add >** > **Finish** > and **OK** to view the Certification Authority.
3. Expand the Certification Authority on the left-hand pane and open **Certificate Templates**.
4. Right-click in the middle-pane with the list of certificate templates, select **New**, and select **Certificate Template to Issue**.
5. Select both **ADFS Enrollment Agent** and **ADFS SSO**, then select **OK**. You should see both templates in the middle-pane.
    ![A screenshot showing list of certificate templates that can be issued, including the new "ADFS Enrollment Agent" and "ADFS SSO".](media/adfs-certificate-templates.png)
6. On the AD FS VM, run the following PowerShell command to configure AD FS to use the certificate templates:
  ```powershell
  Set-AdfsCertificateAuthority -EnrollmentAgentCertificateTemplate "ADFSEnrollmentAgent" -LogonCertificateTemplate "ADFSSSO" -EnrollmentAgent
  ```

## Configure a relying-party trust on your AD FS

You must create a relying-party trust between your AD FS server and the Windows Virtual Desktop service so single sign-on certificate requests can be forwarded correctly to your domain environment.

The PowerShell script **ConfigureWVDSSO.ps1** available in the [PowerShell Gallery](https://www.powershellgallery.com/packages/ConfigureWVDSSO) will configure your AD FS server for the relying-party trust.

This script only has one required parameter:

- *ADFSAuthority* - This is the URL that resolves to your AD FS and uses "/adfs" as its suffix. For example, `https://adfs.contoso.com/adfs`.

To configure AD FS single sign-on for users in your Windows Virtual Desktop tenant, run the following PowerShell cmdlet on the AD FS server with `ADFSServiceUrl` replaced with the full URL to reach your AD FS service:

```powershell
Install-Script ConfigureWVDSSO
 $config = ConfigureWVDSSO.ps1 -ADFSAuthority "<ADFSServiceUrl>"
```

By default, this will use a shared key as the secret. If you prefer using a certificate, save the pfx file locally on the AD FS server and add the necessary parameters, specifying the path to the pfx file:

```powershell
Install-Script ConfigureWVDSSO
 $config = ConfigureWVDSSO.ps1 -ADFSAuthority "<ADFSServiceUrl>" -UseCert -CertPath "Path to the pfx file"
```

If you are enabling SSO in one of the sovereign clouds, you will need to change the location of the Windows Virtual Desktop service with additional parameters, replacing WvdWebAppAppUri and RdWebURL with the appropriate values.

```powershell
Install-Script ConfigureWVDSSO
 $config = ConfigureWVDSSO.ps1 -ADFSAuthority "<ADFSServiceUrl>" -WvdWebAppAppIDUri "https://www.wvd.microsoft.com" -RdWebURL "https://rdweb.wvd.microsoft.com"
```

> [!Note]
> You need the `$config` variable values to complete the next part of the instructions, so don't close the PowerShell window you used to complete the previous instructions. You can either keep using the same PowerShell window or leave it open while launching a new PowerShell session.

## Store the certificate or key in Azure Key Vault

The certificate or key used to generate the token to sign in to Windows must be stored securely in [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview). You can store the secret in an existing Key Vault or deploy a new one. In either case, you must ensure to set the right access policy so the Windows Virtual Desktop service can access it.

To set the access policy on the Key Vault:

1. Navigate to your Key Vault in the Azure Portal.
1. In the left pane, select **Access policies**.
1. Ensure the **Permission model** is set to "Vault access policy".
1. Click on **+ Add Access Policy**.
1. Expand the **Secret permissions** dropdown and select "Get".
1. Click **None selected** next to the "Select principal".
1. Search for and select **Windows Virtual Desktop** in the Principal pane. The associated ID is "9cdead84-a844-4324-93f2-b2e6bb768d07".
1. Click **Select** at the bottom and then **Add**.

If using a shared key, store it in Key Vault:

1. Navigate to your Key Vault in the Azure Portal.
1. In the left pane, select **Secrets**.
1. Click **+ Generate/Import**.
1. Leave **Upload options** to "Manual".
1. Provide a **Name**. Example: adfsssosecret.
1. From the previous PowerShell window where you ran ConfigureWVDSSO, type $config and copy the SSOClientSecret.
1. Paste the SSOClientSecret as the **Value** for the secret in the Azure Portal.
1. Click **Create**.

If using a certificate, store it in Key Vault:

1. Navigate to your Key Vault in the Azure Portal.
1. In the left pane, select **Certificates**.
1. Click **+ Generate/Import**.
1. Change the **Method of Certificate Creation** to "Import".
1. Provide a **Certificate Name**. Example: adfsssocert.
1. Click to **Upload Certificate File** and navigate to the pfx file.
1. Enter the **Password** if needed.
1. Click **Create**.

For added security, you must add a tag to the secret so it can only be used in specified subscriptions:

1. From your Key Vault, select your previously saved secret or certificate.
1. Select the ID under **CURRENT VERSION**.
1. Click on **Tags**.
1. Set the **Tag Name** to "AllowedWVDSubscriptions".
1. Set the **Tag Value** to a coma separated list of subscription IDs that can use this secret.
1. Click **OK**.
1. Copy the **Secret Identifier** for later. 
1. Click **Save**.

## Update your Windows Virtual Desktop Host Pool

Now it's time to configure the AD FS SSO parameters on your Windows Virtual Desktop Host Pool. To do this, [set up your PowerShell environment](powershell-module.md) for Windows Virtual Desktop if you haven't already and connect to your account.

After that, update the SSO information for your Host Pool by running the following cmdlet.

```powershell
Update-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" -SsoadfsAuthority "<ADFSServiceUrl>" -SsoClientId "https://www.wvd.microsoft.com" -SsoSecretType SharedKeyInKeyVault -SsoClientSecretKeyVaultPath <KeyVault secret identifier>
```

They **KeyVault secret identifier** is the one copied in the previous step. You can remove the version number so it has this format: `https://<KeyVaultName>.vault.azure.net/secrets/<SecretName>`.

If you are enabling SSO in one of the sovereign clouds, you'll need to change the SsoClientID with the appropriate URL.

If using a certificate instead of a shared key, replace the **SsoSecretType** with CertificateInKeyVault.

## Removing SSO

To disable SSO on the Host Pool, run the following cmdlet:

```powershell
Update-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" -SsoadfsAuthority '' -SsoClientId '' -SsoSecretType SharedKeyInKeyVault -SsoClientSecretKeyVaultPath ''
```

To also remove the configuration from your AD FS server, run the following:

```powershell
Install-Script UnConfigureWVDSSO
UnConfigureSSOClient.ps1
```

If you are disabling SSO in one of the sovereign clouds, you'll need to change the location of the Windows Virtual Desktop service by replacing WvdWebAppAppUri with the appropriate URL.

```powershell
Install-Script UnConfigureWVDSSO
UnConfigureSSOClient.ps1 -WvdWebAppAppIDUri "https://www.wvd.microsoft.com"
```

## Next steps

Now that you've configured single sign-on, you can sign in to a supported Windows Virtual Desktop client to test it as part of a user session. These next two How-tos will tell you how to connect to a session using two of the clients:

- [Connect with the Windows Desktop client](connect-windows-7-10.md)
- [Connect with the web client](connect-web.md)