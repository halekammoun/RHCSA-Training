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



