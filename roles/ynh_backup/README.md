# Ansible Role: Yunohost

[🇫🇷 French version](README-FR.md)

Deploy [Yunohost](https://yunohost.org/#/) with Ansible!

## Requirements

Yunohost needs to be installed on your server.

## Role Variables

Default variables are available in `default/main.yml` however it is necessary to override them according to your needs for ...

### Backups management

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

The _backup_ task will allow to backup Yunohost applications and their data by setting up a cron job. This backup uses the one provided by [Yunohost](https://yunohost.org/fr/backup) and it is local to the server.

- `ynh_backup.scheduled`: to enable the Yunohost applications backup feature, set the value to `True`.
- `ynh_backup.directory`: the default backup folder is `/home/yunohost.backup/archives` however you can choose to save the backups in another folder with this variable. In this case, in order to be able to restore the backups from the web interface, Yunohost automatically creates a symbolic link from the created archive to its default folder.
- `ynh_backup.scheduled_[hour|minute|weekday|month]`: modifies the scheduling of the cron task. By default it will run every day of the year at 1am. For more information about cron time settings, this tool can be useful: <https://crontab.guru/>.
- `ynh_backup.system` : is mandatory. Enables automatic backup of the Yunohost system by setting the value to `True`.
- `ynh_backup.apps` : is mandatory. Enables automatic backup of Yunohost applications by setting the value to `True`.

## Dependencies

None.

## Example Playbook

```yml
---
- name: Configure Yunohost backups
  hosts: all
  become: True
  collections:
    - lydra.yunohost
  roles:
    - ynh_backup
```

## License

[![ansible-yunohost Copyright 2021 Lydra](https://www.gnu.org/graphics/gplv3-with-text-136x68.png)](https://choosealicense.com/licenses/gpl-3.0/)

**ansible-yunohost** is maintained by [Lydra](https://lydra.fr/) and released under the GPL3 license.
