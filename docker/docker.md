
# 安装docker
1.  系统更新
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl
```
2.  运行 Docker 官方一键安装脚本
```bash
curl -fsSL https://get.docker.com | sudo sh
```
3.  验证安装
```bash
sudo docker run hello-world
```

4.  配置国内镜像加速器
```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<EOF
{
    "registry-mirrors": [
        "https://docker.1ms.run",
        "https://docker.xuanyuan.me"
    ]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

5. 设置开机自启
```bash
systemctl enable docker.service
```

## 常用docker命令
```bash
systemctl start docker 
systemctl status docker
sudo systemctl restart docker

sudo docker ps

sudo docker stop {containerId}

sudo docker rm {containerId}

# check docker log
sudo docker logs -f mysql

# check status of docker
sudo systemctl status docker

docker start tomcat

docker start nginx

# update container for always restart 
docker update --restart=always tomcat
```

# docker配置mysql

```bash
docker run -d --name mysql-server -e MYSQL_ROOT_PASSWORD=123456 -e MYSQL_DATABASE=testdb -p 3306:3306 --restart unless-stopped -v /mysql/data:/var/lib/ mysql:8.0 

docker exec -it mysql-server mysql -u pi -praspberry
```
