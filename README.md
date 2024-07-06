# Formation RHCSA : Administration Système sous Red Hat Enterprise Linux

## Table des Matières
1. [Installation de Red Hat Enterprise Linux & Enregistrement](#installation-de-red-hat-enterprise-linux--enregistrement)
2. [Commandes basiques & Scripts Shell](#commandes-basiques--scripts-shell)
3. [Gestion des Utilisateurs et des Groupes](#gestion-des-utilisateurs-et-des-groupes)
4. [Automatisation des Tâches](#automatisation-des-tâches)
5. [Gestion des Services](#gestion-des-services)
6. [Archivage & Compression](#archivage--compression)
7. [Nfs & Autofs](#nfs--autofs)
8. [Gestion du Stockage](#gestion-du-stockage)
9. [Gestion des Conteneurs](#gestion-des-conteneurs)
10. [BaseOs & AppStream repositories](#baseos--appstream-repositories)
11. [Réseaux](#réseaux)
12. [Craquer ou Réinitialiser le Mot de Passe Root](#craquer-ou-réinitialiser-le-mot-de-passe-root)

## 1. Installation de Red Hat Enterprise Linux & Enregistrement

### Étapes :
1. Créer un compte sur [redhat.com](https://www.redhat.com)
2. S'inscrire sur [developers.redhat.com](https://developers.redhat.com)
3. Télécharger l'image [ici](https://developers.redhat.com/products/rhel/download)
4. Enregistrer votre système :
    ```bash
    subscription-manager register
    ```
    Puis entrez votre nom d'utilisateur et mot de passe.

## 2. Commandes basiques & Scripts Shell

### Commandes théoriques :
- `pwd` : afficher path du répertoire courant.
- `cd directory` : change le répertoire courant vers "directory".
- `cd ..` : change le répertoire courant vers le répertoire parent.
- `ls` : liste les fichiers et répertoires du répertoire courant.
- `ls -l` : liste les détails des fichiers et répertoires.
- `ls -a` : liste tous les fichiers, y compris les fichiers cachés.
- `whoami` : affiche le nom de l'utilisateur actuel.
- `touch file` : crée un nouveau fichier.
- `mkdir dir` : crée un nouveau répertoire.
- `cat` : affiche le contenu d'un fichier.
- `echo "text"` : affiche du texte.
- `vim / nano` : édite un fichier en utilisant l'éditeur Vim ou Nano.
- `rmdir` : supprime un répertoire vide.
- `rm` : supprime un fichier.
- `cp -rf sourceLocation destinationLocation` : copie un répertoire.
- `mv sourceLocation destinationLocation` : renomme ou déplace un fichier ou un répertoire.
- `history` : affiche l'historique des commandes.
- `grep` : recherche des motifs dans un fichier.
- `find` : recherche des fichiers et des répertoires dans un répertoire.

### Exercices pratiques :
1. Copier `/etc/passwd` dans `/tmp` :
    ```bash
    cp /etc/passwd /tmp
    ```
2. Copier `/etc/shadow` dans `/home/tekup/` et créer un nouveau fichier appelé "nopass" contenant toutes les lignes avec "!!" :
    ```bash
    mkdir /home/tekup
    cp /etc/shadow /home/tekup
    grep '!!' /home/tekup/shadow > nopass
    ```
3. Trouver toutes les lignes dans le fichier `/usr/share/dict/words` qui contiennent le mot "seismic". Copier toutes ces lignes dans le fichier `/root/wordlist` :
    ```bash
    grep -w seismic /usr/share/dict/words > /root/wordlist
    ```
4. Localiser tous les fichiers appartenant à "user" :
    ```bash
    find / -type f -user user 2> /dev/null
    ```
5. Trouver tous les fichiers de moins de 1 KB dans `/var` :
    ```bash
    find /var -type f -size -1k
    ```
6. Trouver tous les fichiers se terminant par ".txt" dans le répertoire `/root/Documents` :
    ```bash
    find /root/Documents -name "*.txt"
    ```
7. Copier tous les fichiers appartenant à "user" dans `/root/dir` :
    ```bash
    find / -type f -user user -exec cp -a {} /root/dir \;
    ```
8. Copier tous les fichiers de plus de 100 MB dans le répertoire `/backup` :
    ```bash
    find / -type f -size +100M -exec cp -a {} /backup \;
    ```
9. Copier tous les fichiers dans le répertoire `/home/user/documents` qui ont les permissions de lecture et écriture pour le propriétaire, lecture seule pour le groupe, et aucune permission pour les autres dans le répertoire `/secure_backup` :
    ```bash
    find /home/user/documents -perm 640 -exec cp -a {} /secure_backup \;
    ```

## 3. Gestion des Utilisateurs et des Groupes
### Théorie
- Ajout, suppression et modification d'utilisateurs et de groupes.
- Utilisation des commandes `useradd`, `usermod`, `userdel`, `groupadd`, `groupmod`, `groupdel`.

### Pratique
1. Ajouter un nouvel utilisateur :
    ```bash
    useradd nouvel_utilisateur
    ```
2. Changer le mot de passe de l'utilisateur :
    ```bash
    passwd nouvel_utilisateur
    ```
3. Ajouter un utilisateur à un groupe :
    ```bash
    usermod -aG groupe utilisateur
    ```

...

(Continuez à ajouter les sections restantes de votre cours de manière similaire)

## 12. Craquer ou Réinitialiser le Mot de Passe Root

### Théorie
- Réinitialisation du mot de passe root via le mode single-user.

### Pratique
1. Démarrer en mode single-user.
2. Réinitialiser le mot de passe root :
    ```bash
    passwd
    ```

---

## Ressources Supplémentaires
- [Documentation officielle de Red Hat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/)
- [Tutoriels et guides](https://developers.redhat.com/learn/)

## Contribuer
Si vous souhaitez contribuer à ce cours, veuillez consulter le fichier [CONTRIBUTING.md](CONTRIBUTING.md).

## Licence
Ce cours est sous licence [MIT](LICENSE).

