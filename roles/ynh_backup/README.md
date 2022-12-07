# Ansible Role: YunoHost

[🇫🇷 French version](README-FR.md)

Deploy [YunoHost](https://yunohost.org/#/) with Ansible!

## Requirements

YunoHost needs to be installed on your server.

## Role Variables

The default variables are available in `default/main.yml` however it is possible to override them according to your needs.
We have integrated three different backup systems to this YunoHost role:

- YunoHost native local backups
- Remote backups with a [BorgBackup repository](https://borgbackup.readthedocs.io/en/stable/)
- Remote backups with a [Restic repository](https://restic.readthedocs.io/en/stable/)

### YunoHost native local backups

YunoHost provides its own native backup system. It is able to back up YunoHost configuration, mails (if YunoHost is used as a mail server) and applications installed on YunoHost. It is possible to create and restore backups from the web administration interface as well as from the command line in SSH (`yunohost backup`). Backups are available locally, and we have automated the triggering of these backups. More info [here](https://yunohost.org/en/backup).

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

- `ynh_backup.scheduled`: Enable the YunoHost applications backup feature by setting the value to `True`.
- `ynh_backup.directory`: the default backup folder is `/home/yunohost.backup/archives`. You can choose to save the backups in another folder with this variable. In this case, in order to be able to restore the backups from the web interface, YunoHost automatically creates a symbolic link from the created archive to its default folder.
- `ynh_backup.scheduled_[hour|minute|weekday|month]`: modifies the scheduling of the cron task. By default, it will run every day of the year at 3am. For more information about cron time settings, this tool can be useful: <https://crontab.guru/>.
- `ynh_backup.system`: Disable YunoHost system backup by setting the value to `False`, the default value is `True`.
- `ynh_backup.apps`: Disable backup of YunoHost applications by setting the value to `False`, the default is `True`.
- `ynh_backup.number_days_to_keep`: Determines the number of days to keep for the purging system, the default is 2.
- ⚠️ Beware, once you enable the local backup feature `ynh_backup.scheduled`, you cannot disable system **and** application backups. If you set `ynh_backup.system` **and** `ynh_backup.apps` to `False`, the role will fail.

### remote backups with YunoHost BorgBackup

- Backups with [BorgBackup](https://borgbackup.readthedocs.io/en/stable/) and [Borgmatic](https://github.com/witten/borgmatic): Thanks to the Ansible role `m3nu.ansible_role_borgbackup` we can automate the installation and configuration process of Borg Backup on a YunoHost server. Borg backups are accessible on a local or a remote Borg repository. More info about this role [here](https://github.com/borgbase/ansible-role-borgbackup).

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

- `ynh_borg_backup_scheduled`: Enable / disable the backup feature with BorgBackup.
- `m3nu_ansible_role_borgbackup_version`: Allows you to specify which version of the Borg Backup Ansible role you want to use. The default version of the role is v0.9.3 but you can check the releases of the role [here](https://github.com/borgbase/ansible-role-borgbackup).
- `ynh_borg_backup_remote_repo`: Enable / disable the backup functionality on a BorgBackup remote repository (tasks related to SSH keys setup). If you enable this feature, then you will need to use `borg_ssh_keys_src` and `borg_ssh_keys_dest` variables.
- `borg_source_directories`: List of source folders to back up. By default, this is the folder in which YunoHost local backups are located.
- `borg_repository`: Full path to the Borg repository. Possibility to give a list of repositories to save data in several places.
- `borg_encryption_passphrase` : **Mandatory**, password to use for the Borg repository encryption key.
- `borgmatic_config_name`: **Optional**, name of the Borgmatic configuration file.
- `borgmatic_cron_name`: **Optional**, name of the cron task file.
- `borg_retention_policy.keep_[hourly|daily|weekly|monthly]`: Allows you to fine-tune the number of recent archives the repository should keep.
- `borg_ssh_keys_src`: Path to the SSH public/private key pair on the Ansible host. Consider using [Ansible Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html) to protect your SSH keys.
- `borg_ssh_keys_dest`: Path where the SSH key pair will be copied to the YunoHost server.
- `ynh_ssh_borg_command`: **Optional**, custom SSH command run when using Borg on a remote repository.

Feel free to look at the variables available in the [role](https://github.com/borgbase/ansible-role-borgbackup).

### remote backups with YunoHost Restic

- Backups with [Restic](https://restic.net/): Thanks to the Ansible role `do1jlr.restic` we can automate the installation and configuration process of Restic on a YunoHost server. Restic backups can be done on a local or a remote Restic repository and compatible with S3 object storage. More info about this role [here](https://github.com/roles-ansible/ansible_role_restic).

⚠️ Be careful, in order to use the Ansible role `do1jlr.restic`, you must have the following [packages](https://github.com/roles-ansible/ansible_role_restic#requirements) installed on the machine running Ansible:

- `bzip2` (binary available on most Linux systems).
- `jmespath` (python package, can be installed through pip).

```yml
ynh_restic_backup_scheduled: True
restic_create_schedule:      True
restic_keep_time:            "0y2m0d0h"
restic_version:              "0.14.0"

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

- `ynh_restic_backup_scheduled`: Enable / disable the backup feature with Restic.
- `restic_keep_time`: Allows to fine tune the time period during which snapshots should be kept. The default value is 1 month `0y1m0d0h`.
- `restic_version`: Allows you to specify the version of Restic you want to use. The default version of the role is 0.14.0. You can check Restic versions [here](https://github.com/restic/restic/releases).
- `restic_repos`: Restic keeps data in repositories. You must specify at least one repository to use this role. A repository must have the following variables:
  - `location`: **Mandatory**, the path to the repository. This can be a local path (e.g. `/data/backup`) or a path to an S3 bucket (see example above).
  - `password`: **Mandatory**, password to use for the Restic repository.
  - `init`: Describes whether the repository should be initialized or not. Use `false` if you are using an already initialized Restic repository.
  - ⚠️ Beware, if this is an S3 object storage repository, you must provide additional variables for Restic to authenticate and access the cloud provider (see example above).
- `restic_backups`: A backup specifies a directory or file to be backed up. It has the following variables:
  - `name` : **Mandatory**, this name of this backup. It must be unique and is used with the __pruning__ and scheduling.
  - `repo`: **Mandatory**, the name of the repository where to save the snapshots. This repository should have been declared beforehand (see above for variables to fill in).
  - `src` : **Mandatory**, the directory or file to be backed up.
  - `tags`: **Optional**, list of tags to add information.
  - `keep-within`: Can be used in connection with the `restic_keep_time` variable (in this case, keep this variable as it is) or you can choose a retention period for each backup.
  - `scheduled`: Use `true` if you want to set up a cron job to trigger a backup at regular intervals. In correlation with `restic_create_schedule: true` (both need to be set to `true` for the cron task to be created).
  - `schedule_[minute|hour|weekday|month]`: Allows you to fine-tune the timing of the cron job.

Feel free to look at the variables available in the [role](https://github.com/roles-ansible/ansible_role_restic).

## Dependencies

The `m3nu.ansible_role_borgbackup` and `do1jlr.restic` roles will be installed on the machine running Ansible for Borg and Restic related tasks to work.

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
