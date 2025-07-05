<h1 align="center" style="color: red;">NFS & AutoFS</h1>

## Introduction
👋 In this section, we will explore how to manage an NFS server in a Red Hat Linux environment.

## NFS:

### Theory:
<p align="center">
  <img src="images/nfs.JPG" alt="cap" style="width: 700px;"/>
</p> 

File server?  
A computer responsible for storage so that other computers on the same network can access the files, via Network share: NFS (Network share protocol in Linux).  

How to use the NFS service?  
The server exports the directory, then the client mounts the NFS filesystem.

Any service has layered protection:
- **firewall**: controlled using `firewalld-cmd` (default: rules are blocked),
- **SELinux**: controlled using the `semanage` command (Security-Enhanced Linux).

### Practice:
**Server-side** (must be root: `su -`):
- `dnf install nfs*` → install the service
- `systemctl enable --now nfs-server` → enable & start the service  
- `systemctl status nfs-server` → check the status of the service

**Configuration:**
- `mkdir /shared_directory` → create the directory to share
- `echo “/shared_directory	@ipclient(x ,y)” > /etc/exports` → configure the shared directory  
  - `x`: can be `ro` or `rw`  
  - `y`: can be `no_root_squash` or `async`  
  - `no_root_squash`: the client’s root user retains full privileges on the shared files  
  - `root_squash` (default): the client’s root privileges are limited  
  - `rw`: directory is readable & writable  
  - `ro`: directory is read-only  
  - `sync` (default): synchronous connection; the server waits for data to be written before replying  
  - `async`: server can reply before writing is completed  

- `systemctl restart nfs-server` → restart after modifying `/etc/exports`
- `semanage boolean -l | grep nfs_export_all_ro` → check if NFS is allowed to export in read-only mode
- `semanage boolean -l | grep nfs_export_all_rw` → check if NFS is allowed to export in read-write mode
- `setsebool -P nfs_export_all_rw=1` → allow export in rw mode
- `setsebool -P nfs_export_all_ro=1` → allow export in ro mode
- `firewall-cmd --list-all` → check enabled firewall services
- `firewall-cmd --add-service=nfs --permanent` → allow NFS through firewall
- `firewall-cmd --reload` → apply firewall changes
- `exportfs -avr` → export the directory

**Client-side** (must be root: `su -`):
- `dnf install nfs-utils` → install the service
- `mkdir /mount_point` → directory where the NFS will be mounted
- `mount -t nfs -o rw @ipserver:/shared_directory /mount_point` → mount the shared directory  
- `umount /shared_directory` → unmount the shared directory
- `echo “@ipserver:/shared_directory /mnt/mount_point nfs _netdev 0 0” >> /etc/fstab` → mount it permanently
- `mount -a` → apply `/etc/fstab`
- `df -h` → verify mount

---

## AutoFS:

### Theory:

<p align="center">
  <img src="images/autofs.png" alt="cap" style="width: 400px;"/>
</p> 

What is Autofs?

On-demand NFS or automatic mount of a shared directory.  
Autofs allows on-demand mounting of filesystems such as NFS when users access specific directories. This helps optimize resource usage by mounting only when needed.

- **Server** → exports
- **Client** → automount (uses `auto.master` and `auto.misc`)

**Use Case:**  
To share a home directory from the server so the user can use it when logging in on the client.

---

### Q0. Create a user named `john` on both machines with UID 2001. His home directory on the server is `/host/john`. It will be mounted on `/host/john` using Autofs when John logs in.

**Note**: the user must have a base directory on the server, but not on the client (for testing).

**Server-side:**
- `dnf install nfs*`
- `useradd -u 2001 -b /host john` → create the user's base directory
- `echo “/host 	*(rw,no_root_squash)” >> /etc/exports` → export the base directory
- `firewall-cmd --add-service=rpc-bind --permanent`
- `firewall-cmd --add-service=mountd --permanent`
- `firewall-cmd --add-service=nfs --permanent`
or simply:
- `firewall-cmd --add-service={rpc-bind,nfs,mountd} --permanent`
- `firewall-cmd --reload`
- `exportfs -arv`

**Client-side:**
- `dnf install nfs-utils`
- `dnf install autofs` → install needed packages
- `useradd -M -u 2001 -d /host/john john` → create user without home directory
- `echo “/host		/etc/auto.misc” >> /etc/auto.master`
- `echo “john -rw  @ipserver:/host/john” >> /etc/auto.misc`
- `systemctl restart nfs-server`
- `systemctl restart autofs`

---

<h1 align="center" style="color: red;">Lab Evaluation 02</h1>

```bash
Q0. Create a cron job running as root at 10PM daily to execute hello.sh  
crontab -e  
0 22 * * * bash hello.sh  

Q2. Configure httpd to run on port 93 with /tekup as DocumentRoot  
vim /etc/httpd/conf/httpd.conf  
firewall-cmd --add-port=93/tcp --permanent  
firewall-cmd --reload  
semanage port -a -t http_port_t -p tcp 93  
systemctl restart httpd  
mkdir -p /tekup/html  
vim /etc/httpd/conf/httpd.conf  
semanage fcontext -a -t httpd_sys_content_t "/tekup/html(/.*)?"  
restorecon -R -v /tekup/html  
systemctl restart httpd  
echo "on port 93" >> /tekup/html/index.html  
curl http://localhost:93  

Q3. Archive and compress /tmp into tmp.tgz  
tar -cvzf tmp.tar.gz /tmp  

Q4. An NFS share has been configured from domain11.example.com (192.168.0.24):  
192.168.0.24:/remoteuser/user20.  
Configure this share to mount automatically under /remoteuser/user20.  
User’s home directory in /etc/passwd is /remoteuser/user20.

Server:
dnf install nfs*  
useradd -u 2222 -b /remoteuser user20  
echo “/remoteuser 	*(rw,no_root_squash)” >> /etc/exports  
firewall-cmd --add-service {rpc-bind,mountd,nfs} --permanent  
firewall-cmd --reload  
exportfs -arv  

Client:
dnf install nfs-utils  
dnf install autofs  
useradd -M -u 2222 -d /remoteuser/user20 user20  
echo “/remoteuser		/etc/auto.misc” >> /etc/auto.master  
echo “user20 -rw  192.168.0.24:/remoteuser/user20” >> /etc/auto.misc  
systemctl restart autofs  
