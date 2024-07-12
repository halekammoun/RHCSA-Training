<h1 align="center" style="color: red;">Automatisation des Tâches</h1>

## Introduction
👋 Dans cette section, nous allons explorer comment automatiser des tâches en utilisant la commande `crontab`.


La commande `crontab` permet de programmer des tâches pour qu'elles s'exécutent automatiquement à des intervalles réguliers.

### Commandes Principales
- `cat /etc/crontab` : pour voir les détails de la configuration actuelle.
<p align="center">
  <img src="images/Capture.JPG" alt="cap" style="width: 500px;"/>
</p>
- `crontab -e` : pour éditer la crontab de l'utilisateur actuellement connecté.
- `crontab -e -u username` : pour éditer la crontab d'un utilisateur spécifique.

### Intervalles de Temps
- `@hourly` : chaque heure.
- `@daily` : chaque jour.
- `@weekly` : chaque semaine.
- `@monthly` : chaque mois.
- `@annually` : chaque année.
- `@reboot` : à chaque redémarrage.

### Remarques sur les Intervalles
- `x,y` : x et y.
- `x-y` : de x à y.
- `x-y/z` : de x à y avec un pas de z.
- `*` : chaque unité.
- `*/z` : chaque z unités.

## Lab 05
#### Q0. Supprimer le contenu de /tmp chaque mercredi à 15h15 et à 12h30 pour user1.
<!--
```bash
crontab -e
30,15 12,15 * * 3 rm -rf /tmp
```
-->
#### Q1. executer le script backup.sh chaque redémarrage de systeme  
<!--
```bash
@reboot bash backup.sh
-->
#### Q2. écrire le message “hello” dans hello.txt chaque 3 minutes  
<!--
```bash
crontab -e
*/3 * * * * echo “hello” >> hello.txt
-->
#### Q3.En tant qu’utilisateur natasha, planifiez une tâche qui affiche toutes les 5 minutes le texte suivant dans les messages logs  «Examen en cours ».  
<!--
```bash
crontab -e -u natasha
*/5 * * * * logger “Examen en cours”
-->
#### Q4. Configurez une tâche pour l’utilisateur natasha devant s’exécuter chaque 2 minutes du lundi au vendredi pour insérer la phrase « Examen EX200 en cours » dans les logs.  
<!--
```bash
crontab -e -u natasha
*/2  * * * 1-5  logger “Examen EX200 en cours”
-->
