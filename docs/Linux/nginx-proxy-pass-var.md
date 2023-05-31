---
tags:
    - Linux
    - Nginx
---

# Using variables as a domain in proxy_pass

Let's say, users request to http://localhost/ttyd/mydomain/blabla.

But, we want the requests actually send to http://mydomain

Here, nginx configuration 

```
http {
    server {
        listen 80;

        resolver 192.168.122.1 ipv6=off;  # <-- DNS resolver that can resolve the domain we specified

        location ~ ^/ttyd/(?<domain>[^/]+)/?(?<path>.*)$ {
            proxy_pass http://$domain;
        }
    }
}
```

Since Nginx uses its own DNS resolver, we **need** to specify it.

In `location ~ ^/ttyd/(?<domain>[^/]+)/?(?<path>.*)$` meaning any requests that contain `/ttyd/` will match to this block of `location`

Anything that follows `/ttyd/` will be caught as `$domain` and `$path`