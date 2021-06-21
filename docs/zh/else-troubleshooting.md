# 故障处理

此处收集使用 Ansible 过程中最常见的故障，供您参考

> 大部分故障与云平台密切相关，如果你可以确认故障的原因是云平台造成的，请参考[云平台文档](https://support.websoft9.com/docs/faq/zh/tech-instance.html)

#### 如何查看错误日志？

日志文件路径为：`/data/logs`。检索关键词 **Failed** 或者 **error** 查看错误

#### Ansible 中经常会出现 python-urllib3 之类的报错，如何处理？

python-urllib3 报错大部分情况下，通过 yum install python-urllib3 解决，而不是 pip install

#### 账号准确无误，仍然无法连接受控机？

清空服务器中的 */root/.ssh/known_hosts* 文件

