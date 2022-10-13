# Rôle Ansible : Yunohost Apps

[🇬🇧 English version](README.md)

Installez les applications [Yunohost](https://yunohost.org/#/) avec Ansible !
Retrouvez la liste des applications Yunohost [ici](https://yunohost.org/fr/applications/catalog).

## Prérequis

Aucun.

## Variables du rôle

Les variables par défaut sont disponibles dans `default/main.yml` cependant il est nécessaire de les surcharger selon vos besoins en termes de domaines, d'utilisateurs et d'applications sur Yunohost.

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
- `is_public` est  un argument qu'on retrouve souvent. Paramétré sur `yes`, l'application sera accessible à tout le monde, même sans authentification sur le portail SSO Yunohost. Paramétré sur `no`, l'application ne sera accessible qu'après authentification depuis le SSO YunoHost. Enfin, si l'application a une API qu'on peut interroger, que l'argument `is_public` soit sur `yes` ou `no` ne change rien. Les appels API ne sont pas bloqués par le SSO YunoHost mais nécessitent toujours une authentification par token si l'application le requiert.

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
- name: Install Yunohost apps
  hosts: all
  become: True

  roles:
    - lydra.yunohost.ynh_apps
```

## License

[![ansible-yunohost Copyright 2021 Lydra](https://www.gnu.org/graphics/gplv3-with-text-136x68.png)](https://choosealicense.com/licenses/gpl-3.0/)

**ansible-yunohost** est maintenu par [Lydra](https://lydra.fr/) et publié sous la licence GPL3.
