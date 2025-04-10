# 三大功能
反向代理，负载均衡，动静分离

# nginx 常用命令
```bash
./nginx # start nginx
./nginx -s stop # stop nginx
./nginx -s quit # quit nginx safely
./nginx -s reload # reload config
ps aux | grep nginx
```

# nginx 反向代理配置
```nginx
server {
    listen 80;
    server_name api.example.com;

    location / {
        proxy_pass http://localhost:3000;  # 后端服务地址
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;  # 传递客户端真实IP
    }
}
```

# nginx 负载均衡
```nginx
# 定义后端服务器组
upstream backend {
    server 192.168.1.100:8080 weight=2;  # 权重越高，分配请求越多
    server 192.168.1.101:8080;
    server 192.168.1.102:8080 backup;    # 备用服务器（主服务器宕机时启用）
}

server {
    listen 80;
    server_name app.example.com;

    location / {
        proxy_pass http://backend;  # 指向后端服务器组
    }
}
```
