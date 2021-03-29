# FAQ

#### 如何成为Ansible程序高手？

Shell命令是根本，夯实基础稳步走；  
晦涩理论看一遍，动手实验是正道。  
经典教材床头放，官方文档经常看；  
闲时看书有收获，勤动笔来总结多。  
三人成行有我师，学会提问收获多；  
疑难问题要会诊，切莫独钻死胡同。  
稳定简约见功底，数据结构来撑腰；  
软件没有终结日，长久迭代价值高。  

学会驾驭Ansible，用通用的软件方法论去理解Ansible，千万不要被Ansible的技术术语所牵制。

#### 如何成为 Ansible 应用高手？

多想场景，多看官方[文档](https://docs.ansible.com/ansible/latest)，多看官方[博客](https://www.ansible.com/blog?hsLang=en-us)，多想想生态

#### 系统变量 ansible_os_family 支持哪些值？

```
OS_FAMILY = dict(
            RedHat = 'RedHat',
            Fedora = 'RedHat', 
            CentOS = 'RedHat', 
            Scientific = 'RedHat',
            SLC = 'RedHat', 
            Ascendos = 'RedHat', 
            CloudLinux = 'RedHat', 
            PSBM = 'RedHat',
            OracleLinux = 'RedHat', 
            OVS = 'RedHat', 
            OEL = 'RedHat', 
            Amazon = 'RedHat',
            XenServer = 'RedHat', 
            Ubuntu = 'Debian', 
            Debian = 'Debian', 
            SLES = 'Suse',
            SLED = 'Suse', 
            OpenSuSE = 'Suse', 
            SuSE = 'Suse', 
            Gentoo = 'Gentoo',
            Archlinux = 'Archlinux', 
            Mandriva = 'Mandrake', 
            Mandrake = 'Mandrake',
            Solaris = 'Solaris', 
            Nexenta = 'Solaris',  
            OmniOS = 'Solaris', 
            OpenIndiana = 'Solaris',
            SmartOS = 'Solaris', 
            AIX = 'AIX', 
            Alpine = 'Alpine', 
            MacOSX = 'Darwin',
            FreeBSD = 'FreeBSD', 
            HPUX = 'HP-UX'
        )
```

#### Ansible受控端是否必须提前安装Python？

不是。Ansible的[raw](https://docs.ansible.com/ansible/latest/modules/raw_module.html#raw-module)模块和[script](https://docs.ansible.com/ansible/latest/modules/script_module.html#script-module)模块不依赖于客户端安装的Python来运行。从技术上讲，您可以使用Ansible使用raw模块安装兼容版本的Python ，然后使用该模块使用其他所有模块。例如，如果需要将Python 2引导到基于RHEL的系统上，则可以按以下方式安装它：  

```
ansible myhost --become -m raw -a "yum install -y python2"
```
#### 主控端如何安装Ansible最方便？

推荐采用 pip install ansible

#### Ansible 的应用模块好用吗？例如：Docker, MySQL等

建议弃用，直接使用命令更为稳定可靠，这样可以避免这边模块的版本兼容性问题

#### Ansible中的变量优先级有哪些？

有高到低：ansible命令带入的变量 > cfg配置文件的变量 > 主项目的var变量 > role中的var变量 > role default 变量

#### Ansible有全局变量的概念吗？

没有，但我们可以将：ansible命令带入的变量 | cfg配置文件的变量 | 主项目的var变量 视为全局变量。但特别需要注意的是：Ansible项目中即使有同名变量，它们不会共享一个内存区域，而是各自独占内存（区别于Java等语言变量指针的概念）。

#### Ansible 如何实现模块化？

Ansible Galaxy 就是模块化唯一的方案

#### Ansible 中经常会出现 python-urllib3 之类的报错，如何处理？

python-urllib3 报错大部分情况下，通过 yum install python-urllib3 解决，而不是 pip install

#### Ansible 中的条件判断有哪些可能性？

True, not False, !=none, !="", 

#### Ansbile 中Python Pip apt/yum 总结

1. 客户端和服务端 python版本可以不一致
2. 升级最新pip版本会导致 pip 命令无法使用 官方解释使用 python3 -m pip install xxx
3. apt lock 问题可以在脚本中预处理

#### Ansbile 客户端和服务端 Python版本是否可以不一致？

可以

#### pip和pip3共存吗？

可以共存。但建议通过：python3 -m pip install xxx 这样的方式使用Python3下的pip，启用pip3这种表达方式


#### 为什么Ansible中apt升级容易导致 lock？

AWS上非常容易出错，建议在脚本中预处理

#### Ansible 之PIP模块是否可以制定Python版本？

可以，参考如下

```
# Install (Bottle) for Python 3.3 specifically,using the 'pip3.3' executable.
- pip:
    name: bottle
    executable: pip3.3
```

#### dnf 模块现在可以用吗？

现在不建议使用dnf模块

#### 一个Ansible项目中，主入口文件中 *vars_files* 与 *vars* 哪个变量优先级高？

vars_files的优先级更高。需要注意的是Ansible的变量是无法覆盖的，即同名变量在内存中都有单独的存储区域，而Ansible只是通过优先级的方式使用。

##### 如何从一个裸机快速运行Ansible项目？

下面以 CentOS 为例列出运行 Ansible 项目的步骤：

```
yum install ansible git -y
git clone https://github.com/Websoft9/ansible-activemq.git
cd ansible-activemq
ansible-galaxy install -r requirements.yml -f
ansible-playbook activemq.yml -c local
```

#### 条件判断中变量 none,null.undefined 有什么区别？

* undefined 代表变量未定义，即变量不存在
* null 即空字符，varA="" 就代表定义个 null 变量 varA
* none 空值是Python里一个特殊的值，varA=None 就代表定义了一个 None 的变量 varA。None不能理解为0，因为0是有意义的，而None是一个特殊的空值。

#### jinja2 模板中如何判断一个变量 varA 是否未定义，为空或 false:

使用 {% if varA %} 即可，等同于{% if varA is defined and varA is not none %}

#### 本项目中 Ansible 采用何种安装方式？

采用 rpm/deb 包的安装方式

#### 如何以调试模式启动Ansible服务？

```
systemctl stop ansible-server
ansible-server console
```

#### 是否可以通过命令行修改Ansible后台密码？

可以，`rabbitmqctl change_password  admin newpassword`

#### 如果没有域名是否可以部署 Ansible？

可以，访问`http://服务器公网IP` 即可

#### 数据库 root 用户对应的密码是多少？

密码存放在服务器相关文件中：`/credentials/password.txt`

#### 是否有可视化的数据库管理工具？

有，内置phpMyAdmin，访问地址：*http://服务器公网IP:9090*

#### 如何禁止外界访问phpMyAdmin？

连接服务器，编辑 [phpMyAdmin 配置文件](/zh/stack-components.md#phpmyadmin)，将其中的 `Require all granted` 更改为 `Require ip 192.160.1.0`，然后重启 Apache 服务

#### 是否可以修改Ansible的源码路径？

不可以

#### 如何修改上传的文件权限?

```shell
# 拥有者
chown -R apache.apache /data/wwwroot/
# 读写执行权限
find /data/wwwroot/ -type d -exec chmod 750 {} \;
find /data/wwwroot/ -type f -exec chmod 640 {} \;
```

#### 部署和安装有什么区别？

部署是将一序列软件按照不同顺序，先后安装并配置到服务器的过程，是一个复杂的系统工程。  
安装是将单一的软件拷贝到服务器之后，启动安装向导完成初始化配置的过程。  
安装相对于部署来说更简单一些。 

#### 云平台是什么意思？

云平台指提供云计算服务的平台厂家，例如：Azure,AWS,阿里云,华为云,腾讯云等

#### 实例，云服务器，虚拟机，ECS，EC2，CVM，VM有什么区别？

没有区别，只是不同厂家所采用的专业术语，实际上都是云服务器
