---
title: Caddy
url: "/single/caddy"
---

> Caddy 是一款 Go 实现 web 服务器, 内置默认 https 访问, 使用正式域名时, 自动获取 Let's Encrypt 非营利性组织的 CA 证书, 实现安全访问.
>
> 当访问 `https://localhost` 是, 会自签 CA 证书, 这时候把 root.crt 根证书, 安装进系统, 也可实现安全访问.

## 声明 docker 相关配置

系统通过 Dockerfile 与 docker-compose.yaml 配置在容器使用 Caddy.

`third_apps/caddy/Dockerfile`

```docker
# syntax=docker/dockerfile:1
FROM caddy:2.6
```
> 自打包镜像, 后期可以扩展自己的修改

`docker-compose.yaml`

```yaml
.
.
.
  caddy: # compose 服务名称
    build: ./third_apps/caddy # 打包目录
    container_name: caddy # 容器名称
    restart: always # 总是重启
    ports: # 端口映射
      - "80:80"
      - "443:443"
      - "443:443/udp"
    volumes: # 本机目录挂载进容器内
      - ./third_apps/caddy/Caddyfile:/etc/caddy/Caddyfile
      - ./third_apps/caddy/site:/srv
      - ./third_apps/caddy/data:/data
      - ./third_apps/caddy/config:/config
      - ./public/static:/public/static # Web 服务 root 目录下静态资源
      - ./storage/app/upload:/public/upload # Web 服务 root 目录上传的文件
    networks:
      - default # docker 网络
```

## 使用

因为 gower, dev-full, test-full, prod-full 所有服务发布方式都依赖了 caddy, 所以无需指定运行 caddy

```shell
docker compose up -d dev-full
```

别忘记打包前端的代码哦, 所以建议用平台脚本运行启动

```shell
$ ./run-dev-full
```

> 这样将启动 dev-full 和 caddy 容器

### 有关服务发布方式的区别, 见发布章节

## 配置

`third_apps\caddy\Caddyfile`

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

> 三种发布方式, 全部实现相同容器 Web 服务的运行配置
>
> localhost: 本机开发模式
>
> www.qq.com: 发布测试版后, 我修改了本机 hosts 文件, 用测试服务器 ip 绑定到了 www.qq.com, 实现了安全测试访问服务
>
> www.weixin.com: 发布生产版后, 同样修改 hosts 文件, 实现安全访问

### 关于其它配置见 [Caddy](https://caddyserver.com)