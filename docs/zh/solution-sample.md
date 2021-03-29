---
sidebarDepth: 3
---

# 代码范例

下面根据常用的场景列出一些代码的范例和技巧：

## 文件管理

### 管理目录

```
- name: Create knowage directory
  file:
    path: /data/wwwroot/knowage
    state: directory
    owner: knowage
    group: knowage


# 删除目录，下载解压，重命名
# if [ ! d "scratch-gui*") ] 这种写法当不存在需要更名的目录的时候会报错，弃用
- name: Remove extra dir
  shell: rm -rf /data/wwwroot/scratch

- name: Download Scratch
  unarchive:
    src: "{{scratch_download_url}}"
    dest: /data/wwwroot
    remote_src: yes

- name: Rename dir name
  shell: if [ $( ls | grep "scratch-gui") ]; then mv scratch-gui* scratch; fi
  args:
    chdir: /data/wwwroot
```

### 创建文件

```
- name: Create file
  shell: if [ ! $( ls | grep "scratch-gui") ]; then touch scratch-gui; fi
  args:
    chdir: /data/wwwroot
```

### 下载解压

```

- name: Unarchive a file that needs to be downloaded (added in 2.0)
  unarchive:
    src: https://example.com/example.zip
    dest: /usr/local/bin
    remote_src: yes
```

### 软链接

```
- name: Create a Apache Log symbolic link
  file:
    src: '{{item.src}}'
    dest: '{{item.dest}}'
    state: link
  with_items:
    - {src: /etc/apache2,dest: /etc/httpd}
    - {src: /usr/sbin/apache2,dest: /usr/sbin/apache}
    - {src: /usr/sbin/apache2,dest: /usr/sbin/httpd}
    - {src: /etc/apache2/sites-enabled,dest: /etc/httpd/conf.d}
    - {src: /etc/apache2,dest: /etc/httpd/conf}
    - {src: /etc/apache2/apache2.conf,dest: /etc/httpd/conf/httpd.conf}
    - {src: /etc/apache2/sites-available/000-default.conf,dest: /etc/apache2/sites-enabled/vhost.conf}
    - {src: /var/log/apache2,dest: /var/log/httpd}
    - {src: /var/log/apache2,dest: /data/logs/apache}
    - {src: /var/log/apache2,dest: /etc/apache2/logs}
    - {src: /etc/apache2/sites-enabled,dest: /data/config/apache/sites-enabled}
    - {src: /etc/apache2/mods-enabled,dest: /data/config/apache/mods-enabled}
    - {src: /etc/apache2/sites-enabled,dest: /etc/apache2/vhost}
```

### 替换

```
#匹配regexp，如果找到，用line替换；找不到，末尾追加line的内容。
- name: Change postgresql databases directory
  lineinfile:
      dest: /etc/postgresql/{{postgresql_version}}/main/postgresql.conf
      regexp: "data_directory = '/var/lib/postgresql/{{postgresql_version}}/main'"
      line: "data_directory = '/data/pgsql'"
      
 #匹配regexp，如果找到，用line替换；找不到，什么都不做。
- name: Change postgresql databases directory
  lineinfile:
      dest: /etc/postgresql/{{postgresql_version}}/main/postgresql.conf
      regexp: "data_directory = '/var/lib/postgresql/{{postgresql_version}}/main'"
      line: "data_directory = '/data/pgsql'"
      backrefs: yes

#匹配regexp，找到后删除这行；找不到，什么都不做。（这种情况下line的内容无意义）
- name: Change postgresql databases directory
  lineinfile:
      dest: /etc/postgresql/{{postgresql_version}}/main/postgresql.conf
      regexp: "data_directory = '/var/lib/postgresql/{{postgresql_version}}/main'"
      state: absent

#特殊字符的匹配，如"."，需要在前面加上\进行转义，否则无法进行正则匹配(#elasticsearch.hosts 本例为替换所有#elasticsearch.hosts开头的行)
- name: elasticsearch.hosts
  lineinfile:
    path: /etc/kibana/kibana.yml
    regexp: '^#elasticsearch\.hosts'
    line: 'elasticsearch.hosts: ["http://localhost:9200"]'
    state: present
    backrefs: yes

#循环替换，数据结构

mysql_configure_extras:
  - name: innodb_buffer_pool_size
    value: 2G
  - name: innodb_log_file_size
    value: 500M

- name: Create MySQL extra databases 
  mysql_db:
    login_user: root
    login_password: '{{mysql_root_password}}'
    name: "{{ item.name }}"
    encoding: "{{ item.encoding | default('utf8mb4',true) }}"
    state: "{{ item.state | default('present',true) }}"
  with_items: "{{ mysql_databases }}"
  when: (mysql_databases is defined) and (mysql_databases != none)
```

