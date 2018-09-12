# <div style="text-align:center;color:#FF9900">Ngrok</div>

## 1. Ngrok + Nginx 实现端口转发

### nginx配置

```nginx
server {
        listen 80;
        server_name .starzone.site;
        access_log /home/hongxing/data/ngrok-logs/access_log;
        error_log /home/hongxing/data/ngrok-logs/error.log;
        location / {
          proxy_pass http://starzone.site:8088/;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header Host $http_host:8088;
          proxy_set_header X-Forwarded-Proto $scheme;
          proxy_set_header X-Nginx-Proxy true;
          proxy_buffering off;
          proxy_redirect  off;
          proxy_set_header X-Real-IP $remote_addr;
        }
    }
```

## 2. firewalld设置

```shell
# 启动防火墙
systemctl start firewalld
systemctl enable firewalld
    
# 放行ngrok需要的4443端口
firewall-cmd --zone=public --add-port=4443/tcp --permanent
    
# 放行80及443端口
firewall-cmd --add-service=http --permanent
firewall-cmd --add-service=https --permanent
    
# 放行自定义端口，例如：TCP 7070及7071端口
firewall-cmd --zone=public --add-port=7070/tcp --permanent
firewall-cmd --zone=public --add-port=7071/tcp --permanent
firewall-cmd --zone=public --add-port=7443/tcp --permanent
    
# 重新加载防火墙规则
firewall-cmd --reload  
```

## 3. 启动命令

服务端：

```shell
ngrokd -tlsKey="device.key" -tlsCrt="device.crt" -domain="starzone.site" -httpAddr=":8088" -httpsAddr=":444"
```

客户端：

```shell
ngrok -config=ngrok.cfg -subdomain=test 8081
```

### 3.1 配置文件

**ngrok-conf.yml**/**ngrok.conf** - 命名随意

```yml
server_addr: “starzone.site:4443"
trust_host_root_certs: false
# tunnels:
#   ssh:
#     remote_port: 6666
#     proto:
#       tcp: 22
```



## 4. 搭建过程

```shell
export NGROK_DOMAIN="starzone.site"
# 生成证书
openssl genrsa -out rootCA.key 2048
openssl req -x509 -new -nodes -key rootCA.key -subj "/CN=$NGROK_DOMAIN" -days 5000 -out rootCA.pem
openssl genrsa -out device.key 2048
openssl req -new -key device.key -subj "/CN=$NGROK_DOMAIN" -out device.csr
openssl x509 -req -in device.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out device.crt -days 5000

# 拷贝证书
\cp -f rootCA.pem assets/client/tls/ngrokroot.crt
\cp -f device.crt assets/server/tls/snakeoil.crt
\cp -f device.key assets/server/tls/snakeoil.key

# 编译server
make clean
make release-server

# 编译client
make release-client   # linux
GOOS=windows GOARCH=amd64 make release-client  # windows 64bit
```

上面的代码中`cp`命令为何使用右斜线`\`，请查看[博客](http://blog.sina.com.cn/s/blog_4963a7990100j467.html)