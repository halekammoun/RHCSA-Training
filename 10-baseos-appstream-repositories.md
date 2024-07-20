<h1 align="center" style="color: red;"> BaseOs & AppStream repositories</h1>

## Introduction
👋 Dans cette section, je vous montrerai comment configurer les 'YUM repositories'.  

- `vim /etc/yum.repos.d/BaseOs.repo`
```bash
[BaseOs]
name=BaseOs
baseurl=…/BaseOs
enabled=1
(gpgcheck=1 (s’il ya un url key) sinon gpgcheck=0
gpgkey=…(s’il vous donne un url key))
```
- `vim /etc/yum.repos.d/AppStream.repo`
``` bash
[AppStream]
name= AppStream
baseurl=…/AppStream
enabled=1
(gpgcheck=1 (s’il ya un url key) sinon gpgcheck=0
gpgkey=…(s’il vous donne un url key))
```
en cas ou vous avez crée les fichiers dans vos machines 
il suffit de faire les étapes suivantes pour résoudre la probléme:
`rm -rf /etc/yum.repos.d/*` → `reset the machine` → `subscription-manager refresh`
