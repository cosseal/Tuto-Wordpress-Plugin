Consignes :

I - Installation et configuration de Wordpress en local

- Commencez par installer Wordpress en local : rendez vous sur wordpress.org et téléchargez l'archive de Wordpress.

- Une fois l'archive dézippée, copier/coller le dossier wordpress dans le dossier eds-www de easyPHP.

- Importez le dossier contenant wordpress dans phpStorm ( file > new project from existing files ).

- rendez vous dans phpMyAdmin et créez une nouvelle base de donnée , appelez là : wordpress

- Renommez le fichier wp-config-sample en wp-config, et renseignez les informations de connexions à la base de données.

- Une fois que le fichier est modifié, vous allez pouvoir lancer l'installation de wordpress en visitant localhost/<dossier wordpress>

- Suivez les étapes d'installation qui va vous permettre de créer votre compte administrateur.

- Si tout va bien, une fois l'install terminée, vous arrivez sur l'espace d'administration.


_________________________________________________________________________________________________________

II - Ecriture de votre premier plugin wordpress

- Les plugins wordpress peuvent s'installer de deux façons, soit depuis l'administration en important une archive ( zip)
contenant les fichiers du plugin,
soit en plaçant directement le ou les fichiers du plugins dans le repertoire wp-content/plugins/

- Pour notre premier plugin, nous allons le placer manuellement dans le repertoire wp-content/plugins/

- Créez un nouveau fichier php dans ce repertoire et appelez le monPlugin.php

- Lorsque vous créez un fichier php, phpStorm ajoute au début du fichier des commentaires, nous allons les supprimer car
Wordpress va lire ces commentaires pour déterminer les informations ( et autres configurations ) liés à notre plugin.

