# 初始化安装

在云服务器上部署 Ansible 预装包之后，请参考下面的步骤快速入门。

## 准备

1. 在云控制台获取您的 **服务器公网IP地址** 
2. 在云控制台安全组中，检查 **Inbound（入）规则** 下的 **TCP:22** 端口是否开启

## Ansible 验证向导

1. 使用 SSH 连接到服务器，运行 `ansible -h` 命令，查看帮助信息
   ```
   usage: ansible [-h] [--version] [-v] [-b] [--become-method BECOME_METHOD]
                  [--become-user BECOME_USER] [-K] [-i INVENTORY] [--list-hosts]
                  [-l SUBSET] [-P POLL_INTERVAL] [-B SECONDS] [-o] [-t TREE] [-k]
                  [--private-key PRIVATE_KEY_FILE] [-u REMOTE_USER]
                  [-c CONNECTION] [-T TIMEOUT]
                  [--ssh-common-args SSH_COMMON_ARGS]
                  [--sftp-extra-args SFTP_EXTRA_ARGS]
                  [--scp-extra-args SCP_EXTRA_ARGS]
                  [--ssh-extra-args SSH_EXTRA_ARGS] [-C] [--syntax-check] [-D]
                  [-e EXTRA_VARS] [--vault-id VAULT_IDS]
                  [--ask-vault-pass | --vault-password-file VAULT_PASSWORD_FILES]
                  [-f FORKS] [-M MODULE_PATH] [--playbook-dir BASEDIR]
                  [-a MODULE_ARGS] [-m MODULE_NAME]
                  pattern

   Define and run a single task 'playbook' against a set of hosts

   positional arguments:
   pattern               host pattern

   ```

2. 运行 `ansible localhost -m setup` 命令，查看本机获取的系统信息
   ```
   localhost | SUCCESS => {
    "ansible_facts": {
        "ansible_all_ipv4_addresses": [
            "172.23.0.1",
            "172.27.0.1",
            "172.22.0.1",
            "172.18.0.1",
            "172.28.0.1",
            "172.17.0.1",
            "172.19.0.11"
        ],
        "ansible_all_ipv6_addresses": [
            "fe80::42:9fff:fe11:a0f3",
            "fe80::42:b5ff:feb0:13b3",
            "fe80::42:1fff:fe68:d58b",
            "fe80::8874:21ff:fe2a:cada",
            "fe80::42:2cff:fefd:f576",
            "fe80::688a:7ff:fea2:9a76",
            "fe80::42:bdff:fe4e:d3ab",
            "fe80::42:edff:fef4:5bd3",
            "fe80::5054:ff:fe4a:8b63",
            "fe80::14d0:b6ff:fecb:a383",
            "fe80::2cae:e1ff:fee1:aa10"
        ],
        "ansible_apparmor": {
            "status": "disabled"
        },
        "ansible_architecture": "x86_64",
        "ansible_bios_date": "04/01/2014",
        "ansible_bios_version": "seabios-1.9.1-qemu-project.org",
        ...
   ```
4. 运行服务器上的 playbook 范例
   ```
   cd /data/apps/ansible
   ansible-playbook -i hosts test.yml -c local
   ```

4. 以上命令顺利运行，即表明 Ansible 验证通过

## 常见问题

#### Ansible 是否有可视化工具？

有，[Ansible Tower](https://support.websoft9.com/docs/awx) 即可视化工具

#### Ansible 可以管理远程服务器吗？

可以管理远程主机，也可以管理本机

#### Ansible 是否有系统服务？

没有，Ansible 是一套开发语言工具，主要提供 CLI 供用户使用

