[![](https://img.shields.io/liberapay/receives/cchaudier.svg?logo=liberapay)](https://liberapay.com/cchaudier/donate)
[![](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/badges/main/pipeline.svg)](https://lab.frogg.it/lydra/yunohost/ansible-yunohost/-/pipelines)
[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](http://www.gnu.org/licenses/gpl-3.0)
[![Ansible Role](https://img.shields.io/ansible/role/56544)](https://galaxy.ansible.com/lydra/yunohost)
[![Ansible Quality Score](https://img.shields.io/ansible/quality/56544)](https://galaxy.ansible.com/lydra/yunohost)
[![Ansible Role](https://img.shields.io/ansible/role/d/56544)](https://galaxy.ansible.com/lydra/yunohost)
[![GitHub last commit](https://img.shields.io/github/last-commit/LydraFr/ansible-yunohost)](https://github.com/LydraFr/ansible-yunohost)
[![GitHub Release Date](https://img.shields.io/github/release-date/LydraFr/ansible-yunohost)](https://github.com/LydraFr/ansible-yunohost)
[![GitHub Repo stars](https://img.shields.io/github/stars/LydraFr/ansible-yunohost?style=social)](https://github.com/LydraFr/ansible-yunohost)

 Collection Ansible - `lydra.yunohost`

[🇬🇧 English version](README.md)

Cette collection vise à installer, configurer et sauvegarder [Yunohost](https://yunohost.org/#/).
Comme il s'agit d'une collection indépendante, elle peut être publiée selon sa propre cadence de publication. De plus, les rôles qu'elle contient sont mis à jour indépendamment.

## Prérequis

Votre serveur doit être basé sur du Debian Buster et Yunohost ne doit pas déjà être installé.

## Contenu de la collection

### Rôles

- [`lydra.yunohost.ynh_setup`](roles/ynh_setup/README-FR.md) : Ce rôle prépare les serveurs à base de Debian-Buster à exécuter Yunohost. Il configure Yunohost avec ses paramètres initiaux, les domaines et les utilisateurs de votre choix.
- [`lydra.yunohost.ynh_apps`](roles/ynh_apps/README-FR.md): Ce rôle installe les applications Yunohost de votre choix et peut également les configurer grâce aux tâches de post-installation.
- [`lydra.yunohost.ynh_config`](roles/ynh_config/README-FR.md) : Ce rôle gère la configuration de différents services de Yunohost (relais SMTP, mises à jour automatiques).
- [`lydra.yunohost.ynh_backup`](roles/ynh_backup/README-FR.md) : Ce rôle gère la configuration des sauvegardes.

### Tags du rôle

Ces tags sont applicables suivant les rôles.

|tags|commentaires|
|----|-------|
|yunohost|Tâches spécifiques à Yunohost lui-même (installation ou configuration).|
|users|Tâches spécifiques aux utilisateurs de Yunohost.|
|domains|Tâches spécifiques aux domaines liés à Yunohost.|
|apps|Tâches spécifiques aux applications de Yunohost.|
|update|Tâches liées aux paramètres de mise à jour de Yunohost.|
|smtp|Tâches liées aux paramètres de relais smtp de Yunohost.|
|backup|Tâches liées aux sauvegardes de Yunohost.|
|pkg|Tâches d'installation de paquets.|
|linux|Tâches liées à l'OS Linux.|

## License

[![ansible-yunohost Copyright 2021 Lydra](https://www.gnu.org/graphics/gplv3-with-text-136x68.png)](https://choosealicense.com/licenses/gpl-3.0/)

**ansible-yunohost** est maintenu par [Lydra](https://lydra.fr/) et publié sous la licence GPL3.
