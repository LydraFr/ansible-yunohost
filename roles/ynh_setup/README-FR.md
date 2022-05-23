# Rôle Ansible : Yunohost

[🇬🇧 English version](README.md)

Déployez [Yunohost](https://yunohost.org/#/) avec Ansible !

## Prérequis

Aucun.

## Variables du rôle

Les variables par défaut sont disponibles dans `default/main.yml` cependant il est nécessaire de les surcharger selon vos besoins en termes de domaines, d'utilisateurs et d'applications sur Yunohost.

### Installation de Yunohost

```yml
# Script pour Debian 10 uniquement.
ynh_install_script_url: https://install.yunohost.org

ynh_admin_password: MYINSECUREPWD_PLZ_OVERRIDE_THIS
```

- `ynh_install_script_url` est le script d'installation des packages Yunohost, par défaut c'est le script officiel. Yunohost ne s'installe que sur Debian 10.
- `ynh_admin_password` est le mot de passe permettant d'accéder à l’interface d’administration du serveur.

### Gestion des domaines

```yml
# Liste des domaines gérés par Yunohost.
ynh_main_domain: domain.tld
ynh_extra_domains:
  - forum.domain.tld
  - wiki.domain.tld
ynh_ignore_dyndns_server: False
```

- `ynh_main_domain` correspond au domaine principal qui permet l’accès au serveur ainsi qu’au portail d’authentification des utilisateurs. On peut se contenter d'un nom de domaine qui nous appartient ou en utiliser un en .nohost.me / .noho.st / .ynh.fr (plus d'infos [ici](https://yunohost.org/fr/install/hardware:vps_debian)).
- `ynh_extra_domains` sont des sous-domaines optionnels. Ils permettent d'installer une application par sous-domaine (plus d'infos [ici](https://yunohost.org/fr/dns_subdomains)).
- `ynh_ignore_dyndns_server` permet d'enregistrer les domaines avec un service de DNS dynamique (plus d'infos [ici](https://yunohost.org/fr/dns_dynamicip)).

### Gestion des utilisateurs

```yml
# Liste des utilisateurs Yunohost.
ynh_users:
   - name: user1
     pass: MYINSECUREPWD_PLZ_OVERRIDE_THIS
     firstname: Jane
     lastname: Doe
     mail_domain: domain.tld 
```

- `ynh_users` est la liste des utilisateurs à créer. Chaque champ est obligatoire. Certaines applications Yunohost nécessitent qu'un utilisateur soit administrateur de l'application. Il aura ensuite le droit de gérer l'application depuis l'interface l'administration du serveur. Vous pouvez en apprendre plus sur la gestion des utilisateurs Yunohost [ici](https://yunohost.org/fr/administrate/overview/users).

### Gestion des applications

```yml
# Liste des applications Yunohost.
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

- `ynh_apps` est la liste des applications à installer.
- `label` permet de donner un nom personnalisé à l'application sur l'interface utilisateur.
- `link` correspond au nom de l'application Yunohost qu'on veut installer.

#### Concernant les arguments

- `domain` est obligatoire. Il faut choisir un des domaines de son instance Yunohost.
- `path` est obligatoire. Il faut choisir une URL pour accéder à son application comme `domain.tld/my_app`. Utilisez juste `/` si l'application doit s'installer sur un sous-domaine.
- `is_public` est  un argument qu'on retrouve souvent. Paramétré sur `yes`, l'application sera accessible à tout le monde, même sans authentification sur le portail SSO Yunohost. Paramétré sur `no`, l'application ne sera accessible qu'après authentification.

Pour les autres arguments, il faut se référer au `manifest.json` disponible dans le dépôt de l'application Yunohost qu'on installe. Vous pouvez en apprendre plus sur cette partie [ici](https://yunohost.org/fr/packaging_apps_manifest).

#### Concernant la post-installation

Il est possible de compléter l'installation des applications par l'ajout de templates jinja de configuration ou de scripts que vous aurez écrit de votre côté.
Pour activer cette fonctionnalité, définissez la variable `post_install` qui correspond à la liste des fichiers de post-installation de votre application.
Cette tâche utilisant le module template, vous pouvez tout à fait utiliser vos propres variables et les appeler dans vos fichiers de template. Pour en savoir sur ce module, cliquez [ici](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/template_module.html).

- `src` est obligatoire. Il s'agit du répertoire où le fichier de template se situe sur la machine qui execute Ansible.
- `dest` est obligatoire. Il s'agit du répertoire où le fichier de template va être stocké.
- `type` est obligatoire :
  - Si vous précisez comme valeur `script` alors le fichier de template aura pour droits 740. Il sera exécuté après son transfert sur le serveur Yunohost (généralement dans `/tmp/`) puis il sera supprimé. 
  - Si vous précisez comme valeur `config` alors le fichier de template aura pour droits 660. Il sera transféré sur le serveur Yunohost (généralement dans `/var/www/AppName/`) et vous pourrez l'importer avec un script shell à côté par exemple.

Pour `owner` et `group`, par défaut le fichier va prendre comme utilisateur propriétaire le nom de l'application et comme groupe propriétaire www-data (groupe NGINX). Vous pouvez les changer en précisant des valeurs différentes.

## Dépendances

Aucune.

## Exemple de Playbook

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
    - ynh_config
    - ynh_backup
```

## License

[![ansible-yunohost Copyright 2021 Lydra](https://www.gnu.org/graphics/gplv3-with-text-136x68.png)](https://choosealicense.com/licenses/gpl-3.0/)

**ansible-yunohost** est maintenu par [Lydra](https://lydra.fr/) et publié sous la licence GPL3.
