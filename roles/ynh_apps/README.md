# Ansible Role: Yunohost Apps

[🇫🇷 French version](README-FR.md)

Install [Yunohost](https://yunohost.org/#/) apps with Ansible!
You can find the list of available Yunohost applications [here](https://yunohost.org/en/apps).

## Requirements

None.

## Role Variables

Default variables are available in `default/main.yml` however it is necessary to override them according to your needs for Yunohost domains, users and apps.

### App management

```yml
# The list of Yunohost apps.
ynh_apps:
  - label: WikiJS
    link: wikijs
    args:
      domain: wiki.domain.tld
      path: /
      admin: user1
      is_public: no
  - label: Discourse
    link: discourse
    args:
      domain: forum.domain.tld
      path: /
      admin: user1
      is_public: yes
    post_install:
      - src: "templates/site_settings.yml.j2"
        dest: "/var/www/discourse/config/site_settings.yml"
        type: "config"

      - src: "templates/configure_discourse.sh.j2"
        dest: "/tmp/configure_discourse.sh"
        type: "script"
        owner: root
        group: root
```

- `ynh_apps` is the list of applications to install.
- `label` allows you to give a custom name to the application on the user interface.
- `link` is the name of the Yunohost application to install.

#### About the arguments

- `domain` is essential. You have to choose one of the domains of your Yunohost instance.
- `path` is required. You have to choose a URL to access your application like `domain.tld/my_app`. Just use `/` if the application is to be installed on a subdomain.
- `is_public` argument is a common one. Set to `yes`, the application will be accessible to everyone, even without authentication to the Yunohost SSO portal. Set to `no`, the application will be accessible only after authentication.

For the other arguments, you have to refer to the `manifest.json` available in the repository of the Yunohost application you install. You can learn more about this part [here](https://yunohost.org/fr/packaging_apps_manifest).

#### About the post-installation

It is possible to complete the installation of applications by adding jinja template configuration files or scripts written by yourself.
To enable this feature, define the `post_install` variable which corresponds to the list of post-installation files of your applications.
Because this task uses the template module, you can use your own variables and call them in your template files. To know more about this module, click [here](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/template_module.html).

- `src` is mandatory. This is the directory where the template file is located on the machine running Ansible.
- `dest` is mandatory. This is the directory where the template file will be stored.
- `type` is mandatory:
  - If you specify `script` as the value, then the template file will have 740 rights. It will be executed after it is transferred to the Yunohost server (usually in `/tmp/`) and then deleted.
  - If you specify `config` as the value, then the template file will have 660 rights. It will be transferred to the Yunohost server (usually in `/var/www/AppName/`) and after you could import it with a shell script on the side for example.

For `owner` and `group`, by default the file will take as owner the name of the application and as owner www-data(NGINX group). You can change them by specifying different values.

## Dependencies

None.

## Example Playbook

```yml
---
- name: Install Yunohost apps
  hosts: all
  become: True
  collections:
    - lydra.yunohost
  roles:
    - ynh_apps
```

## License

[![ansible-yunohost Copyright 2021 Lydra](https://www.gnu.org/graphics/gplv3-with-text-136x68.png)](https://choosealicense.com/licenses/gpl-3.0/)

**ansible-yunohost** is maintained by [Lydra](https://lydra.fr/) and released under the GPL3 license.
