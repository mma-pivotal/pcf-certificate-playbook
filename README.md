# pcf-certificate-playbook

This is a playbook intended to cover the following points.

1. Explain the certificates used, trusted by a PCF deployment.
2. Demostrate how to add/update/delete trusted certificates in a PCF deployment.
3. Demostrate how to setup SSL for an application with custom URL in PWS.

## Root Certificate in a PCF deployment.

After the initial installation and setup of OPS Manager Director, the root CA certificate of PCF deployment can be found at `/var/tempest/workspaces/default/root_ca_certificate` on the OPS Manager Director VM.

Despite the **Subject Name** is identical (C=US,O=Pivotal) for all PCF deployment, this certificate is actually unique for each PCF deployment (no PCF deployment would share the same root CA certificate).

Here is an example from lab environment and you can compare the signature with your own root CA certificate to see the difference.

```
openssl x509 -in root_ca_certificate -text -noout

Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            4a:65:34:a4:2c:8e:a4:e8:a4:2b:6f:f6:54:63:0c:93:1c:8e:e9:a6
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: C=US, O=Pivotal  //Note that Issuer = Subject which means this is a self-signed Root CA
        Validity
            Not Before: Feb 21 21:13:13 2018 GMT
            Not After : Feb 22 21:13:13 2022 GMT
        Subject: C=US, O=Pivotal
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:d4:a0:bf:91:41:a0:45:c8:7c:b5:d7:e5:64:b4:
                    ba:e3:ff:b4:5c:eb:00:cc:ce:23:f2:1d:38:1d:76:
                    87:d7:a2:e1:70:af:71:6e:8e:e5:df:f0:b9:e6:7a:
                    8c:81:1e:24:5a:77:b7:28:aa:92:cc:89:47:8e:17:
                    fb:11:f7:3d:48:0c:e4:24:e3:76:59:38:53:da:16:
                    12:ae:ea:47:c3:4d:da:d6:85:b8:1e:f1:04:40:ec:
                    ea:fa:25:96:25:71:13:2e:8f:f6:ac:1a:ee:2e:4a:
                    43:81:0d:6a:2e:26:2b:42:0b:55:ab:e7:83:41:c0:
                    4f:62:12:d4:c9:d8:87:3e:33:98:3f:fc:22:00:a1:
                    6c:9b:c4:08:12:26:07:0d:3e:9c:8a:fc:e4:49:fa:
                    43:ec:c8:ad:56:78:46:8a:03:94:c9:7b:e1:66:20:
                    92:eb:5c:c6:10:f8:15:ef:b9:a2:e8:fa:e0:de:93:
                    18:e0:8e:a6:53:08:c8:d7:05:af:11:31:09:fc:d5:
                    22:86:d2:0b:5b:1f:ea:b9:33:d9:8d:1e:9f:87:81:
                    d1:cb:21:48:94:75:60:6b:5b:3e:ce:7a:47:64:b9:
                    fe:46:87:8b:78:51:4e:41:1f:8d:21:ac:fe:96:a9:
                    1d:df:e9:3f:8d:05:01:f5:95:60:da:24:06:30:35:
                    f1:c9
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Subject Key Identifier:
                C7:9A:3F:9E:CC:28:AC:F6:14:42:46:28:81:C7:67:51:04:0D:1E:A5
            X509v3 Authority Key Identifier:
                keyid:C7:9A:3F:9E:CC:28:AC:F6:14:42:46:28:81:C7:67:51:04:0D:1E:A5

            X509v3 Extended Key Usage:
                TLS Web Client Authentication, TLS Web Server Authentication
            X509v3 Basic Constraints: critical
                CA:TRUE
            X509v3 Key Usage: critical
                Certificate Sign, CRL Sign
    Signature Algorithm: sha256WithRSAEncryption
         82:9b:ff:42:21:d5:a2:8a:b5:55:c0:ec:d8:b3:51:20:db:bd:
         9b:92:6b:27:65:aa:72:2a:20:e2:c5:74:b0:73:5a:98:e5:40:
         bf:9c:88:74:52:c1:a2:d5:64:bd:b6:05:eb:38:71:7c:c4:fc:
         2d:f2:cf:e8:03:31:93:9a:ad:4a:45:ea:72:05:26:c2:b8:0d:
         91:0f:e1:53:d5:37:9a:b1:82:80:99:fb:03:c6:fd:49:f0:67:
         8b:b9:81:f7:f8:b2:94:66:5d:74:d1:ce:e8:bb:36:ff:41:43:
         53:59:c7:33:8a:e8:d3:33:6e:86:c0:68:19:e5:5b:f0:c4:3b:
         1f:52:8d:64:78:dc:ee:f1:98:3e:e7:61:4f:65:b2:da:4e:87:
         ca:1e:b1:c3:eb:24:4e:8a:fc:73:41:24:51:7c:75:aa:b2:20:
         72:c2:4b:22:57:c8:a6:66:2e:b1:c6:87:14:a4:e0:c9:ea:14:
         94:4f:01:5d:97:ae:bc:29:04:ae:34:ff:96:23:e6:06:b4:d9:
         1a:82:e3:56:26:7b:b6:53:0e:2d:0e:b5:2c:66:58:89:b2:c0:
         ff:b4:33:e9:fc:f4:c5:95:fb:e6:cd:b6:49:48:9c:ad:77:a0:
         84:3e:43:92:31:1c:8d:6b:b2:c4:e7:32:38:08:a7:1c:b4:5e:
         69:e6:6f:58
```

Unless explicitly specified, PCF will use this root CA to issue all certificates needed for all components. 

Here are some examples.

### SSL certificate of API endpoint 

```
openssl s_client -connect api.run-10.haas-59.pez.pivotal.io:443 -showcerts
CONNECTED(00000003)
depth=0 C = US, O = Pivotal, CN = *.cfapps-10.haas-59.pez.pivotal.io
verify error:num=20:unable to get local issuer certificate
verify return:1
depth=0 C = US, O = Pivotal, CN = *.cfapps-10.haas-59.pez.pivotal.io
verify error:num=21:unable to verify the first certificate
verify return:1
---
Certificate chain
 0 s:/C=US/O=Pivotal/CN=*.cfapps-10.haas-59.pez.pivotal.io
   i:/C=US/O=Pivotal
```

Look at the last line (i: means issuer) and you can see the root CA subject name.

