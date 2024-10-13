<h1 align="center" style="color: red;">Gestion des Utilisateurs et des Groupes</h1>

## Introduction
👋 Dans cette section, nous allons explorer les commandes et fichiers essentiels pour la gestion des utilisateurs et des groupes 
## Fichiers Importants

- **/etc/shadow** : contient les mots de passe.
- **/etc/passwd** : contient les utilisateurs.
- **/etc/group** : contient les groupes.
- **/etc/skel** : modèle de répertoire personnel.
- **/etc/login.defs** : contient des paramètres de création de l’utilisateur.
- **/etc/default/useradd** : contient les valeurs par défaut.
- **~/.bash_profile** & **~/.bashrc** : pour ajouter des variables spécifiques à un utilisateur.
- **/etc/security/pwquality.conf** : changer les caractéristiques du mot de passe (root).

## Commandes Utiles

- `su user` : Passe à l'utilisateur spécifié.  
- `su - user` : Passe à l'utilisateur spécifié et change vers son répertoire personnel.  
- `sudo` : Exécuter une commande avec des privilèges root.  
- `useradd [option] username ` : ajouter utilisateur.  
- `passwd username `: affecter un mot de passe à l'utilisateur.  
- `useradd -M username` : ajouter un utilisateur sans répertoire personnel.  
- `chage [option] username` : gérer mot de passe.  
- `chage -d 0 username` : changer le mot de passe dès que username se connecte (Date de la dernière modification =0 days).  
options:  
-m → durée minimum en jour pour que le user change mot de passe (days).  
-M → nombre maximum du jour avant expiration du mot de passe.  
-E → date exacte d’expiration (yyyy-mm-dd).  
-W → nombre des jours de warning :avant expiration.  
- `usermod [option] username` : modifier utilisateur.  
options:  
-u → modifier UID  
-g → modifier GID (grp primaire)  
-G → modifier grp secondaire  
-aG → ajouter un autre grp secondaire  
-c  → ajouter un commentaire  
-s → modifier le shell.  
-d → modifier le répertoire personnel.  
- `usermod -s /sbin/nologin username` : rendre un utilisateur inactif.  
- `usermod –L username` : désactive le compte de username en verrouillant son mot de passe.  
- `usermod –U username` : pour déverouiller.  
- `echo “username  ALL=(ALL)	ALL” >> /etc/sudoers` → ajouter l’utilisateur pour pouvoir utiliser la commande sudo.  
- `echo “username  ALL=(ALL)	NOPASSWD:ALL” >> /etc/sudoers` → ajouter l’utilisateur pour pouvoir utiliser la commande sudo sans demande de mot de passe.  
- `groupadd [option] groupname` : ajouter groupe.  
- `groupmod [option] groupname` : modifier groupe.  
- `groupadd -o -g 490 groupname` : donner un gid déjà utilisé.  
- `usermod -g groupname` : affecter un utilisateur à un groupe primaire.  
- `usermod -G groupname` : affecter un utilisateur à un groupe secondaire.  
- `usermod -aG groupname` : affecter un utilisateur à un autre groupe secondaire.  
- `groups username` : affiche les groupes de username.  
- `userdel username` : supprimer un utilisateur.  
- `userdel -r username` : supprimer un utilisateur avec son répertoire personnel.  
- `groupdel groupname` : supprimer un groupe.  
- `chown [options] [newowner][:newgroup] filename` : modifier le propriétaire et le groupe propriétaire d’un fichier  
-R : pour un répertoire.  
- `chown newowner filename` : modifier le propriétaire.  
- `chown :newgroup filename` : modifier le groupe propriétaire.  
## Lab 03

#### Q0.  Every newly created user must have a UID between 1000 and 3000.

```bash
vim /etc/login.defs
UID_MIN                  1000
UID_MAX                  3000

```


#### Q1. Create a user named 'student' with the password 'tekup'.

```bash
useradd student
passwd student

```

#### Q2. Set a specific umask=013 for 'student'.


```bash
su - student
vim .bashrc
umask 0013
exit
su  student
umask

```

