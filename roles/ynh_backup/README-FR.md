# Rôle Ansible : YunoHost Backup

[🇬🇧 English version](README.md)

Sauvegardez [YunoHost](https://yunohost.org/#/) avec Ansible !

## Prérequis

YunoHost doit déjà être installé sur votre serveur.

## Variables du rôle

Les variables par défaut sont disponibles dans `default/main.yml` cependant il est possible de les surcharger selon vos besoins.
Nous avons intégré trois systèmes de sauvegardes différents à ce rôle YunoHost :

- sauvegardes natives YunoHost en local
- sauvegardes à distance avec un [dépôt BorgBackup](https://borgbackup.readthedocs.io/en/stable/)
- sauvegardes à distance avec un [dépôt Restic](https://restic.readthedocs.io/en/stable/)

### Sauvegardes natives YunoHost locales

- Les backups locaux natifs à YunoHost : YunoHost propose son propre système de sauvegardes natif. Il est capable de sauvegarder la configuration YunoHost, les mails (si YunoHost est utilisé en tant que serveur de mails) et les applications installées sur YunoHost. Il est possible de créer et restaurer les sauvegardes depuis l'interface d'administration web ainsi que la ligne de commande en SSH (`yunohost backup`). Les sauvegardes sont disponibles en local et nous avons automatisé le déclenchement de ces sauvegardes par une tâche cron. Plus d'infos [ici](https://yunohost.org/fr/backup).

```yml
ynh_backup:
  scheduled:         True
  directory:         "/data/backup/local_ynh_backups"
  scheduled_hour:    "*"
  scheduled_minute:  "*/30"
  scheduled_weekday: "*"
  scheduled_month:   "*"
  system:            True
  apps:              True
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

- Les sauvegardes avec [BorgBackup](https://borgbackup.readthedocs.io/en/stable/) et [Borgmatic](https://github.com/witten/borgmatic) : Grâce au rôle Ansible `m3nu.ansible_role_borgbackup`, nous pouvons automatiser le processus d'installation et de configuration de BorgBackup sur un serveur YunoHost. Les sauvegardes Borg sont accessibles sur un dépôt Borg local ou distant. Plus d'info sur ce rôle [ici](https://github.com/borgbase/ansible-role-borgbackup)

```yml
ynh_borg_backup_scheduled:            True
m3nu_ansible_role_borgbackup_version: "v0.9.3"
borg_source_directories:              "{{ ynh_backup.directory }}"
borg_repository:                      "/data/backup/borg_repository"
borg_encryption_passphrase:           "PLEASECHANGEME"
borgmatic_config_name:                "borgmatic_ynh_config"
borgmatic_cron_name:                  "borgmatic_ynh_cron"
borg_retention_policy:
  keep_daily:                         "4"
ynh_borg_backup_remote_repo:          True
borg_ssh_keys_src:                    "files/prd/ssh_keys/ynh_ed25519.vault"
borg_ssh_keys_dest:                   "/home/debian/.ssh/ynh_ed25519"
ynh_ssh_borg_command:                 "ssh_command: ssh -p 7410 -o StrictHostKeychecking=no -i {{ borg_ssh_keys_dest }}"
```

- `ynh_borg_backup_scheduled` : Active / désactive la fonctionnalité de sauvegarde avec BorgBackup.
- `m3nu_ansible_role_borgbackup_version` : Vous permet de spécifier la version du rôle Ansible Borg Backup que vous souhaitez utiliser. La version par défaut du rôle est v0.9.3 mais vous pouvez vérifier les versions du rôle [ici](https://github.com/borgbase/ansible-role-borgbackup).
- `ynh_borg_backup_remote_repo` : Active / désactive la fonctionnalité de sauvegarde sur un dépôt distant BorgBackup (tâches liées à la mise en place des clés SSH). Si vous activez cette fonctionnalité, vous aurez besoin d'utiliser les variables `borg_ssh_keys_src` et `borg_ssh_keys_dest`.
- `borg_source_directories` : Liste des dossiers source à sauvegarder. Par défaut, il s'agit du dossier qui contient les sauvegardes faites par YunoHost.
- `borg_repository` : Chemin complet vers le dépôt Borg. Possibilité de donner une liste de dépôts pour sauvegarder les données dans plusieurs endroits.
- `borg_encryption_passphrase` : **Obligatoire**, mot de passe à utiliser pour la clé de chiffrement du dépôt Borg.
- `borgmatic_config_name` : **Optionnel**, nom du fichier de configuration Borgmatic.
- `borgmatic_cron_name` : **Optionnel**, nom du fichier de tâche cron.
- `borg_retention_policy.keep_[hourly|daily|weekly|monthly]` : Permet de régler finement le nombre d'archives récentes que le dépôt doit garder.
- `borg_ssh_keys_src` : Chemin où se trouve le couple clé publique / privée SSH sur l'hôte Ansible. Pensez à utiliser [Ansible Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html) pour protéger vos clés SSH.
- `borg_ssh_keys_dest` : Chemin où va être copié la paire de clés SSH sur le serveur YunoHost.
- `ynh_ssh_borg_command`: **Optionnel**, commande SSH personnalisée lors de l'utilisation de Borg sur un dépôt distant.

N'hésitez pas à regarder les variables disponibles dans le [rôle](https://github.com/borgbase/ansible-role-borgbackup).

### Sauvegardes distantes avec Restic

- Les sauvegardes avec [Restic](https://restic.net/) : Grâce au rôle Ansible `do1jlr.restic`, nous pouvons automatiser le processus d'installation et de configuration de Restic sur un serveur YunoHost. Les sauvegardes Restic peuvent être effectuées sur un dépôt Restic en local ou à distance (dépôt compatible stockage objet S3). Plus d'info sur ce rôle [ici](https://github.com/roles-ansible/ansible_role_restic).

⚠️ Attention, pour pouvoir utiliser le rôle Ansible `do1jlr.restic`, vous devez avoir les [paquets suivants](https://github.com/roles-ansible/ansible_role_restic#requirements) installés sur la machine qui exécute Ansible :

- `bzip2` (binaire disponible sur la plupart des systèmes Linux).
- `jmespath` (paquet python, installable avec pip).

```yml
ynh_restic_backup_scheduled: True
restic_create_schedule:      True
restic_keep_time:            "0y2m0d0h"

restic_repos:
  s3_ynh_restic_repo:
    location:               "s3:s3.fr-par.scw.cloud/dummy_bucket_name"
    password:               "dummy_restic_repo_password"
    aws_access_key:         "dummy_access_key"
    aws_secret_access_key:  "dummy_secret_access_key"
    aws_default_region:     "fr-par"
    init:                   True

restic_backups:
  YunoHost_remote:
    name:                   "remote_ynh_restic"
    repo:                   "s3_ynh_restic_repo"
    src:                    "{{ ynh_backup.directory }}"
    tags:
      - yunohost
      - remote
    keep_within:            "{{ restic_keep_time }}"
    scheduled: True
    schedule_hour: 1
    schedule_minute: 0
```

- `ynh_restic_backup_scheduled` : Active / désactive la fonctionnalité de sauvegarde avec Restic.
- `restic_keep_time` : Permet de régler finement la période de temps durant laquelle les snapshots doivent être conservés. la valeur par défaut est de 1 mois `0y1m0d0h`.
- `restic_repos`: Restic conserve les données dans des dépôts. Vous devez spécifier au moins un dépôt pour utiliser ce rôle. Un dépôt doit comporter les variables suivantes :
  - `location` : **Obligatoire**, le chemin vers le dépôt. Ça peut être un chemin local (par exemple `/data/backup`) ou un chemin vers un bucket S3 (voir l'exemple ci-dessus).
  - `password`: **Obligatoire**, mot de passe à utiliser pour le dépôt Restic.
  - `init` : Décrit si le dépôt doit être initialisé ou pas. Utilisez `false` si vous utilisez un dépôt Restic déjà initialisé.
  - ⚠️ Attention, s'il s'agit d'un dépôt stockage objet S3, vous devez fournir des variables supplémentaires pour que Restic puisse s'authentifier et accéder au fournisseur cloud (voir l'exemple ci-dessus).
- `restic_backups`: Un backup précise un répertoire ou un fichier à sauvegarder. Il comporte les variables suivantes :
  - `name` : **Obligatoire**, ce nom de cette sauvegarde. Il doit être unique et est utilisé avec le __pruning__  et la planification.
  - `repo` : **Obligatoire**, le nom du dépôt où sauvegarder les snapshots. Ce dépôt devra avoir été déclaré au préalable (voir plus haut pour les variables à renseigner).
  - `src` : **Obligatoire**, le répertoire ou le fichier à sauvegarder.
  - `tags` : **Optionnel**, liste de tags pour ajouter des informations.
  - `keep-within` : Peut être utilisé en relation avec la variable `restic_keep_time` (dans ce cas, gardez la variable telle quelle) ou alors vous pouvez choisir une période de rétention pour chaque sauvegarde.
  - `scheduled` : Utilisez `true` si vous souhaitez mettre en place une tâche cron pour le déclenchement d'une sauvegarde à intervalle régulier. En corrélation avec `restic_create_schedule: true` (les deux doivent être à `true` pour que la tâche soit créé).
  - `schedule_[minute|hour|weekday|month]` : Permet de régler finement la planification du déclenchement de la tâche cron.

N'hésitez pas à regarder les variables disponibles dans le [rôle](https://github.com/roles-ansible/ansible_role_restic).

## Dépendances

Le rôle `m3nu.ansible_role_borgbackup` et `do1jlr.restic` seront installés sur la machine exécutant Ansible pour que les tâches liées à Borg et Restic fonctionnent.

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
