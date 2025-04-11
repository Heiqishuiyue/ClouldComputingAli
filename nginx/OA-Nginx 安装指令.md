# OA-Nginx 安装指令

```bash
sudo apt update
sudo apt upgrade -y

sudo snap install docker

sudo usermod -aG docker $USER
sudo snap restart docker

sudo docker pull docker.m.daocloud.io/portainer/portainer-ce
sudo docker volume create portainer_data
sudo docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data docker.m.daocloud.io/portainer/portainer-ce

lsblk
sudo mkfs.ext4 /dev/vdb
sudo mkdir /home/uadmin/nginx/logs
sudo mount /dev/vdb /home/uadmin/nginx/logs
sudo nano /etc/fstab

sudo docker pull docker.m.daocloud.io/nginx
sudo docker run -d -p 80:80 -p 443:443 --name oa_nginx --restart=always -v /home/uadmin/nginx/conf:/etc/nginx -v /home/uadmin/nginx/www:/usr/share/nginx/html -v /home/uadmin/nginx/logs:/var/log/nginx docker.m.daocloud.io/nginx
```

