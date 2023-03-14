# Rôle Ansible : Yunohost Configuration

[🇬🇧 English version](README.md)

Configurez [Yunohost](https://yunohost.org/#/) avec Ansible !

## Prérequis

Yunohost doit déjà être installé sur votre serveur.

## Variables du rôle

Les variables par défaut sont disponibles dans `default/main.yml` cependant il est possible de les surcharger selon vos besoins.

### Configuration d'un relais SMTP

```yml
ynh_smtp_relay:
    host: smtp.domain.tld
    port: 25
    user: user1
    password: Pa$$w0rd
```

Yunohost possède son propre serveur SMTP natif mais il est aussi possible de configurer Yunohost pour qu'il utilise un relais SMTP à la place.
Pour faire cela, créez la variable `ynh_smtp_relay` pour y mettre vos propres valeurs. Vous pouvez en apprendre plus sur les relais SMTP [ici](https://yunohost.org/fr/administrate/specific_use_cases/email_relay).

### Configuration des mises à jour

```yml
# Autoupdate Yunohost and its apps
ynh_autoupdate:
  scheduled: True
  special_time: "daily" #Choices are [annually,daily,hourly,monthly,reboot,weekly,yearly]
  apps: True
  system: True
  dest_script: "/usr/bin/"
```

Une tâche cron peut être mise en place pour automatiser la vérification des mises à jour système et applications suivant la périodicité de votre choix.

- `ynh_autoupdate.scheduled` : activez la tâche cron en mettant la valeur à `True`.
- `ynh_autoupdate.special_time`: est obligatoire. Elle permet de préciser quand vous souhaitez que cette tâche soit exécutée. Valeurs possibles : (`annually`,`daily`,`hourly`,`monthly`,`reboot`,`weekly`,`yearly`). Pour en savoir plus sur les _special times_, cliquez [ici](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/cron_module.html).
- `ynh_autoupdate.apps` : est obligatoire. Activez la mise à jour automatique des applications Yunohost en mettant la valeur à `True`.
- `ynh_autoupdate.system` : est obligatoire. Activez la mise à jour automatique du système Yunohost en mettant la valeur à `True`.
- `ynh_autoupdate.dest_script` : c'est le chemin du répertoire où le script de mise à jour sera installé sur le serveur. La valeur par défaut est `/usr/local/bin`. Le script s'appelle `ynh_autoupdate.sh`.

Si des mises à jour sont disponibles, elles sont faites automatiquement. En cas de problème suite à la mise à jour d'une application, vous pouvez lire les logs qui sont disponibles ici `/var/log/yunohost/categories/operation`. Vous avez aussi la possibilité de revenir à la version précédente car Yunohost fait toujours une sauvegarde automatique d'une application lorsqu'elle est mise à jour.

Pour en savoir plus sur le fonctionnement des mises à jour dans Yunohost vous pouvez vous rendre [ici](https://yunohost.org/fr/update). Le changelog des versions de Yunohost est aussi disponible [ici](https://forum.yunohost.org/tag/ynh_release).

### Options de YunoHost

```yml
ynh_settings:
  security.ssh.port: "22"
  security.password.passwordless_sudo: "true"
```

#### Modification du port SSH

Parmi les paramètres proposés dans YunoHost, il est possible de modifier le port SSH à l'aide de la variable `security.ssh.port`. En modifiant la variable, YunoHost va effectuer les changements appropriés pour fail2ban et le firewall interne de YunoHost.
Si votre instance YunoHost est derrière un firewall applicatif ou propre à votre fournisseur cloud, il faudra également ouvrir le groupe de sécurité approprié et ne pas oublier de renseigner le port SSH utilisé (par exemple `ssh -p 812 username@hostname`). Vous pouvez également externaliser cette configuration vers un fichier de configuration SSH (plus d'infos [ici](https://linuxize.com/post/using-the-ssh-config-file/)). Vous pouvez enfin indiquer cette configuration dans votre fichier d'inventaire sinon Ansible ne pourra plus se connecter à votre serveur. (plus d'infos [ici](https://docs.ansible.com/ansible/latest/reference_appendices/faq.html#how-do-i-handle-different-machines-needing-different-user-accounts-or-ports-to-log-in-with)).

### Utilisation de sudo sans mot de passe

À partir de Yunohost 11.1, un nouveau groupe d'administrateurs est créé sur l'instance. Il s'agit d'un groupe Unix qui est intégré à YunoHost et son LDAP. Tous les utilisateurs dans ce groupe auront accès à la console d'administration en ligne YunoHost mais pourront aussi se connecter en SSH et utiliser la commande sudo (pour prendre temporairement les droits root).
Par défaut, l'utilisateur doit taper son mot de passe pour utiliser la commande sudo mais il est possible de désactiver cette vérification depuis l'interface web (`outils` > `Paramètres de YunoHost` > `Permettre aux administrateurs d'utiliser 'sudo' sans retaper leur mot de passe`) ou en modifiant la variable `security.password.passwordless_sudo` à `true` dans votre fichier de variables Ansible. Plus d'informations disponibles [ici](https://forum.yunohost.org/t/yunohost-11-1-release-sortie-de-yunohost-11-1/23378#sudo-sans-mot-de-passe-16).

#### Paramètres supplémentaires

Vous pouvez fournir des paramètres supplémentaires à la variable `ynh_settings`. Pour en savoir plus, utilisez la commande `yunohost settings list`.

## Dépendances

Aucune.

## Exemple de Playbook

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

**ansible-yunohost** est maintenu par [Lydra](https://lydra.fr/) et publié sous la licence GPL3.
