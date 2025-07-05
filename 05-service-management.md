<h1 align="center" style="color: red;">Service Management</h1>

## Introduction

👋 In this section, we will learn how to configure and manage the **SSHD**, **HTTPD**, and **NTP** services.

---

## SSHD Service:

### Theory:

<p align="center">
  <img src="images/ssh.JPG" alt="cap" style="width: 600px;"/>
</p>  
In simple terms, SSH allows a user to connect securely to another computer, as if they were physically in front of it. It uses strong encryption to secure communication between the two machines, protecting exchanged data including credentials and commands from interception.  
Note: Well-known ports: numbered from 0 to 1023, they are associated with standard services like HTTP (port 80) and SSH (port 22)...

### Practice:

#### Connect to another machine via SSH using a password:

* `yum install openssh-server` → installs the SSH server (on the server machine).
* `systemctl enable sshd`
* `sudo systemctl start sshd` → start and enable the SSH service (on the server machine).
  Note: On Red Hat, every service is protected by two default security layers: the **Firewall** (`Firewalld`) and **SELinux** (Security-Enhanced Linux).

<p align="center">
  <img src="images/word-image.png" alt="cap" style="width: 300px;"/>
</p>  
- `firewall-cmd --add-port=22/tcp --permanent`
- `firewall-cmd --reload`
- `firewall-cmd --list-ports` → open SSH port for incoming connections (on the client machine).
- `ssh username@server_ip_address` → test SSH connection (on the client machine; user must have a password), or  
- `vim /etc/hosts`  
  `client_ip    hostname` → assign a hostname to each machine’s IP address.  
- `ssh user_client@hostname`

#### Connect without a password (key-based authentication):

To allow passwordless login, generate and configure SSH keys on machine B and copy them to the server. This allows the server to recognize and authorize connections from machine B.

* `ssh-keygen -t rsa` → generate SSH keys (on the client machine).
* `ssh-copy-id user@server_machine` → copy the public key to the server; adds it to the `authorized_keys` file in the user's `.ssh` directory.
* `ssh user@server_machine`

#### Connect via a port other than 22:

On the server side:

* `vim /etc/ssh/sshd_config`
  `Port 2222` → change the port to 2222.
* `semanage port -a -t ssh_port_t -p tcp 2222` → register the new port in SELinux.
* `firewall-cmd --permanent --add-port=2222/tcp`
* `firewall-cmd --reload` → update firewall rules.
* `systemctl restart sshd` → restart the SSH service.
  On the client side:
* `ssh user@server_address -p 2222` → connect using the new port.

---

## HTTPD Service:

### Theory:

<p align="center">
  <img src="images/web.JPG" alt="cap" style="width: 600px;"/>
</p>  
The web server stores all files needed to deliver website content, including static pages and dynamic applications. Its role is to serve files and manage interactions between the user's browser and the website's resources.  
An HTTP (GET) request is like sending a command to the server to fetch a specific file (e.g. "index.html"), and the HTTP response is the server sending back the requested content.

* `/var/www/html` is the default directory for web content.
* `"index.html"` is the homepage or landing file of a website.

### Practice:

* `dnf install httpd`
* `systemctl enable --now httpd` → install and enable the service.
* `systemctl status httpd` → check that the service is active.
* `echo "bnj" >> /var/www/html/index.html`
* `firewall-cmd --permanent --add-service=http`
* `firewall-cmd --permanent --add-service=https`
* `firewall-cmd --reload` → allow HTTP and HTTPS in the firewall.

Once the web server is running and `index.html` is in `/var/www/html`, accessing the server’s IP/domain in a browser will show its content.

* `dnf install curl`
* `curl http://server_ip/index.html`
  (If it doesn't work, try `chown apache:apache /var/www/html/index.html`)

#### Change default port (80) to a new port:

* `vim /etc/httpd/conf/httpd.conf`
  `Listen <new_port>`
* `semanage port -a -t http_port_t -p tcp <new_port>` → register the new port in SELinux.
* `firewall-cmd --permanent --add-port=<new_port>/tcp`
* `firewall-cmd --reload`
* `systemctl restart httpd`
* `curl localhost:<new_port>/index.html` → test the new port.

#### Change default directory `/var/www/html` to `/var1/web/html`:

* `vim /etc/httpd/conf/httpd.conf`
  `DocumentRoot "/var1/web/html"`

```apache
<Directory "/var1/web">
    AllowOverride None
    Require all granted
</Directory>
<Directory "/var1/web/html">
```

* `semanage fcontext -a -t httpd_sys_content_t "/var1/web/html(/.*)?"`
* `restorecon -R -v /var1/web/html/`
* `echo "hello2" >> /var1/web/html/test.html`
* `systemctl restart httpd`
* `curl localhost/test.html` or `curl http://host_ip/test.html`
  (If it doesn't work, try `chown -R apache:apache /var1`)

---

## NTP Service:

### Theory:

<p align="center">
  <img src="images/ntp.JPG" alt="cap" style="width: 600px;"/>
</p>  
An NTP server provides accurate time to client computers on a network. It maintains precise time by regularly syncing with atomic or GPS time sources and responds to client requests with high accuracy.

### Practice:

#### On the server (to allow client access):

* `dnf install chrony`
* `systemctl enable chronyd`
* `systemctl start chronyd` → install and start the service.
* `vim /etc/chrony.conf`
  `allow client_ip`
* `systemctl restart chronyd`
* `firewall-cmd --add-service=ntp --permanent`
* `firewall-cmd --reload`

#### On the client (to access the time):

* `dnf install chrony`
* `systemctl enable chronyd`
* `systemctl start chronyd`
* `vim /etc/chrony.conf`
  `server server_ip iburst`
* `systemctl restart chronyd`
* `chronyc sources -c` → check synchronization

To enable NTP:

* `timedatectl set-ntp true`
* `timedatectl`

Set a time zone:

* `timedatectl list-timezones`
* `timedatectl set-timezone <zone>`
* `timedatectl`

<p style="text-align: right;">
  <a href="https://github.com/halekammoun/RHCSA-Training/blob/main/README.md#table-des-matieres">Back to Table of Contents</a>
</p>

