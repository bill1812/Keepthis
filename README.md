# [Example scripts](https://community.axway.com/s/feed/0D52X000065Ykx2SAC) to create certificate chain with [OpenSSL](https://github.com/openssl/openssl)

Scripts to create certificates and keys by OpenSSL. It was tested on OpenSSL 1.1.0.


**<span style="color:red">Important: </span>the self-signed certificates must be used only for internal and development purposes.**


## Below you can find scripts to create:
- CA certificate
- Intermediary CA Certificate
- Server Certificate (signed by CA)
- Server Certificate (signed by Intermediary CA)
- Client Certificate (signed by CA)
- Client Certificate (signed by Intermediary CA)


## Script Openssl Certificate

## <span style="color:blue">Step A. Generate <span style="color:red">CA</span> Certificate</span>

### 01. Generate private Key
```javascript
openssl genrsa -out CA.key 2048
```

### 02. Generate CA CSR
```javascript
openssl req -new -sha256 -key CA.key -out CA.csr -subj "/C=BR/ST=SAO PAULO/L=SAO PAULO/O=AXWAY/CN=CA CERTIFICATE"
```

### 03. Generate CA Certificate (10 years)
```javascript
openssl x509 -signkey CA.key -in CA.csr -req -days 3650 -out CA.pem
```

## <span style="color:blue">Step B. Generate <span style="color:red">Intermediary CA</span> Certificate</span>

### 04. Generate private Key
```javascript
openssl genrsa -out CA_Intermediary.key 2048
```

### 05. Create Intermediary CA CSR
```javascript
openssl req -new -sha256 -key CA_Intermediary.key \
  -out CA_Intermediary.csr \
  -subj "/C=BR/ST=SAO PAULO/L=SAO PAULO/O=AXWAY/CN=CA INTERMEDIARY CERTIFICATE"
```

### 06. Generate Server Certificate (10 years)
```javascript
openssl x509 -req -in CA_Intermediary.csr -CA CA.pem -CAkey CA.key \
  -CAcreateserial -out CA_Intermediary.crt -days 3650 -sha256
```

## <span style="color:blue">Step C. Generate <span style="color:red">Server Certificate</span> signed by CA</span>

### 07. Generate private Key
```javascript
openssl genrsa -out ServerCert_signedByCA.key 2048
```

### 08. Create Server CSR
```javascript
openssl req -new -sha256 -key ServerCert_signedByCA.key \
  -out ServerCert_signedByCA.csr \
  -subj "/C=BR/ST=SAO PAULO/L=SAO PAULO/O=AXWAY/CN=axway.lab/subjectAltName=DNS.1=axway.lab,DNS.2=your-alt-name"
```

### 09. Generate Server Certificate
```javascript
openssl x509 -req -in ServerCert_signedByCA.csr -CA CA.pem -CAkey CA.key \
  -CAcreateserial -out ServerCert_signedByCA.crt -days 3650 -sha256
```

### 10. View Certificate
```javascript
openssl x509 -text -noout -in ServerCert_signedByCA.crt
```

## <span style="color:blue">Step D. Generate <span style="color:red">Server Certificate</span> signed by Intermediary CA</span>

### 11. Generate private Key
```javascript
openssl genrsa -out ServerCert_signedByCAIntermediary.key 2048
```

### 12. Create Server CSR
```javascript
openssl req -new -sha256 -key ServerCert_signedByCAIntermediary.key \
  -out ServerCert_signedByCAIntermediary.csr \
  -subj "/C=BR/ST=SAO PAULO/L=SAO PAULO/O=AXWAY/CN=localhost/subjectAltName=DNS.1=axway.lab,DNS.2=your-alt-name"
```

### 13. Generate Server Certificate
```javascript
openssl x509 -req -in ServerCert_signedByCAIntermediary.csr -CA CA.pem \
  -CAkey CA.key -CAcreateserial -out ServerCert_signedByCAIntermediary.crt -days 3650 -sha256
```


### 14. View Certificate
```javascript
openssl x509 -text -noout -in ServerCert_signedByCAIntermediary.crt
```

## <span style="color:blue">Step E. Generate <span style="color:red">Client Certificate</span> signed by CA</span>

### 15. Generate private Key
```javascript
openssl genrsa -out ClientCert_signedByCA.key 2048
```

### 16.Create Client CSR
```javascript
openssl req -new -sha256 -key ClientCert_signedByCA.key \
  -out ClientCert_signedByCA.csr -subj "/C=BR/ST=SAO PAULO/L=SAO PAULO/O=AXWAY/CN=client"
```

### 17. Generate Client Certificate
```javascript
openssl x509 -req -in ClientCert_signedByCA.csr -CA CA.pem -CAkey CA.key \
  -CAcreateserial -out ClientCert_signedByCA.crt -days 3650 -sha256
```

