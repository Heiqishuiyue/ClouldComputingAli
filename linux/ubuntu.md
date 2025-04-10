# 更新openssl
```bash
sudo apt-get install --only-upgrade openssl 

# ubuntu ssh
sudo apt install openssh-server 
```

# 开放端口

```bash
# 安装UFW（如果未安装 
sudo apt update sudo apt install ufw

# 启用UFW
sudo ufw enable

# 开放指定端口 
sudo ufw allow 22 # 开放SSH端口 sudo ufw allow 80/tcp # 开放HTTP端口 
sudo ufw allow 443/tcp # 开放HTTPS端口

# 检查UFW状态
sudo ufw status
```

# ubuntu 升级
```bash
sudo apt update
sudo apt upgrade
sudo apt dist-upgrade

# 清理旧软件包
sudo apt autoremove --purge

# 修改升级配置 将 Prompt=lts（确保仅升级到长期支持版本）
sudo nano /etc/update-manager/release-upgrades

# 完成升级后重启并验证版本
cat /etc/os-release
```
