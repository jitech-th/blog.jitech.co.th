---
tags:
    - Linux
    - Systemd
    - Bird
---
# Setup systemd for bird routing

## Instructions

- #### write file in the systemd directory
```
$ vim /usr/lib/systemd/system/bird.service
```

- #### add the systemd configuration
```
[Unit]
Description=BIRD Internet Routing Daemon
After=network.target

[Service]
Type=forking
ExecStart=/usr/sbin/bird
ExecReload=/usr/bin/kill -HUP $MAINPID

[Install]
WantedBy=multi-user.target
```

- #### reload systemd
```
$ systemctl daemon-reload
```

- #### start the service
```
$ systemctl start bird
```