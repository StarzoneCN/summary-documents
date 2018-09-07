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

