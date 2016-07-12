These systemd service definitions encode the three containers defined in the [docker-letsencrypt-nginx-proxy-companion](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion)'s [Separate Containers ](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion/blob/9adaeef603600d37fb02620e1d4b50eaaad926a4/README.md#separate-containers-recommended-method) method.

To install, from within a clone of this repository, run -

```
  git clone https://github.com/chrisprice/docker-letsencrypt-nginx-proxy-companion-systemd-services.git
  cd docker-letsencrypt-nginx-proxy-companion-systemd-services
  sudo su
  mkdir -p /etc/nginx
  cp nginx.tmpl /etc/nginx/nginx.tmpl
  cp *.service /etc/systemd/system
  systemctl enable nginx nginx-gen nginx-letsencrypt
  systemctl start nginx nginx-gen nginx-letsencrypt
```

Containers can then be reverse proxied by specifying the following environment variables when launching them -

| Name | Description | Example Value |
|------|-------------|---------------|
| VIRTUAL_HOST | The domain(s) associated with the virtual host (comma-separated values supported) | foo.bar.com |
| LETSENCRYPT_HOST | If SSL required, the domain(s) to register certificate(s) for, must match VIRTUAL_HOST  (comma-separated values supported) | foo.bar.com |
| LETSENCRYPT_EMAIL | If SSL required, the email address to associated with certificates | foo@bar.com |

For more details, see the [docker-letsencrypt-nginx-proxy-companion](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion) readme.

E.g. -

```
  docker run -d --name foo.bar.com -e VIRTUAL_HOST=foo.bar.com -e LETSENCRYPT_HOST=foo.bar.com -e LETSENCRYPT_EMAIL=foo@bar.com  nginx
```

Although you'll probably want to wrap this up in a service file (`foo.bar.com.service`)  -

```
[Unit]
Description=foo.bar.com
Requires=docker.service
After=docker.service

[Service]
TimeoutStartSec=0
Restart=always
RestartSec=3s
ExecStartPre=-/usr/bin/docker kill foo.bar.com
ExecStartPre=-/usr/bin/docker rm foo.bar.com
ExecStartPre=/usr/bin/docker pull nginx
ExecStart=/usr/bin/docker run --name foo.bar.com -e VIRTUAL_HOST=foo.bar.com -e LETSENCRYPT_HOST=foo.bar.com -e LETSENCRYPT_EMAIL=foo@bar.com nginx
ExecStop=/usr/bin/docker stop foo.bar.com

[Install]
WantedBy=multi-user.target
```

Which you'll want to copy to the right place, enable and start like so -

```
  sudo su
  cp foo.bar.com.service /etc/systemd/system
  systemctl enable foo.bar.com
  systemctl start foo.bar.com
```
