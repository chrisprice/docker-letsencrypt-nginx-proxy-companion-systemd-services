These systemd service definitions encode the three containers defined in the [docker-letsencrypt-nginx-proxy-companion](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion)'s [Separate Containers ](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion/blob/9adaeef603600d37fb02620e1d4b50eaaad926a4/README.md#separate-containers-recommended-method) method.

To install, from within a clone of this repository, run -

```
  sudo su
  mkdir -p /etc/nginx
  cp nginx.tmpl > /etc/nginx/nginx.tmpl
  cp *.service /etc/systemd/system
  systemctl enable nginx nginx-gen nginx-letsencrypt
```

Followed by one of -
```
  systemctl start nginx nginx-gen nginx-letsencrypt
```

Or -
```
  reboot
```
