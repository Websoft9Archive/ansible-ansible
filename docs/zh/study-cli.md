# 命令行

以下是 Ansible 命令行的完整列表。每个页面均包含对该实用程序的描述以及所支持参数的列表。

- [ansible](https://docs.ansible.com/ansible/latest/cli/ansible.html)：主命令
- [ansible-config](https://docs.ansible.com/ansible/latest/cli/ansible-config.html)：配置文件和配置项修改
- [ansible-console](https://docs.ansible.com/ansible/latest/cli/ansible-console.html)：交互式运行 ansible 命令
- [ansible-doc](https://docs.ansible.com/ansible/latest/cli/ansible-doc.html)：查询文档
- [ansible-galaxy](https://docs.ansible.com/ansible/latest/cli/ansible-galaxy.html)：galaxy 项目库发布与下载
- [ansible-inventory](https://docs.ansible.com/ansible/latest/cli/ansible-inventory.html)：主机清单管理
- [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)：运行 playbook 程序
- [ansible-pull](https://docs.ansible.com/ansible/latest/cli/ansible-pull.html)：拉取并运行 playbook 程序
- [ansible-vault](https://docs.ansible.com/ansible/latest/cli/ansible-vault.html)：加密处理

ansible-playbook 是最常见的命令，也是运行程序的主要入口。  

实际上，Ansible 也支持在一条命令中运行使用模块，实现我们的部署目标。  

官方称这种命令的运行方式为 [Ad-doc](https://docs.ansible.com/ansible/2.9/user_guide/intro_adhoc.html)

示例  

```
# 打印本机磁盘信息
ansible localhost -m command -a 'df -h'

# 获取 facts 信息
ansible localhost -m setup

# 连通性测试
ansible all -m ping

# 本机上安装 docker-composer
ansible localhost -m get_url -a "url=https://getcomposer.org/composer-stable.phar dest=/usr/bin/composer mode=0750"
```

命令解释：

* localhost/all：主机名/IP/分组
* -m：指定模块（默认是command，所以可以把-m command这个去掉）
* command/setup/ping：模块名称
* -a：模块参数
* 'df -h'：参数值