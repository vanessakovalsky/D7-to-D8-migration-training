# Migrer le coeur et les modules de drupal

Cet exercice a pour objectif de migrer un site drupal 7 vers un site drupal 8.

## Installation des modules de migrations 
* Afin de pouvoir lancer la migration, il est nécessaire d'installer les modules suivants fournis par le coeur de Drupal 8 sur le nouveau site :
* * Migrate 
* * Migrate Drupal
* * Migrate Drupal UI
* Si vous votre migration est simple avec peu de modules et de contenu, vous pouvez la faire via l'interface et n'avez donc pas besoin d'installer des modules supplémentaires

* Si votre site a de nombreux contenus et / ou de nombreux modules, il est recommandé de passé par un utilitaire comme drush pour faire la mise à jour. Dans ce cas les modules suivants sont nécessaires en plus :
* * Migrate Upgrade : https://www.drupal.org/project/migrate_upgrade 
* * Migrate Plus : https://www.drupal.org/project/migrate_plus 
* * Migrate Tools : https://www.drupal.org/project/migrate_tools 


## Migration via l'interface graphique
* Aller sur l'adresse de votre site drupal 8 et rajouter /upgrade au chemin
* Vous arrivez alors sur les écrans de migrations. Les différentes étapes sont les suivantes :
* * renseigner les informations de connexion à la BDD de votre site en drupal 7 et le chemin vers les fichiers existants pour pouvoir les récupérer
* * Vérifier l'analyse affichée (Drupal vous liste alors les modules qui seront ou non mis à jour) -> certains modules ne sont pas migrables en tant que tel, c'est le cas de Views, de color, Addressfield qui est remplacé par le module address
* * La migration doit être faite sur un site complétement vide (sans aucun contenu, ni aucune entité), vous allez avoir un message d'avertissement qui vous indique qu'il y a des conflits et que vous savez que vous pouvez perdre des données (par exemple l'utilisateur créer à l'installation)
* * Lancer la migration et attendre le temps que celle-ci soit effectuée
* * Une fois la migration terminé, Drupal vous affiche le nombre de mise à jour effectuée et le nombre de mise à jour qui ont échouées. 
* Votre mise à jour est maintenant terminé, il vous reste à recréer les vues, et à mettre à jour votre thème

## Migration avec drush 

* Dans le fichier settings.php (ou settings.yml), ajouter la base de l'ancien site avec la clé migrate 
```php
$databases['default']['default'] = [
  'database' => 'drupal8',
  'username' => 'drupal8',
  'password' => 'drupal8',
  'prefix' => '',
  'host' => 'database',
  'port' => '3306',
  'namespace' => 'Drupal\\Core\\Database\\Driver\\mysql',
  'driver' => 'mysql',
];

$databases['migrate']['default'] = [
  'database' => 'drupal7db',
  'username' => 'drupal7db',
  'password' => 'drupal7db',
  'prefix' => '',
  'host' => 'd7db',
  'port' => '3306',
  'namespace' => 'Drupal\\Core\\Database\\Driver\\mysql',
  'driver' => 'mysql',
];
```
* Il est alors nécessaire de déclarer les migrations à Drupal avec la commande suivante (à adapter avec vos informations de connexion)
```
drush migrate-upgrade --legacy-db-url=mysql://user:password@server/db --legacy-root=http://example.com --configure-only 
```
* Puis nous listons les migrations
```
drush migrate-status 
```
* Ensuite nous lançons les migrations soit de manière individuelle avec : 
```
drush migrate-import <migration name> 
```
* Soit toutes les migrations avec : 
```
drush migrate-import --all 
```
* La aussi la migration est effectuée, il vous reste à recréer vos vues, et votre thème pour Drupal 8.

* L'ensemble des commandes de migrations pour drush est disponible ici : https://www.drupal.org/docs/upgrading-drupal/upgrade-using-drush#s-reference-of-drush-migration-commands 
-> Félicitations vous avez migrer votre site 