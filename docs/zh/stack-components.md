---
sidebarDepth: 3
---

# 参数

Ansible 预装包包含 Ansible 运行所需一序列支撑软件（简称为“组件”），下面列出主要组件名称、安装路径、配置文件地址、端口、版本等重要的信息。

## 路径

Ansible 配置文件： */etc/ansible/ansible.cfg*  
Ansible 示例目录： */data/apps/ansible*  

> 可创建 ansible.cfg 到用户家目录或当前工作目录。优先级：当前工作目录 > 用户家目录 > etc 目录

## 端口号

在云服务器中，通过 **[安全组设置](https://support.websoft9.com/docs/faq/zh/tech-instance.html)** 来控制（开启或关闭）端口是否可以被外部访问。 

通过命令`netstat -tunlp` 看查看相关端口，下面列出可能要用到的端口：

| 名称 | 端口号 | 用途 |  必要性 |
| --- | --- | --- | --- |
| TCP | 22 | Linux系统端口 | 必须 |
| TCP | 80 | 网站应用端口 | 可选 |

## 版本号

组件版本号可以通过云市场商品页面查看。但部署到您的服务器之后，组件会自动进行更新导致版本号有一定的变化，故精准的版本号请通过在服务器上运行命令查看：

```shell
# Check all components version
sudo cat /data/logs/install_version.txt

# Linux Version
lsb_release -a

# Ansible  Version
ansible --version
```
