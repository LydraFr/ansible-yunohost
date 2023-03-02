# Ansible Role: Yunohost

[🇫🇷 French version](README-FR.md)

Deploy [Yunohost](https://yunohost.org/#/) with Ansible!

## Requirements

None.

## Role Variables

Default variables are available in `default/main.yml` however it is necessary to override them according to your needs for Yunohost domains, users and apps.

### Yunohost Installation

```yml
# Debian 10 script only.
ynh_install_script_url: https://install.yunohost.org

ynh_admin_password: MYINSECUREPWD_PLZ_OVERRIDE_THIS

ynh_dir: "/data/yunohost"

ynh_data_dirs:
  - path: "{{ ynh_dir }}/etc"
    link: "/etc/yunohost"
  - path: "{{ ynh_dir }}/var"
    link: "/var/www"
  - path: "{{ ynh_dir }}/share"
    link: "/usr/share/yunohost"
  - path: "{{ ynh_dir }}/backup"
    link: "/home/yunohost.backup/archives"
  - path: "{{ ynh_dir }}/home_apps"
    link: "/home/yunohost.app"
ynh_data_dirs_enabled: True
```

- `ynh_install_script_url` The url provided downloads the official Yunohost script for installing Yunohost packages. Yunohost is only available on Debian 10.
- `ynh_admin_password` is the password used to access to the server's administration interface.

- `ynh_data_dirs.enabled`: Enables symbolic links and allows you to move YunoHost's configuration and data directories wherever you want. By default, this value is set to `True`. We use symbolic links because the `/data` folder is used by us to make _object storage_ backups.
- `ynh_data_dirs.path`: these are the directories where Yunohost configuration data and applications are stored.
- `ynh_data_dirs.link`: this is the directory where symbolic links will be made.

### Domain management

```yml
# The list of Yunohost domains.
ynh_main_domain: domain.tld
ynh_extra_domains:
  - forum.domain.tld
  - wiki.domain.tld
ynh_ignore_dyndns_server: False
```

- `ynh_main_domain` is the main domain used by the server's users to access the authentication portal. If you already own a domain name, you probably want to use it here. You can also use a domain in .nohost.me / .noho.st / .ynh.fr (more info [here](https://yunohost.org/en/install/hardware:vps_debian)).
- `ynh_extra_domains` are optional and allow you to install one app per subdomain (more info [here](https://yunohost.org/en/administrate/specific_use_cases/domains/dns_subdomains)).
- `ynh_ignore_dyndns_server` allow to register domains with a Dynamic DNS service (more info [here](https://yunohost.org/en/dns_dynamicip)).

### User management

```yml
# The list of Yunohost users.
ynh_users:
   - name: user1
     pass: MYINSECUREPWD_PLZ_OVERRIDE_THIS
     fullname: Jane DOE
     mail_domain: domain.tld
```

- `ynh_users` is the list of users to create. Each field is mandatory. Some Yunohost applications require that a user be the app administrator. He will then have the right to manage the application from the server administration interface. You can learn more about Yunohost user management [here](https://yunohost.org/en/users).

## Dependencies

None.

## Example Playbook

```yml
---
- name: Install Yunohost on Debian Server
  hosts: all
  become: True

  roles:
    - lydra.yunohost.ynh_setup
    - lydra.yunohost.ynh_apps
    - lydra.yunohost.ynh_config
    - lydra.yunohost.ynh_backup
```

## License

[![ansible-yunohost Copyright 2021 Lydra](https://www.gnu.org/graphics/gplv3-with-text-136x68.png)](https://choosealicense.com/licenses/gpl-3.0/)

**ansible-yunohost** is maintained by [Lydra](https://lydra.fr/) and released under the GPL3 license.
