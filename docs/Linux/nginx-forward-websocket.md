---
tags:
    - Linux
    - Nginx
---

# Forward requests to websocket ttyd

> **Websocket** is a type of network protocol that runs on the HTTP and HTTPS standards. It has additional features that enable real-time, bi-directional communication without the need for sending frequent requests.

```
location /wsapp/ {
   proxy_pass http://wsbackend;
   proxy_http_version 1.1;
   proxy_set_header Upgrade $http_upgrade;
   proxy_set_header Connection "upgrade";
}
```

The `Upgrade` and `Connection` headers are used to initiate the WebSocket handshake and to switch the protocol from HTTP to WebSocket.

- `proxy_set_header Upgrade $http_upgrade;` sets the Upgrade header to the value of the `$http_upgrade` variable. This variable will contain the value of the `Upgrade` header from the client request, which will typically be set to websocket if the client supports WebSockets.

- `proxy_set_header Connection "upgrade";` sets the Connection header to upgrade, which tells NGINX to upgrade the connection to WebSocket after the handshake is complete.
