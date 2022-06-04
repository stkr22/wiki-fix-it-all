# NGINX

## How do I open a datastream?

Simply with the nginx.conf

```conf
stream {
  upstream {{ HOST_INTERNAL_NAME }} {
  server {{ TARGET_HOST }};
  }
  server {
    listen 8768;
    proxy_pass {{ HOST_INTERNAL_NAME }};
    include /etc/nginx/manual_ip;
    allow {{ IP_ADDRESS_RANGE }}/20;
    deny all;
    }
}
```

The server in the sites-available then looks like this:

```conf
server {
 listen 8768;
 proxy_pass {{ HOST_INTERNAL_NAME }};
}
```
