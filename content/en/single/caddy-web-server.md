---
title: Caddy Web Server
url: "/en/single/caddy-web-server"
---

Caddy is a web server implemented in Go, with built-in default HTTPS access. When using an official domain name, it automatically obtains a CA certificate from Let's Encrypt, a non-profit organization, to enable secure access.

When accessing `https://localhost`, it will generate a self-signed CA certificate. At this point, you can install the `root.crt` root certificate into the system to also achieve secure access.

## Declare Docker Configuration

The system uses `Dockerfile` and `docker-compose.yaml` configurations to run Caddy in a container.

`third_apps/caddy/Dockerfile`

```docker
# syntax=docker/dockerfile:1
FROM caddy:2.6
```


> Custom-built image, which can be extended with your own modifications later.

`docker-compose.yaml`

```yaml
.
.
.
  caddy: # Service name in compose
    build: ./third_apps/caddy # Build directory
    container_name: caddy # Container name
    restart: always # Always restart
    ports: # Port mapping
      - "80:80"
      - "443:443"
      - "443:443/udp"
    volumes: # Mount local directories into the container
      - ./third_apps/caddy/Caddyfile:/etc/caddy/Caddyfile
      - ./third_apps/caddy/site:/srv
      - ./third_apps/caddy/data:/data
      - ./third_apps/caddy/config:/config
      - ./public/static:/public/static # Static resources under the web service root directory
      - ./storage/app/upload:/public/upload # Files uploaded under the web service root directory
    networks:
      - default # Docker network
```


## Usage

Since all services (gower, dev-full, test-full, prod-full) depend on Caddy for deployment, there is no need to specify running Caddy separately.

```shell
docker compose up -d dev-full
```


Don't forget to build the frontend code, so it is recommended to use the platform script to start the services.

```shell
$ ./docker/run-dev-full
```


> This will start the `dev-full` and `caddy` containers.

### For differences in service deployment methods, see the deployment chapter.

## Configuration

`third_apps/caddy/Caddyfile`

```yaml
#{
#  debug
#}

# development, test, production
localhost, www.qq.com, www.weixin.com {
  tls internal

  root * /public

  handle /static/* {
    file_server
  }
  handle /upload/* {
    file_server
  }

  handle {
    reverse_proxy gower:8080
  }
}
```


> All three deployment methods implement the same container web service configuration.
>
> localhost: Development mode on the local machine
>
> www.qq.com: After deploying the test version, I modified the hosts file on my local machine to bind the test server IP to `www.qq.com`, achieving secure test access to the service.
>
> www.weixin.com: After deploying the production version, similarly modify the hosts file to achieve secure access.

### For other configurations, see [Caddy](https://caddyserver.com)
