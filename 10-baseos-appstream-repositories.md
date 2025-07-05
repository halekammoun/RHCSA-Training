
# BaseOS & AppStream Repositories

## Introduction
👋 In this section, I will show you how to configure 'YUM repositories'.

Edit the repository files:

```bash
vim /etc/yum.repos.d/BaseOs.repo
[BaseOs]
name=BaseOs
baseurl=…/BaseOs
enabled=1
gpgcheck=1 (if a key URL is provided) otherwise gpgcheck=0
gpgkey=… (if a key URL is provided)
```

```bash
vim /etc/yum.repos.d/AppStream.repo
[AppStream]
name=AppStream
baseurl=…/AppStream
enabled=1
gpgcheck=1 (if a key URL is provided) otherwise gpgcheck=0
gpgkey=… (if a key URL is provided)
```

If you have created the files manually and are facing issues:
```bash
rm -rf /etc/yum.repos.d/*
reset the machine
subscription-manager refresh
```
