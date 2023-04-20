# sub-web

基于 vue-cli 与 [tindy2013/subconverter](https://github.com/tindy2013/subconverter) 后端实现的配置自动生成。

## 写在前面

参考文档：
https://v2rayssr.com/sub-web.html

总结起来主要以下几点：

1. 需要后端 + 前端配合使用，当然不实用前端也可以，需要研究后端的接口然后通过 postman 之类的调用
2. 这里只是前端的代码
3. 后端代码可以直接使用编译好的文件：https://github.com/tindy2013/subconverter/releases
4. 前端正常安装部署，其中需要修改的是 src/view/Subconverter.vue 中的 backendOptions，改成后端的域名。以及 evn 中有关后端的域名
5. 后端需要设置
    1. subconverter/pref.ini 中的 api_access_token 设置成复杂的字符串，managed_config_prefix 设置成对应的后端域名，比如 suc.tvos.io，listen=127.0.0.1
    2. nginx 域名反向代理到 127.0.0.1:25500
    3. 将后端的程序设置成开机自动运行，参考博客
6. 这样访问前端代码，就能使用我们自己的后端生成订阅链接
7. 随系统启动可以设置  /etc/systemd/system，创建一个名为 sub.service 的文件

```
[Unit]
Description=A API For Subscription Convert
After=network.target
 
[Service]
Type=simple
ExecStart=/root/subconverter/subconverter
WorkingDirectory=/root/subconverter
Restart=always
RestartSec=10
 
[Install]
WantedBy=multi-user.target
```

## Table of Contents

- [ChangeLog](#ChangeLog)
- [Docker](#Docker)
- [Requirements](#Requirements)
- [Install](#install)
- [Usage](#usage)
- [Related](#Related)
- [Contributing](#contributing)
- [License](#license)

## ChangeLog

- 20200730

  - 独立各类后端配置到 .env 文件中，现在修改后端只需要修改 .env 即可。


## Docker

```shell
docker run -d -p 58080:80 --restart always --name subweb careywong/subweb:latest
```

若需要对代码进行修改，你需要在本地构建镜像并运行。
注：每次修改代码，你都需要重新执行 docker build 来执行打包操作。

```shell
docker build -t subweb-local:latest .

docker run -d -p 58080:80 --restart always --name subweb subweb-local:latest
```

## Requirements

你需要安装 [Node](https://nodejs.org/zh-cn/) 与 [Yarn](https://legacy.yarnpkg.com/en/docs/install) 来安装依赖与打包发布。你可以通过以下命令查看是否安装成功。
注：以下步骤为 Ubuntu 下相应命令，其他系统请自行修改。为了方便后来人解决问题，有问题请发 issue。

```shell
node -v
yarn -v
```

## Install

```shell
yarn install
```

## Usage

```shell
yarn serve
```

浏览器访问 <http://localhost:8080/>

## Deploy

发布到线上环境，你需要安装依赖，执行以下打包命令，生成的 dist 目录即为发布目录。如需修改默认后端，请修改 src/views/Subconverter.vue 中 **defaultBackend** 配置项。

```shell
yarn build
```

你需要安装 nginx (或其他 web 服务器)并正确配置。以下为示例配置，你需要修改 example.com 为自己域名并配置正确的项目根路径（https 自行配置）。

```shell
server {
    listen 80;
    server_name example.com;

    root /var/www/http/sub-web/dist;
    index index.html index.htm;

    error_page 404 /index.html;

    gzip on; #开启gzip压缩
    gzip_min_length 1k; #设置对数据启用压缩的最少字节数
    gzip_buffers 4 16k;
    gzip_http_version 1.0;
    gzip_comp_level 6; #设置数据的压缩等级,等级为1-9，压缩比从小到大
    gzip_types text/plain text/css text/javascript application/json application/javascript application/x-javascript application/xml; #设置需要压缩的数据格式
    gzip_vary on;

    location ~* \.(css|js|png|jpg|jpeg|gif|gz|svg|mp4|ogg|ogv|webm|htc|xml|woff)$ {
        access_log off;
        add_header Cache-Control "public,max-age=30*24*3600";
    }
}
```

## backend

可以直接使用 release 版本
https://github.com/tindy2013/subconverter/releases




## Related

- [tindy2013/subconverter](https://github.com/tindy2013/subconverter)
- [CareyWang/MyUrls](https://github.com/CareyWang/MyUrls)
- [CareyWang/bitly](https://github.com/CareyWang/bitly)

## Contributing

PRs accepted.

Small note: If editing the README, please conform to the [standard-readme](https://github.com/RichardLitt/standard-readme) specification.

## License

MIT © 2020 CareyWang
