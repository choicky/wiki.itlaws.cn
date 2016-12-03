Ubuntu 的安装与配置，主要参考 DigitalOcean 的教程 [Initial Server Setup with Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04) 以及 [Additional Recommended Steps for New Ubuntu 14.04 Servers](https://www.digitalocean.com/community/tutorials/additional-recommended-steps-for-new-ubuntu-14-04-servers)

# 新建用户 choicky 并增加到 sudo 组

```
adduser choicky
usermod -aG sudo choicky
```

用新用户 ssh 登录。

# 修改 ssh 的端口 以及禁用 root 登录

    sudo nano /etc/ssh/sshd_config

把 `Port 22` 改为自己想要的端口例如`123`，把 `PermitRootLogin yes` 改为 `PermitRootLogin no`。然后reload配置文件

    sudo systemctl reload sshd


# 让防火墙ufw放行新ssh端口

```
sudo ufw allow 123 # 一定要写对啊
sudo ufw enable
```

# 创建 swapfile 文件作为虚拟内存

```
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
sudo sh -c 'echo "/swapfile none swap sw 0 0" >> /etc/fstab'
```
