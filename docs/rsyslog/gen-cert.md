# Generate Self-Sign Certificate
A self-sign certificate is a digital certificate that is signed by its own private key instead of the trusted certificate authority (CA). So creating a self-signed is common for setting up a local development environment such as testing SSL/TLS functionality. It can be a production use but some environments will error due to a lack of trusted certificate authority.
## Prerequisites
To follow this guide to create self-signed, you need to have following prerequisites in place:

1. **OpenSSL:** Ensure that you have OpenSSL in your system. You can obtain the latest version of OpenSSL from the [official OpenSSL website](https://www.openssl.org/). (**Note:** this guide using OpenSSL 3.0.1)
   
2. **Basic Command Line Knowledge:** Due to the different version of software, familarity with the command line interface (CLI) is neccessary to excute OpenSSL commands, navigate the file system and debugging error.
   
3. **Administrative Privileges:** Depending on your operating system, you may require admministrative to perform some operations, such as install OpenSSL or create files in specifics directories.

## How to generate certificate

Here's a step-by-step guide to creating self-signed certificates using the provided commands

1. **Check OpenSSL Version:** This command checks the version of OpenSSL installed on your system. Make sure you have OpenSSL 3.0.1 or a compatible version.
```
[user@localhost ~]$ openssl version
OpenSSL 3.0.1 14 Dec 2021 (Library: OpenSSL 3.0.1 14 Dec 2021)
```

2. **Create a Certificate Directory:** Create a directory "cert" and navigate to it. This will be the location where you generate and store your certificate files.
```
[user@localhost ~]$ mkdir cert
[user@localhost ~]$ cd cert
```

3. **Generate the CA (Certificate Authority) Key:** 
This command generates a private key (`ca.key`) for the Certificate Authority (CA). The CA key is use to sign the self-signed certificates.
```
[user@localhost cert]$ openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:4096 -out ca.key
....+.....+++++++++++++++++++++++++++++++++++
<output ommited>
+...............+.....+.+......+........+.+...
```

4. **Generate the CA Certificate Signing Request (CSR):** This command creates a Certificate Signing Request (CSR) for the CA. You'll be prompted to provide information such as country, state, organization, and common name. Feel free to adjust these values as needed.
```
[user@localhost cert]$ openssl req -new -key ca.key -out ca.csr
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:TH
State or Province Name (full name) []:Bangkok
Locality Name (eg, city) [Default City]:Chatuchak
Organization Name (eg, company) [Default Company Ltd]:Jitech
Organizational Unit Name (eg, section) []:Services Team
Common Name (eg, your name or your server's hostname) []:ca.jite.ch
Email Address []:service@jitech.co.th

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []: <Enter>
An optional company name []: <Enter>
```

5. **Generate the CA Certificate:** This command generates the CA certificate (`ca.crt`) by signing the CA CSR (`ca.csr`) with the CA private key (`ca.key`).
```
[user@localhost cert]$ openssl x509 -sha512 -req -days 1095 -in ca.csr -signkey ca.key -out ca.crt
```

6. **Generate the Server Key:** This command generates a private key for the server (`server.key`). The server key will be used to secure the server's SSL/TLS connections.
```
[user@localhost cert]$ openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:2048 -out server.key
....+.....+++++++++++++..++++++++++++++++++++
<output ommited>
+++++++++++++++++++++++++++++++++++++++++++++
```

7. **Generate the Server Certificate Signing Request (CSR):** This command creates a CSR (`server.csr`) for the server. You'll be prompted to provide information similar to step 4, but this time for the server certificate.
```
[user@localhost cert]$ openssl req -new -key server.key -out server.csr
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:TH
State or Province Name (full name) []:Bangkok
Locality Name (eg, city) [Default City]:Chatuchak
Organization Name (eg, company) [Default Company Ltd]:Jitech
Organizational Unit Name (eg, section) []:Services Team
Common Name (eg, your name or your server's hostname) []:server.jite.ch
Email Address []:service@jitech.co.th

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:<Enter>
An optional company name []:<Enter>
```

8. **Generate the Server Certificate:**
This command generates the server certificate (`server.crt`) by signing the server CSR (`server.csr`) with the CA certificate (`ca.crt`) and key (`ca.key`). The server certificate is given a shorter expiration time compared to the CA certificate for security reasons. Server certificates are specific to individual servers or services and are subject to more frequent changes or updates. Assigning a shorter expiration time, such as 90 days in the example command, encourages regular certificate renewal and helps mitigate risks associated with compromised or outdated certificates.
```
[user@localhost cert]$ openssl x509 -sha512 -in server.csr -out server.crt \
                       -req -CA ca.crt -CAkey ca.key -CAcreateserial -days 90
```

9. **Verify Generated Files:** Use this command to list the files in the "cert" directory. Verify that the following files are present: `ca.crt`, `ca.csr`, `ca.key`, `server.crt`, `server.csr`, and `server.key`. These are the generated certificate files.
```
[user@localhost cert]$ ls -al
total 24
-rw-r--r--. 1 user user 2041 Jul 13 17:42 ca.crt
-rw-r--r--. 1 user user 1760 Jul 13 17:41 ca.csr
-rw-------. 1 user user 3272 Jul 13 17:40 ca.key
-rw-r--r--. 1 user user 1700 Jul 13 17:45 server.crt
-rw-r--r--. 1 user user 1074 Jul 13 17:44 server.csr
-rw-------. 1 user user 1704 Jul 13 17:43 server.key
```

Congratulations! You have successfully created self-signed certificates using OpenSSL. Now you can use these certificates for testing or development purposes. Remember that self-signed certificates are not trusted by default by most browsers and clients, so they are not suitable for some production use.
