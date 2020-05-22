# Installation

## DNS

Edit your hosts file (`C:\Windows\system32/drivers/etc/hosts`) as administrator and add the following entries

```custom
127.0.0.1 web1.localhost web2.localhost
```

## Create certificates

### Root certificate

Use [mkcert](https://github.com/FiloSottile/mkcert) to generate local self-signed certificates.

> If the domain is publicly available through DNS, you can use Let's Encypt

On windows `mkcert -install` must be executed under elevated Administrator privileges.

```bash
cd compose/nginx/certs
mkcert --install
copy $env:LOCALAPPDATA\mkcert\rootCA.pem ./cacerts.pem
```

### Website certificates

```bash
cd compose/nginx/certs
mkcert -cert-file web1.localhost.crt -key-file web1.localhost.key web1.localhost
mkcert -cert-file web2.localhost.crt -key-file web2.localhost.key web2.localhost
```

## Docker compose

Start the configuration with

```bash
docker-compose up
```

### Extra api on subfolder

Edit the `./compose/nginx/<hostfile>` and add the extra location to redirect to the appropriate host

```custom
location ~/app2(.*)$ {
    proxy_set_header X-Real-IP  $remote_addr;
    proxy_set_header X-Forwarded-For $remote_addr;
    proxy_set_header Host $host;
    proxy_pass http://aaa.bbb.ccc.ddd:8001$1;
}
```
