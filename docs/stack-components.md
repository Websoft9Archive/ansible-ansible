# Parameters

The Ansible deployment package contains a sequence of software (referred to as "components") required for Ansible to run. Below list the important information, the component name, installation directory path, configuration file path, port, version, etc.

## Path

### Ansible

Ansible installation directory:  */data/ansible*  
Ansible logs directory:  */data/logs/ansible*  

### Python

Python source code directory： */usr/lib/python*  
Python logs directory: */data/logs/python*  

### Docker

Docker root directory: */var/lib/docker*  
Docker image directory: */var/lib/docker/image*   
Docker daemon.json: please create it when you need and save to to the directory */etc/docker*   

## Ports

Open or close ports by **[Security Group Setting](https://support.websoft9.com/docs/faq/tech-instance.html)** of your Cloud Server to decide whether the port can be accessed from Internet.  

You can run the cmd `netstat -tunlp` to check all related ports.  

The following are the ports you may use:

| Name | Number | Use |  Necessity |
| --- | --- | --- | --- |
| TCP | 22 | Connect to Server by SSH | Required |
| TCP | 80 | Use for Web APPS test | Optional |


## Version

You can see the version on product pages at Marketplace. However, after being deployed to your server, the components will be updated automatically, resulting in a certain change in the version number. Therefore, run the command on the server to view the exact version number. 

```shell
# Check all components version
sudo cat /data/logs/install_version.txt

# Linux Version
lsb_release -a

# Ansible  Version
ansible --version

# Docker version
docker -v

# Python version
python -V
```
