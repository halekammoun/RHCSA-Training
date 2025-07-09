# **User and Group Management**

## Introduction

👋 In this section, we will explore the essential commands and files used for managing users and groups.

## Important Files

* **/etc/shadow**: Contains password hashes.
* **/etc/passwd**: Contains user information.
* **/etc/group**: Contains group information.
* **/etc/skel**: Template for new user home directories.
* **/etc/login.defs**: Contains user creation settings.
* **/etc/default/useradd**: Contains default user settings.
* **\~/.bash\_profile** & **\~/.bashrc**: Used to add user-specific environment variables.
* **/etc/security/pwquality.conf**: Used to configure password strength (as root).

## Useful Commands

* `su user`: Switch to the specified user.
* `su - user`: Switch to the user and also change to their home directory.
* `sudo`: Run a command as root.
* `useradd [option] username`: Add a user.
* `passwd username`: Set the user's password.
* `useradd -M username`: Add a user without creating a home directory.
* `chage [option] username`: Manage password expiry policies.
* `chage -d 0 username`: Force password change on next login.

  * `-m`: Minimum days between password changes.
  * `-M`: Maximum days before password expires.
  * `-E`: Expiration date (yyyy-mm-dd).
  * `-W`: Number of warning days before expiration.
* `usermod [option] username`: Modify user.

  * `-u`: Change UID.
  * `-g`: Change primary group.
  * `-G`: Change secondary group(s).
  * `-aG`: Add to another secondary group.
  * `-c`: Add a comment.
  * `-s`: Change the shell.
  * `-d`: Change the home directory.
* `usermod -s /sbin/nologin username`: Make a user inactive.
* `usermod -L username`: Lock the account.
* `usermod -U username`: Unlock the account.
* `echo “username ALL=(ALL) ALL” >> /etc/sudoers`: Grant sudo rights.
* `echo “username ALL=(ALL) NOPASSWD:ALL” >> /etc/sudoers`: Sudo without password.
* `groupadd [option] groupname`: Add a group.
* `groupmod [option] groupname`: Modify a group.
* `groupadd -o -g 490 groupname`: Use an already existing GID.
* `usermod -g groupname`: Set primary group.
* `usermod -G groupname`: Set secondary group.
* `usermod -aG groupname`: Add to a secondary group.
* `groups username`: Show user's groups.
* `userdel username`: Delete a user.
* `userdel -r username`: Delete user and their home directory.
* `groupdel groupname`: Delete a group.
* `chown [options] [newowner][:newgroup] filename`: Change file ownership.

  * `-R`: Recursively apply to directory.
  * `chown newowner filename`: Change file owner.
  * `chown :newgroup filename`: Change group owner.

## Lab 03

**Q0.** Set UID range for new users between 1000 and 3000:

```bash
vim /etc/login.defs
UID_MIN 1000
UID_MAX 3000
```

**Q1.** Create user 'student' with password 'tekup':

```bash
useradd student
passwd student
```

**Q2.** Set default umask to 013 for 'student':

```bash
su - student
vim .bashrc
umask 0013
exit
su student
umask
```

**Q3.** Add 'student' to secondary group 'tekup':

```bash
groupadd tekup
usermod -G tekup student
```

**Q4.** Give 'student' sudo access without password:

```bash
echo “student ALL=(ALL) NOPASSWD:ALL” >> /etc/sudoers
# or edit manually
vim /etc/sudoers
```

**Q5.** As 'student', create file 'fich':

```bash
su - student
touch fich
```

**Q6.** Change group ownership of 'fich' to 'tekup':

```bash
sudo chown :tekup fich
```

## File Permissions

* Read – Write – Execute
* Octal: r=4, w=2, x=1
* Symbolic: u (user), g (group), o (others)
* Example:

  * Octal: `chmod 674 file`
  * Symbolic: `chmod u=rw,g=rwx,o=r file`

**Common Examples:**

