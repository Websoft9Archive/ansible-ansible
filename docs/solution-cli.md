# CLI

Most users are familiar with ansible and ansible-playbook, but those are not the only utilities Ansible provides. Below is a complete list of Ansible utilities. Each page contains a description of the utility and [a listing of supported parameters](https://docs.ansible.com/ansible/latest/user_guide/command_line_tools.html).

- [ansible](https://docs.ansible.com/ansible/latest/cli/ansible.html)
- [ansible-config](https://docs.ansible.com/ansible/latest/cli/ansible-config.html)
- [ansible-console](https://docs.ansible.com/ansible/latest/cli/ansible-console.html)
- [ansible-doc](https://docs.ansible.com/ansible/latest/cli/ansible-doc.html)
- [ansible-galaxy](https://docs.ansible.com/ansible/latest/cli/ansible-galaxy.html)
- [ansible-inventory](https://docs.ansible.com/ansible/latest/cli/ansible-inventory.html)
- [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)
- [ansible-pull](https://docs.ansible.com/ansible/latest/cli/ansible-pull.html)
- [ansible-vault](https://docs.ansible.com/ansible/latest/cli/ansible-vault.html)

Although `ansible-playbook` is used for running playbook, but you can also use [Ad-doc](https://docs.ansible.com/ansible/2.9/user_guide/intro_adhoc.html) to run one module every time.  

```
# Print disk usage information
ansible localhost -m command -a 'df -h'

# Get facts
ansible localhost -m setup

# Test connection
ansible all -m ping

# Install docker-composer
ansible localhost -m get_url -a "url=https://getcomposer.org/composer-stable.phar dest=/usr/bin/composer mode=0750"
```

Usage Instructions:  

* localhost/all: inventory
* -m: module name, i.g command/setup/ping , default is `command` module
* -a：parameter, e.g 'df -h'