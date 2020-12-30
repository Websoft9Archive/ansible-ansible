# 服务启停

使用由Websoft9提供的 Ansible 部署方案，可能需要用到的服务如下：

### Ansible

```shell
sudo systemctl start ansible-server
sudo systemctl stop ansible-server
sudo systemctl restart ansible-server
sudo systemctl status ansible-server

# you can use this debug mode if Ansible service can't run
ansible-server console
```

### MySQL

```shell
sudo systemctl start mysql
sudo systemctl stop mysql
sudo systemctl restart mysql
sudo systemctl status mysql
```

### Redis

```shell
systemctl start redis
systemctl stop redis
systemctl restart redis
systemctl status redis
```
