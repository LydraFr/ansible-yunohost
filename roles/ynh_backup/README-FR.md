# Rôle Ansible : Yunohost Backup

[🇬🇧 English version](README.md)

Sauvegardez [Yunohost](https://yunohost.org/#/) avec Ansible !

## Prérequis

Yunohost doit déjà être installé sur votre serveur.

## Variables du rôle

Les variables par défaut sont disponibles dans `default/main.yml` cependant il est possible de les surcharger selon vos besoins en ...

### Gestion des sauvegardes

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
  src_script: "templates/ynh_backup.sh.j2"
  dest_script: "/usr/bin"
```

La tâche _backup_ va permettre de sauvegarder les applications Yunohost ainsi que leurs données grâce à la mise en place d'une tâche cron. Cette sauvegarde utilise celle fournie par [Yunohost](https://yunohost.org/fr/backup) et elle est locale au serveur.

- `ynh_backup.scheduled` : active la fonctionnalité de sauvegarde des applications Yunohost, mettez la valeur à `True`.
- `ynh_backup.directory` : le dossier de sauvegarde par défaut est `/home/yunohost.backup/archives` cependant vous pouvez tout à fait choisir de sauvegarder les backups dans un autre dossier grâce à cette variable. Dans ce cas, de manière à pouvoir restaurer les backups depuis l'interface web, Yunohost créé automatiquement un lien symbolique de l'archive créée vers son dossier par défaut.
- `ynh_backup.scheduled_[hour|minute|weekday|month]`: modifie la planification de la tâche cron. Par défaut elle se déclenchera tous les jours de l'année à 1 heure du matin. Pour plus d'informations concernant les réglages horaires cron, cet outil peut être utile : <https://crontab.guru/>.
- `ynh_backup.system` : est obligatoire. Activez la sauvegarde du système Yunohost en mettant la valeur à `True`.
- `ynh_backup.apps` : est obligatoire. Activez la sauvegarde des applications Yunohost en mettant la valeur à `True`.

## Dépendances

Aucune.

## Exemple de Playbook

```yml
---
- name: Configure Yunohost backups
  hosts: all
  become: True

  roles:
    - lydra.yunohost.ynh_backup
```

## License

[![ansible-yunohost Copyright 2021 Lydra](https://www.gnu.org/graphics/gplv3-with-text-136x68.png)](https://choosealicense.com/licenses/gpl-3.0/)

**ansible-yunohost** est maintenu par [Lydra](https://lydra.fr/) et publié sous la licence GPL3.