* `chmod u+x file`: Add execute for user.
* `chmod g-x file`: Remove execute for group.
* Maximum file permission: 0666
* Maximum directory permission: 0777
* Default umask: 0022

**Changing umask:**

* Temporary: `umask 027`
* Persistent (system-wide): Add to `/etc/login.defs`
* Persistent (user-specific): Add to `~/.bashrc`

**Permission calculation:**

* Default = 777 - umask
* umask = 777 - desired default

### Example:


**Default directory ad file permissions with umask = 013**
Result:

* User: `rwx`
* Group: `rw-`
* Others: `r--`

## Special Permissions
SUID gives a user temporary access to run a file as the file’s owner.


SGID ensures files created in a directory inherit the group of the directory.


The sticky bit is used on shared directories so that only the file’s owner or root can delete files.

* **SUID (4000)**: Run file with owner's privileges.

  ```bash
  chmod u+s file
  ```

* **SGID (2000)**: Files in the directory inherit group.

  ```bash
  chmod g+s directory
  ```

* **Sticky Bit (1000)**: Only owner or root can delete files.

  ```bash
  chmod o+t directory
  ```

## ACLs

* View: `getfacl file`
* Set: `setfacl -m u:username:perm,g:groupname:perm,o:perm file`
* Remove: `setfacl -b file`

## Lab 04

**Q0.** Create `test_directory` with full control to owner, inherit group, restrict delete:

```bash
chmod 3700 test_directory
# or step-by-step
chmod u=rwx,o=- test_directory
chmod g+s test_directory
chmod o+t test_directory
```

**Q1.** File "confidential": allow user "friend" to read, group "colleagues" to read/write, no access to others:

```bash
setfacl -m u:friend:r,g:colleagues:rw,o:- confidential
```

---

# **Evaluation Lab 01**

```bash
Q1. Create groups:
groupadd redhat
groupadd tekup

Q2. Create users:
useradd foulen1
useradd foulen2
useradd foulen3
passwd foulen1
passwd foulen2
passwd foulen3
usermod -u 1300 -c guest foulen1
usermod -s /bin/bash foulen2
usermod -u 1301 -d /home/test foulen3
usermod -g tekup foulen1
usermod -g redhat -G tekup foulen2
usermod -G redhat -aG tekup foulen3

Q3. foulen2 account expires April 5, 2030 with min password age 5, warning 7:
chage -E 2030-04-05 -m 5 -W 7 foulen2

Q4. foulen2 password expires March 23, 2025:
# Calculate days from today to 2025-03-23
chage -M [days] foulen2
chage -l foulen2

Q5. Create directory `/home/tekup`:
mkdir /home/tekup
chown :redhat /home/tekup
chmod g=rx /home/tekup
chmod o= /home/tekup
chmod g+s,o+t /home/tekup

Q6. Copy `/etc/passwd` to `/tmp`, give foulen1 rw, foulen2 none:
cp /etc/passwd /tmp/
setfacl -m u:foulen1:rw,u:foulen2:- /tmp/passwd

Q7. Copy all files owned by "user" to `/opt/dir`:
find / -type f -user user -exec cp -a {} /opt/dir \;

Q8. Copy `/etc/shadow` to `/home/tekup`, extract “!!” lines:
cp /etc/shadow /home/tekup/
grep "!!" /home/tekup/shadow > nopass

Q9. All user passwords expire after 500 days:
vim /etc/login.defs
PASS_MAX_DAYS 500

Q10. Password must contain 1 uppercase and 2 digits:
vim /etc/security/pwquality.conf
ucredit = -1
dcredit = -2

Q11. Set minimum password length to 9:
minlen = 9

Q12. foulen3 password expires after 20 days:
chage -M 20 foulen3
chage -l foulen3

Q13. Files created by foulen2 should be `r--rw-r--`:
vim /home/foulen2/.bash_profile
umask 202

Q14. Lock user foulen1:
usermod -L foulen1
```
