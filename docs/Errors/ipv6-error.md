# Fix ipv6.addr-gen-mode property is invalid

- #### Add this to `/etc/NetworkManager/NetworkManager.conf`
```
[connection]
ipv6.addr-gen-mode=eui64
```