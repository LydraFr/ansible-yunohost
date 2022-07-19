# Ansible Role: YunoHost

[🇫🇷 French version](README-FR.md)

Deploy [YunoHost](https://yunohost.org/#/) with Ansible!

## Requirements

YunoHost needs to be installed on your server.

## Role Variables

The default variables are available in `default/main.yml` however it is possible to override them according to your needs.
We have integrated two different backup systems to this YunoHost role:

- YunoHost native local backups
- Remote backups with a [BorgBackup repository](https://borgbackup.readthedocs.io/en/stable/)

### YunoHost native local backups

YunoHost provides its own native backup system. It is able to back up YunoHost configuration, mails (if YunoHost is used as a mail server) and applications installed on YunoHost. It is possible to create and restore backups from the web administration interface as well as from the command line in SSH (`yunohost backup`). Backups are available locally, and we have automated the triggering of these backups. More info [here](https://yunohost.org/en/backup).

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

- `ynh_backup.scheduled`: to enable the YunoHost applications backup feature, set the value to `True`.
- `ynh_backup.directory`: the default backup folder is `/home/yunohost.backup/archives` however you can choose to save the backups in another folder with this variable. In this case, in order to be able to restore the backups from the web interface, YunoHost automatically creates a symbolic link from the created archive to its default folder.
- `ynh_backup.scheduled_[hour|minute|weekday|month]`: modifies the scheduling of the cron task. By default, it will run every day of the year at 1am. For more information about cron time settings, this tool can be useful: <https://crontab.guru/>.
- `ynh_backup.system`: **mandatory**. Enables automatic backup of the YunoHost system by setting the value to `True`.
- `ynh_backup.apps`: **mandatory**. Enables automatic backup of YunoHost applications by setting the value to `True`.

### remote backups with YunoHost BorgBackup

- Backups with [BorgBackup](https://borgbackup.readthedocs.io/en/stable/) and [Borgmatic](https://github.com/witten/borgmatic): Thanks to the Ansible role `m3nu.ansible_role_borgbackup` we can automate the installation and configuration process of Borg Backup on a YunoHost server. Borg backups are accessible on a local or a remote Borg repository. More info about this role [here](https://github.com/borgbase/ansible-role-borgbackup).

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
```

- `ynh_borg_backup_scheduled`: Enable / disable the backup feature with BorgBackup.
- `ynh_borg_backup_remote_repo`: Enable / disable the backup functionality on a BorgBackup remote repository (tasks related to SSH keys setup). If you enable this feature, then you will need to use `borg_ssh_keys_src` and `borg_ssh_keys_dest` variables. 
- `borg_source_directories`: List of source folders to back up. By default, this is the folder containing all YunoHost data (configuration, applications).
- `borg_repository`: Full path to the Borg repository. Possibility to give a list of repositories to save data in several places.
- `borg_encryption_passphrase` : **Mandatory**, password to use for the Borg repository encryption key.
- `borgmatic_config_name`: **Optional**, name of the Borgmatic configuration file.
- `borgmatic_cron_name`: **Optional**, name of the cron task file.
- `borg_retention_policy.keep_[hourly|daily|weekly|monthly]`: Allows you to fine-tune the number of recent archives the repository should keep.
- `borg_ssh_keys_src`: Path to the SSH public/private key pair on the Ansible host. Consider using [Ansible Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html) to protect your SSH keys.
- `borg_ssh_keys_dest`: Path where the SSH key pair will be copied to the YunoHost server.
- `ynh_ssh_borg_command`: **Optional**, custom SSH command run when using Borg on a remote repository.

Feel free to look at the variables available in the [role](https://github.com/borgbase/ansible-role-borgbackup).

## Dependencies

The `m3nu.ansible_role_borgbackup` role will be installed on the machine running Ansible for Borg-related tasks to work. A `requirements.yml` file is in the root of the role and will download the role (by default to `~/.ansible/roles`).

## Example Playbook

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

**ansible-yunohost** is maintained by [Lydra](https://lydra.fr/) and released under the GPL3 license.
