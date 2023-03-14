# lydra.yunohost Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html),
and the commits message follow the [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/).

## [[1.1.4] - 2023-03-14](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/releases/1.1.4)

### Added

- [feat(ynh_config): add the possibility to change SSH Port](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/issues/63)
- [refactor(ynh_config): use dict to store all settings](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/issues/95)

### Fixed

- [fix(ynh_backup): fix purging system for YNH backups](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/issues/82)
- [fix: collection badges](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/issues/50)
- [fix(ynh_setup): change user first name / last name to full name](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/issues/96)
- [fix(ynh_config): SMTP enable](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/issues/97)

## [[1.1.3] - 2022-12-16](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/releases/1.1.3)

### Added

- In role `ynh_backup`:
  - The default version of Ansible Borg role is now v.0.9.4. With this new version, you have the possibility to fix which version of Borg and Borgmatic you want to use but also to choose how does it install those packages. You can check the releases of the role [here](https://github.com/borgbase/ansible-role-borgbackup/releases).
  - Updated the readme files.

## [[1.1.2] - 2022-12-07](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/releases/1.1.2)

### Added

- In role `ynh_backup`:
  - Added Ansible variable `restic_version`. The default version of Restic is 0.14.0, but it can easily be changed by overriding the default value. You can check the releases of Restic [here](https://github.com/restic/restic/releases).
  - Updated the readme files.

- In role `ynh_apps`:
  - Updated readme for details about API calls.

## [[1.1.1] - 2022-09-05](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/releases/1.1.1)

### Added

- In role `ynh_backup`:
  - Added Ansible variable `m3nu_ansible_role_borgbackup_version`. The default version of the role is v0.9.3, but it can easily be changed by overriding the default value. You can check the releases of the role [here](https://github.com/borgbase/ansible-role-borgbackup).
  - Updated the readme files.

- In general readme:
  - Added restic tag.

### Fixed

- In role `ynh_backup`:
  - Ansible role Borg has been updated to v0.9.3 due to a bug that I discovered. More info [here](https://github.com/borgbase/ansible-role-borgbackup/issues/101).

## [[1.1.0] - 2022-08-30](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/releases/1.1.0)

### Added

- In role `ynh_backup`:
  - You now have the possibility to use BorgBackup with an [extra Ansible role](https://github.com/borgbase/ansible-role-borgbackup) as well as [Restic](https://github.com/roles-ansible/ansible_role_restic). These are built-in into this role, which means that once configured correctly, Ansible will download the chosen roles and will trigger their tasks.
  - In order to use [Restic Ansible role](https://github.com/roles-ansible/ansible_role_restic), you need to install the pip package jmespath. For developers of the collection, it can be installed from the provided Pipfile using the following command: `pipenv install`.

### Changed

- In roles:
  - Changed various tasks-related tags.

- In role `ynh_backup`:
  - The `ynh_backup.sh` script now has a pruning function. By default, all local backups older than two days old are automatically deleted when a new backup is being created by the cron task.
  - Improved README for the local backups.

### Fixed

- In role `ynh_backup`:
  - Only one of `ynh_backup.system` or `ynh_backup.apps` is required. If the user puts var `ynh_backup.system` and `ynh_backup.apps` to False the role throws an error.

## [[1.0.4] - 2022-06-27](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/releases/1.0.4)

### Added

- In role `ynh_setup`:
  - New symbolic link created by default `/home/yunohost/backup` failed to get created. Added a new task so that the folder is created beforehand and then force the creation of the symbolic link.

### Fixed

- In role `ynh_backup`:
  - A WIP version of a dev branch has been uploaded in version 1.0.3. Therefore, `lydra.yunohost` v1.0.3 is malfunctioning and shouldn't be used. This new version contains the right source files from version 1.0.3.

## [[1.0.3] - 2022-06-24]

> ⚠️ Warning: DO NOT USE THIS VERSION, and go to version 1.0.4. Thanks.

## [[1.0.2] - 2022-06-22](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/releases/1.0.2)

### Added

- In role `ynh_setup`:
  - New symbolic links have been added in `/defaults/main.yml`. You can now define symbolic links for `/usr/share/yunohost`, `/home/yunohost.backup/archives` and `/home/yunohost.app`.

### Changed

- In role `ynh_setup`:
  - README.md and README-FR.md have been updated to explain more about the new symbolic links.

## [[1.0.1] - 2022-06-02](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/releases/1.0.1)

### Added

- Our Ansible collection is now available on Ansible-Galaxy!

### Changed

- This version provides a minor fix to hypertext links in README files.

## [[1.0.0] - 2022-05-23](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/releases/1.0.0)

### Added

- As we grew in terms of added features, we have decided to transform this role into a collection.
- You can now define symbolic links for `/etc/yunohost/` and `/var/www/`.
- You can now create backups for Yunohost core and apps using `ynh_backup` role.

### Changed

- This role is now divided into four roles: `ynh_setup`, `ynh_config`, `ynh_apps`, `ynh_backup`. Each role has its own README so you can easily navigate between them and understand their purpose.

### Deprecated

- Be careful when you upgrade because of the modularity of the different roles. Don't hesitate to read the latest REDME for an example on how to incorporate the roles into a playbook.

## [[0.2.0] - 2022-03-31](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/releases/0.2.0)

### Added

- Creation of the CHANGELOG.
- CI: Ansible galaxy lint

### Deprecated

- The v1.0.0 will transform this role into an ansible collection.
The roles will be more modular. Be careful when you upgrade to v1.0.0.

## [0.1.3] - 2021-11-09

### Added

- Two new tasks have been added for post-install apps feature. During Yunohost application setup you can now add additionnal scripts / configuration files which will be transferred to your Yunohost instance and run if necessary.

### Changed

- README explains how to use apps post-install feature.

## [0.1.2] - 2021-10-27

### Added

- SMTP relay settings in Yunohost core can now be changed. This is a new task called `SMTP`.

### Changed

- README explains how to change SMTP relay values using specific variables.

## [0.1.1] - 2021-10-21 [YANKED]

### Fixed

- fix default folder name into "defaults" to respect Ansible file architecture.

## [0.1.0] - 2021-10-21

### Added

- The first release of the lydra.yunohost role. This role is a fork of two former Ansible roles : <https://github.com/TheRojam/ansible-yunohost> and <https://github.com/e-lie/ansible-yunohost>.
- CI: ansible lint and syntax check
- README is present in French and English as well as License info.