### 18. View Certificate
```javascript
openssl x509 -text -noout -in ClientCert_signedByCA.crt
```

## <span style="color:blue">Step F. Generate <span style="color:red">Client Certificate</span> signed by Intermediary CA</span>

### 19. Generate private Key
```javascript
openssl genrsa -out ClientCert_signedByCAIntermediary.key 2048
```

### 20. Create Client CSR
```javascript
openssl req -new -sha256 -key ClientCert_signedByCAIntermediary.key \
  -out ClientCert_signedByCAIntermediary.csr \
  -subj "/C=BR/ST=SAO PAULO/L=SAO PAULO/O=AXWAY/CN=clientCA_Intermediary"
```

### 21. Generate Client Certificate
```javascript
openssl x509 -req -in ClientCert_signedByCAIntermediary.csr \
  -CA CA_Intermediary.crt -CAkey CA_Intermediary.key \
  -CAcreateserial -out ClientCert_signedByCAIntermediary.crt -days 3650 -sha256
```

### 22. View Certificate
```javascript
openssl x509 -text -noout -in ClientCert_signedByCAIntermediary.crt
```

## [Additional information:](https://jamielinux.com/docs/openssl-certificate-authority/index.html)
- [Create the root pair](https://jamielinux.com/docs/openssl-certificate-authority/create-the-root-pair.html)
- [Create the intermediate pair](https://jamielinux.com/docs/openssl-certificate-authority/create-the-intermediate-pair.html)
- [Sign server and client certificates](https://jamielinux.com/docs/openssl-certificate-authority/sign-server-and-client-certificates.html)
- [Certificate revocation lists](https://jamielinux.com/docs/openssl-certificate-authority/certificate-revocation-lists.html)
- [Online Certificate Status Protocol](https://jamielinux.com/docs/openssl-certificate-authority/online-certificate-status-protocol.html)


### [Root CA configuration](https://jamielinux.com/docs/openssl-certificate-authority/appendix/root-configuration-file.html)
```
# OpenSSL root CA configuration file.
# Copy to `/root/ca/openssl.cnf`.

[ ca ]
# `man ca`
default_ca = CA_default

[ CA_default ]
# Directory and file locations.
dir               = /root/ca
certs             = $dir/certs
crl_dir           = $dir/crl
new_certs_dir     = $dir/newcerts
database          = $dir/index.txt
serial            = $dir/serial
RANDFILE          = $dir/private/.rand

# The root key and root certificate.
private_key       = $dir/private/ca.key.pem
certificate       = $dir/certs/ca.cert.pem

# For certificate revocation lists.
crlnumber         = $dir/crlnumber
crl               = $dir/crl/ca.crl.pem
crl_extensions    = crl_ext
default_crl_days  = 30

# SHA-1 is deprecated, so use SHA-2 instead.
default_md        = sha256

name_opt          = ca_default
cert_opt          = ca_default
default_days      = 375
preserve          = no
policy            = policy_strict

[ policy_strict ]
# The root CA should only sign intermediate certificates that match.
# See the POLICY FORMAT section of `man ca`.
countryName             = match
stateOrProvinceName     = match
organizationName        = match
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional

[ policy_loose ]
# Allow the intermediate CA to sign a more diverse range of certificates.
# See the POLICY FORMAT section of the `ca` man page.
countryName             = optional
stateOrProvinceName     = optional
localityName            = optional
organizationName        = optional
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional

[ req ]
# Options for the `req` tool (`man req`).
default_bits        = 2048
distinguished_name  = req_distinguished_name
string_mask         = utf8only

# SHA-1 is deprecated, so use SHA-2 instead.
default_md          = sha256

# Extension to add when the -x509 option is used.
x509_extensions     = v3_ca

[ req_distinguished_name ]
# See <https://en.wikipedia.org/wiki/Certificate_signing_request>.
countryName                     = Country Name (2 letter code)
stateOrProvinceName             = State or Province Name
localityName                    = Locality Name
0.organizationName              = Organization Name
organizationalUnitName          = Organizational Unit Name
commonName                      = Common Name
emailAddress                    = Email Address

# Optionally, specify some defaults.
countryName_default             = GB
stateOrProvinceName_default     = England
localityName_default            =
0.organizationName_default      = Alice Ltd
organizationalUnitName_default  =
emailAddress_default            =

[ v3_ca ]
# Extensions for a typical CA (`man x509v3_config`).
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ v3_intermediate_ca ]
# Extensions for a typical intermediate CA (`man x509v3_config`).
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true, pathlen:0
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ usr_cert ]
# Extensions for client certificates (`man x509v3_config`).
basicConstraints = CA:FALSE
nsCertType = client, email
nsComment = "OpenSSL Generated Client Certificate"
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid,issuer
keyUsage = critical, nonRepudiation, digitalSignature, keyEncipherment
extendedKeyUsage = clientAuth, emailProtection

[ server_cert ]
# Extensions for server certificates (`man x509v3_config`).
basicConstraints = CA:FALSE
nsCertType = server
nsComment = "OpenSSL Generated Server Certificate"
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid,issuer:always
keyUsage = critical, digitalSignature, keyEncipherment
extendedKeyUsage = serverAuth

[ crl_ext ]
# Extension for CRLs (`man x509v3_config`).
authorityKeyIdentifier=keyid:always

[ ocsp ]
# Extension for OCSP signing certificates (`man ocsp`).
basicConstraints = CA:FALSE
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid,issuer
keyUsage = critical, digitalSignature
extendedKeyUsage = critical, OCSPSigning
```

### [Intermediate CA configuration](https://jamielinux.com/docs/openssl-certificate-authority/appendix/intermediate-configuration-file.html)
```
# OpenSSL intermediate CA configuration file.
# Copy to `/root/ca/intermediate/openssl.cnf`.

[ ca ]
# `man ca`
default_ca = CA_default

[ CA_default ]
# Directory and file locations.
dir               = /root/ca/intermediate
certs             = $dir/certs
crl_dir           = $dir/crl
new_certs_dir     = $dir/newcerts
database          = $dir/index.txt
serial            = $dir/serial
RANDFILE          = $dir/private/.rand

# The root key and root certificate.
private_key       = $dir/private/intermediate.key.pem
certificate       = $dir/certs/intermediate.cert.pem

# For certificate revocation lists.
crlnumber         = $dir/crlnumber
crl               = $dir/crl/intermediate.crl.pem
crl_extensions    = crl_ext
default_crl_days  = 30

# SHA-1 is deprecated, so use SHA-2 instead.
default_md        = sha256

name_opt          = ca_default
cert_opt          = ca_default
default_days      = 375
preserve          = no
policy            = policy_loose

[ policy_strict ]
# The root CA should only sign intermediate certificates that match.
# See the POLICY FORMAT section of `man ca`.
countryName             = match
stateOrProvinceName     = match
organizationName        = match
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional

[ policy_loose ]
# Allow the intermediate CA to sign a more diverse range of certificates.
# See the POLICY FORMAT section of the `ca` man page.
countryName             = optional
stateOrProvinceName     = optional
localityName            = optional
organizationName        = optional
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional

[ req ]
# Options for the `req` tool (`man req`).
default_bits        = 2048
distinguished_name  = req_distinguished_name
string_mask         = utf8only

# SHA-1 is deprecated, so use SHA-2 instead.
default_md          = sha256

# Extension to add when the -x509 option is used.
x509_extensions     = v3_ca

[ req_distinguished_name ]
# See <https://en.wikipedia.org/wiki/Certificate_signing_request>.
countryName                     = Country Name (2 letter code)
stateOrProvinceName             = State or Province Name
localityName                    = Locality Name
0.organizationName              = Organization Name
organizationalUnitName          = Organizational Unit Name
commonName                      = Common Name
emailAddress                    = Email Address

# Optionally, specify some defaults.
countryName_default             = GB
stateOrProvinceName_default     = England
localityName_default            =
0.organizationName_default      = Alice Ltd
organizationalUnitName_default  =
emailAddress_default            =

[ v3_ca ]
# Extensions for a typical CA (`man x509v3_config`).
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ v3_intermediate_ca ]
# Extensions for a typical intermediate CA (`man x509v3_config`).
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true, pathlen:0
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ usr_cert ]
# Extensions for client certificates (`man x509v3_config`).
basicConstraints = CA:FALSE
nsCertType = client, email
nsComment = "OpenSSL Generated Client Certificate"
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid,issuer
keyUsage = critical, nonRepudiation, digitalSignature, keyEncipherment
extendedKeyUsage = clientAuth, emailProtection

[ server_cert ]
# Extensions for server certificates (`man x509v3_config`).
basicConstraints = CA:FALSE
nsCertType = server
nsComment = "OpenSSL Generated Server Certificate"
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid,issuer:always
keyUsage = critical, digitalSignature, keyEncipherment
extendedKeyUsage = serverAuth

[ crl_ext ]
# Extension for CRLs (`man x509v3_config`).
authorityKeyIdentifier=keyid:always

[ ocsp ]
# Extension for OCSP signing certificates (`man ocsp`).
basicConstraints = CA:FALSE
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid,issuer
keyUsage = critical, digitalSignature
extendedKeyUsage = critical, OCSPSigning
```