#### Q3. Assign this user to a secondary group named 'tekup'.

```bash
groupadd tekup
usermod -G tekup student

```

#### Q4. give this user permission to use sudo without a password.

```bash
echo “student	ALL=(ALL)	NOPASSWD:ALL” >> /etc/sudoers
or 
vim /etc/sudoers
student	ALL=(ALL)	NOPASSWD:ALL
```

#### Q5. As the 'student' user, create a file named 'fich'.

```bash
su - student
touch fich

```

#### Q6. Change the group owner of this file to 'tekup'.

```bash
sudo chown :tekup fich
```

## Les droits d'accès  
read – write – execute  
r	 w 	x  
2^2	2^1	2^0  
4	2	1 → de manière octale  
user    group  others  
u 	g 	o → de manière symbolique  
7 	7	3 → exemple  
`man chmod`  
- `chmod [options] permission filename` : modifier les permissions de fichier.  
exemple:  
Propriétaire : lire et écrire  
Groupe : Lire , écrire et exécuter  
Autres : Lire  
Mode octal : chmod 674 fichier  
Mode symbolique : chmod u=rw,g=rwx,o=r filename  
ajouter x pour user: chmod u+x filename  
retirer x pour qroup: chmod g-x filename  
0666 → Est la permission maximum pour un fichier  
0777 → Est la permission maximum pour un répertoire  
0022 → Est la valeur de Umask par défaut  
- `umask new_value` : modifier temporairement le umask
- `echo “umask new_value” /etc/login.defs` → modifier le umask de manière persistante, s'appliquera à tous les utilisateurs lorsqu'ils se connectent au système.  
- `echo “umask new_value” ~/.bashrc` → modifier le umask de manière persistante, s'appliquera à un utilisateur spécifique lorsqu'il se connectent au système.  
permission maximum(777) - mask =permission par défaut  
mask= permission maximum(777) - permission par défaut  



exemples:  
- Quelles seront les permissions par défaut pour un fichier si le mask est 013?  
```bash
r w x	 r w x 	r w x  
- - -	 - - x	 - w x   
----------------------------------  
= rwx	 rw-	r --  
```

- Quelles seront les permissions par défaut pour un dossier si le mask est 013? 
```bash 
r w x	 r w x 	r w x  
- - -	 - - x	 - w x  
----------------------------------  
= rwx	 rw-	r --  
```
## Droits spéciaux:  
- SUID: 4000 ,(rws --- --- pour rwx --- ---), (rwS --- --- pour rw- --- ---)  
Une fois que le SUID est appliqué à un fichier ou une commande, le dernier sera exécuté avec les permissions du propriétaire lorsque n'importe quel utilisateur le lance.
exemple de commande ayant le SUID →  passwd (ls -l /usr/bin/passwd)  
`chmod u+s filename`→ appliquer SUID a un fichier 
- SGID: 2000  ,(--- rws --- pour --- rwx ---), (--- rwS --- pour --- rw- ---)  
Appliquée à un répertoire, dont tous ses fichiers nouvellement créés auront le même groupe propriétaire
`chmod g+s dirname`→ appliquer SGID à un répertoire.  
- Sticky bit: 1000 ,(--- --- rwt pour --- --- rwx), (--- --- rwT pour — — rw-)  
Appliquée à un répertoire, il interdit la suppression des fichiers qu’il contient à tout utilisateur (autre le owner et le root).  
`chmod o+t dirname`→ appliquer Sticky bit  à un répertoire.  
## les ACLs:
`getfacl filename` → afficher les ACLs d’un fichier.  
`setfacl -m u:username:perm,g:groupname:perm,o:perm filename` → positionner les ACLs; pour donner un droit de permission d’un utilisateur spécifique ou un  groupe spécifique.  
`setfacl -b filename` → retirer les acls.  


## Lab 04

#### Q0. Create a directory named "test_directory" in your home directory, Ensure that only the owner has the right to read, write, and execute, while others have no access rights, files created in this directory inherit the group too and cannot be deleted only by their owners or root. 

