# FAQ

#### How many Linux distribution  **ansible_os_family** support?

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

#### How is ansible installed?

pip

#### Can I install software in the Server the same with Ansible?

Yes, Ansible not only for Control node / Managed nodes mode

#### What is the password for the database postgres user?

The password is stored in the server related file: */credentials/password.txt*

#### Is there a web-base GUI Ansible management tool?

No, you should install it by yourself, refer to [Ansible GUI](solution-more.md#ansible-gui)

#### How to change the permissions of filesystem?

Change owner(group) or permissions as below:

```shell
chown -R apache.apache /data/wwwroot
find /data/wwwroot -type d -exec chmod 750 {} \;
find /data/wwwroot -type f -exec chmod 640 {} \;
```

#### What's the difference between Deployment and Installation?

- Deployment is a process of installing and configuring a series of software to the server in a different order, which is a complex system engineering.  
- Installation is the process of starting the initial wizard after the application is prepared.  
- Installation is simpler than deployment. 

#### What's Cloud Platform?

Cloud platform refers to platform manufacturers that provide cloud computing services, such as: **Azure, AWS, Alibaba Cloud, HUAWEI CLOUD, Tencent Cloud**, etc.

#### What is the difference between Instance, Cloud Server, Virtual Machine, ECS, EC2, CVM, and VM?

No difference. All refer to cloud servers. They are the different terminology used by manufacturers.
