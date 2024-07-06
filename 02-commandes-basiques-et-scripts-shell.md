<h1 align="center" style="color: red;">Commandes basiques & Scripts Shell</h1>

## Introduction
👋 Dans cette section, nous allons explorer les commandes de base et les scripts shell essentiels pour l'administration système sous Red Hat Enterprise Linux.

## Commandes de Base

### Théorie
- `pwd` : Affiche le chemin du répertoire courant.
- `cd directory` : Change le répertoire courant vers "directory".
- `cd ..` : Change le répertoire courant vers le répertoire parent.
- `ls` : Liste les fichiers et répertoires du répertoire courant.
- `ls -l` : Liste les détails des fichiers et répertoires.
- `ls -a` : Liste tous les fichiers, y compris les fichiers cachés.
- `whoami` : Affiche le nom de l'utilisateur actuel.
- `touch file` : Crée un nouveau fichier.
- `mkdir dir` : Crée un nouveau répertoire.
- `mkdir -p` : Crée des répertoires récursivement, y compris les parents.
- `cat` : Affiche le contenu d'un fichier.
- `echo "text"` : Affiche du texte.
- `echo "text" > file` : Écrase le contenu du fichier avec le texte.
- `echo "text" >> file` : Ajoute du texte à la fin du fichier.
- `vim / nano` : Édite un fichier en utilisant l'éditeur Vim ou Nano.
- `rmdir` : Supprime un répertoire vide.
- `rm` : Supprime un fichier.
- `rm -rf` : Supprime un répertoire récursivement et de façon forcée.
- `man / help` : Fournit des informations sur la commande.
- `cp -rf sourceLocation destinationLocation` : Copie un répertoire.
- `cp sourceLocation destinationLocation` : Copie un fichier.
- `mv sourceLocation destinationLocation` : Renomme ou déplace un fichier ou un répertoire.
- `ctrl + a` : Déplace le curseur au début de la ligne.
- `ctrl + e` : Déplace le curseur à la fin de la ligne.
- `ctrl + c` : Arrête l'exécution d'une commande.
- `history` : Affiche l'historique des commandes.
- `grep` : Recherche des motifs dans un fichier.
- `grep -w` : Recherche un mot exact.
- `find` : Recherche des fichiers et des répertoires dans un répertoire.  
 Syntaxe: find répertoire [options] [-exec cp -a {} destination \;]  
 options:  
 -name: nom de fichier,  
 -group: le grp propriétaire,  
 -user: le propriétaire,  
 -size: taille n{k,M,G…} ou +n{k,M,G…} ou -n{k,M,G…},  
 -perm: permission,  
 -type: f pour fichier et d pour directory,  
 commande:  
 cp: copier les fichiers et les répertoires recherchés (cp -a {})  

 ## Pratique

### Q1. Copy '/etc/passwd' to '/tmp'.
```bash
cp /etc/passwd /tmp
 ```
<!-- 
```bash
cp /etc/passwd /tmp
 ```
 -->
<p style="text-align: right;">
  <a href="https://github.com/halekammoun/RHCSA-Training/blob/main/README.md#table-des-matieres">Retour à la Table des Matières</a>
</p>