```bash
chmod 3700 test_directory
or 
chmod u=rwx,o=- test_directory
chmod g+s test_directory
chmod o+t test_directory

```



#### Q1. You have a sensitive file named "confidential" in your home directory. You want to share this file with the user "friend" allowing them only to read it, and with the group "colleagues" giving them both read and write permissions. you also want to ensure that no one else has access to the file.


```bash
setfacl -m u:friend:r,g:colleagues:rw,o:- confidential 

```
<h1 align="center" style="color: red;">Lab d'évaluation 01</h1>

```bash
Q1. create groups redhat and tekup .  
groupadd redhat  
groupadd tekup  
Q2. create users : foulen1, flouen2 and foulen3 as follow :  
- all users have their password set to “thuctive”  
- foulen1 has 1300 as uid and a comment “guest”  
- foulen2 has “/bin/bash” as a shell  
- foulen3 has a home directory “/home/test” and uid of 1301  
- foulen1 has tekup as his primary group, foulen2 has redhat as primary group and tekup as secondary, foulen3 has both groups as secondary  
useradd foulen1  
useradd foulen2  
useradd foulen3  
passwd foulen1 	passwd foulen2 	passwd foulen3  
usermod -u 1300 -c guest foulen1  
usermod -s /bin/bash foulen2  
usermod -u 1301 -d /home/test foulen1  
tail -3 /etc/passwd  
usermod -g tekup foulen1  
usermod -g redhat -G tekup foulen2  
usermod -G redhat -aG tekup foulen2  
groups foulen1 	groups foulen2 	groups foulen3  
Q3. foulen2’s  account will expire on april 5 th 2030, has a minimum password age of 5, 7 days of warning.  
chage -E 2030-04-05 -m 5 -W 7 foulen2  
Q4. foulen2’s  password will expire on march 23 th 2025.  
chage -M (compter de ce jour vers 23 mars 2025) foulen2  
chage -l foulen2  
Q5. create directory “/home/tekup”. owned by root and its group is redhat. redhat members has access (r+x) permission, others have none ,files created in this directory inherit the group too and cannot be deleted only by their owners or root.  
mkdir /home/tekup  
chown :redhat /home/tekup  
chmod g=r /home/tekup  
chmod o=- /home/tekup  
chmod g+s,o+t /home/tekup  

Q6. copy ‘/etc/passwd’ under ‘/tmp’. foulen1 has the right to read and change it foulen2 has none.  
cp /etc/passwd /tmp/  
setfacl -m u:foulen1:rw,u:foulen2:- /tmp/  

Q7. copy all files owned by “user” under “/opt/dir.  
find / -type f -user nom exec cp -a {} /opt/dir \ ;  
Q8. copy “/etc/shadow” under “/home/tekup/”, create a new file called nopass containing all lines that have ”!!” in them.  
cp /etc/shadow /home/tekup/  
 grep “\!!” /home/tekup/shadow > nopass  
Q9. All users passwords expire after 500 days.  
vim /etc/login.defs  
PASS_MAX_DAYS 500  

Q10. All password should contain at least one capital letter and two digits.  
vim /etc/security/pwquality.conf  
ucredit = -1  
dcredit = -2  

Q11. Set the minimum password length to 9.  
vim /etc/security/pwquality.conf  
minlen = 9  

Q12. Modify foulen3 user account's so the password expires after 20 days.  
sudo chage -M 20 foulen3  
chage -l foulen3  

Q13. Configure the necessary settings so that all files created by the user foulen2 will have the following permissions: r--rw-r--.  
vim /home/foulen2/.bash_profile  
umask 202  
en effet : max:777 - mask = def → max - def =mask  
   rwx rwx rwx  -  
   r-- rw- r--   
= -wx --x -wx = 313  

Q14. now make the user 'foulen1' inactive.
usermod -L foulen1  
```
<p style="text-align: right;">
  <a href="https://github.com/halekammoun/RHCSA-Training/blob/main/README.md#table-des-matieres">Retour à la Table des Matières</a>
</p>


