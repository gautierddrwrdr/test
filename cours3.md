## **PHP */ Requêtes SQL***

# **Connexion à la base de données** 

Pour pouvoir manipuler nos bases de données MySQL en PHP (sans passer
par phpMyAdmin), nous allons déjà devoir nous connecter à MySQL.

Pour cela, le PHP met à notre disposition deux API (Application
Programming Interface) :

- L'extension MySQLi ;

- L'extension PDO (PHP Data Objects).

Il existe notamment une différence notable entre ces deux API :
l'extension MySQLi ne va fonctionner qu'avec les bases de données MySQL
tandis que PDO va fonctionner avec 12 systèmes de bases de données
différents.\
On va donc privilégier l'utilisation de PDO.

Pour se connecter à une base de données on va utiliser la commande
suivante :

```php
$connexion = new PDO('mysql:host=localhost; dbname=bibliotheque;charset=utf8', 'root', 'rootpassword');
```
Ici on indique le serveur MySQL (dans notre cas notre propre ordinateur
donc localhost, dans la réalité on utilisera plus une adresse IP), le
nom de la base de données et les informations d'authentification login
et mot de passe (de base le compte root n'a pas de mot de passe).

Afin de sécuriser l'accès à notre base on peut modifier le mot de passe
root, pour cela dans phpMyAdmin on clique sur la roue crantée puis
"Comptes utilisateurs" :

![](images/fedb92de5b46b58232ae3d30a1000b6104156778.png){width="6.177083333333333in"
height="0.9115398075240595in"}

On clique sur l'utilisateur root puis "Change password" :

![](images/1985d4e6adebf1c70057140187b78eab0de420da.png){width="6.229166666666667in"
height="1.2458333333333333in"}

### **Comment lancer une requête**

Une fois connecté à la base de données, notre but sera d'exécuter des
requêtes afin d'afficher du contenu ou alors d'insérer de nouveaux
enregistrements dans notre base.

### **INSERTION**

En reprenant la base de données "bibliothèque" utilisé dans les
précédents cours, on peut y intégrer de nouveaux enregistrements en
utilisant le code ci-dessous :

+-------------------------------------------------------------------------+
| // on définit les variables qu\'on veut intégrer                        |
|                                                                         |
| \$titre = \"LIVRE3\";                                                   |
|                                                                         |
| \$annee = 1879;                                                         |
|                                                                         |
| \$nbpages = 59;                                                         |
|                                                                         |
| \$langue = \"UK\";                                                      |
|                                                                         |
| \$idauteur = 2;                                                         |
|                                                                         |
| // on définit notre requête                                             |
|                                                                         |
| \$requete = \$connexion-\>prepare(\"INSERT INTO livre                   |
| (titre,annee,nombre_page,langue,id_auteur) VALUES (?,?,?,?,?)\");       |
|                                                                         |
| // on exécute la requête                                                |
|                                                                         |
| \$requete-\>execute(\[\$titre,\$annee,\$nbpages,\$langue,\$idauteur\]); |
+=========================================================================+

Ci-dessous une autre méthode :

+----------------------------------------------------------------------+
| // on définit notre requête                                          |
|                                                                      |
| \$requete = \$connexion-\>prepare(\"INSERT INTO livre                |
| (titre,annee,nombre_page,langue,id_auteur) VALUES (:titre, :annee,   |
| :nbpages, :langue, :idauteur)\");                                    |
|                                                                      |
| // on exécute la requête                                             |
|                                                                      |
| \$requete-\>execute(array(                                           |
|                                                                      |
| \":titre\"=\>\$titre,                                                |
|                                                                      |
| \":annee\"=\>\$annee,                                                |
|                                                                      |
| \":nbpages\"=\>\$nbpages,                                            |
|                                                                      |
| \":langue\"=\>\$langue,                                              |
|                                                                      |
| \":idauteur\"=\>\$idauteur)                                          |
|                                                                      |
| );                                                                   |
+======================================================================+

On peut vérifier via phpMyAdmin si le LIVRE3 à bien été ajouté dans la
table "livre" de la base de données "bibliothèque".

### **VISUALISATION**

Maintenant qu'on est capable d'intégrer des données via le PHP il va
valoir apprendre à les récupérer et les mettre en page.

Dans notre exemple on décide d'afficher le titre, l'année, le nombre de
pages et la langue, le tout séparé par des espace et un retour à la
ligne entre chaque livre :

+----------------------------------------------------------------------+
| // on définit notre requête et on range dans une variable le         |
| résultat de cette requête                                            |
|                                                                      |
| \$requete2 = \"SELECT \* FROM livre\";                               |
|                                                                      |
| \$resultat2 = \$connexion-\>query(\$requete2);                       |
|                                                                      |
| // tant que la requête nous retourne des données on les met en forme |
| et on les affiche                                                    |
|                                                                      |
| while (\$ligne = \$resultat2-\>fetch()) {                            |
|                                                                      |
| echo \$ligne\[\'titre\'\].\" \".\$ligne\[\'annee\'\].\"              |
| \".\$ligne\[\'nombre_page\'\].\"                                     |
| \".\$ligne\[\'langue\'\].\"\<br\>\";                                 |
|                                                                      |
| }                                                                    |
+======================================================================+

Ce qui nous donne :

![](images/a3d795902d961f6c05532bbdd1995633a43cebfa.png){width="5.0in"
height="1.108390201224847in"}

Afin d'améliorer la mise en page on peut aussi afficher les données
directement dans un tableau, ce qui nous donne pour la mise en place de
la structure du tableau :

+----------------------------------------------------------------------+
| // on crée un tableau via la balise HTML                             |
|                                                                      |
| echo \"\<table border=1\>\";                                         |
|                                                                      |
| echo \"\<tr\>                                                        |
|                                                                      |
| \<th width=\'100px\'\>TITRE\</th\>                                   |
|                                                                      |
| \<th\>ANNEE\</th\>                                                   |
|                                                                      |
| \<th\>NB PAGES\</th\>                                                |
|                                                                      |
| \<th\>LANGUE\</th\>                                                  |
|                                                                      |
| \</tr\>\";                                                           |
+======================================================================+

Puis ensuite on ajoute la boucle pour afficher les données et y a mettre
en forme via les balises HTML :

+----------------------------------------------------------------------+
| // tant que la requête nous retourne des données on les met en forme |
| et on les affiche                                                    |
|                                                                      |
| while (\$ligne = \$resultat2-\>fetch()) {                            |
|                                                                      |
| // on met en forme les données grâce aux balises \<td\> et \<tr\>    |
|                                                                      |
| echo \"\<tr\>\";                                                     |
|                                                                      |
| echo \"\<td width=\'100px\'\>\" . \$ligne\[\'titre\'\] .             |
| \"\</td\>\";                                                         |
|                                                                      |
| echo \"\<td\>\".\$ligne\[\'annee\'\] . \"\</td\>\";                  |
|                                                                      |
| echo \"\<td\>\".\$ligne\[\'nombre_page\'\] . \"\</td\>\";            |
|                                                                      |
| echo \"\<td\>\".\$ligne\[\'langue\'\] . \"\</td\>\";                 |
|                                                                      |
| echo \"\</tr\>\";                                                    |
|                                                                      |
| }\                                                                   |
| echo \"\</table\>\";                                                 |
+======================================================================+

Ce qui nous donne :

![](images/cc9dfde81bfd8d6dc188334504640035666b88fb.png){width="4.229166666666667in"
height="1.5416666666666667in"}

On peut aussi réaliser la même chose avec une boucle FOREACH :

+----------------------------------------------------------------------+
| foreach (\$resultat2 as \$ligne) {                                   |
|                                                                      |
| echo \"\<tr\>\";                                                     |
|                                                                      |
| echo \"\<td\>\".\$ligne\[\'titre\'\] . \"\</td\>\";                  |
|                                                                      |
| print \"\<td\>\".\$ligne\[\'annee\'\] . \"\</td\>\";                 |
|                                                                      |
| print \"\<td\>\".\$ligne\[\'nombre_page\'\] . \"\</td\>\";           |
|                                                                      |
| echo \"\</tr\>\";                                                    |
|                                                                      |
| }                                                                    |
+======================================================================+

### **MISE A JOUR**

Si on souhaite modifier un enregistrement dans la base :

```php

// on défini notre requête    
$requete = $connexion->prepare("UPDATE livre SET titre='nouveautitre' WHERE id_livre=43");     
 // on exécute la requête     
$requete->execute(); 
````

Ici le titre du livre sera changé en "nouveau titre" pour le livre
portant l'id numéro 43

![](images/c460164be4a3082c86e13e60a0e707c31f1b1a78.png){width="6.239583333333333in"
height="0.28598097112860893in"}

### **Exercice**

Nous réutiliserons la table auteur que nous avions créé précédemment,
pour rappel :

![](images/c444efdcd497876b199933779d0c4050d1a2f5fc.png){width="1.875in"
height="1.9166666666666667in"}

Le but ici est de créer une page PHP qui va afficher la liste des
auteurs sous forme de tableau. Cette liste sera récupérée directement de
la base de table auteur.\
Exemple de réalisation :

![](images/a320913a66f81a9edc9ec1ee4620b8b9a5bfeb63.png){width="5.6875in"
height="2.357942913385827in"}

Une fois que nos auteurs sont listés, nous allons ajouter en dessous de
cette liste un formulaire qui nous permettra d'en ajouter de nouveaux :

![](images/60e08513e0d841a98f81ed2c2b0dea33566339ff.png){width="5.739583333333333in"
height="2.283875765529309in"}
