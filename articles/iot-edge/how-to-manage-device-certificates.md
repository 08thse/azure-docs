---
title: Manage device certificates - Azure IoT Edge | Microsoft Docs
description: Create test certificates, install, and manage them on an Azure IoT Edge device to prepare for production deployment. 
author: PatAltimore

ms.author: patricka
ms.date: 08/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
---
# Manage certificates on an IoT Edge device

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

All IoT Edge devices use certificates to create secure connections between the runtime and any modules running on the device. IoT Edge devices functioning as gateways use these same certificates to connect to their downstream devices, too. For more information about the function of the different certificates on an IoT Edge device, see [Understand how Azure IoT Edge uses certificates](iot-edge-certs.md).

> [!NOTE]
> The term "root CA" used throughout this article refers to the topmost authority public certificate of the certificate chain for your IoT solution. You do not need to use the certificate root of a syndicated certificate authority, or the root of your organization's certificate authority. In many cases, it is actually an intermediate CA public certificate.

## How to apply configuration changes described in this article

Generally, the configuration involves editing `config.toml` and applying the change with `sudo iotedge config apply`.

## Format requirements

IoT Edge requires the certificate and private key to be:

- PEM format
- Separate files

> [!TIP]
> In public key cryptography, the public component is the "certificate". The private component is the "private key".
> 
> - The certificate can be encoded in a binary representation called DER, or a textual representation called PEM that is `-----BEGIN CERTIFICATE-----` + base64-encoded DER + `-----END CERTIFICATE-----`.
> - The private key can be encoded in a binary representation called DER, or a textual representation called PEM that is `-----BEGIN PRIVATE KEY-----` + base64-encoded DER + `-----END PRIVATE KEY-----`
> 
> Because PEM is delineated, it is also possible to construct a PEM that contains both the `CERTIFICATE` and `PRIVATE KEY` one after the other in the same file.
> 
> Lastly, the certificate and private key can be encoded together in another binary representation called PKCS#12, that is encrypted with an optional password.
> 
> File extensions are arbitrary and one needs to run `file` on the file or open it to check, but in general:
> 
> - `.cer` is probably a certificate in DER or PEM form.
> 
> - `.pem` is probably a certificate or private key or both in PEM form.
> 
> - `.pfx` is probably a PKCS#12 file.

### Files from Public Key Infrastructure (PKI) provider

If you get a `.pfx` file from your PKI provider, it's likely the certificate and private key in encoded together in one file. Double-check with `file` that it's a PKCS#12 file. You can convert your PKCS#12 `.pfx` file to PEM with [`openssl pkcs12` command](https://www.openssl.org/docs/man1.1.1/man1/pkcs12.html). The two `.pem` files are the certificate and private key.

If you get a `.cer` file as well, it may contain the same certificate as the `.pfx`, or it might be the PKI provider's issuing (root) certificate. To see which one it is, inspect it with `openssl x509` command. If it's the latter, then:

