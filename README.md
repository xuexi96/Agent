## trojan的部署

为编译的

原版：[https://github.com/trojan-gfw](http://link.zhihu.com/?target=https%3A//www.youtube.com/redirect%3Fevent%3Dvideo_description%26redir_token%3DQUFFLUhqa2o4YjdjSzVHcWN3NEdaZWZMLWRtLUU2R2Y1Z3xBQ3Jtc0tsbVhQcGRLLW1JU0g5b0lKSGtzYlhjV3BDZ2gtc2dSLTRBQXpVVlJjV0RrdFlJRUp1N2ttd2I4eG9DNWFKSWktTkxqRUM2YjhDa00zdEdnUlZmZ1B6ZDhodHhKQ1ZpeXFmcXQzNHAxZ3ZpMExrb0drYw%26q%3Dhttps%3A%2F%2Fgithub.com%2Ftrojan-gfw%26v%3DFhu6XxEno_o)

go版本：[https://github.com/p4gefau1t/trojan-go](http://link.zhihu.com/?target=https%3A//github.com/p4gefau1t/trojan-go)

下载编译的成二进制的

```bash
wget  https://github.com/p4gefau1t/trojan-go/releases/download/v0.10.6/trojan-go-linux-amd64.zip
```

进入trojan的解压目录

```ps1con
unzip trojan-go-linux-amd64.zip
```

创建文件

```text
vim config.json
```

文件的内容如下

```json
{
    "run_type": "server",
    "local_addr": "0.0.0.0",
    "local_port": 443,
    "remote_addr": "192.83.167.78",
    "remote_port": 80,
    "password": [
        "你的密码"
    ],
    "ssl": {
        "cert": "server.crt",
        "key": "server.key"
    }
}
```

参数说明

run_type:表示该配置文件作用，server表示服务器端使用

local_addr:表示监听的ip

remote_addr:表示当tls握手失败会去访问的代理，这里的代理指向的nginx服务器，所要[安装Nginx](https://zhuanlan.zhihu.com/p/588895566)

remote_port:访问的代理端口,端口是Nginx端口

password：数组中的值表示密码

ssl:表示证书的内容，cert表示证书，key表示秘钥

更多选选项说明

[https://p4gefau1t.github.io/trojan-go/basic/full-config/](http://link.zhihu.com/?target=https%3A//p4gefau1t.github.io/trojan-go/basic/full-config/)

生成私秘

```text
openssl ecparam -genkey -name prime256v1 -out server.key
```

使用ecparam下的prime256v1进行加密

生成证书

```text
openssl req -new -x509 -days 36500 -key server.key -out server.crt  -subj "/CN=bing.com"
```

参数说明

req -new -x509 申请(req) 一个新的(new) 的x509证书

-days 证书的有效期

-key 使用那个秘钥生成证书

-out 输出的证书路径

-subj 指定证书的信息， /CN指的是证书的持有者，/O指的是证书的组织



后台运行

```text
nohup ./trojan-go > trojan.log 2>&1 &
```

[安装BBR加速](http://link.zhihu.com/?target=https%3A//itlanyan.com/use-bbr-in-centos-7/)

客户端下载

[https://itlanyan.com/v2ray-clients-download/](http://link.zhihu.com/?target=https%3A//itlanyan.com/v2ray-clients-download/)

[Release 6.2 · 2dust/v2rayN](http://link.zhihu.com/?target=https%3A//github.com/2dust/v2rayN/releases/tag/6.2)

[https://github.com/Fndroid/clash_for_windows_pkg/releases](http://link.zhihu.com/?target=https%3A//github.com/Fndroid/clash_for_windows_pkg/releases)



![img](https://picx.zhimg.com/v2-ed96d2f03d0b90b69dd4aca4f7f26ea3_720w.jpg?source=d16d100b)

![img](https://pic1.zhimg.com/v2-3d3bba63ac270b45ccd2033a7533481b_720w.jpg?source=d16d100b)

[trojan配置的详细说明](http://link.zhihu.com/?target=https%3A//p4gefau1t.github.io/trojan-go/)

clash配置

```yaml
proxies:
  - name: trojan
    type: trojan
    server: #服务器IP或域名
    port: 1236 
    password: 12345678 #密码
    udp: true
    sni: bing.com #证书
    alpn:
      - h2
      - http/1.1
    skip-cert-verify: false

proxy-groups:
  - name: 代理
    type: select
    proxies:
      - trojan #对应上面的nmme
       
```

------

## Shadowsocks-libev部署

项目地址：[https://github.com/shadowsocks](http://link.zhihu.com/?target=https%3A//github.com/shadowsocks)

### 服务器端

安装依赖

```bash
yum install epel-release -y

yum install gcc gettext autoconf libtool automake make pcre-devel asciidoc xmlto c-ares-devel libev-devel libsodium-devel mbedtls-devel -y
```

下载shadowsockes-libev

```text
wget https://github.com/shadowsocks/shadowsocks-libev/releases/download/v3.3.5/shadowsocks-libev-3.3.5.tar.gz
```

解压文件

```text
tar -zxvf  shadowsocks-libev-3.3.5.tar.gz
```

进入shadowsocks-libev-3.3.5文件夹，执行如下命令

检查环境,指定路径时，可以使用`./configure -prefix=安装路径`

```text
./configure 
```

安装

```text
make && make install
```

在不指定安装路径的情况下，要运行服务要到`/usr/local/bin` 目录下

运行服务的命令：

```text
./ss-server -c config.json
```

`-c`:用于指定配置文件，配置文件的路径

配置文件的内容如下

```json
{
    "server":"0.0.0.0",
    "mode":"tcp_and_udp",
    "server_port":8388,
    "local_port":1080,
    "password":"12345678",
    "timeout":86400,
    "method":"chacha20-ietf-poly1305"
}
```

`server`：您的主机名或服务器 IP (IPv4/IPv6)。

`server_port`：服务器端口号。

`local_port`：本地端口号。

`password`：用于加密传输的密码

`timeout`: 超时时间，单位为秒，表示当连接在一段时间内没有活动时会被关闭。

`method`: 加密方式。

`plugin`: 插件名称，用于使用插件功能，如V2Ray、simple-obfs等

`plugin_opts`: 插件的选项参数，通常与插件一起使用。



使用Git安装Shadowsocks

拉取shadowsocks项目

```text
git clone https://github.com/shadowsocks/shadowsocks-libev.git
```

进入shadowsocks-libev文件夹

```text
cd shadowsocks-libev
```

初始化git

```text
git submodule update --init --recursive
```

执行autogen.sh生成configure

```text
./autogen.sh
```

检查环境

```text
./configure
```

安装

```text
make && make install 
```

## Shadowsocks-rust 部署

```text
wget https://github.com/shadowsocks/shadowsocks-rust/releases/download/v1.20.2/shadowsocks-v1.20.2.x86_64-unknown-linux-gnu.tar.xz
```

解压

```text
tar -xvJf shadowsocks-v1.20.2.x86_64-unknown-linux-gnu.tar.xz
```

解决`GLIBC_2.18' not found (required by ./ssserver)

```text
wget http://ftp.gnu.org/gnu/glibc/glibc-2.18.tar.gz
tar zxf glibc-2.18.tar.gz 
cd glibc-2.18/
mkdir build
cd build/
../configure --prefix=/usr
make -j2
make install
```

## 使用 systemd 守护进程

vim /etc/systemd/system/shadowsocks.service

```text
[Unit]
Description=Shadowsocks Server
After=network.target

[Service]
#ExecStart=ssserver的路径 -c config.json路径
ExecStart=/usr/local/shadowsocks-rust/ssserver -c /usr/local/shadowsocks-rust/config.json


Restart=on-abort

[Install]
WantedBy=multi-user.target
```

运行

```text
systemctl start shadowsocks
```

### 添加v2ray-plugin插件

[v2ray-plugin下载](http://link.zhihu.com/?target=https%3A//github.com/shadowsocks/v2ray-plugin/releases/tag/v1.3.2)

```text
wget https://github.com/shadowsocks/v2ray-plugin/releases/download/v1.3.2/v2ray-plugin-linux-amd64-v1.3.2.tar.gz
```

解压

```text
tar -zxvf v2ray-plugin-linux-amd64-v1.3.2.tar.gz
```

将解压好的v2ray-plugin_linux_amd64放到和ss-server同一个目录

配置ss-server的config.json

```json
{
    "server":"0.0.0.0",
    "mode":"tcp_and_udp",
    "server_port":8388,
    "local_port":1080,
    "password":"12345678",
    "timeout":86400,
    "method":"chacha20-ietf-poly1305",
    "plugin":"v2ray-plugin_linux_amd64",
    "plugin_opts":"server"
}
```

plugin`: `这里填写的是解压出来的v2ray-plugin_linux_amd64
plugin_opts`: `插件的选项参数，详细说明

### 证书配置

```json
{
    "server":"0.0.0.0",
    "mode":"tcp_and_udp",
    "server_port":3458,
    "password":"12345678",
    "timeout":86400,
    "method":"chacha20-ietf-poly1305",
    "plugin":"v2ray-plugin_linux_amd64",
        "plugin_opts":"server;tls;host=www.wogong.top;cert=/home/shadowsocks/wogong.top_other/wogong.top_bundle.crt;key=/home/shadowsocks/wogong.top_other/wogong.top.key;"

}
```

### 客户端

[window版的shadowsock下载](http://link.zhihu.com/?target=https%3A//github.com/shadowsocks/shadowsocks-windows/releases)

![img](https://pic1.zhimg.com/v2-67636094b745844b80f17768f2974c39_720w.jpg?source=d16d100b)

解压文件后

![img](https://pic1.zhimg.com/v2-15ecc32b52935d6c4176528bc7dd6c5e_720w.jpg?source=d16d100b)

[v2ray-plugin下载](http://link.zhihu.com/?target=https%3A//github.com/shadowsocks/v2ray-plugin/releases)

![img](https://pica.zhimg.com/v2-f300986d23fbd5d087b7ae3e45d03171_720w.jpg?source=d16d100b)

解压文件,注意解压后的v2ray-plugin要放到和shadowsocks.exe在同一个目录

![img](https://pic1.zhimg.com/v2-4f27d4f6b3a6f4d4710868083e306254_720w.jpg?source=d16d100b)

![img](https://pic1.zhimg.com/v2-f632b165d8a0166cc1b50589f1781001_720w.jpg?source=d16d100b)



![img](https://picx.zhimg.com/v2-541308ff6de6001da414f18d8951d1f0_720w.jpg?source=d16d100b)

启动代理

![img](https://picx.zhimg.com/v2-59463a3da3fa7b6878ac94f14f7b0299_720w.jpg?source=d16d100b)

[v2ray-plugin安装](http://link.zhihu.com/?target=https%3A//gist.github.com/jshensh/bc2f76b20c4e6876ec6d667e707ec29d)教程

### clash配置

```yaml
proxies:
  - name: myserver
    type: ss
    server: #服务器或域名
    port: 3460
    cipher: chacha20-ietf-poly1305
    password: 12345678
    plugin: v2ray-plugin
    plugin-opts:
      mode: websocket
      tls: true
      skip-cert-verify: false
      host: www.wogong.top
proxy-groups:
  - name: 代理
    type: select
    proxies:
      - myserver
```

------

## V2Ray的安装和使用

[教程](http://link.zhihu.com/?target=https%3A//guide.v2fly.org/basics/vmess.html%23%E9%85%8D%E7%BD%AE%E5%89%8D%E7%9A%84%E5%87%86%E5%A4%87)

下载和安装 [官方脚本](http://link.zhihu.com/?target=https%3A//github.com/v2fly/fhs-install-v2ray)

```bash
bash <(curl -L https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh)
```

![img](https://pic1.zhimg.com/v2-af7a4b39c9d3c0a3c2821875702e7c7c_720w.jpg?source=d16d100b)

命令运行

启动V2Ray

```text
systemctl start v2ray
```

![img](https://pica.zhimg.com/v2-47b6b6e491adc33aab2e74d8b8a4d281_720w.jpg?source=d16d100b)

命令

查看状态

![img](https://pic1.zhimg.com/v2-63da701733c3a95b1dd06adaabee7b76_720w.jpg?source=d16d100b)

修改配置文件默认位置在/usr/local/etc/v2ray

```text
vim config.json
```

congig.json中的内容

**vmess+tcp的配置：**

```json
{
  "inbounds": [
    {
      "port": 8388,
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "af41686b-cb85-494a-a554-eeaa1514bca7",
            "alterId": 0
          }
        ]
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    }
  ]
}
```

参数说明：

```
port`：`表示监听的端口号
protocol`:`协议类型
```

`id`:`id`， 使用/usr/local/bin/v2ray uuid 生成，[https://www.v2fly.org/awesome/tools.html](http://link.zhihu.com/?target=https%3A//www.v2fly.org/awesome/tools.html)

```
alterId`:`额外ID
```

对应客户端配置

![img](https://picx.zhimg.com/v2-163331661eed461e0c033790b2112b2c_720w.jpg?source=d16d100b)

**vmess+tcp(ws)+tls配置**

```json
{
  "inbounds": [
    {
      "port": 8388, 
      "protocol": "vmess",    
      "settings": {
        "clients": [
          {
            "id": "af41686b-cb85-494a-a554-eeaa1514bca7",  
            "alterId": 0
          }
        ]
      },
      "streamSettings": {
        "network": "tcp",
        "security": "tls",
        "tlsSettings": {
          "certificates": [
            {
              "certificateFile": "/usr/local/etc/v2ray/server.crt", 
              "keyFile": "/usr/local/etc/v2ray/server.key" 
            }
          ]
        }
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    }
  ]
}
参数说明：
```

`network`:数据流所使用的网络类型

```
security`:`是否启用传输层加密
certificateFile`:`证书路径
keyFile`: `证书路径
```

对应客户端配置

![img](https://picx.zhimg.com/v2-271f6a20f542319c5b00a4c18fd8f43a_720w.jpg?source=d16d100b)

class配置

```yaml
proxies:
  - name: v2ray
    server: #服务器或域名
    port: 8388
    type: vmess
    uuid: af41686b-cb85-494a-a554-eeaa1514bca7
    alterId: 0
    cipher: auto
    tls: true
    skip-cert-verify: false
    servername: wogong.top

proxy-groups:
  - name: 代理
    type: select
    proxies:
      - v2ray
```

**vmess+ws+tls+web配置**

**安装Nginx**

**v2Ray的配置**

```json
{
  "inbounds": [
    {
      "port": 8388,
      "listen":"127.0.0.1",
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "af41686b-cb85-494a-a554-eeaa1514bca7",
            "alterId": 0
          }
        ]
      },
      "streamSettings": {
        "network": "ws",
        "wsSettings": {
        "path": "/ray"
        }
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    }
  ]
}
```

Nginx配置

替换http中的内容为下面内容

需要安装`--with-http_sub_module`

`./configure --prefix=/home/nginx/ --with-http_sub_module --with-http_ssl_module`make

```text
server {
   listen 443 ssl;
   listen [::]:443 ssl;

   server_name wong.top;  #你的域名
   ssl_certificate       /usr/local/etc/v2ray/server.crt; 
   ssl_certificate_key   /usr/local/etc/v2ray/server.key;
   ssl_session_timeout 1d;
   ssl_session_cache shared:MozSSL:10m;
   ssl_session_tickets off;

   ssl_protocols         TLSv1.2 TLSv1.3;
   ssl_ciphers           ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
   ssl_prefer_server_ciphers off;
    
    location / {
        proxy_pass https://www.bing.com; #伪装网址
        proxy_ssl_server_name on;
        proxy_redirect off;
        sub_filter_once off;
        sub_filter "www.bing.com" $server_name;
        proxy_set_header Host "www.bing.com";
        proxy_set_header Referer $http_referer;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header User-Agent $http_user_agent;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
        proxy_set_header Accept-Encoding "";
        proxy_set_header Accept-Language "zh-CN";
    }
    
    location /ray {
       proxy_redirect off;
       proxy_pass http://127.0.0.1:10000; #v2ray的监听地址
       proxy_http_version 1.1;
       proxy_set_header Upgrade $http_upgrade;
       proxy_set_header Connection "upgrade";
       proxy_set_header Host $host;
       proxy_set_header X-Real-IP $remote_addr;
       proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
   }
}

server {
    listen 80;
    server_name v.buliang0.tk;    #你的域名
    rewrite ^(.*)$ https://${server_name}$1 permanent;
}
```



## xray安装使用

[下载](https://github.com/xtls/Xray-core/releasesxray)

```
wget  https://github.com/XTLS/Xray-core/releases/download/v1.8.23/Xray-linux-64.zip
```

解压

```
unzip Xray-linux-64.zip
```

配置文件config.json

```json
{
  "inbounds": [
    {
      "port": 10086, // 服务器监听端口
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "b831381d-6324-4d53-ad4f-8cda48b30811"  // 记得替换这个字段，使用 `xray uuid` 或 `uuidgen` 生成
          }
        ]
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom"
    }
  ]
}
```

使用`./xray uuid`生成id

运行

```
./xray run -c config.json
```



xray脚本安装

[教程](http://link.zhihu.com/?target=https%3A//xtls.github.io/document/)

[下载和安装脚本](http://link.zhihu.com/?target=https%3A//github.com/XTLS/Xray-install)

```bash
bash -c "$(curl -L github.com/XTLS/Xray-install/raw/main/install-release.sh)" @ install -u root
```

启动xray

```text
systemctl start xray
```

查看状态

```text
systemctl status xray
```

![img](https://pic1.zhimg.com/v2-7eb312a371e5a7fb00312ec7f6822ab5_720w.jpg?source=d16d100b)

重启

```text
systemctl restart xray
```

修改配置文件/usr/local/etc/xray

```json
vim config.json
```

配置vmess

```json
{
    "log": {
        "loglevel": "warning"
    },
    "routing": {
        "domainStrategy": "AsIs",
        "rules": [
            {
                "type": "field",
                "ip": [
                    "geoip:private"
                ],
                "outboundTag": "block"
            }
        ]
    },
    "inbounds": [
        {
            "listen": "0.0.0.0", 
            "port": 8387, 
            "protocol": "vless",
            "settings": {
                "clients": [
                    {
                        "id": "72bac1c4-02de-49b4-e498-fa8767638c23", 
                        "flow": "xtls-rprx-vision"
                    }
                ],
                "decryption": "none"
            },
            "streamSettings": {
                "network": "tcp",
                "security": "tls",
                "tlsSettings": {
                    "rejectUnknownSni": true,
                    "minVersion": "1.2",
                    "certificates": [ 
                        {
                            "ocspStapling": 3600,
                            "certificateFile": "自己的crt文件绝对路径", 
                            "keyFile": "自己的key文件绝对路径"
                        }
                    ]
                }
            },
            "sniffing": {
                "enabled": true,
                "destOverride": [
                    "http",
                    "tls",
                    "quic"
                ]
            }
        }
    ],
    "outbounds": [
        {
            "protocol": "freedom",
            "tag": "direct"
        },
        {
            "protocol": "blackhole",
            "tag": "block"
        }
    ]
}
```

***如果报错，请确认文件权限\*crt、key、config的权限**

### **客户端**

![img](https://picx.zhimg.com/v2-430255c9bbb1c0c71ffec93022ed7136_720w.jpg?source=d16d100b)

## x-ui面板安装

[网址](http://link.zhihu.com/?target=https%3A//github.com/vaxilu/x-ui)

命令

```text
bash <(curl -Ls https://raw.githubusercontent.com/vaxilu/x-ui/master/install.sh)
```

![img](https://picx.zhimg.com/v2-1fd08b9e184fff3dbc96ea60a36e4a1d_720w.jpg?source=d16d100b)

运行效果

在命令中设置用户、密码、端口

在浏览器输入vps的ip和设置的端口

![img](https://pic1.zhimg.com/v2-d6ef760476a682e5b842772ab9575d09_720w.jpg?source=d16d100b)



登录后的效果

![img](https://picx.zhimg.com/v2-9b50cf441981d3a2103a95f9642c3537_720w.jpg?source=d16d100b)

添加协议

![img](https://picx.zhimg.com/v2-18d86475205c40ffa8f327db8600512c_720w.jpg?source=d16d100b)

添加shadowsocks协议

获取订阅链接

![img](https://pic1.zhimg.com/v2-b27266c8a3d482a00f481731ca0701b7_720w.jpg?source=d16d100b)

![img](https://pica.zhimg.com/v2-eb885e2606d36328ac74e4bdf1fbf787_720w.jpg?source=d16d100b)

![img](https://picx.zhimg.com/v2-2c67f5259067e655bd4a1d6d7d9cfa1f_720w.jpg?source=d16d100b)

------

## Hysteria 2

[官网](http://link.zhihu.com/?target=https%3A//v2.hysteria.network/zh/)

### 服务器端配置

安装

```bash
bash <(curl -fsSL https://get.hy2.sh/)
```

移除

```text
bash <(curl -fsSL https://get.hy2.sh/) --remove
```

修改配置文件/etc/hysteria

```text
vim config.yaml
```

自动下发证书配置文件内容

```yaml
listen: :8382

acme:
  domains:
    - your.domain.net
  email: your@email.com

auth:
  type: password
  password: 6M3iuJhWwJN/E4xM2K0ijAUD

masquerade:
  type: proxy
  proxy:
    url: https://news.ycombinator.com/
    rewriteHost: true
```

说明：

```
listen`:`监听端口
acme`:`自动获取证书
masquerade`:`伪装
url`:`伪装的的网站
```

自制证书内容

```bash
listen: :443 
tls:
  cert: your_cert.crt #证书文件路径
  key: your_key.key  #密钥文件路径

auth:
  type: password
  password: Se7RAuFZ8Lzg #密码

masquerade: 
  type: proxy
  proxy:
    url: https://news.ycombinator.com/ #伪装成的网站
    rewriteHost: true
```

运行

```bash
systemctl start hysteria-server
```

查看状态

```text
systemctl status hysteria-server
```

运行成功显示如下

![img](https://pic1.zhimg.com/v2-8b3d433822f630fdd0476836a7c062a4_720w.jpg?source=d16d100b)

运行成功是显示server up and running

### 客户端配置

下载客户端软件

[v2rayN下载](http://link.zhihu.com/?target=https%3A//github.com/2dust/v2rayN/releases/tag/6.43)

![img](https://picx.zhimg.com/v2-9120c779f89766210345a4b1ac70e9b4_720w.jpg?source=d16d100b)

[hysteria客户下载](http://link.zhihu.com/?target=https%3A//github.com/apernet/hysteria/releases/tag/app%2Fv2.4.3)

![img](https://pic1.zhimg.com/v2-7e7c9e5b15679fe1f36f9fc1d454199b_720w.jpg?source=d16d100b)

将下载的hysteria替换到v2rayN下的bin/hysteria下。

![img](https://picx.zhimg.com/v2-574afa1389e001dee7b7cf1d2312dbc4_720w.jpg?source=d16d100b)



创建客户端配置文件

![img](https://picx.zhimg.com/v2-e320f9eb413a64d58e0c987a64964ee0_720w.jpg?source=d16d100b)

```adl
# 客户端配置内容
server: IP:443 
auth: Se7RAuFZ8Lzg
tls:
  sni: lflovemhj.top
  insecure: true #使用自签时需要改成true 
bandwidth: 
  up: 20 mbps
  down: 100 mbps
socks5:
  listen: 127.0.0.1:1080 
http:
  listen: 127.0.0.1:8080
```



v2ray设置

![img](https://picx.zhimg.com/v2-6134151ed6a63d9aaa9abfce7cc9bd6e_720w.jpg?source=d16d100b)

![img](https://pica.zhimg.com/v2-f8c9327ea0454558b6de0f995e9de463_720w.jpg?source=d16d100b)

## clash配置文件说明

yaml

```text
#HTTP 代理监听端口号
port: 7890

#Socks5 代理监听端口号
socks-port: 7891

#透明代理监听端口号，用于重定向流量到代理
redir-port: 7892

#混合代理监听端口号，可以同时支持 HTTP 和 Socks5 代理
mixed-port: 7893

#禁止局域网内设备使用代理
allow-lan: false

#使用规则模式，即根据规则配置来决定代理行为
mode: Rule

#日志级别，控制日志输出的详细程度
log-level: info

#禁用 IPv6 解析
ipv6: false

#设置外部控制器的地址
external-controller: 127.0.0.1:9090

#
clash-for-android:
  #不将系统 DNS 添加到 Clash 中
  append-system-dns: false

#用于配置自定义的主机名解析规则，允许你将特定的主机名解析为指定的 IP 地址。这个功能通常用于域名屏蔽、域名重定向等需求
hosts:
  mtalk.google.com: 108.177.125.188
dns:
  #启用自定义 DNS 解析
  enable: true

  #启用自定义 DNS 解析
  listen: 127.0.0.1:53

  #指定的默认 DNS 服务器列表
  default-nameserver:
    - 156.154.70.1
    - 1.0.0.1

  #指定的默认 DNS 服务器列表
  ipv6: false

  #启用增强模式，使用虚假的 IP 地址进行解析
  enhanced-mode: fake-ip

  #指定虚假 IP 地址的范围
  fake-ip-range: 198.10.0.1/16

  #这里是虚假 IP 过滤列表，用于过滤特定的域名，使它们不使用虚假 IP 解析。这些域名通常是一些你希望正常解析的域名，而不需要进行虚假 IP 伪装。
  fake-ip-filter:
    - "*.lan"
    - stun.*.*.*
    - stun.*.*
    - time.windows.com
    - time.nist.gov
    - time.apple.com
    - time.asia.apple.com
    - "*.ntp.org.cn"
    - "*.openwrt.pool.ntp.org"
    - time1.cloud.tencent.com
    - time.ustc.edu.cn
    - pool.ntp.org
    - ntp.ubuntu.com
    - ntp.aliyun.com
    - ntp1.aliyun.com
    - ntp2.aliyun.com
    - ntp3.aliyun.com
    - ntp4.aliyun.com
    - ntp5.aliyun.com
    - ntp6.aliyun.com
    - ntp7.aliyun.com
    - time1.aliyun.com
    - time2.aliyun.com
    - time3.aliyun.com
    - time4.aliyun.com
    - time5.aliyun.com
    - time6.aliyun.com
    - time7.aliyun.com
    - "*.time.edu.cn"
    - time1.apple.com
    - time2.apple.com
    - time3.apple.com
    - time4.apple.com
    - time5.apple.com
    - time6.apple.com
    - time7.apple.com
    - time1.google.com
    - time2.google.com
    - time3.google.com
    - time4.google.com
    - music.163.com
    - "*.music.163.com"
    - "*.126.net"
    - musicapi.taihe.com
    - music.taihe.com
    - songsearch.kugou.com
    - trackercdn.kugou.com
    - "*.kuwo.cn"
    - api-jooxtt.sanook.com
    - api.joox.com
    - joox.com
    - y.qq.com
    - "*.y.qq.com"
    - streamoc.music.tc.qq.com
    - mobileoc.music.tc.qq.com
    - isure.stream.qqmusic.qq.com
    - dl.stream.qqmusic.qq.com
    - aqqmusic.tc.qq.com
    - amobile.music.tc.qq.com
    - "*.xiami.com"
    - "*.music.migu.cn"
    - music.migu.cn
    - "*.msftconnecttest.com"
    - "*.msftncsi.com"
    - localhost.ptlogin2.qq.com
    - "*.*.*.srv.nintendo.net"
    - "*.*.stun.playstation.net"
    - xbox.*.*.microsoft.com
    - "*.ipv6.microsoft.com"
    - "*.*.xboxlive.com"

  #自定义的 DNS 服务器列表，以 HTTPS 方式进行 DNS 查询
  nameserver:
    - https://223.6.6.6/dns-query
    - https://rubyfish.cn/dns-query
    - https://dns.pub/dns-query

proxies:
  #代理节点的名称
- name: TW01
  #代理节点的类型
  type: ss
  #代理服务器的地址
  server: cn00.mynode.me
  #代理服务器监听的端口号
  port: 8801
  #加密方式
  cipher: rc4-md5
  #连接到代理服务器所需的密码
  password: passwd
  #启用混淆插件，用于伪装流量
  plugin: obfs
  #混淆插件的选项配置
  plugin-opts:
    #混淆模式，这里设置为 HTTP 模式
    mode: http
    #混淆所需的主机名
    host: 33160-GeX91N5S.download.microsoft.com
proxy-groups:
  - name: 代理
    type: select
    proxies:
       ["TW01"]

rules:
 - DOMAIN-SUFFIX,local,DIRECT
 - IP-CIDR,192.168.0.0/16,DIRECT,no-resolve
 - DOMAIN,sycm.mmstat.com,DIRECT
 - DOMAIN-KEYWORD,SMTP,DIRECT
 - IP-CIDR,23.246.0.0/18,Netflix,no-resolve
 - IP-CIDR6,2001:b28:f23f::/48,Telegram,no-resolve
 - DOMAIN,hls-amt.itunes.apple.com,代理
 - MATCH,规则外路由选择

  
```



各个代理的配置

```text
proxies:
# 支持的协议及加密算法示例请查阅 Clash 项目 README 以使用最新格式：https://github.com/Dreamacro/clash/blob/master/README.md

# Shadowsocks 支持的加密方式:
#   aes-128-gcm aes-192-gcm aes-256-gcm
#   aes-128-cfb aes-192-cfb aes-256-cfb
#   aes-128-ctr aes-192-ctr aes-256-ctr
#   rc4-md5 chacha20-ietf xchacha20
#   chacha20-ietf-poly1305 xchacha20-ietf-poly1305

# Shadowsocks
- name: "ss"
  type: ss
  server: server
  port: 443
  cipher: chacha20-ietf-poly1305
  password: "password"
  # udp: true

# Shadowsocks(simple-obfs)
- name: "ss-obfs"
  type: ss
  server: server
  port: 443
  cipher: chacha20-ietf-poly1305
  password: "password"
  plugin: obfs
  plugin-opts:
      mode: tls
      host: example.com

# Shadowsocks(v2ray-plugin)
- name: "ss-v2ray"
  type: ss
  server: server
  port: 443
  cipher: chacha20-ietf-poly1305
  password: "password"
  plugin: v2ray-plugin
  plugin-opts:
    mode: websocket # no QUIC now
    # tls: true # wss
    # skip-cert-verify: true
    # host: bing.com
    # path: "/"
    # mux: true
    # headers:
    #   custom: value

# VMess
- name: "v2ray"
  type: vmess
  server: server
  port: 443
  uuid: a3482e88-686a-4a58-8126-99c9df64b7bf
  alterId: 64
  cipher: auto
  # udp: true
  # tls: true
  # skip-cert-verify: true
  # tls-hostname: 填写伪装域名
  # network: ws
  # ws-path: /path
  # ws-headers: #这一行后面不要写东西
  #   Host: v2ray.com # 填写伪装域名

# Trojan
- name: "trojan"
  type: trojan
  server: server
  port: 443
  password: password
  # udp: true
  # sni: example.com # 填写伪装域名
  alpn:
     - h2
     - http/1.1
  # skip-cert-verify: true
```

------

## Socks5代理服务器

CenOS下安装

[ss5下载地址](http://link.zhihu.com/?target=https%3A//jaist.dl.sourceforge.net/project/ss5/ss5/)

安装依赖

```text
yum -y install gcc gcc-c++ automake make pam-devel openldap-devel cyrus-sasl-devel openssl-devel
```

下载文件

```text
wget https://jaist.dl.sourceforge.net/project/ss5/ss5/3.8.9-8/ss5-3.8.9-8.tar.gz
```

解压文件

```text
tar -zxvf ss5-3.8.9-8.tar.gz
```

编译安装

```text
./configure
make && make install
```

设置权限和注册服务

```text
chmod +x /etc/init.d/ss5
chkconfig --add ss5
```

设置开机自启

```text
chkconfig ss5 on
```

配置端口号和用户

```text
vim /etc/init.d/ss5
```

添加内容

```text
export SS5_SOCKS_PORT=5432
export SS5_SOCKS_USER=root
```

![img](https://picx.zhimg.com/v2-69761ee7d31a0f6358a0a28cdac79a31_720w.jpg?source=d16d100b)

上面的配置不行是可以试用下面的配置

```text
vim  /etc/sysconfig/ss5
```

内容

```text
SS5_OPTS=" -u root -b 0.0.0.0:10899"
```

![img](https://picx.zhimg.com/v2-6fdc4dc57aa8a22481915bc6ce0b318f_720w.jpg?source=d16d100b)

修改权限和认证方式

```text
/etc/opt/ss5/ss5.conf
```

修改内容，将下面的注释放开，有些位置改为u

```text
auth    0.0.0.0/0               -               u
permit u        0.0.0.0/0       -       0.0.0.0/0       -       -       -       -       -
```

设置用户和密码

```text
vim /etc/opt/ss5/ss5.passwd
```

内容，前面表示用户名，后面表示密码

```text
user1 12345678
user2 12345678
user3 12345678
```

启动ss5

```text
/etc/rc.d/init.d/ss5  start
```

------

## Shadowtls

[下载地址](http://link.zhihu.com/?target=https%3A//github.com/SagerNet/sing-box/releases)

```text
wget https://github.com/SagerNet/sing-box/releases/download/v1.10.0-alpha.21/sing-box_1.10.0-alpha.21_linux_amd64.rpm
```

安装

```text
yum install sing-box_1.10.0-alpha.21_linux_amd64.rpm 
```


[https://www.2280129.xyz/article/000041/shadowtls.html](http://link.zhihu.com/?target=https%3A//www.2280129.xyz/article/000041/shadowtls.html)

[接码平台](http://link.zhihu.com/?target=https%3A//sms-activate.org/)

[订阅转换](http://link.zhihu.com/?target=https%3A//sub.dler.io/)

[网站排名](http://link.zhihu.com/?target=https%3A//www.similarweb.com/top-websites/)

[虚拟银行卡](http://link.zhihu.com/?target=https%3A//gpt.fomepay.com/%23/)

[临时邮箱](http://link.zhihu.com/?target=https%3A//temp-mail.org/)
