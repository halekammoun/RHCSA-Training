<h1 align="center" style="color: red;">Automatisation des Tâches</h1>

## Introduction
👋 Dans cette section, nous allons explorer comment automatiser des tâches en utilisant la commande `crontab`.

## Théorie
La commande `crontab` permet de programmer des tâches pour qu'elles s'exécutent automatiquement à des intervalles réguliers.

### Commandes Principales
- `cat /etc/crontab` : pour voir les détails de la configuration actuelle.
<p align="center">
  <img src="images/capture.jpg" alt="capo" style="width: 400px;"/>
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

## Pratique
### Q0. Supprimer le contenu de /tmp chaque mercredi à 15h15 et à 12h30 pour user1.

```bash
crontab -e
30,15 12,15 * * 3 rm -rf /tmp
