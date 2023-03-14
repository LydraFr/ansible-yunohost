# Ansible Role: Yunohost

[🇫🇷 French version](README-FR.md)

Deploy [Yunohost](https://yunohost.org/#/) with Ansible!

## Requirements

None.

## Role Variables

Default variables are available in `default/main.yml` however it is necessary to override them according to your needs for Yunohost domains, users and apps.

### SMTP relay configuration

```yml
ynh_smtp_relay:
    host: smtp.domain.tld
    port: 25
    user: user1
    password: Pa$$w0rd
```

There is a built-in SMTP server on Yunohost but you can also set up Yunohost to use a SMTP relay instead.
In order to do so, create the `ynh_smtp_relay` variable in order to provide your own values. You can learn more about SMTP relay [here](https://yunohost.org/en/administrate/specific_use_cases/email_relay).

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

### YunoHost options

```yml
ynh_settings:
  security.ssh.port: "22"
  security.password.passwordless_sudo: "true"
```

#### SSH port modification

Among the settings offered in YunoHost, it is possible to change the SSH port using the `security.ssh.port` variable. By modifying the variable, YunoHost will make the appropriate changes to fail2ban and YunoHost's internal firewall.
If your YunoHost instance is behind an application or cloud provider specific firewall, you will also need to open the appropriate security group and remember to enter the SSH port in use (e.g. `ssh -p 812 username@hostname`). You can also externalize this configuration in an SSH configuration file (more info [here](https://linuxize.com/post/using-the-ssh-config-file/)). Finally, you can indicate this configuration in your inventory file otherwise Ansible will not be able to connect to your server. (more info [here](https://docs.ansible.com/ansible/latest/reference_appendices/faq.html#how-do-i-handle-different-machines-needing-different-user-accounts-or-ports-to-log-in-with)).

### Using sudo without password

Starting with Yunohost 11.1, a new administrator group is created on the instance. This is a Unix group that is integrated with YunoHost and its LDAP. All users in this group will have access to the YunoHost online administration console but will also be able to connect via SSH and use the sudo command (to temporarily take root rights).
By default, the user must type their password to use the sudo command but it is possible to disable this check from the web interface (`tools` > `YunoHost settings` > `Allow admins to use ‘sudo’ without re-typing their passwords`) or by changing the `security.password.passwordless_sudo` variable to `true` in your Ansible variables file. More information available [here](https://forum.yunohost.org/t/yunohost-11-1-release-sortie-de-yunohost-11-1/23378#passwordless-sudo-4).


#### Extra settings

You can provide extra parameters to the variable `ynh_settings`. To know more, use the command `yunohost settings list`.

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
