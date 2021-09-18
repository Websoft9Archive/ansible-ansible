# Playbook

本章我们讲解 Ansible 最核心的组件 playbook。  


## 概述

**playbook 是什么？**

如果把 Ansible 比作开发语言，那么 playbook 就是一个程序文件。程序代码在程序文件中按顺序执行，最终完成所需处理的任务。

**为什么称之 playbook 而不是程序文件呢？**

playbook 翻译过来就是剧本的意思。如果你是文艺爱好者，可能阅读过电影/舞台剧的剧本（下图）

![电影剧本](https://libs.websoft9.com/Websoft9/DocsPicture/zh/ansible/film-playbook.png)

剧本是个导演组织演员、道具、拍摄等资源一种编排叙述性的文字说明。

Ansible 的程序代码与电影具备具有类似性，故被作者命名为 playbook

## 格式

playbook 有着规范化的格式，下面就是一个典型的可以被直接运行的 playbook 范例：

```
- hosts: localhost
  remote_user:root
  vars:
    https_port: 443
  tasks:
    - name: Create file1
      ansible.builtin.file:
        state: touch
        path: /tmp/file1

    - name: Create file2
      ansible.builtin.file:
        state: touch
        path: /tmp/file2

    - name: Create file3
      ansible.builtin.file:
        state: touch
        path: /tmp/file3
```

playbook 包含几个关键字，每个关键字的含义如下：

* hosts: 主机IP 或 主机组名 或 all
* remote_user: 以某个用户身份运行，通常设置为 root
* vars: 变量

![Ansible playbook 关系图](https://libs.websoft9.com/Websoft9/DocsPicture/zh/ansible/ansible-palybookvsplay-websoft9.png)  
**图：playbook, paly, task 三者关系**

## 模块

模块 Ansible 官方已经编写好了的软件包，是 Ansible 的功能核心。  

正是由于 Ansible 提供了大量的模块，才大大简化运维工作，可以让运维人员只需要少量的 Shell 知识便可以完成复杂的运维任务。  

有两种使用模块的方式：  

在 Playbook 中使用模块：  

```
- name: restart webserver
  service:
    name: httpd
    state: restarted

```

在 命令行中使用模块：

```
ansible localhost -m service -a "name=httpd state=started"
```

查看所有[模块](https://docs.ansible.com/ansible/2.9/modules/modules_by_category.html)

## 插件

插件是对模块功能的一种补充。


## 变量

## 循环

## 条件

Ansible 中使用 `when` 作为条件判断的关键词，条件判断注意事项：

* 变量名不需要双大括号“{{}}”
* 运算符兼容 jinja2 格式：==, !=, >, >=
* 支持逻辑运算符：and, or, not
* 支持变量的定义判断：defined, undefined, none

详情参考：[Ansible条件判断详解](https://www.ityoudao.cn/posts/ansible-conditionals/)

## 过滤

## 模板

## 查询

## 交互

- name: Create file1
  ansible.builtin.file:
    state: touch
    path: /tmp/file1