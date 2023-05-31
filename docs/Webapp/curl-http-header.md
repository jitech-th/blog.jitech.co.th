---
tags:
    - Wepapp
    - curl
    - Note
---
# Curl with HTTP headers

I've just found out, the difference and advantage of HTTP headers.

Here for examples, we will take them from [ory.sh](https://www.ory.sh/docs/kratos/self-service)

Let's take a look at the differences between these two

- With the header `"Accept: text/html"`
```
curl -s -i -X GET \
    -H "Accept: text/html" \
    https://playground.projects.oryapis.com/self-service/login/browser

HTTP/2 303
date: Fri, 09 Jul 2021 10:23:52 GMT
content-type: text/html; charset=utf-8
content-length: 121
location: https://playground.projects.oryapis.com/hosted/login?flow=3fc63726-8461-43f4-974a-5579ff4174f1
cache-control: private, no-cache, no-store, must-revalidate
set-cookie: aHR0cHM6Ly9wbGF5Z3JvdW5kLnByb2plY3RzLm9yeWFwaXMuY29tL2FwaS9rcmF0b3MvcHVibGlj_csrf_token=Lk9swSOlimGS9LI5HslOyEKGL4hMQzWHnwnQpm9HGAA=; Path=/api/kratos/public; Domain=playground.projects.oryapis.com; Max-Age=31536000; HttpOnly; Secure; SameSite=None
vary: Origin
vary: Cookie
strict-transport-security: max-age=15724800; includeSubDomains

<a href="https://playground.projects.oryapis.com/hosted/login?flow=3fc63726-8461-43f4-974a-5579ff4174f1">See Other</a>.
```

- - With the header `"Accept: application/json"`
```
curl -v -s -X GET \
    -H "Accept: application/json"  \
    https://playground.projects.oryapis.com/self-service/login/browser | jq

> GET /self-service/login/browser HTTP/2
> Host: playground.projects.oryapis.com
> User-Agent: curl/7.64.1
> Accept: application/json

< HTTP/2 200
< date: Fri, 09 Jul 2021 10:25:12 GMT
< content-type: application/json; charset=utf-8
< content-length: 1359
< cache-control: private, no-cache, no-store, must-revalidate
< set-cookie: aHR0cHM6Ly9wbGF5Z3JvdW5kLnByb2plY3RzLm9yeWFwaXMuY29tL2FwaS9rcmF0b3MvcHVibGlj_csrf_token=UlKMcLe00G8B9GjC7D1I5rvQ6P79Q0YpzKb4lo7uLtw=; Path=/api/kratos/public; Domain=playground.projects.oryapis.com; Max-Age=31536000; HttpOnly; Secure; SameSite=None
< vary: Origin
< vary: Cookie
< strict-transport-security: max-age=15724800; includeSubDomains

{
  "id": "ff0c97c4-a7bb-49a5-a8a6-ebf174877fa5",
  "type": "browser",
  "expires_at": "2021-07-09T11:25:12.136099226Z",
  "issued_at": "2021-07-09T10:25:12.136099226Z",
  "request_url": "http://playground.projects.oryapis.com/self-service/login/browser",
  "ui": {
    "action": "https://playground.projects.oryapis.com/self-service/login?flow=ff0c97c4-a7bb-49a5-a8a6-ebf174877fa5",
    "method": "POST",
    "nodes": [ /* ... */ ]
  },
  "created_at": "2021-07-09T10:25:12.137554Z",
  "updated_at": "2021-07-09T10:25:12.137554Z",
  "forced": false
}
```

This means, when users access `https://playground.projects.oryapis.com/self-service/login/browser` via their web browser (or request with `text/html` header) users will be redirected to the login page.

But, if we have a web server, or we run a SPA (single page app) we need some data from the same server, we could use `"Accept: application/json"`.