- Ecrivons notre premiere ligne de configuration, au début du fichier ( aprés <?php, ecrivez :

/*
Plugin Name: Mon Premier Plugin

Cette premiére information est requise par Wordpress, les autres ne le sont pas, Plugin Name va être le nom du plugin
affiché dans Wordpress ( dans l'administration )

- La seconde ligne , Plugin Uri, défini l'adresse distante où il est possible de télécharger le plugin, écrivez à la suite:

Plugin Uri : http://votre adresse webhost ( par exemple )

- La troisieme ligne, Description affichera une courte description de votre plugin, écrivez :

Description : Mon premier plugin wordpress

- La quatriéme ligne, Version indique à wordpress la version actuelle de votre plugin, si vous faites une mise à jour,
vous incrémenterez le numéro de version ( wordpress effectue une comparaison avec le numéro de version du plugin actuellement
installé pour déterminer si une mise à jour doit être effectué ou pas. ), ecrivez :

Version : 1.0

- La cinquieme ligne, Author indique l'auteur du plugin, ecrivez :

Author : <Votre nom>

- Il existe d'autres options pouvant être définie mais pour ce premier plugin, ces informations seront suffisantes.

______________________________________________________________________________________________________________________

III - Les Hooks

- Wordpress repose sur des événements déclenchés par son code PHP, un exemple de Hook trés utile est le hook d'activation,
celui ci se déclenche lorsque l'utilisateur active le plugin dans l'espace d'administration.

C'est typiquement à cette action que nous allons créer les tables en bases de données necessaire au plugin ( si besoin )

Ceci s'écrit de cette façon :

register_activation_hook( __FILE__ , maFonction);

Le premier parametre de la méthode indique le chemin vers le fichier à éxécuter , ici nous utilisons la constante magique
__FILE__ pour faire référence au fichier en cour d'éxécution
Le second parametre est le nom d'une fonction à executer, ici j'ai spécifié maFonction

Nous n'allons pas utiliser de base de donnée dans un premier temps car le plugin est volontairement simpliste.


4 - Les Hooks d'actions

- Pour executer du code aprés une action bien précise de wordpress, on utilisera la commande suivante :

add_action(<Action>,<Fonction à executer>);

- Ecrivons dans notre plugin une action, ajoutez dans le fichier php du plugin :

add_action("wp_loaded",maFonction);

wp_loaded , correspond au moment où wordpress est chargé et où notre site wordpress est affiché

- Ecrivon dans notre plugin, la fonction maFonction :

function maFonction()
{
    echo "test";
}

- Rendez vous dans l'espace d'administration > Extensions , puis activez votre plugin.

- Testez votre site wordpress, si tout va bien, le message Test s'affiche désormais sur toutes les pages.


5 - Les Filtres

- Les filtres permettent de modifier le comportement d'une action wordpress, nous allons ajouter à notre plugin un filtre
qui va remplacer certains mots définis par un autre ( exemple le mot censuré pour filtrer des mots interdits )

- Ecrivons notre premier filtre, dans le fichier de plugin, ecrivez :

add_filter('comment_text',monFiltre);

Ce filtre va s'appliquer au niveau des commentaires, dans le contenu textuel de ceux ci.


- Ecrivons maintenant notre fonction monFiltre :

function monFiltre($text) {
    $mots = array('machin','truc','chose');
    $text=str_ireplace($mots,'{censuré}',$text);
    return $text;
}


- Testons maintenant si notre plugin fonctionne bien, lancez votre site wordpress et cliquez sur le post Bonjour tout le monde
afin d'accéder au formulaire de commentaire, ecrivez un commentaire de ce style :

truc machin chose

Puis envoyez le commentaire, si tout va bien, les mots machin truc et chose sont tous devenu {censuré}


______________________________________________________________________________________________________________________

6 - Le shortcode

- Grâce au shortcode, il va être possible d'éxécuter du code PHP à l'intérieur d'un post Wordpress

- Pour écrire un shortcode, il faut inclure la méthode suivante dans votre script php de plugin :

add_shortcode('monShortcode','maFonctionShortcode');

monShortcode corresponds ici au contenu qui devra être présent entre [] dans le contenu du post wordpress et qui activera ma fonction.


- Ecrivons à présent la fonction maFonctionShortcode

function maFonctionShortcode()
{
$contenu = "Mon Super contenu php";
return $contenu;
}

- Pour tester , rendez vous sur l'administration de wordpress, et créez une nouvelle page.

- Ajoutez le titre que vous voulez et dans le contenu de la page, ecrivez le shortcode :

[monShortcode]

- Publiez la page puis visitez la, le contenu de la page affiche : Mon super contenu php

______________________________________________________________________________________________________________________

7 - Travailler avec les bases de données wordpress, l'objet $wpdb

- Vous trouverez la documentation compléte ici : https://codex.wordpress.org/Class_Reference/wpdb

- Si votre plugin doit utiliser une base de donnée, vous travaillerez avec cet objet.

______________________________________________________________________________________________________________________

8 - Allez plus loin : Convertir un ancien projet php en plugin wordpress

Pourquoi ne pas convertir le projet meteo ou le projet chat en plugin wordpress, en utilisant le systeme de shortcode,
vous allez pouvoir intégrer votre code php à wordpress, en utillisant l'objet $wpdb, vous allez pouvoir stocker les infos
en base de donnée.

Bon courage !


Note : Wordpress supporte également le php orienté objet, il est possible d'executer une méthode de classe en utilisant
un tableau associatif à la place du nom de la fonction ( exemple maFonction deviendra array("maClasse","maMethode") )


Références :

- Documentation compléte sur l'écriture de plugins wordpress
https://developer.wordpress.org/plugins/intro/

- Références de l'api wordpress :
https://developer.wordpress.org/reference/


Théorie :

- Vous allez créer votre premier plugin wordpress.

Les plugins sous Wordpress permettent d'ajouter des fonctionnalités sans avoir à modifier les fichiers sources de Wordpress.

Les plugins utilisent des fonctions de Wordpress pour se greffer à l'éxécution de wordpress ( on apelle ça des Hooks )

Il est aussi possible d'utiliser du "shortcode" qui permet d'intégrer un plugin directement dans un article wordpress.

