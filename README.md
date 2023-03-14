[![](https://img.shields.io/liberapay/receives/cchaudier.svg?logo=liberapay)](https://liberapay.com/cchaudier/donate)
[![](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/badges/main/pipeline.svg)](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/pipelines)
[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](http://www.gnu.org/licenses/gpl-3.0)
[![Ansible Collection](https://img.shields.io/ansible/collection/1838)](https://galaxy.ansible.com/lydra/yunohost)
[![GitHub last commit](https://img.shields.io/github/last-commit/LydraFr/ansible-yunohost)](https://github.com/LydraFr/ansible-yunohost)
[![GitHub Release Date](https://img.shields.io/github/release-date/LydraFr/ansible-yunohost)](https://github.com/LydraFr/ansible-yunohost)
[![GitHub Repo stars](https://img.shields.io/github/stars/LydraFr/ansible-yunohost?style=social)](https://github.com/LydraFr/ansible-yunohost)

# Ansible Collection - `lydra.yunohost`

[🇫🇷 French version](README-FR.md) (only on [GitHub](https://github.com/LydraFr/ansible-yunohost/blob/main/README-FR.md))

This collection aims at installing, configuring and backing up [Yunohost](https://yunohost.org/#/).
As this is an independent collection, it can be released on its own release cadence. Moreover, the roles it contains are updated independently.

# Prerequisites

Your server must be Debian-Buster based and Yunohost shouldn't be already installed.

## Collection contents

### Roles

- [`lydra.yunohost.ynh_setup`](roles/ynh_setup/README.md): This role prepares servers with Debian-Buster-based to run Yunohost. It sets up Yunohost with its initial settings and domains, users and apps of your choice.
- [`lydra.yunohost.ynh_apps`](roles/ynh_apps/README.md): This role installs Yunohost apps of your choice and can perform post-install tasks.
- [`lydra.yunohost.ynh_config`](roles/ynh_config/README.md): This role configures various Yunohost services (SMTP relay, auto updates).
- [`lydra.yunohost.ynh_backup`](roles/ynh_backup/README.md): This role manages the configuration of backups.

## Role Tags

These tags are applicable to roles.

|tags|comment|
|----|-------|
|pkg|Tasks that install packages.|
|linux|Tasks related to Linux.|
|yunohost|Tasks specific to Yunohost itself (setup or configuration).|
|users|Tasks specific to users in Yunohost.|
|domains|Tasks specific to domains linked to Yunohost.|
|apps|Tasks specific to Yunohost apps.|
|update|Tasks related to Yunohost update settings.|
|smtp|Tasks related to Yunohost smtp relay settings.|
|settings|Tasks related to Yunohost settings.|
|backup|Tasks related to local Yunohost backups.|
|borg|Tasks related to backups with BorgBackup.|
|restic|Tasks related to backups with Restic.|

## License

[![ansible-yunohost Copyright 2021 Lydra](https://www.gnu.org/graphics/gplv3-with-text-136x68.png)](https://choosealicense.com/licenses/gpl-3.0/)

**ansible-yunohost** is maintained by [Lydra](https://lydra.fr/) and released under the GPL3 license.
