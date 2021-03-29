# Role

Ansible role 是用于规范化管理 playbook 程序文件以及附带的其他文件的一种软件包组织机制。  

## 结构

运行 `ansible-galaxy init myrole` 可以创建名称为 myrole 的 role。

使用 `tree` 命令查看包的结构：  

```
$ tree myrole
myrole
|-- defaults
|   `-- main.yml
|-- files
|-- handlers
|   `-- main.yml
|-- meta
|   `-- main.yml
|-- README.md
|-- tasks
|   `-- main.yml
|-- templates
|-- tests
|   |-- inventory
|   `-- test.yml
`-- vars
    `-- main.yml

8 directories, 8 files

```

* tasks/main.yml 存放项目的主文件
* templates 存放 jinjia2 模板文件
* files 存放程序所需的文件
* defaults/vars 存放变量文件，vars 下的变量优先级更高
* tests 存放本 role 入口 playbook
* handlers 存放 task 的 handler
* meta 存放 role 的元数据，包括系统兼容性、role 依赖等

## 运行

以上创建的 role 模板也可以使用 ansible-playbook 运行。  

```
ansible-playbook myrole/tests/test.yml
```

> 这种运行方式是很多初学者可能会忽视的

## Ansible Galaxy

[Ansible Galaxy](https://galaxy.ansible.com/) 是官方提供的帮助用户分享 role 的网站平台。

同时官方也提供了一套 cli 命令：  

```
$ ansible-galaxy role -h
usage: ansible-galaxy role [-h] ROLE_ACTION ...

positional arguments:
  ROLE_ACTION
    init       Initialize new role with the base structure of a role.
    remove     Delete roles from roles_path.
    delete     Removes the role from Galaxy. It does not remove or alter the
               actual GitHub repository.
    list       Show the name and version of each role installed in the
               roles_path.
    search     Search the Galaxy database by tags, platforms, author and
               multiple keywords.
    import     Import a role
    setup      Manage the integration between Galaxy and the given source.
    info       View more details about a specific role.
    install    Install role(s) from file(s), URL(s) or Ansible Galaxy

optional arguments:
  -h, --help   show this help message and exit
```