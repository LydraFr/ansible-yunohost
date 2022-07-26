# Rôle Ansible : YunoHost Backup

[🇬🇧 English version](README.md)

Sauvegardez [YunoHost](https://yunohost.org/#/) avec Ansible !

## Prérequis

YunoHost doit déjà être installé sur votre serveur.

## Variables du rôle

Les variables par défaut sont disponibles dans `default/main.yml` cependant il est possible de les surcharger selon vos besoins.
Nous avons intégré deux systèmes de sauvegardes différents à ce rôle YunoHost :

- sauvegardes natives YunoHost en local
- sauvegardes à distance avec un [depot BorgBackup](https://borgbackup.readthedocs.io/en/stable/)

### Sauvegardes natives YunoHost locales

- Les backups locaux natifs à YunoHost : YunoHost propose son propre système de sauvegardes natif. Il est capable de sauvegarder la configuration YunoHost, les mails (si YunoHost est utilisé en tant que serveur de mails) et les applications installées sur YunoHost. Il est possible de créer et restaurer les sauvegardes depuis l'interface d'administration web ainsi que la ligne de commande en SSH (`yunohost backup`). Les sauvegardes sont disponibles en local et nous avons automatisé le déclenchement de ces sauvegardes par une tâche cron. Plus d'infos [ici](https://yunohost.org/fr/backup).

```yml
ynh_backup:
  scheduled: True
  directory: "/data/backup"
  scheduled_hour: "*"
  scheduled_minute: "*/30"
  scheduled_weekday: "*"
  scheduled_month: "*"
  system: True
  apps: True
  number_days_to_keep: "2"

```

- `ynh_backup.scheduled` : active la fonctionnalité de sauvegarde des applications YunoHost en mettant la valeur à `True`.
- `ynh_backup.directory` : le dossier de sauvegarde par défaut est `/home/yunohost.backup/archives`. Vous pouvez choisir de sauvegarder les backups dans un autre dossier grâce à cette variable. Dans ce cas, de manière à pouvoir restaurer les backups depuis l'interface web, YunoHost créé automatiquement un lien symbolique de l'archive créée vers son dossier par défaut.
- `ynh_backup.scheduled_[hour|minute|weekday|month]`: modifie la planification de la tâche cron. Par défaut, elle se déclenchera tous les jours de l'année à 3 heure du matin. Pour plus d'informations concernant les réglages horaires cron, cet outil peut être utile : <https://crontab.guru/>.
- `ynh_backup.system` : Désactivez la sauvegarde du système YunoHost en mettant la valeur à `False`, la valeur par défaut est à `True`.
- `ynh_backup.apps` : Désactivez la sauvegarde des applications YunoHost en mettant la valeur à `False`, la valeur par défaut est à `True`.
- `ynh_backup.number_days_to_keep` : Détermine le nombre de jours à garder pour le système de purge, la valeur par défaut est 2.
- ⚠️ Attention, à partir du moment où vous activez la fonctionnalité de sauvegarde locale `ynh_backup.scheduled`, vous ne pouvez pas désactiver les sauvegardes système **et** applications. Si vous mettez `ynh_backup.system` **et** `ynh_backup.apps` à `False`, le rôle tombera en erreur.

### Sauvegardes distantes avec BorgBackup

- Les sauvegardes avec [BorgBackup](https://borgbackup.readthedocs.io/en/stable/) et [Borgmatic](https://github.com/witten/borgmatic) : Grâce au rôle Ansible `m3nu.ansible_role_borgbackup` nous pouvons automatiser le processus d'installation et de configuration de BorgBackup sur un serveur YunoHost. Les sauvegardes Borg sont accessibles sur un dépôt Borg local ou distant. Plus d'info sur ce rôle [ici](https://github.com/borgbase/ansible-role-borgbackup)

```yml
ynh_borg_backup_scheduled: True
borg_source_directories:
  - "/data/yunohost"
borg_repository: "/data/backup/live"
borg_encryption_passphrase: "PLEASECHANGEME"
borgmatic_config_name: "borgmatic_ynh_config"
borgmatic_cron_name: "borgmatic_ynh_cron"
borg_retention_policy:
  keep_daily: "4"
ynh_borg_backup_remote_repo: True
borg_ssh_keys_src: "files/prd/ssh_keys/ynh_ed25519.vault"
borg_ssh_keys_dest: "/home/debian/.ssh/ynh_ed25519"
ynh_ssh_borg_command: "ssh_command: ssh -p 7410 -o StrictHostKeychecking=no -i {{ borg_ssh_keys_dest }}"
```

- `ynh_borg_backup_scheduled` : Active / désactive la fonctionnalité de sauvegarde avec BorgBackup.
- `ynh_borg_backup_remote_repo` : Active / désactive la fonctionnalité de sauvegarde sur un dépôt distant BorgBackup (tâches liées à la mise en place des clés SSH). Si vous activez cette fonctionnalité, vous aurez besoin d'utiliser les variables `borg_ssh_keys_src` et `borg_ssh_keys_dest`.
- `borg_source_directories` : Liste des dossiers source à sauvegarder. Par défaut, il s'agit du dossier contenant toutes les données YunoHost (configuration, applications).
- `borg_repository` : Chemin complet vers le dépôt Borg. Possibilité de donner une liste de dépôts pour sauvegarder les données dans plusieurs endroits. Par défaut, il s'agit du dépôt `/data/backup/live`.
- `borg_encryption_passphrase` : **Obligatoire**, mot de passe à utiliser pour la clé de chiffrement du dépôt Borg.
- `borgmatic_config_name` : **Optionnel**, nom du fichier de configuration Borgmatic.
- `borgmatic_cron_name` : **Optionnel**, nom du fichier de tâche cron.
- `borg_retention_policy.keep_[hourly|daily|weekly|monthly]` : Permet de régler finement le nombre d'archives récentes que le dépôt doit garder.
- `borg_ssh_keys_src` : Chemin où se trouve le couple clé publique / privée SSH sur l'hôte Ansible. Pensez à utiliser [Ansible Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html) pour protéger vos clés SSH.
- `borg_ssh_keys_dest` : Chemin où va être copié la paire de clés SSH sur le serveur YunoHost.
- `ynh_ssh_borg_command`: **Optionnel**, commande SSH personnalisée lors de l'utilisation de Borg sur un dépôt distant.

N'hésitez pas à regarder les variables disponibles dans le [rôle](https://github.com/borgbase/ansible-role-borgbackup).

## Dépendances

Le rôle `m3nu.ansible_role_borgbackup` sera installé sur la machine exécutant Ansible pour que les tâches liées à Borg fonctionnent. Un fichier `requirements.yml` est à la racine du rôle et va télécharger le rôle (par défaut vers `~/.ansible/roles`).

## Exemple de Playbook

```yml
---
- name: Configure YunoHost backups
  hosts: all
  become: True

  roles:
    - lydra.yunohost.ynh_backup
```

## License

[![ansible-yunohost Copyright 2021 Lydra](https://www.gnu.org/graphics/gplv3-with-text-136x68.png)](https://choosealicense.com/licenses/gpl-3.0/)

**ansible-yunohost** est maintenu par [Lydra](https://lydra.fr/) et publié sous la licence GPL3.
