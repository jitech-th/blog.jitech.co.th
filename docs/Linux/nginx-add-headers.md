---
tags:
    - Linux
    - Nginx
---

# Nginx add_header vs. proxy_set_header

```
location ~ ^/protected {
   auth_request          /auth;
   auth_request_set      $userid $upstream_http_x_kratos_authenticated_identity_id;
   # add_header          x-user-id $userid;
   proxy_set_header      x-user-id $userid;
   proxy_pass            http://backend_api;
}
```
- `auth_request` directive triggers an internal subrequest to the "/auth" endpoint to validate the user's authentication.
- `auth_request_set` directive sets the `$userid` variable based on the value of the response header `X-Kratos-Authenticated-Identity-Id` (which is received from Kratos).
- `add_header` sends headers to client (browser)
- `proxy_set_header` sends headers to backend server (the one proxy_pass to)