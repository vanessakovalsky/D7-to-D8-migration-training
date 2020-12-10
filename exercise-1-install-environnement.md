# Installer l'environnement de migration

Cet exercice a pour objectifs : 
* d'installer un site drupal 7 existant
* de créer un site drupal 8 avec composer et d'ajouter les modules nécessaires à la migration

## Installation du site drupal 7 
* Décompresser l'archive site-d7.zip dans votre dossier web
* Importer la BDD depuis le fichier dump-d7.sql 
* Ajuster les paramètres du fichiers sites/default/settings.php pour correspondre aux informations d'accès de votre environnement
* Vérifier dans votre navigateur que le ste fonctionne et que vous pouvez naviguez dessus, avec du contenu qui s'affiche

## Mise à jour de l'ancien site
* Avant de préparer une migration, il est indispensable de mettre à jour l'ancien site. Voici les étapes à suivre pour le coeur et les modules (l'exemple fourni est probablement pas à jour, à vous de vérifier si des mises à jour du coeur ou des modules contrib sont nécessaires)
* * Sauvegarder le site (DB + Fichiers) manuellement (en plus des backups périodiques)
* * Mettre le site en mode maintenance via Configuration > Mode maintenance pour empêcher les utilisateurs de se connecter et de faire des modifications le temps le mise à jour
* * Faire la mise à jour des fichiers par l'interface graphique via Rapports > Mise à jours disponibles ou en remplaçant les fichiers sur le serveur
* * En étant connecter avec l'utilisateur 1 (compte admin crée pendant l'installation), lancer le script update.php se trouvant à la racine du site pour faire les mises à jours de base de données
* * Tester rapidement le site
* * Supprimer le mode maintenance si tout s'est bien passé.


## Création d'un site drupal 8 avec composer :
* Installer composer s'il n'est pas présent sur votre environnement : 
https://getcomposer.org/ 
* Une fois composer installer, lancer la commande suivante :
``` 
composer create-project drupal-composer/drupal-project:8.x-dev ./ --stability dev -vv 
```
* Une fois le traitement terminer, ouvrer votre navigateur et rendez vous à la page d'installation de votre site
* Suivre les différentes étapes d'installation de Drupal

## Gestion des modules
* Aller sur la page admin/modules de l'ancien site pour vérifier quels sont les modules utilisés 
* Vérifier sur drupal.org si les modules existent en version drupal 8 
* Pour ceux qui existe, les installer sur le nouveau site avec composer (penser à adapter le nom du module de la commande et à enlever les crochets):
```
composer require [drupal/nomdumodule]
```

-> Votre environnement est maintenant prêt pour la migration 