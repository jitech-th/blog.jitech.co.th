---
tags:
    - Linux
    - SSL
    - Certificate
    - OpenVPN
---
# Generate SSL certificate using easyrsa

> **Easy-RSA** is a utility for managing X.509 PKI, or Public Key Infrastructure. A PKI is based on the notion of trusting a particular authority to authenticate a remote peer.

## Instructions

- #### Download and extracct easyrsa

```
$ wget https://github.com/OpenVPN/easy-rsa/releases/download/v3.1.2/EasyRSA-3.1.2.tgz
$ tar -xvzf EasyRSA-3.1.2.tgz
```

- #### Verify that NTP is enabled

```
$ timedatectl 
               Local time: Fri 2023-04-07 17:57:37 +07
           Universal time: Fri 2023-04-07 10:57:37 UTC
                 RTC time: Fri 2023-04-07 10:57:37
                Time zone: Asia/Bangkok (+07, +0700)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no

## if not 
$ timedatectl set-ntp yes
```

- #### Setup variables for generating certificates

```
$ cd easy-rsa
$ vim vars
set_var EASYRSA                 "$PWD"
set_var EASYRSA_PKI             "$EASYRSA/pki"
set_var EASYRSA_DN              "cn_only"
set_var EASYRSA_REQ_COUNTRY     "TH"
set_var EASYRSA_REQ_PROVINCE    "Bangkok"
set_var EASYRSA_REQ_CITY        "Bangkok"
set_var EASYRSA_REQ_ORG         "My ORG"
set_var EASYRSA_REQ_EMAIL       "mymail@myorg.org"
set_var EASYRSA_REQ_OU          ""
set_var EASYRSA_KEY_SIZE        2048
set_var EASYRSA_ALGO            rsa
set_var EASYRSA_CA_EXPIRE       7500
set_var EASYRSA_CERT_EXPIRE     365
set_var EASYRSA_NS_SUPPORT      "no"
set_var EASYRSA_NS_COMMENT      ""
set_var EASYRSA_EXT_DIR         "$EASYRSA/x509-types"
set_var EASYRSA_SSL_CONF        "$EASYRSA/openssl-easyrsa.cnf"
set_var EASYRSA_DIGEST          "sha256"
```

- #### Make the vars file executable

```
$ chmod +x vars
```

- #### Initialize easyrsa

```
$ cd easyrsa
$ ./easyrsa init-pki
```

- #### Move the vars file to the pki directory

```
$ mv vars pki/.
```

- #### Generate CA certificate

```
$ ./easyrsa build-ca nopass 
```

- #### Generate server certificate

```
$ ./easyrsa gen-req <server_name> nopass
$ ./easyrsa sign-req server <server_name>
```

- #### Generate client certificate

```
$ ./easyrsa gen-req <client_name> nopass
$ ./easyrsa sign-req client <client_name>
```

- #### Generate DH key

```
$ ./easyrsa gen-dh
```
