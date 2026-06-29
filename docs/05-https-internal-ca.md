# HTTPS Configuration with Internal CA

The purpose of this task was to secure access to the Checkmk web interface using HTTPS.
In this lab environment, Checkmk was running on port `5000`. To provide HTTPS access, the system Apache web server was configured as an HTTPS reverse proxy in front of the Checkmk site Apache.

Since this was a difficult configuration phase, the [Checkmk official documentation](https://docs.checkmk.com/latest/en/omd_https.html) was used as the main reference. 
ChatGPT was also used to help understand the steps, troubleshoot errors and document the configuration process.

## Architecture

```text
Browser
  |
  | HTTPS :443
  v
System Apache
  |
  | HTTP reverse proxy
  v
Checkmk site Apache :5000
```

The final access URL was:

```text
https://<vm-ip-address>/monitoring/check_mk/
```

## Background

Before this configuration, Checkmk was accessed directly over HTTP:

```text
http://<vm-ip-address>:5000/monitoring/check_mk/
```

To improve security and practice HTTPS configuration, Apache was configured to provide HTTPS access using a certificate issued by an internal lab Certificate Authority (CA).

## Port Changes

NGINX was previously using port `80`. Since Apache needed to use ports `80` and `443`, NGINX was moved to port `8081`.

After the change, the port usage was:

```text
Apache HTTP      :80
Apache HTTPS     :443
Checkmk Apache   :5000
NGINX            :8081
```

## Apache Modules

The required Apache modules were enabled:

```bash
sudo a2enmod ssl rewrite headers proxy proxy_http
```

The enabled modules were verified with:

```bash
apachectl -M | grep -E 'headers|rewrite|ssl|proxy'
```

The following modules were confirmed:

```text
headers_module
proxy_module
proxy_http_module
rewrite_module
ssl_module
```

## Internal CA Certificate Setup

An internal lab Root CA was created for this local environment.

### 1. Create a certificate directory

```bash
sudo mkdir -p /etc/certs/checkmk-lab
cd /etc/certs/checkmk-lab
```

### 2. Create the Root CA private key

```bash
sudo openssl genrsa -out lab-root-ca.key 4096
```

### 3. Create the Root CA certificate

```bash
sudo openssl req -x509 -new -nodes \
-key lab-root-ca.key \
-sha256 -days 3650 \
-out lab-root-ca.crt
```

The Root CA certificate represents the internal certificate authority for this lab.

### 4. Create the Checkmk server private key

```bash
sudo openssl genrsa -out checkmk.lab.local.key 2048
```

### 5. Create the Certificate Signing Request

```bash
sudo openssl req -new \
-key checkmk.lab.local.key \
-out checkmk.lab.local.csr
```

The Common Name used for the server certificate was:

```text
checkmk.lab.local
```

### 6. Create a Subject Alternative Name extension file

```bash
sudo nano checkmk.lab.local.ext
```

Content:

```ini
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, keyEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
DNS.1 = checkmk.lab.local
IP.1 = <vm-ip-address>
```

The SAN entry was added so that the certificate is valid for both the local DNS name and the VM IP address.

### 7. Sign the Checkmk server certificate with the Root CA

```bash
sudo openssl x509 -req \
-in checkmk.lab.local.csr \
-CA lab-root-ca.crt \
-CAkey lab-root-ca.key \
-CAcreateserial \
-out checkmk.lab.local.crt \
-days 365 \
-sha256 \
-extfile checkmk.lab.local.ext
```

This created the server certificate used by Apache.

## Certificate Files

The following files were created:

```text
lab-root-ca.key
lab-root-ca.crt
lab-root-ca.srl
checkmk.lab.local.key
checkmk.lab.local.csr
checkmk.lab.local.ext
checkmk.lab.local.crt
```

Important security note:

```text
lab-root-ca.key and checkmk.lab.local.key are private keys and must not be shared or uploaded to GitHub.
```

Only documentation and screenshots are included in this repository. Private keys are not included.

## Apache SSL Configuration

The Apache SSL configuration file was edited:

```bash
sudo nano /etc/apache2/sites-available/default-ssl.conf
```

The certificate paths were changed to use the lab certificate:

```apache
SSLCertificateFile      /etc/certs/checkmk-lab/checkmk.lab.local.crt
SSLCertificateKeyFile   /etc/certs/checkmk-lab/checkmk.lab.local.key
```

## Reverse Proxy Configuration

Apache was configured to forward requests to the Checkmk site running on port `5000`.

Inside the HTTPS VirtualHost, the following configuration was added:

```apache
ProxyPreserveHost On
ProxyPass /monitoring/ http://127.0.0.1:5000/monitoring/
ProxyPassReverse /monitoring/ http://127.0.0.1:5000/monitoring/

RequestHeader set X-Forwarded-Proto "https"
RequestHeader set X-Forwarded-SSL "on"
```

This allows users to access Checkmk through HTTPS while Checkmk itself continues to run internally on port `5000`.

## Configuration Test

The Apache configuration was tested with:

```bash
sudo apachectl configtest
```

Result:

```text
Syntax OK
```

Apache was then reloaded:

```bash
sudo systemctl reload apache2
```

## Browser Trust

Because an internal CA was used, the Root CA certificate had to be imported into the Windows trusted root certificate store.

The imported file was:

```text
lab-root-ca.crt
```

The private key was not copied.

```text
Copied to Windows: lab-root-ca.crt
Not copied: lab-root-ca.key
```

After importing the Root CA certificate, the browser could trust certificates issued by the lab CA.

## Result

The Checkmk web interface was made accessible through HTTPS:

```text
https://<vm-ip-address>/monitoring/check_mk/
```

This configuration demonstrates:

* Apache HTTPS configuration
* Internal CA certificate creation
* Server certificate signing
* Subject Alternative Name usage
* Reverse proxy configuration
* Separation of public certificates and private keys

This setup was created for a local lab environment. In a production environment, a trusted certificate authority should be used.
