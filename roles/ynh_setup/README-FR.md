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

- `ynh_install_script_url` est l'url du script d'installation des packages Yunohost, par défaut c'est le script officiel. Yunohost ne s'installe que sur Debian 10.
- `ynh_admin_password` est le mot de passe permettant d'accéder à l’interface d’administration du serveur.

- `ynh_data_dirs.enabled`: active les liens symboliques et permet de déplacer les répertoires de configurations et de données de YunoHost où vous le desirez. Par défaut, cette valeur est à `True`. Nous utilisons les liens symboliques car le dossier `/data` nous sert à faire des sauvegardes de type _object storage_.
- `ynh_data_dirs.path`: il s'agit des répertoires où stocker les données de configuration de Yunohost ainsi que les applications.
- `ynh_data_dirs.link`: il s'agit des répertoire où seront fait les liens symboliques.

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
     fullname: Jane DOE
     mail_domain: domain.tld 
```

- `ynh_users` est la liste des utilisateurs à créer. Chaque champ est obligatoire. Certaines applications Yunohost nécessitent qu'un utilisateur soit administrateur de l'application. Il aura ensuite le droit de gérer l'application depuis l'interface l'administration du serveur. Vous pouvez en apprendre plus sur la gestion des utilisateurs Yunohost [ici](https://yunohost.org/fr/administrate/overview/users).

## Dépendances

Aucune.

## Exemple de Playbook

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

**ansible-yunohost** est maintenu par [Lydra](https://lydra.fr/) et publié sous la licence GPL3.
