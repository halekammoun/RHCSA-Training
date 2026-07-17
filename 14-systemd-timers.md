<h1 align="center" style="color: red;">Systemd Timers</h1>

## Introduction

👋 In this section, we will learn how to automate recurring tasks using **systemd timers**.

A **systemd timer** is an alternative to **cron**. Instead of executing commands directly, a timer starts a **systemd service** at a scheduled time.

Systemd timers are fully integrated with **systemd**, making them easier to monitor, manage and troubleshoot.

---

## Cron vs Systemd Timers

| Cron                       | Systemd Timer                        |
| -------------------------- | ------------------------------------ |
| Executes commands directly | Starts a systemd service             |
| Uses crontab               | Uses `.service` and `.timer` units   |
| Basic logging              | Integrated with `journalctl`         |
| Simple scheduling          | Advanced scheduling options          |
| Suitable for simple tasks  | Recommended for modern Linux systems |

---

## How a Systemd Timer Works

Unlike **cron**, a timer does not execute a command directly.

Instead, it starts a **systemd service**, which then executes the desired script.

```text
                   Every Minute
                         │
                         ▼
               log-capture.timer
                         │
                         ▼
             Starts the Service
                         │
                         ▼
             log-capture.service
                         │
                         ▼
 Executes /usr/local/bin/log_capture
                         │
                         ▼
Creates/Updates

/root/log_output/system_logs.trc
```

---

## Directory Structure

```text
/usr/local/bin/
└── log_capture

/etc/systemd/system/
├── log-capture.service
└── log-capture.timer
```

---

## Main Commands

* `systemctl daemon-reload`: Reload systemd unit files.
* `systemctl start <unit>`: Start a service or timer.
* `systemctl stop <unit>`: Stop a service or timer.
* `systemctl restart <unit>`: Restart a service or timer.
* `systemctl enable <unit>`: Enable a unit at boot.
* `systemctl disable <unit>`: Disable a unit.
* `systemctl status <unit>`: Display unit status.
* `systemctl list-timers`: List active timers.
* `journalctl -u <service>`: Display service logs.
* `systemctl cat <unit>`: Display the contents of a unit file.

---

## Timer Directives

Some commonly used timer directives are:

| Directive          | Description                        |
| ------------------ | ---------------------------------- |
| `OnBootSec=`       | Run after system boot              |
| `OnStartupSec=`    | Run after systemd starts           |
| `OnCalendar=`      | Run on a calendar schedule         |
| `OnUnitActiveSec=` | Run after the previous execution   |

For this lab, we will use:

```ini
OnUnitActiveSec=1min
```

which means:

> Execute the service every **1 minute** after the previous execution.

---

## Lab 13

#### Q0. Create the script `/usr/local/bin/log_capture`

Create the output directory:

```bash
mkdir -p /root/log_output
```

Create the script:

```bash
vi /usr/local/bin/log_capture
```

```bash
#!/bin/bash

ls -la /tmp > /root/log_output/system_logs.trc
```

Make it executable:

```bash
chmod +x /usr/local/bin/log_capture
```

---

#### Q1. Create the Service Unit

Create the service file:

```bash
vi /etc/systemd/system/log-capture.service
```
(En RHCSA, lorsque tu crées ton propre service, tu le places toujours dans : /etc/systemd/system)
```ini
[Unit]
Description=Capture /tmp directory listing

[Service]
Type=oneshot
ExecStart=/usr/local/bin/log_capture
```
([Unit] contient toujours des informations générales comme commentaire ou documentation  
tandis que la section [Service] décrit comment exécuter le service;  
on utilise oneshot dans le cas de systems timer car Systemd lance le programme,  
attends qu'il termine, puis le service est terminé.
ExecStart: C'est la commande que systemd exécutera lorsque le service démarre.  
Le chemin doit être absolu. Systemd ne recherche pas la commande dans le PATH comme un shell interactif.  
On pourrait écrire directement :  
ExecStart=/usr/bin/ls -la /tmp
Mais imaginons que demain tu veuilles :  
créer un répertoire ;  
copier des fichiers ;  
envoyer un e-mail ;  
utiliser une boucle...  
Le fichier .service deviendrait vite compliqué.
)

---
| Type      | Utilisation                                                    |
| --------- | -------------------------------------------------------------- |
| `simple`  | Programme qui reste en exécution (nginx, sshd, httpd...)       |
| `forking` | Programme qui se met en arrière-plan (ancien modèle de daemon) |
| `oneshot` | Exécute une tâche puis se termine                              |  
---

#### Q2. Create the Timer Unit

Create the timer file:

```bash
vi /etc/systemd/system/log-capture.timer
```

```ini
[Unit]
Description=Run log capture every minute

[Timer]
OnUnitActiveSec=1min
Persistent=true

[Install]
WantedBy=timers.target
```

(
The [Timer] section defines when the service should be executed.
- `OnUnitActiveSec=1min`: Starts the associated service 1 minute after its last execution.
- `Persistent=true`: If the system was powered off during a scheduled execution, the missed execution is started automatically after the next boot.
The [Install] section defines when the timer should be enabled.
- `WantedBy=timers.target`: Makes the timer start automatically during system boot when it is enabled using:
)
---

#### Q3. Reload Systemd

Whenever a new unit file is created or modified, reload systemd:

```bash
systemctl daemon-reload
```

---

#### Q4. Enable and Start the Timer

```bash
systemctl enable --now log-capture.timer
```

This command:

* Enables the timer at boot.
* Starts it immediately.

---

#### Q5. Verify the Timer

Check the timer status:

```bash
systemctl status log-capture.timer
```

List active timers:

```bash
systemctl list-timers
```

Expected output:

```text
log-capture.service
```

---

#### Q6. Verify the Service

Display the service status:

```bash
systemctl status log-capture.service
```

Display the logs:

```bash
journalctl -u log-capture.service
```

---

#### Q7. Verify the Output File

Wait about one minute, then verify the output:

```bash
cat /root/log_output/system_logs.trc
```

You should see the contents of the `/tmp` directory.

## Exam Tips

Remember:

* A **timer** never executes a script directly.
* A timer **starts a service**.
* The **service executes the script**.
* Always run `systemctl daemon-reload` after creating or modifying a `.service` or `.timer` file.
* Use `systemctl list-timers` to verify that your timer is active.
* Use `Persistent=true` to execute missed runs after a reboot.

<p style="text-align: right;">
  <a href="https://github.com/halekammoun/RHCSA-Training/blob/main/README.md#table-des-matieres">Back to Table of Contents</a>
</p>
