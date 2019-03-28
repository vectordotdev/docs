---
description: Sends logs to Timber via Syslog
---

# Syslog

Timber offers first-class native support for the various types of Syslog, avoiding the need to install additional log forwarders. Just point Syslog to Timber and go.

## Installation

{% tabs %}
{% tab title="Rsyslog" %}
{% hint style="info" %}
The following are general instructions for integrating rsyslog v8+ with Timber. These instruction will cause _all_ syslog messages to be forwarded to Timber. This is probably _not_ what you want. Please review the instructions on using [filter conditions](https://www.rsyslog.com/doc/v8-stable/configuration/filters.html) to limit the messages that are forwarded with the action.
{% endhint %}

1. Ensure you have any extra packages necessary for rsyslog to support TLS communication. On [Ubuntu](https://www.ubuntu.com/), this means installing the `rsyslog-gnutls` package \(the package and method to do this will vary based on your distribution\):  


   ```bash
   apt-get install rsyslog-gnutls
   ```
CentOS 6 and higher already have these packages installed. You may also need sudo access to run some of the commands below.

2. Create the directory `/etc/rsyslog.d/keys/ca.d` to place the Timber public certificate in \(the connection effectively uses certificate pinning\)  


   ```bash
   mkdir -p /etc/rsyslog.d/keys/ca.d
   ```

3. Download the [Timber.io public certificate](https://files.timber.io/certificates/latest/io.timber-wildcard.pem):



   ```text
   curl \
   https://files.timber.io/certificates/latest/io.timber-wildcard.pem \
   --output /etc/rsyslog.d/keys/ca.d/io.timber-wildcard.pem
   ```

4. Copy the following into `/etc/rsyslog.d/60-timber.conf`, _**replace `YOUR_API_KEY` and `YOUR_SOURCE_ID` accordingly:**_



   ```text
   global(defaultNetstreamDriverCAFile="/etc/rsyslog.d/keys/ca.d/io.timber-wildcard.pem")

   template(name="TimberFormat" type="list") {
    constant(value="<")
    property(name="pri")
    constant(value=">")
    constant(value="1")
    constant(value=" ")
    property(name="timestamp" dateFormat="rfc3339")
    constant(value=" ")
    property(name="hostname")
    constant(value=" ")
    property(name="app-name")
    constant(value=" ")
    property(name="procid")
    constant(value=" ")
    property(name="msgid")
    constant(value=" ")
    property(name="structured-data" regex.expression="[^-]" regex.nomatchmode="BLANK" regex.submatch="0")
    constant(value="[authentication@51576 source_id=\"YOUR_SOURCE_ID\" api_key=\"YOUR_API_KEY\"]")
    constant(value=" ")
    property(name="msg" droplastlf="on")
   }

   action(
    type="omfwd"
    protocol="tcp"
    target="logs.timber.io"
    port="6514"
    template="TimberFormat"
    TCP_Framing="octet-counted"
    StreamDriver="gtls"
    StreamDriverMode="1"
    StreamDriverAuthMode="x509/name"
    StreamDriverPermittedPeers="*.timber.io"
    queue.spoolDirectory="/var/spool/rsyslog"
    queue.filename="timber"
    queue.maxdiskspace="75m"
    queue.type="LinkedList"
    queue.saveonshutdown="on"
   )
   ```

5. Now restart the rsyslog service:  


   ```text
   systemctl restart rsyslog
   ```

6. Check status of rsyslog:
    
    
    ``` bash 
    systemctl restart rsyslog
    ```   
    
7. Your logs will be forwarded to Timber. _You will need to update the Timber public certificate when a new one is issued._
{% endtab %}

{% tab title="Syslog-ng" %}
{% hint style="info" %}
The following are general instructions for integrating syslog-ng v3.5+ with Timber. These instructions will cause _all_ syslog messages to be forwarded to Timber. This is probably _not_ what you want. Please review the instructions on [filtering logs](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/47#TOPIC-956564) to limit the messages that are forwarded with the action.
{% endhint %}

1. Create the directory `/etc/syslog-ng/keys/ca.d` \(you will need to install the DigiCert Global Root CA certificate here\):  


   ```bash
   mkdir /etc/syslog-ng/keys/ca.d
   ```

2. Download the [DigiCert Global Root CA certificate](https://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt):  


   ```bash
   curl \
   -o /etc/syslog-ng/keys/ca.d/digicert_global_root_ca.der \
   https://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt
   ```

3. Convert the DigiCert Global Root CA certificate to the PEM format:  


   ```bash
   openssl x509 \
   -inform der \
   -outform pem \
   -in /etc/syslog-ng/keys/ca.d/digicert_global_root_ca.der \
   -out /etc/syslog-ng/keys/ca.d/digicert_global_root_ca.pem
   ```

4. Get the hash of the DigiCert Global Root CA certificate:  


   ```bash
   openssl x509 \
   -in /etc/syslog-ng/keys/ca.d/digicert_global_root_ca.pem \
   -hash \
   -noout
   ```

5. Use the result of line 4 \(should be `3513523f`\) with the suffix `.0` as the target for a symbolic link to the DigiCert Global Root CA in PEM format:  


   ```bash
   ln \
   -s /etc/syslog-ng/keys/ca.d/digicert_global_root_ca.der \
   /etc/syslog-ng/keys/ca.d/351323f.0
   ```

6. Copy the following into `/etc/syslog-ng/conf.d/timber.conf`_**, replace `YOUR_API_KEY` and `YOUR_SOURCE_ID` accordingly:**_



   ```bash
   destination d_timber {
    syslog(
      "logs.timber.io"
      transport("tls")
      port(6514)
      tls(
        ca-dir("/etc/syslog-ng/ca.d")
        cipher-suites("ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256")
        trusted-dn("*, CN=*.timber.io")
      )
    );
   };

   rewrite add_timber_credentials {
    set("YOUR_SOURCE_ID" value(".SDATA.authentication@51576.source_id"));
    set("YOUR_API_KEY" value(".SDATA.authentication@51576.api_key"));
   };

   log {
    source(s_src);
    rewrite(add_timber_credentials);
    destination(d_timber);
   };
   ```

7. Now restart the syslog-ng service:  


   ```bash
   systemctl restart syslog-ng
   ```
    
{% endtab %}

{% tab title="Manual" %}
{% hint style="info" %}
These are the general instructions for forwarding syslog messages to Timber. We also provide detailed instructions for installing with `rsyslog` and `syslog-ng` \(please see the tabs above\).
{% endhint %}

1. Configure your Syslog system to forward logs to Timber. Timber's syslog collection service is available at `logs.timber.io` on port `6514`.
2. Ensure that your Syslog messages include the following within the structured data portion of the message. _**Replace `YOUR_SOURCE_ID` and `YOUR_API_KEY` accordingly:**_



   ```text
   [authentication@51576 source_id="YOUR_SOURCE_ID" api_key="YOUR_API_KEY"]
   ```

3. Ensure that your TLS ciphers are configured properly. The Timber service expects TLS v1.2 and recognizes the following ciphers \(in OpenSSL notation\): 
   1. ECDHE-ECDSA-AES128-GCM-SHA256
   2. ECDHE-ECDSA-AES128-SHA256
   3. ECDHE-ECDSA-AES256-GCM-SHA384
   4. ECDHE-ECDSA-AES256-SHA384
   5. ECDHE-RSA-AES128-GCM-SHA256
   6. ECDHE-RSA-AES128-SHA256
   7. ECDHE-RSA-AES256-GCM-SHA384
   8. ECDHE-RSA-AES256-SHA384 
4. Install the Timber certificate. The [Timber TLS public certificate](https://files.timber.io/certificates/latest/io.timber-wildcard.pem) is a wildcard certificate for `*.timber.io` issued by RapidSSL \(managed by DigiCert\):  


   ```text
   Expiration: 2018-12-09T12:00:00Z
   MD5: 8E:46:8D:CD:4A:BF:35:70:41:9B:D3:E7:C3:01:A9:94
   SHA1: 23:EC:0E:36:1C:52:D0:E7:D0:69:55:7F:D8:03:59:52:49:E0:80:C2
   SHA256: 05:94:FE:0B:79:53:F3:3E:35:5C:42:6C:5B:DD:8C:F7:D6:B2:A7:A3:AD:7A:7B:AD:CF:B2:1E:F3:D5:1B:78:E4
   ```

   If you require root and intermediate certificates, we recommend that you download them directly from [DigiCert's Trusted Root Authority Certificates website](https://www.digicert.com/digicert-root-certificates.htm). The intermediate certificate is the "RapidSSL RSA CA 2018" certificate with serial number `08:A5:A2:46:CD:4B:5C:8C:83:D7:02:B4:BB:AB:53:49`. The root certificate is the "DigiCert Global Root CA" with serial number `08:3B:E0:56:90:42:46:B1:A1:75:6A:C9:59:91:C7:4A`.

   DigiCert provides certificates in the DER format. To convert to PEM, use openssl:  


   ```text
   openssl -inform der -in digicert.der -outform pem -out digicert.pem
   ```
{% endtab %}
{% endtabs %}

## Configuration

Please see the [syslog-ng configuration documentation](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide), especially the [filter conditions](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/47#TOPIC-956564) section since you'll want to use that to filter out logs being sent to Timber.

## Troubleshooting

To begin, please see our [log delivery troubleshooting guide](../../guides/troubleshooting-log-delivery.md). This covers the most common issues we see with log delivery:

{% page-ref page="../../guides/troubleshooting-log-delivery.md" %}

