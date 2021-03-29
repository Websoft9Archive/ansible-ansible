# 主机清单

Ansible 经常用于管理大量的服务器，如果企业有数千台服务器，那么一定需要对这些服务器进行分门别类。与此同时，服务器的用户账号信息等也各不相同。  

[Ansible Inventory](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html#intro-inventory) （主机清单） 就是为了灵活的管理主机信息的技术标准。  

## 格式

静态文件是相对于动态文件来说，Ansible 的静态主机文件是指提前准备好并有完整内容的主机清单

默认的文件是：/etc/ansible/hosts

下面先展示一个包含丰富信息的 hosts 文件示例：

```
[webservers]
86.21.14.177
web1.websoft9.com
we2.websoft9.com
192.165.2.50

[database]
86.21.14.172
host1.websoft9.com
host2.websoft9.com
192.165.2.51

[monitor]
86.21.14.173 ansible_user=root ansible_ssh_pass=123456 proxy=proxy.websoft9.com
86.21.14.174 ansible_user=root ansible_ssh_private_key_file=/root/mykey
host1.websoft9.com
www[01:50].example.com

[monitor:vars]
proxy=proxy.websoft9.com
```

从以上文件可得知 Inventory 的特点：

* 可分组（分组还可以嵌套）
* 可存放用户账号和密码
* 可以为主机和分组指定变量
* 同一个主机可以归属多个组
* 主机名称支持范表达式 www[01:50] 表示：www01,www02...


另外，Ansible 还支持多个 inventory 文件。只需在 ansible.cfg 文件中让 inventory 配置项指向一个目录即可。不过，多个 inventory 文件与单个并没有区别，毕竟可以单个文件由于支持分组，已经完全可以满足各种分类场景了。

## 参数

| ansible_ssh_host             | 远程主机                             |
| ---------------------------- | ------------------------------------ |
| ansible_ssh_port             | 指定原创主机ssh端口                  |
| ansible_ssh_root             | ssh连接远程主机的用户                |
| ansible_ssh_pass             | ssh连接远程主机的密码                |
| ansible_sudo_pass            | sudo密码                             |
| ansible_connection           | 指定连接类型：local、ssh、paramiko   |
| ansible_ssh_private_key_file | ssh连接使用的私钥                    |
| ansible_shell_type           | 指定连接端的shell类型，sh、csh、fish |
| ansible_python_interpreter   | 指定远程主机使用的python路径         |

## 变量

Ansible 支持向 inventory 文件中添加变量，变量适用范围可以是单个主机，也可以是分组。

## 清单插件

[参考](https://docs.ansible.com/ansible/latest/plugins/inventory.html#inventory-plugins)

## 常见问题

#### Ansible 是否支持动态主机清单？

支持。由于在实际生产场景中，如果清单采用手动维护这些列表将是一个非常繁琐的任务。  

Ansible 支持动态生产主机清单，即 ansible.cfg 指向一个生产主机清单的程序，再由程序产生符合格式的清单列表。

#### Ansible 有没有默认分组？

有。默认有包含文件所有主机的 all 组，同时还有没有归属的 ungrouped 组。

