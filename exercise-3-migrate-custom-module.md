# Migration d'un module custom 

Cet exercice a pour objectif : 

* de transformer un module Drupal 7 en module Drupal 8

## Convertir le fichier info

* Le fichier .info permet à Drupal de connaitre l'existence du module.
* Dans Drupal 8 ces fichiers deviennent des fichiers de configuration au format YAML.
* Après avoir renommé le fichier en .info.yml, modifier le contenu : 
```
Drupal 7: mymodule.info
------------------------------
name = My D7 Module
description = This module needs to be ported to D8.
core = 7.x
version = VERSION
package = Custom Modules

configure = admin/config/user-interface/your-module

dependencies[] = ctools
dependencies[] = panels
dependencies[] = views

files[] = lib/FozzieClass.php
files[] = lib/GonzoClass.php
files[] = lib/KermitClass.php

------------------------------
Drupal 8: yourmodule.info.yml
------------------------------
name: 'My D7 Module'
type: module
description: 'This module needs to be ported to D8.'
core: 8.x
version: VERSION
package: 'Custom Modules'

configure: my_module.admin

dependencies:
  - drupal:ctools
  - drupal:panels
  - drupal:views

```
* Il ne vous reste plus qu'à activer le module sur le nouveau site

## Convertir les tests unitaires de son module :

* Convertir ses classes de tests à PSR-4
https://drupal.org/node/1543796
https://drupal.org/node/1320394
* Ajout de WebTestBase::$modules
https://drupal.org/node/1710766
https://drupal.org/node/1911318
* Convertir getInfo() en PHPDoc
https://www.drupal.org/node/2301125
https://www.drupal.org/node/2012184
* Lancer la suite de test

## Convertir les hook_menus et les forms :
* Le système de hook_menu est remplacé par un système de routing dans Drupal 8, il convient donc de déclarer des routes à la place des hook_menus
* Voir ici pour un exemple concrêt : https://www.drupal.org/node/2118147 
* Transformer les callbacks de page en controller : https://www.drupal.org/node/2282025 
* Déclarer ses formulaires avec la Form API : https://www.drupal.org/node/2117411 

## Convertir les variables Drupal 7 en configuration pour Drupal 8 
* La configurationd es modules est dans des fichiers de configuration qui se trouve dans le sous-dossier config/install du module. 
* Lors de l'installation du module, la configuration est alors copié en base de données pour être utilisée.
* On va créer un fichier par configuration avec la convention de nommage :  <module_name>.<config_object_name>.<optional_sub_key>.yml. Par exemple, pour le style d'image 'large' du module image :image.style.large.yml et voici à quoi ressemble la configuration :
```
name: large
label: 'Large (480x480)'
effects:
  ddd73aa7-4bd6-4c85-b600-bdf2b1628d1d:
    id: image_scale
    data:
      width: 480
      height: 480
      upscale: true
    weight: '0'
    ieid: ddd73aa7-4bd6-4c85-b600-bdf2b1628d1d
```
* Dans toute la base de code, remplacer les fonctions variable_get() et variable_set() par \Drupal::config()->get() et \Drupal::configFactory->getEditable()->set(). Exemple
```php
  $items_per_page = \Drupal::config('node.settings')->get('items_per_page');
```
```php
  function node_form_system_site_information_settings_form_submit($form, &$form_state) {
    \Drupal::configFactory()->getEditable('node.settings')
      ->set('items_per_page', $form_state['values']['default_nodes_main'])
      ->save();
  }
```
* Créer également une migration pour ceux qui utilise votre module sur Drupal 7 en utilisant migrate API : https://www.drupal.org/docs/drupal-apis/migrate-api/writing-migrations-for-contributed-and-custom-modules#configuration-entities 


-> Félicitation, vous avez toutes les cartes en main pour mettre à jour vos modules pour Drupal 8