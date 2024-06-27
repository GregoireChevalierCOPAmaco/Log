**24 Juin**
- [ ] Poursuite ESL
- [x] Réunion Copaco 10 h
- [x] Suite et revue de code copaco (voir document réunion correspondant)


**25 Juin**
- [x] Arrêt des containers & docker desktop
- [ ] Poursuite copaco
    - [x] Relance du projet local
    - [ ] Pull & mises à jour
    - [x] Relance de wamp
    - [ ] Revue de code
    ```
    Résolution d'un effet de bord qui ne réserve plus les articles requis à la validation d'une ligne lors de la commande
    cliquer sur l'icone de la calculatrice fait un calcul faux (lancement prod)
    la fonction afficher le chargement (transparent) ser à afficher le gif qui tourne tant que ça charge
    travail sur contenu_lancement_prod.php & gestion_of.php
    Rajout ligne 75 des infos dans la fonction (envoyée que si fabriqué mais pas si acheté)
    L'article apparaît bien dans la ligne avec ses articles réservés maintenant
    au clic de l'arborescence de la nomenclature, 3e bouton sur la ligne d'un lancement de prod, il y a encore l'ancienne façon de faire et pas la nouvelle celle faite par Loïc
    En fait non, juste une erreur de manip par Nicolas
    Dans gestion_of.php , il y a le crud qui redirige. Actuellement il y a un problème lors de la validation en local de l'ordre de fabrication
    En fait la ligne en question, ligne 86 de mobile_contenu_detail_of.php a été commentée et s'est retrouvée sur dev sans le vouloir à cause d'un pull malencontreux
    Codage : A l'expédition, faire sauter la réservation des articles de l'ordre de fabrication
    fichier : gestion_expe.php, lignes   
    ```
- [ ] Petite remise à niveau PHP

**26 Juin**
- [ ] Poursuite copaco
    - [x] Gestion de problème humain : Vincent est venu voir Nicolas pour une galère en fait causée par le fait que Denis n'a pas calculé l'OF dans Copaco, mais uniquement dans Clipper, ce qui a créé un décalage
    - [ ] Résolution du problème en cours ; le port et la pose n'apparaissent pas sur le BL alors qu'ils le devraient
    Recherche dans les fichiers gestion_expe.php
    A priori, l'état de la ligne est à 4 alors qu'il devrait être inférieur à 4 pour apparaître sur le BL.
    ligne 207, ajout d'une condition else : si la l'article n'est pas défini ou n'existe pas
    dans mobile_pce_ligne.php, ligne 223 - 231 retrait du if(isset $articleObj)
    créée une erreur
    ligne 272, problème avec le code_art, retrait du code.
    - [ ] Tester avec du partial la composant relatif à la bâche
Expédition : 
à la sélection d'une commande (bouton +), on génère toutes les lignes relatives aux articles et leurs pièces. Puis on fait le décompte du stock des emplacements correspondants vers l'expédition en sélectionnant manuellement les pièces ligne par ligne.
Quand toutes les lignes sont vertes (marchandise à l'expé), la commande va passer en vert (prête à partir), à l'expédition, Alain Boontz va créer le BL qui va LUI, décompter les articles dans les stocks
Ventilation : ensemble de mouvements de stocks qui permettent d'avoir la bonne quantité préparée par rapport à la qté de la commande. 
Les OFs mettent en ligne la commande
Articles de substitution : référence strictement identique à plusieurs autres sous dénomination/référence différente qui regroupe lesdites références (pour pallier aux demandes spécifiques clients)
- [ ] Résolution de  : Si validation de la même commande deux fois de suite, la première fois tout vabien. Si clic sur la croix rouge qui supprime l'expé et qu'on refait l'expé les qté commande sont fausses


**27 Juin**
- [ ] Reprise ESLs
    - [x] Création de tickets Jira ESL-53→56
    - [ ] ESL-56 : Créer une modale