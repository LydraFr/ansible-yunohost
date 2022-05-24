# Ansible Role: Yunohost

[🇫🇷 French version](README-FR.md)

Deploy [Yunohost](https://yunohost.org/#/) with Ansible!

## Requirements

None.

## Role Variables

Default variables are available in `default/main.yml` however it is necessary to override them according to your needs for Yunohost domains, users and apps.

### SMTP relay configuration

```yml
# SMTP custom settings
ynh_smtp_relay:
    host: smtp.domain.tld
    port: 25
    user: user1
    password: Pa$$w0rd
```

There is a built-in SMTP server on Yunohost but you can also set up Yunohost to use a SMTP relay instead.
In order to do so, create the `ynh_smtp_relay` variable and provide your own values. You can learn more about SMTP relay [here](https://yunohost.org/en/administrate/specific_use_cases/email_relay).

### Updates configuration

```yml
# Autoupdate Yunohost and its apps
ynh_autoupdate:
  scheduled: True
  special_time: "daily" #Choices are [annually,daily,hourly,monthly,reboot,weekly,yearly]
  apps: True
  system: True
  dest_script: "/usr/bin/"
```

A cron job can been set up to automate the check for system and application updates on a schedule of your choice.

- `ynh_autoupdate.scheduled` : enables the cron job by setting the value to `True`.
- `ynh_autoupdate.special_time`: it is mandatory. It allows you to specify when you want this task to be executed. Possible values: (`annually`,`daily`,`hourly`,`monthly`,`reboot`,`weekly`,`yearly`). To learn more about special times, click [here](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/cron_module.html).
- `ynh_autoupdate.apps`: is mandatory. Enables automatic updating of Yunohost applications by setting the value to `True`.
- `ynh_autoupdate.system`: is mandatory. Enables automatic updating of the Yunohost system by setting the value to `True`.
- `ynh_autoupdate.dest_script`: it is the path to the directory where the update script will be installed on the server. The default value is `/usr/local/bin`. The script is named `ynh_autoupdate.sh`.

If available, updates are done automatically. In case of problems following an application update, you can read logs located in `/var/log/yunohost/categories/operation` . You also have the possibility to rollback to the previous version since Yunohost always makes an automatic backup of an application when it is updated.

To learn more about how updates work in Yunohost you can go [here](https://yunohost.org/fr/update). The changelog of Yunohost versions is also available [here](https://forum.yunohost.org/tag/ynh_release).

## Dependencies

None.

## Example Playbook

```yml
---
- name: Configure Yunohost on Debian Server
  hosts: all
  become: True

  roles:
    - lydra.yunohost.ynh_config
```

## License

[![ansible-yunohost Copyright 2021 Lydra](https://www.gnu.org/graphics/gplv3-with-text-136x68.png)](https://choosealicense.com/licenses/gpl-3.0/)

**ansible-yunohost** is maintained by [Lydra](https://lydra.fr/) and released under the GPL3 license.
