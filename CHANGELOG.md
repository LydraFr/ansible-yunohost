# lydra.yunohost Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html),
and the commits message folow the [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/).
## [[1.0.2] - 2022-06-22]

### Added

- In role ynh_setup:
  - New symbolic links have been added in `/defaults/main.yml`. You can now define symbolic links for `/usr/share/yunohost`, `/home/yunohost.backup/archives` and `/home/yunohost.app`.

### Changed

- In role ynh_setup:
  - README.md and README-FR.md have been updated to explain more about the new symbolic links.

## [[1.0.1] - 2022-06-02]

### Added

- Our Ansible collection is now available on Ansible-Galaxy!

### Changed

- This version provides a minor fix to hypertext links in README files.

## [[1.0.0] - 2022-05-23](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/releases/1.0.0)

### Added

- As we grew in terms of added features we have decided to transform this role into a collection.
- You can now define symoblic links for `/etc/yunohost/` and `/var/www/`.
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