1. If it's currently in DER (binary) format, then convert it to PEM with `openssl x509 -in cert.cer -out cert.pem`, and 
1. Use the PEM as the [trust bundle](#manage-trusted-root-ca-or-trust-bundle).

## Install certificates and private keys

To install a certificate on IoT Edge, move the PEM file somewhere that IoT Edge can access like `/var/secrets/`. Then, in `config.toml`, set the path to the file using this pattern:

```toml
[<CERT_TYPE>]
cert = "file:///var/secrets/my-cert.pem"
pk = "file:///var/secrets/my-private-key.key.pem"
```
Ensure that IoT Edge's certificate service `aziotcs` and key service `aziotks` has at least read permission on the certificate and private key, respectively.

### Use PKCS#11 to store private key

To configure IoT Edge to use PKCS#11 interface for retrieving the private key, use specify the PKCS#11 URI in the configuration: 

```toml
pk = "pkcs11:slot-id=0;object=my%20pk?pin-value=1234"
```

You need to also add this other section:

```toml
[aziot_keys]
pkcs11_lib_path = "/usr/lib/libmypkcs11.so"
pkcs11_base_slot = "pkcs11:slot-id=0?pin-value=1234"
```

## Automatic certificate renewal

IoT Edge has built-in ability to renew certificates before expiry.

Certificates renewal requires an issuance method that IoT Edge can manage. Generally, this means an EST server is required, but IoT Edge can also automatically [renew the quickstart CA by default](#renew-quickstart-edge-ca). Certificate renewal is configured per type of certificate. To configure, go to the relevant certificate configuration section in `config.toml` and add:

```toml
[REPLACE_WITH_CERT_TYPE]
method = "est"

[REPLACE_WITH_CERT_TYPE.auto_renew]
rotate_key = true
threshold = "80%" 
retry = "4%"
```

Here:
- `rotate_key` controls if the private key should be rotated.
- `threshold` sets when IoT Edge should start renewing the certificate . It can be specified as:
    - *Percentage* -  integer between `0` and `100` followed by `%`. Renewal starts relative to the certificate lifetime. For example, when set to `80%`, a certificate that is valid for 100 days begins renewal at 20 days before its expiry. 
    - *Absolute time* - integer followed by `m` (minutes) or `d` (days). Renewal starts relative to the certificate expiration time. For example, when set to `4d` for 4 days or `10m` for 10 minutes, the certificate begins renewing at that time before expiry. To avoid unintentional misconfiguration where the `threshold` is bigger than the certificate lifetime, we recommend to use *percentage* instead whenever possible. 
- `retry` controls how often renewal should be retried on failure. Like `threshold`, it can similarly be specified as a *percentage* or *absolute time* using the same format.

## Enrollment over Secure Transport (EST)

IoT Edge can manage certificates automatically with an EST server. Using EST is recommended for production as it replaces the need for manual certificate management, which can be risky and error-prone. It can be configured globally and overridden for each certificate type. Add the following configuration to `config.toml` for global default configuration.

First, configure the path to a trusted root certificate that IoT Edge uses to validate the EST server's TLS certificate. This is optional if the EST server has a publicly-rooted TLS certificate.

```toml
[cert_issuance.est]
trusted_certs = [
    "file:///var/secrets/root-ca.pem",
]
```

To provide a default URL for the EST server, add:

```toml
[cert_issuance.est.urls]
default = "https://example.org/.well-known/est"
```

Depending on the EST server configuration, you may authenticate with certificates or shared secrets (username/password). 

To use certificate for authentication the EST server:

```toml
[cert_issuance.est.auth]
bootstrap_identity_cert = "file:///var/secrets/my-est-id-bootstrap-cert.pem"
bootstrap_identity_pk = "file:///var/secrets/my-est-id-bootstrap-pk.key.pem"

# identity_cert = "my-est-id-cert-name"
# identity_pk = "my-est-id-pk-name"
```

Here, the bootstrap certificate and private key is expected to be long-lived and potentially installed on the device during manufacturing. IoT Edge uses the bootstrap credentials to authenticate to the EST server for the initial request to issue another certificate (the EST identity certificate) that is used in subsequent requests. If you wish, use the `identity_cert` and `identity_pk` values to control the names the EST identity certificate and private key are saved under. These are optional since, if not set, IoT Edge provides a default value and automatically manages it. Like for other certificates, IoT Edge can be configured to automatically renew EST identity certificate and key as well:

```toml
[cert_issuance.est.identity_auto_renew]
rotate_key = true
threshold = "80%"
retry = "4%"
```

If authentication to EST server using certificate isn't possible, use shared secret or username/password instead:

```toml
[cert_issuance.est.auth]
username = "username"
password = "password"
```

Microsoft partners with GlobalSign to provide a demo account that you can sign up for [here](https://www.globalsign.com/globalsign-and-microsoft-azure-iot-edge-enroll-demo).

## Manage trusted root CA or trust bundle

To use a private certificate authority (CA) certificate with IoT Edge and modules as a root of trust, known as "trust bundle", point IoT Edge to it specifying its file path in the configuration. 

```toml
trust_bundle_cert = "file:///var/secrets/trust-bundle.pem"
```

Installing only to the trust bundle file makes the cert available to container modules but not to host modules like Azure Device Update or Defender. To prevent issues with host level components, also install the root CA certificate to the operating system certificate store.

Linux:

```bash
sudo cp /var/secrets/my-root-ca.pem /usr/local/share/ca-certificates/my-root-ca.pem.crt

sudo update-ca-certificates
```

IoT Edge for Linux on Windows (EFLOW):

```bash
sudo cp /var/secrets/azure-iot-test-only.root.ca.cert.pem /etc/pki/ca-trust/source/anchors/azure-iot-test-only.root.ca.cert.pem.crt

sudo update-ca-trust
```

## Device identity certificate and examples

IoT Edge device identity certificate is used to authenticate to IoT Hub or Device Provisioning Service (DPS) depending on your provisioning setup. 

### Device identity certificate files from PKI provider with manual management

Request a client TLS certificate (or similar) and private key from your PKI provider and ensure that the common name (CN) matches the IoT Edge device ID with IoT Hub or registration ID (with DPS). In below device identity certificate example, `Subject: CN = my-device` is the critical field.

Example device identity certificate:

```output
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 48 (0x30)
        Signature Algorithm: ecdsa-with-SHA256
        Issuer: CN = myPkiCA
        Validity
            Not Before: Jun 28 21:27:30 2022 GMT
            Not After : Jul 28 21:27:30 2022 GMT
        Subject: CN = my-device
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                RSA Public-Key: (2048 bit)
                Modulus:
                    00:ad:b0:63:1f:48:19:9e:c4:9d:91:d1:b0:b0:e5:
                    ...
                    80:58:63:6d:ab:56:9f:90:4e:3f:dd:df:74:cf:86:
                    04:af
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Basic Constraints:
                CA:FALSE
            X509v3 Key Usage:
                Digital Signature
            X509v3 Subject Key Identifier:
                C7:C2:DC:3C:53:71:B8:42:15:D5:6C:4B:5C:03:C2:2A:C5:98:82:7E
            X509v3 Authority Key Identifier:
                keyid:6E:57:C7:FC:FE:50:09:75:FA:D9:89:13:CB:D2:CA:F2:28:EF:9B:F6

    Signature Algorithm: ecdsa-with-SHA256
         30:45:02:20:3c:d2:db:06:3c:d7:65:b7:22:fe:df:9e:11:5b:
         ...
         eb:da:fc:f1:6a:bf:31:63:db:5a:16:02:70:0f:cf:c8:e2
-----BEGIN CERTIFICATE-----
MIICdTCCAhugAwIBAgIBMDAKBggqhkjOPQQDAjAXMRUwEwYDVQQDDAxlc3RFeGFt
...
354RWw+eLOpQSkTqXxzjmfw/kVOOAQIhANvRmyCQVb8zLPtqdOVRkuva/PFqvzFj
21oWAnAPz8ji
-----END CERTIFICATE-----
```

> [!TIP]
> To try this without access to certificate files provided by a PKI, see [Create demo certificates to test device features](/azure/iot-edge/how-to-create-test-certificates?view=iotedge-2020-11&tabs=linux) to generate a short-lived non-production device identity certificate and private key.

Once you have the latest certificate and private files, update IoT Edge configuration.

If provisioning with IoT Hub:

```toml
[provisioning]
source = "manual"
# ...
[provisioning.authentication]
method = "x509"

identity_cert = "file:///var/secrets/device-id.pem"
identity_pk = "file:///var/secrets/device-id.key.pem"
```

If provisioning with DPS:

```toml
[provisioning]
source = "dps"
# ...
[provisioning.attestation]
method = "x509"
registration_id = "my-device"

identity_cert = "file:///var/secrets/device-id.pem"
identity_pk = "file:///var/secrets/device-id.key.pem"
```

Ensure that that IoT Edge has the correct permissions for the directories holding the certificates and keys:

```bash
sudo chmod 444 /var/secrets/device-id.pem 
sudo chmod 440 /var/secrets/device-id.key.pem
```

To prevent authentication errors, manually update the files and configuration before certificate expiration. In production, overhead with manual certificate management can be risky and error-prone, so IoT Edge supports automatic management for identity certificates.

### Automatic device identity certificate management with EST

To use EST and IoT Edge for automatic device identity certificate issuance and renewal, which is recommended for production, IoT Edge must provision as part of a [DPS CA-based enrollment group](/azure/iot-edge/how-to-provision-devices-at-scale-linux-x509?view=iotedge-2020-11&tabs=group-enrollment%2Cubuntu) similar to this example:

```toml
## DPS provisioning with X.509 certificate
[provisioning]
source = "dps"
# ...
[provisioning.attestation]
method = "x509"
registration_id = "my-device"

[provisioning.attestation.identity_cert]
method = "est"
common_name = "my-device"

[provisioning.attestation.identity_cert.auto_renew]
rotate_key = true
threshold = "80%"
retry = "4%"
```

Don't to use EST or `auto_renew` with other methods of provisioning, including manual X.509 provisioning with IoT Hub and DPS with individual enrollment. This is because IoT Edge can't update certificate thumbprint in Azure when a certificate is renewed, which prevents IoT Edge from reconnecting.

<!-- iotedge-2020-11 -->
:::moniker range=">=iotedge-2020-11"

## Edge CA configuration and examples

Edge CA has two different modes:

1. "Quickstart": default behavior, only use for testing and not suitable for production
1. Production mode

### Quickstart Edge CA

To help with getting started, IoT Edge automatically generates an **Edge CA certificate** when started up for the first time by default. This self-signed certificate is only meant for development and testing scenarios, not production. This certificate expires after 90 days, but can be configured. This is referred to as *quickstart Edge CA*.

It's called "quickstart" because it's enables `edgeHub` (and other IoT Edge modules) to have valid server certificate when IoT Edge is first installed, without any configuration. It's important that `edgeHub` has this certificate because modules or downstream devices [need to establish secure communication channels](iot-edge-certs.md#part-3-am-i-really-talking-to-edgegateway). Without the quickstart Edge CA, getting started would be significantly harder because you'd need to provide a valid server certificate from a PKI provider or with tools like `openssl`. 

> [!IMPORTANT]
> Never use the quickstart Edge CA for production because the locally generated certificate in it isn't connected to a PKI. 
> 
> The security of a certificates-based identity derives from a well-operated PKI (the infrastructure) in which the certificate (a document) is only a component. A well-operated PKI enables definition, application, management and enforcements of security policies to include but not limited to certificates issuance, revocation, and lifecycle management.

#### Customize lifetime for quickstart Edge CA

To configure the certificate expiration to something other than the default 90 days, add the value in days to the **Edge CA certificate (Quickstart)** section of the config file.

```toml
[edge_ca]
auto_generated_edge_ca_expiry_days = 180
```

Then, delete the contents of the `/var/lib/aziot/certd/certs` and  `/var/lib/aziot/keyd/keys` folders to remove any previously generated certificates, and apply the configuration.

#### Renew quickstart Edge CA

By default, IoT Edge automatically renew the quickstart Edge CA certificate when at 80% of the certificate lifetime. So for certificate with 90 day lifetime, IoT Edge automatically regenerates the Edge CA certificate at 72 days from issuance. 

To configure the auto-renewal logic, add configuration to the "Edge CA certificate" section in `config.toml` following this example: 
   
```toml
[edge_ca.auto_renew]
rotate_key = true
threshold = "70%"
retry = "2%"
```

### Edge CA in production

Once you move into a production scenario, or you want to create a gateway device, you can no longer use the quickstart Edge CA.

One option is to provide your own certificates and manage them manually. However, to avoid the risky and error-prone manual certificate management process, use an EST server whenever possible.

### Edge CA certificate files from PKI provider with manual management

Request the public portion of the root CA certificate and an issuing CA certificate (becomes Edge CA) with its private key from your PKI provider. The issuing CA certificate must have these extensions:

```text
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always
basicConstraints = critical, CA:TRUE, pathlen:0
keyUsage = critical, digitalSignature, keyCertSign
```

An example of the result Edge CA certificate looks like (some parts omitted with `...`):

```output
$ openssl x509 -in my-edge-ca-cert.pem -text
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 4098 (0x1002)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = myPkiCA
        Validity
            Not Before: Aug 27 00:00:50 2022 GMT
            Not After : Sep 26 00:00:50 2022 GMT
        Subject: CN = my-edge-ca.ca
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                RSA Public-Key: (4096 bit)
                Modulus:
                    00:e1:cb:9c:c0:41:d2:ee:5d:8b:92:f9:4e:0d:3e:
                    ...
                    25:f5:58:1e:8c:66:ab:d1:56:78:a5:9c:96:eb:01:
                    e4:e3:49
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Subject Key Identifier:
                FD:64:48:BB:41:CE:C1:8A:8A:50:9B:2B:2D:6E:1D:E5:3F:86:7D:3E
            X509v3 Authority Key Identifier:
                keyid:9F:E6:D3:26:EE:2F:D7:84:09:63:84:C8:93:72:D5:13:06:8E:7F:D1
                DirName:/CN=myPkiCA
                serial:10:00

            X509v3 Basic Constraints: critical
                CA:TRUE, pathlen:0
            X509v3 Key Usage: critical
                Digital Signature, Certificate Sign
    Signature Algorithm: sha256WithRSAEncryption
         20:c9:34:41:a3:a4:8e:7c:9c:6e:17:f5:a6:6f:e5:fc:6e:59:
         ...
         7c:20:5d:e5:51:85:4c:4d:f7:f8:01:84:87:27:e3:76:65:47:
         9e:6a:c3:2e:1a:f0:dc:9d
-----BEGIN CERTIFICATE-----
MIICdTCCAhugAwIBAgIBMDAKBggqhkjOPQQDAjAXMRUwEwYDVQQDDAxlc3RFeGFt
...
354RWw+eLOpQSkTqXxzjmfw/kVOOAQIhANvRmyCQVb8zLPtqdOVRkuva/PFqvzFj
21oWAnAPz8ji
-----END CERTIFICATE-----
```

Once you receive the latest files, first [update the trust bundle](#manage-trusted-root-ca-or-trust-bundle):

```toml
trust_bundle_cert = "file:///var/secrets/root-ca.pem"
```

Then configure IoT Edge to use the certificate and private key files:

```toml
[edge_ca]
cert = "file:///var/secrets/my-edge-ca-cert.pem"
pk = "file:///var/secrets/my-edge-ca-private-key.key.pem"
```

Ensure that that IoT Edge has the correct permissions for the directories holding the certificates and keys:

```bash
sudo chmod 444 /var/secrets/my-edge-ca-cert.pem 
sudo chmod 440 /var/secrets/my-edge-ca-private-key.key.pem
```

If you've used any other certificates for IoT Edge on the device before, delete the files in `/var/lib/aziot/certd/certs`. IoT Edge recreates them with the new CA certificate you provided.

This approach requires you to manually update the files as certificate expires. To avoid this, consider using EST for automatic management.

### Automatic Edge CA management with EST

Use EST automatic Edge CA issuance and renewal for production. Once EST server is configured, you can use the global setting, or override it similar to this example:

```toml
[edge_ca]
method = "est"

common_name = "my-edge-CA"
expiry_days = 90
url = "https://ca.example.org/.well-known/est"

bootstrap_identity_cert = "file:///var/secrets/my-est-id-bootstrap-cert.pem"
bootstrap_identity_pk = "file:///var/secrets/my-est-id-bootstrap-pk.key.pem"
```

By default, and when there's no specific `auto_renew` configuration, Edge CA automatically renews at 80% certificate lifetime if EST is set as the method. You can update the auto renewal values to other values:

```toml
[edge_ca.auto_renew]
rotate_key = true
threshold = "90%"
retry = "2%"
```

Automatic renewal for Edge CA cannot be disabled when issuance method is set to EST, since Edge CA expiration breaks many IoT Edge functionalities. If a situation requires total control over Edge CA certificate lifecycle, use the [manual Edge CA management method instead](#edge-ca-certificate-files-from-pki-provider-with-manual-management).

> [!IMPORTANT]
> #### Planning around the Edge CA renewal
> When the Edge CA certificate is renewed, all the certificates it issued (generally module server certificates) must also be regenerated. To give the modules new server certificates, IoT Edge restarts all modules when Edge CA certificate renews.
> 
> To minimize potential negative effects of module restarts, set an explicit absolute time (for example, `threshold = "10d"`) and notify for dependents of the solution about the downtime.

:::moniker-end
<!-- end iotedge-2020-11 -->

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

## Device CA

All IoT Edge devices use certificates to create secure connections between the runtime and any modules running on the device. IoT Edge devices functioning as gateways use these same certificates to connect to their downstream devices, too. For more information about the function of the different certificates on an IoT Edge device, see [Understand how Azure IoT Edge uses certificates](iot-edge-certs.md).

IoT Edge automatically generates device CA on the device in several cases, including:

* If you don't provide your own production certificates when you install and provision IoT Edge, the IoT Edge security manager automatically generates a **device CA certificate**. This self-signed certificate is only meant for development and testing scenarios, not production. This certificate expires after 90 days.
* The IoT Edge security manager also generates a **workload CA certificate** signed by the device CA certificate

For these two automatically generated certificates, you have the option of setting a flag in the config file to configure the number of days for the lifetime of the certificates.

> [!NOTE]
> There is a third auto-generated certificate that the IoT Edge security manager creates, the **IoT Edge hub server certificate**. This certificate always has a 30 day lifetime, but is automatically renewed before expiring. The auto-generated CA lifetime value set in the config file doesn't affect this certificate.

## Customize quickstart device CA certificate lifetime

Upon expiry after the specified number of days, IoT Edge has to be restarted to regenerate the device CA certificate. The device CA certificate isn't renewed automatically.

# [Linux containers](#tab/linux)

1. To configure the certificate expiration to something other than the default 90 days, add the value in days to the **certificates** section of the config file.

   ```yaml
   certificates:
     device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
     device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
     trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
     auto_generated_ca_lifetime_days: <value>
   ```

   > [!NOTE]
   > Currently, a limitation in libiothsm prevents the use of certificates that expire on or after January 1, 2038.

1. Delete the contents of the `hsm` folder to remove any previously generated certificates.

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. Restart the IoT Edge service.

   ```bash
   sudo systemctl restart iotedge
   ```

1. Confirm the lifetime setting.

   ```bash
   sudo iotedge check --verbose
   ```

   Check the output of the **production readiness: certificates** check, which lists the number of days until the automatically generated device CA certificates expire.

# [Windows containers](#tab/windows)

1. To configure the certificate expiration to something other than the default 90 days, add the value in days to the **certificates** section of the config file.

   ```yaml
   certificates:
     device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
     device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
     trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
     auto_generated_ca_lifetime_days: <value>
   ```

   > [!NOTE]
   > Currently, a limitation in libiothsm prevents the use of certificates that expire on or after January 1, 2038.

1. Delete the contents of the `hsm` folder to remove any previously generated certificates.

   * `C:\ProgramData\iotedge\hsm\certs`
   * `C:\ProgramData\iotedge\hsm\cert_keys`

1. Restart the IoT Edge service.

   ```powershell
   Restart-Service iotedge
   ```

1. Confirm the lifetime setting.

   ```powershell
   iotedge check --verbose
   ```

   Check the output of the **production readiness: certificates** check, which lists the number of days until the automatically generated device CA certificates expire.

---

## Install device CA for production

Once you move into a production scenario, or you want to create a gateway device, you need to provide your own certificates.

### Prerequisites for using device CA in production

* An IoT Edge device.

  If you don't have an IoT Edge device set up, you can create one in an Azure virtual machine. Follow the steps in one of the quickstart articles to [Create a virtual Linux device](quickstart-linux.md) or [Create a virtual Windows device](quickstart.md).

* Have a root certificate authority (CA) certificate, either self-signed or purchased from a trusted commercial certificate authority like Baltimore, Verisign, DigiCert, or GlobalSign.

  If you don't have a root certificate authority yet, but want to try out IoT Edge features that require production certificates (like gateway scenarios) you can [Create demo certificates to test IoT Edge device features](how-to-create-test-certificates.md).

### Create and install device CA for production

1. Use your own certificate authority to create the following files:

   * Root CA
   * Device CA certificate
   * Device CA private key

   Here, the *root CA* is not the topmost certificate authority for an organization. It's the topmost certificate authority for the IoT Edge scenario, which the IoT Edge hub module, user modules, and any downstream devices use to establish trust between each other. 

   To see an example of these certificates, review the scripts that create demo certificates in [Managing test CA certificates for samples and tutorials](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

   > [!NOTE]
   > Currently, a limitation in libiothsm prevents the use of certificates that expire on or after January 1, 2038.

1. Copy the three certificate and key files onto your IoT Edge device. You can use a service like [Azure Key Vault](../key-vault/index.yml) or a function like [Secure copy protocol](https://www.ssh.com/ssh/scp/) to move the certificate files. If you generated the certificates on the IoT Edge device itself, you can skip this step and use the path to the working directory.

   > [!TIP]
   > If you used the sample scripts to [create demo certificates](how-to-create-test-certificates.md), the three certificate and key files are located at the following paths:

   * Device CA certificate: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * Device CA private key: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
   * Root CA: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

# [Linux containers](#tab/linux)

1. Open the IoT Edge security daemon config file: `/etc/iotedge/config.yaml`

1. Set the **certificate** properties in config.yaml to the file URI path to the certificate and key files on the IoT Edge device. Remove the `#` character before the certificate properties to uncomment the four lines. Make sure the **certificates:** line has no preceding whitespace and that nested items are indented by two spaces. For example:

   ```yaml
   certificates:
      device_ca_cert: "file:///<path>/<device CA cert>"
      device_ca_pk: "file:///<path>/<device CA key>"
      trusted_ca_certs: "file:///<path>/<root CA cert>"
   ```

1. Make sure that the user **iotedge** has read/write permissions for the directory holding the certificates.

1. If you've used any other certificates for IoT Edge on the device before, delete the files in the following two directories before starting or restarting IoT Edge:

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. Restart IoT Edge.

   ```bash
   sudo iotedge system restart
   ```

# [Windows containers](#tab/windows)

1. Open the IoT Edge security daemon config file: `C:\ProgramData\iotedge\config.yaml`

1. Set the **certificate** properties in config.yaml to the file URI path to the certificate and key files on the IoT Edge device. Remove the `#` character before the certificate properties to uncomment the four lines. Make sure the **certificates:** line has no preceding whitespace and that nested items are indented by two spaces. For example:

   ```yaml
   certificates:
      device_ca_cert: "file:///C:/<path>/<device CA cert>"
      device_ca_pk: "file:///C:/<path>/<device CA key>"
      trusted_ca_certs: "file:///C:/<path>/<root CA cert>"
   ```

1. If you've used any other certificates for IoT Edge on the device before, delete the files in the following two directories before starting or restarting IoT Edge:

   * `C:\ProgramData\iotedge\hsm\certs`
   * `C:\ProgramData\iotedge\hsm\cert_keys`

1. Restart IoT Edge.

   ```powershell
   Restart-Service iotedge
   ```
---

:::moniker-end
<!-- end 1.1 -->

## Module server certificates

Edge Daemon issues module server and identity certificates for use by Edge modules. It remains the responsibility of Edge modules to renew their identity and server certificates as needed.

### Renewal 

Server certificates may be issued off the Edge CA certificate or through a DPS-configured CA. Regardless of the issuance method, these certificates must be renewed by the module.

## Changes in 1.2 and later

* The **Device CA certificate** was renamed as **Edge CA certificate**.
* The **workload CA certificate** was deprecated. Now the IoT Edge security manager generates the IoT Edge hub `edgeHub` server certificate directly from the Edge CA certificate, without the intermediate workload CA certificate between them.
* The default config file has a new name and location, from `/etc/iotedge/config.yaml` to `/etc/aziot/config.toml` by default. The `iotedge config import` command can be used to help migrate configuration information from the old location and syntax to the new one.

## Next steps

Installing certificates on an IoT Edge device is a necessary step before deploying your solution in production. Learn more about how to [Prepare to deploy your IoT Edge solution in production](production-checklist.md).
