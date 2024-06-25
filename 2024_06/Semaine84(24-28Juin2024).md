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