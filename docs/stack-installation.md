# Initial Installation

If you have completed the Ansible deployment, follow the steps below to start a quick use.

## Preparation

1. Get the **Server's Internet IP** of Server on your Cloud Platform.
2. Check your **[Inbound of Security Group Rule](https://support.websoft9.com/docs/faq/tech-instance.html)** of Cloud Console to ensure the **TCP:8161** is allowed.
3. Make a domain resolution on your Cloud Console if you want to use domain for Ansible.

## Ansible Installation Wizard

1. Use local Chrome or Firefox to access the URL *http://DNS:15672* or *http://Server's Server's Internet IP:15672*. You will enter installation wizard of Ansible.
   ![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/ansible/ansible-login-websoft9.png)

2. Log in Ansible web console. ([Don't have password?](/stack-accounts.md#ansible)) 
   ![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/ansible/ansible-bk-websoft9.png)

3. Set you new password from: 【Users】>【Admin】>【Permissions】>【Update this user】
   ![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/ansible/ansible-pw-websoft9.png)

> More guide about Ansible, please refer to [Ansible Documentation](https://www.ansible.com/documentation.html).

## Q&A

#### Can't visit the start page of Ansible?

Your TCP:15672 of Security Group Rules is not allowed, so there is no response from Chrome or Firefox.

#### Ansible service can't start? 
Reason:  
Solution:  