## 用户管理

创建用户建议使用user模块，否则重复安装的时候会因为已有用户而报错

```
#创建非登陆用户
- name: Create Mattermost System User
  user:
      name: mattermost 
      create_home: no 
      shell: /usr/sbin/nologin
      
#创建可登陆用户jetty，并在home下创建用户目录
- name: Create jetty User
  user:
    name: jetty
    shell: /usr/bin/bash
    create_home: yes

- name: Create User for Canvas -- adduser --disabled-password --gecos canvas canvas
  user: 
    name: canvas
    comment: canvas
    password_lock: yes
```

## 条件判断

```
#避免重复下载一个大的压缩文件包
- name: Ansible check file exists
  shell: if [ ! $(ls /data/wwwroot/knowage | grep Knowage-*.sh) ]; then echo "need_download";else echo "downloaded";fi
  register: result

- name: Download Knowage, it have 2G, need 10 minutes
  unarchive:
    src: "{{knowage_download_url}}"
    dest: "/data/wwwroot/knowage"
    remote_src: yes
    mode: 0750
  when: result.stdout == "need_download"


#变量testpath是路径信息"/testdir"，判断路径是否存
- debug:
    msg: "file exist"
  when: testpath is exists
  #when: testpath is not exists
 
#判断是否包含"."号 
- name: replace
  debug: msg="323.2323"
  when: teststr is match("[0-9]+\.[0-9]+")

#判断变量：定义，未定义，none
vars:
  testvar: "test"
  testvar1:
tasks:
- debug:
    msg: "Variable is defined"
  when: testvar is defined
- debug:
    msg: "Variable is undefined"
  when: testvar2 is undefined
- debug:
    msg: "The variable is defined, but there is no value"
  when: testvar1 is none
```

## 正则表达式

```
#change string "x.y" to "xy"
- set_fact:
    postgresql_version_regex: "{{ postgresql_version | regex_replace('^(?P<before>.+).(?P<after>\\d+)$', '\\g<before>\\g<after>')}}"
```

## 安装

### yum



### apt

#When the service name is added, changed or renamed(created soft link ) on Ubuntu 20.04,
#you must reload service before restart the service.
#egg. There is a service at following path(/lib/systemd/system/php-fpm.service)

```
#指定deb在线安装,效果== $ wget https://packages.graylog2.org/repo/packages/graylog-3.3-repository_latest.deb
#                     $ sudo dpkg -i graylog-3.3-repository_latest.deb
- name: Install Graylog
  apt:
    deb: "{{graylog_deb_url}}"
    state: present
    update_cache: yes


# Add specified repository into sources list.
# {{ansible_distribution_version}}=$(lsb_release -cs) 变量转换成 bionic,xenial,focal等。有待进一步优化
# {{ansible_machine}}=$(arch) 变量转成 x86_64

- apt_repository:
    repo: deb http://archive.canonical.com/ubuntu $(lsb_release -cs) partner $(arch)
    repo: deb http://archive.canonical.com/ubuntu {{ansible_distribution_version}} partner {{ansible_machine}}
    #http://mirror.centos.org/centos/6/os/x86_64/
    repo: http://mirror.centos.org/centos/$releasever/os/$basearch/
    state: present

- name: Create a PHP soft link
  file:
    src: '{{item.src}}'
    dest: '{{item.dest}}'
    state: link
  with_items:
    - {src: '/lib/systemd/system/php{{php_version}}-fpm.service',dest: /lib/systemd/system/php-fpm.service}

- name: Ubuntu20.4 must reload service
  shell: systemctl daemon-reload  
  
- name: Restart PHP-FPM
  service:
    name: php-fpm
    state: restarted
    enabled: yes
```

## 交互

处理交互式输入：

```
- block:
  - pause:
      prompt: "Give your secret code for Odoo ee download"
    register: prompt
    no_log: yes
    run_once: yes

  - name: Install a .deb package from the internet.
    apt:
      deb: "https://libs.websoft9.com/apps/Odoo/{{ prompt.user_input | b64encode }}/odoo_{{odoo_version}}_e_latest_all.deb"
      autoclean: yes
    when: odoo_distribution == "ee"
```
