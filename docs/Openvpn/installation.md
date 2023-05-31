# OpenVPN 2.5 installation 

## Instructions

- #### import openvpn repository
```
$ dnf copr enable dsommers/openvpn-release
Enabling a Copr repository. Please note that this repository is not part
of the main distribution, and quality may vary.

The Fedora Project does not exercise any power over the contents of
this repository beyond the rules outlined in the Copr FAQ at
<https://docs.pagure.org/copr.copr/user_documentation.html#what-i-can-build-in-copr>,
and packages are not held to any quality or security level.

Please do not file bug reports about these packages in Fedora
Bugzilla. In case of problems, contact the owner of this repository.

Do you really want to enable copr.fedorainfracloud.org/dsommers/openvpn-release? [y/N]: y 
Repository successfully enabled.
```

- #### install openvpn
```
# dnf install openvpn openvpn-auth-ldap
Copr repo for openvpn-release owned by dsommers                                                                                                                             2.2 kB/s | 4.7 kB     00:02    
Dependencies resolved.
============================================================================================================================================================================================================
 Package                                    Architecture                    Version                                  Repository                                                                        Size
============================================================================================================================================================================================================
Installing:
 openvpn                                    x86_64                          2.5.9-1.el8                              copr:copr.fedorainfracloud.org:dsommers:openvpn-release <-- new version           690 k
 openvpn-auth-ldap                          x86_64                          2.0.4-6.el8                              epel                                                                              61 k
Installing dependencies:
 libobjc                                    x86_64                          8.5.0-18.el8                             epel                                                                              56 k
 pkcs11-helper                              x86_64                          1.22-7.el8                               epel                                                                              64 k

Transaction Summary
============================================================================================================================================================================================================
Install  4 Packages

```

