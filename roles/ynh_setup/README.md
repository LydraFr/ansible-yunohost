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
```

- `ynh_install_script_url` downloads official Yunohost script for installing Yunohost packages. Yunohost is only available on Debian 10.
- `ynh_admin_password` is the password used to access to the server's administration interface.

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
     firstname: Jane
     lastname: Doe
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
  collections:
    - lydra.yunohost
  pre_tasks:
    - name: Update all packages and index
      ansible.builtin.apt:
        upgrade: dist
        update_cache: yes
    
  roles:
    - ynh_setup
    - ynh_apps
    - ynh_config
    - ynh_backup
```

## License

[![ansible-yunohost Copyright 2021 Lydra](https://www.gnu.org/graphics/gplv3-with-text-136x68.png)](https://choosealicense.com/licenses/gpl-3.0/)

**ansible-yunohost** is maintained by [Lydra](https://lydra.fr/) and released under the GPL3 license.
