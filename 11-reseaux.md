<h1 align="center" style="color: red;"> Réseaux</h1>

## Introduction
### nmtui
👋 Dans cette section, nous allons explorer comment modifier quelques paramétres réseaux.  
- `hostnamectl hostname name.example.com` → changer le hostname.
- (on peut le changer graphiquement) `nmtui` >> set system hostname name.example.com
- `nmtui` → gérer le réseau graphiquement.
`edit a connection` >> `edit` >> `manual` >> `show: edit adrs,gateway,dns.`>>`ok`
NB: ne pas oublier d' activer et désactiver l’interface après modification.
- `active a connection`>> `deactivate` >> `activate` >> `ok`
- `nmcli con sh` → vérifier l’interface (verte).
### target: 
- `systemctl isolate multi-user.target` → pour changer le target en multi-user.
- `systemctl isolate graphical.target` → pour changer le target en graphical.
- `loadkeys fr` → pour changer le clavier en azerty.
- `systemctl set-default graphical.target` → pour changer le target en graphical ,persiste même après redémarrage .
- `systemctl set-default multi-user.target` → pour changer le target en multi-user , persiste  même après redémarrage .
- `systemctl get-default` → pour voir le target par défaut.
### profil: 
- `tuned`: pour changer de profil.
- `dnf install tuned` → installer tuned.
- `tuned-adm recommend`→ voir le profil recommandé.
- `tuned-adm active`→ voir mon profil.
- `tuned-adm list`→ voir tous les profils.
- `tuned-adm profile <profile>` → changer le profil.
- `systemctl restart tuned`→ restart the service.
- `tuned-adm active`→ vérifier le profil.
## Lab 09
#### Q0. Set the recommended profile as tuned profile
tuned-adm recommend → te donne le recommended-profile
tuned-adm profile recommended-profile
systemctl restart tuned
tuned-adm active



