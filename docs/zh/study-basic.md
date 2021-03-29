---
sidebarDepth: 3
---

# 原理

本章尽量全面的介绍 Ansible 的原理，将常用的知识点汇聚在一起，以帮助用户在实践中能够充分准确的用好 Ansible。

## 概述

[Ansible](https://github.com/ansible/ansible) 诞生于 2012 年，目前是 RedHat 旗下的产品，是 Github 上受欢迎的自动化运维工具。

从事过运维相关工作的小伙伴，对 Shell 应该是“有爱有恨”的。一方面我们爱它的无所不能，另外一方面我们恨它的语法晦涩难懂，且难以模块化利用。  

Ansible 的作者兼创始人Michael DeHaan 曾经供职于Puppet Labs、RedHat、Michael，在配置管理和架构设计方面有丰富的经验。其在RedHat任职期间主要开发了Cobble，经历了各种系统简化、自动化基础架构操作的失败和痛苦，在尝试了Puppet、Chef、Cfengine、Capistrano、Fabric、Function、Plain SSH等各式工具后，决定自己打造一款能结合众多工具优点的自动化工具，Ansible由此诞生。

Ansible 为何如此火爆？首先是当前云计算的大规模应用所驱动，然后就是它的核心特点决定的：

* 无需代理也可以控制多台受控机并行管理
* 采用描述性的语言来使用，非常易读、易编写
* 兼容 Python 的语法
* 安装过程简单，学习曲线很平坦


一种解释排版型语言，易读性极强：  

```
- block:
  - name: Create credentials Folder
    file:
      path: /credentials
      state: directory

  - name: Write Databases Password
    template:
      src: password.txt.jinja2
      dest: /credentials/password.txt
      mode: 644
```

一句话总结：简单易用，功能强大，用途广泛。



## 应用领域

Ansible 究竟有什么用呢？

我们从运维工程师或开发人员日常工作中最常见的部署来说：为了部署一个应用，我们需要提前安装 Web 服务、应用程序服务、消息队列、缓存系统、数据库、负载均衡等基础软件，与此同时我们还需要通过手工配置，将各个组件连接起来，让它们发挥功效。甚至，站在软件维护的角度，还需要部署日志系统、监控系统、数据库分析系统、数据库审计等维护工具。如果使用手工来完成这些任务，从购买一台云服务器，再到 SSH 登录直至完成所有任务，需要数百个步骤，而且每一个步骤不能出错。  

如果有一个自动化程序能够完成上述任务，那一定会收到用户的热烈欢迎。幸运的，Ansible 就是这样的工具，它比 Shell 简单，它可以轻轻松松处理：

### 配置管理

配置管理即部署部署应用程序环境，包括对 Linux 和 Windows 上进行各种程序安装，系统操作，Web服务管理、应用服务管理、数据库配置等

### 管理云资源

Ansible 提供包括 AWS,Azure等数十个云资源的创建、操作。即无需用户了解每个云平台的 API，也可以轻松管理云资源。

### 监控告警

Ansible 支持对 Grafana、Nagios、Zabbix、Datalog 等系统监控软件进行直接操作。

### 消息发送

Ansible 提供了大量的 [Notification modules](https://docs.ansible.com/ansible/2.9/modules/list_of_notification_modules.html) 用于帮助应用程序发送消息，支持：邮件、Mattermost、RabbitMQ、syslogger等常见的应用。

### 硬件管理

Ansible 可以对网络设备、存储设备进行直接操作，所支持网络品牌包括 Cisco、Aruba、Check Point等多达几十个，支持的存储品牌包括：IBM、Netapp、EMC等

一句话总结：Ansible 可以完成 DevOps 全过程所需的自动化配置工作：  

![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/ansible/ansible-fordevops-websoft9.png)


## 架构

### 技术架构

**Ansible 技术架构**
![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/ansible/ansible-structure2-websoft9.png)

我们知道，让合适的程序以合适的方式在合适的主机上高效率的运行，是技术架构的出发点。  

Ansible 的技术架构同样遵循这个原理，下面我们从如下三个方面阐述 Ansible 的技术架构：

* **主机**：运行 Ansible 程序的服务器，分为**主控端和受控端**两种类型的主机，主机的在架构中的表现形式为 Host Inventory（主机清单）
* **程序**：官方内置的软件包被成为为模块和插件，用户自己编写的程序被称之为 Playbook （多个 Playbook 以某种形式组合在一起被成为 roles）
* **连接**：主控端和受控端之间的连接与控制，一般采用 SSH 连接，支持认证

为什么主机分为主控端（Control Node）受控端（Managed Node）？  

主要是 Ansible 的用途决定的，由于 Ansible 需要考虑同一个程序在同一个时间部署到多个主机上，故在设计上引入的主控端这种角色，用于以集中式的方式向多个受控主机发布配置任务。 

如果不考虑这种场景，Ansible 也支持在本机上运行程序，即主控模式并不是必须的。


**Ansible 工作原理**
![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/ansible/ansible-structure-websoft9.png)

用户登录到 Ansible 所在的服务器，便可以使用命令行运行 Ansible 程序。

这里需注意的是前面多次提到过的 Inventory 的概念，Ansible 程序在运行的时候，一定提前准备 Inventory 文件，如果缺少这个文件，Ansible 就只能在本机上运行。

下面就是一个在本机运行 ping 模块的程序，`localhost` 参数告之 Ansible 目标主机是本机

```
$ansible localhost -m ping

localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

如果没有准备好 Inventory，而选用所有主机 （`all` 参数），系统就会报错
```
$ansible all -m ping
43.128.22.14 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: Permission denied (publickey,gssapi-keyex,gssapi-with-mic,password).",
    "unreachable": true
}

```

**Ansible 代码执行过程**


### 生态架构

Ansible 是一个卓越的技术产品，也是一个成功的商业软件，它的成功与其商业生态布局有密切关系。  

生态架构的技术基础：

* 模块
* 插件
* Python 语法语法兼容性
* Roles

生态商业整合：

* Ansible Galaxy：开发者共享的代码库（role库）
  ![Ansible Galaxy](https://libs.websoft9.com/Websoft9/DocsPicture/zh/ansible/Ansible-Galaxy-Blog-Post.png)

* Ansible Collection：开发者共享的应用程序库（完整的 Ansible 应用程序）
  ![Ansible Galaxy](https://libs.websoft9.com/Websoft9/DocsPicture/zh/ansible/ansible-collection-websoft9.png)

* Ansible Tower：企业级可视化 Ansible 管理工具，支持 API。
  ![Ansible Tower](https://libs.websoft9.com/Websoft9/DocsPicture/en/awx/awxui-websoft9.png)

* Ansible Automation Platform：由 RedHat 托管的 Ansible 项目，包含 Ansible Tower 以及自动化资源、自动化分析等企业级功能。
  ![Ansible Automation Platform](https://libs.websoft9.com/Websoft9/DocsPicture/zh/ansible/redhat-automation-platform_content-collections.png)


## 安装

### 主控端

安装之前，需了解是否具备安装条件：

**操作系统要求**：Red Hat, Debian, CentOS, macOS, any of the BSDs等，不支持Windows
**Python要求**：Python 2 (version 2.7) or Python 3 (versions 3.5 and higher) installed

条件具备之后，只需要一条命令即可安装：

```
pip install ansible
或
yum install ansible
或
apt install ansible
```

### 受控端

* 采用 SSH（默认为SFTP）与 Control Node 通信，如果SSH不可用，通过修改 ansible.cfg 更改通信协议
* Python支持：Python 2 (version 2.6 or later) or Python 3 (version 3.5 or later).