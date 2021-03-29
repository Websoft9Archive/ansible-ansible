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

## 过滤器
```
#json_query: 用来过滤数组,json的元素属性
users:
- name: tom
  age: 18
- name: jerry
  age: 20

- debug:
      msg: "{{ testvar | json_query('users[*].name') }}"

tasks:
  - debug:
      #将字符串转换成纯大写
      msg: "{{ testvar | upper }}"
  - debug:
      #将字符串转换成纯小写
      msg: "{{ testvar | lower }}"
  - debug:
      #将字符串变成首字母大写,之后所有字母纯小写
      msg: "{{ testvar | capitalize }}"
  - debug:
      #将字符串反转
      msg: "{{ testvar | reverse }}"
  - debug:
      #返回字符串的第一个字符
      msg: "{{ testvar | first }}"
  - debug:
      #返回字符串的最后一个字符
      msg: "{{ testvar | last }}"
  - debug:
      #将字符串开头和结尾的空格去除
      msg: "{{ testvar1 | trim }}"
  - debug:
      #将字符串放在中间，并且设置字符串的长度为30，字符串两边用空格补齐30位长
      msg: "{{ testvar1 | center(width=30) }}"
  - debug:
      #返回字符串长度,length与count等效,可以写为count
      msg: "{{ testvar2 | length }}"
  - debug:
      #将字符串转换成列表，每个字符作为一个元素
      msg: "{{ testvar3 | list }}"
  - debug:
      #将字符串转换成列表，每个字符作为一个元素，并且随机打乱顺序
      msg: "{{ testvar3 | shuffle }}"
  - debug:
      #将字符串转换成列表，每个字符作为一个元素，并且随机打乱顺序
      #在随机打乱顺序时，将ansible_date_time.epoch的值设置为随机种子
      #也可以使用其他值作为随机种子，ansible_date_time.epoch是facts信息
      msg: "{{ testvar3 | shuffle(seed=(ansible_date_time.epoch)) }}"

tasks:
  #在调用shell模块时，如果引用某些变量时需要添加引号，则可以使用quote过滤器代替引号
  - shell: "echo {{teststr | quote}} > /testdir/testfile"
  #上例中shell模块的写法与如下写法完全等效
  #shell: "echo '{{teststr}}' > /testdir/testfile"
  #上例中，如果不对{{teststr}}添加引号，则会报错，因为teststr变量中包含"\n"转义符

  #ternary过滤器可以实现三元运算的效果 示例如下
  #如下示例表示如果name变量的值是John，那么对应的值则为Mr,否则则为Ms
  #简便的实现类似if else对变量赋值的效果
  - debug: 
      msg: "{{ (name == 'John') | ternary('Mr','Ms') }}"
  #而这和"{{ 'Mr' if name == 'John' else 'Ms' }}"是等价的

  #basename过滤器可以获取到一个路径字符串中的文件名
  - debug:
      msg: "{{teststr | basename}}"

  #获取到一个windows路径字符串中的文件名,2.0版本以后的ansible可用
  - debug:
      msg: "{{teststr | win_basename}}"

  #dirname过滤器可以获取到一个路径字符串中的路径名
  - debug:
      msg: "{{teststr | dirname}}"

  #realpath过滤器可以获取软链接文件所指向的真正文件
  - debug:
      msg: "{{ path | realpath }}"

  #relpath过滤器可以获取到path对于“指定路径”来说的“相对路径”
  - debug:
      msg: "{{ path | relpath('/testdir/testdir') }}"
    vars:
      path: "/testdir/ansible"

  #splitext过滤器可以将带有文件名后缀的路径从“.后缀”部分分开
  - debug:
      msg: "{{ path | splitext }}"

  #可以配置之前总结的过滤器，获取到文件后缀
  #msg: "{{ path | splitext | last}}"
  #可以配置之前总结的过滤器，获取到文件前缀名
  #msg: "{{ path | splitext | first | basename}}"

  #to_uuid过滤器能够为对应的字符串生成uuid
  - debug:
      msg: "{{ teststr | to_uuid }}"

  #bool过滤器可以根据字符串的内容返回bool值true或者false
  #字符串的内容为yes、1、True、true则返回布尔值true，字符串内容为其他内容则返回false
  - debug:
      msg: "{{ teststr | bool }}"

  #当和用户交互时，有可能需要用户从两个选项中选择一个，比如是否继续，
  #这时，将用户输入的字符串通过bool过滤器处理后得出布尔值，从而进行判断，比如如下用法
  #- debug:
  #    msg: "output when bool is true"
  #  when: some_string_user_input | bool

  #map过滤器可以从列表中获取到每个元素所共有的某个属性的值，并将这些值组成一个列表
  #当列表中嵌套了列表，不能越级获取属性的值，也就是说只能获取直接子元素的共有属性值。
  - vars:
      users:
      - name: tom
        age: 18
        hobby:
        - Skateboard
        - VideoGame
      - name: jerry
        age: 20
        hobby:
        - Music
    debug:
      msg: "{{ users | map(attribute='name') | list }}"
  #也可以组成一个字符串，用指定的字符隔开，比如分号
  #msg: "{{ users | map(attribute='name') | join(';') }}"

  #使用base64编码方式对字符串进行编码
  - debug:
      msg: "{{ 'hello' | b64encode }}"
  #使用base64编码方式对字符串进行解码
  - debug:
      msg: "{{ 'aGVsbG8=' | b64decode }}"

  #使用sha1算法对字符串进行哈希
  - debug:
      msg: "{{ '123456' | hash('sha1') }}"
  #使用md5算法对字符串进行哈希
  - debug:
      msg: "{{ '123456' | hash('md5') }}"
  #获取到字符串的校验和,与md5哈希值一致
  - debug:
      msg: "{{ '123456' | checksum }}"
  #使用blowfish算法对字符串进行哈希，注:部分系统支持
  - debug:
      msg: "{{ '123456' | hash('blowfish') }}"
  #使用sha256算法对字符串进行哈希,哈希过程中会生成随机"盐",以便无法直接对比出原值
  - debug:
      msg: "{{ '123456' | password_hash('sha256') }}"
  #使用sha256算法对字符串进行哈希,并使用指定的字符串作为"盐"
  - debug:
      msg: "{{ '123456' | password_hash('sha256','mysalt') }}"
  #使用sha512算法对字符串进行哈希,哈希过程中会生成随机"盐",以便无法直接对比出原值
  - debug:
      msg: "{{ '123123' | password_hash('sha512') }}"
  #使用sha512算法对字符串进行哈希,并使用指定的字符串作为"盐"
  - debug:
      msg: "{{ '123123' | password_hash('sha512','ebzL.U5cjaHe55KK') }}"
  #如下方法可以幂等的为每个主机的密码生成对应哈希串
  #有了之前总结的过滤器用法作为基础，你一定已经看懂了
  - debug:
      msg: "{{ '123123' | password_hash('sha512', 65534|random(seed=inventory_hostname)|string) }}"
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
