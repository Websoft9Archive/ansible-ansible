# Troubleshooting

If you're having trouble with running Ansible, here is a quick guide to solve most common problems.

> Most faults about the Instance is closely related to the Instance provider, Cloud Platform. Provided you're sure the fault is caused by Cloud Platform, refer to [Cloud Platform Documentation](https://support.websoft9.com/docs/faq/tech-instance.html).

#### How can I check the error logs?

You can find the keywords **Failed** or **error** from the logs directory: `/data/logs`

#### Credential is correct but always can't connect Managed nodes?

Empty all content of file: */root/.ssh/known_hosts*