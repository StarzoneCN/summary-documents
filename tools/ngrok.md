# <div style="text-align:center;color:#FF9900">Ngrok</div>

## Ngrok + Nginx 实现端口转发

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
