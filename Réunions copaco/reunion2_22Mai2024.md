- [x] Réunion Copaco 22/05, matinée
    Mise dans la boucle de Michel Jezequiel & Denis
    Commande de test pour prod
    Montage (assemblage) & expédition à tester de façon extensive, le reste a été fait par Nicolas (tests sur libourne car 012 protection moneygram se 12 001 819)
    but des tests ; en plus d'avoir le feedback visuel, s'assurer que les mouvements de stocks et inventaires soient ajustés correctement
    étapes du test : 
    - synchronisation des commandes client (page lancement de prod)
    - synchronisation de clipper
    - mise à jour du Calcul du Besoin Net qui fait le lien entre les stocks et les commandes clients qui réservent du stock et lève des alertes dans le cas où il y a un problème de stock ou de délai (stock commandé arrivant trop juste ou après une date limite)
    - dans l'admin des of, lancer la création d'OF va avoir une incidence sur le CBN qui va générer des ordres d'achat sur les articles manquants le cas échéant
    - marchandise suivie lors de la réception dans le module mobile réception d'une commande en rentrant les références du bon de livraison
    - impression d'étiquette pour scanner et savoir ce qu'il y a dans les caisses/palettes
    - création de la commande fournisseur en fonction du bon de livraison et toutes les étapes que faisait Claudine manuellement
    - import dans copaco des données (nomenclature plus gamme mais pas couleur) de clipper
    - lancement de commande complète (ou affaire précise) en production
    - suivi sur la page en arborescence du suivi des Ordres de Fabrication, qui sont ordonnées par étapes chronologiques de production, et suivi possible de leur état d'avancement (via code couleur)
    - Possibilité d'ajouter des articles en drag and drop dans /fab/creation de dt une fois qu'on a cliqué sur l'icone ronde de créo
    - au lancement de production, les O.F sont dispatchés chez les chefs d'atelier (fab/ lancement atelier)
    - Montage, assemblage & expédition pas trackés dans copaco atm

En cas d'interruption de copaco pour cause de bug, repasser sous clipper+ancienne méthode papier. Et ensuite, redresser manuellement le stock copaco pour calquer sur celui de clipper. Dans le cas où l'interruption est prolongée, se référer à la table des mouvements

Rester à jour et synchroniser souvent copaco avec les bases via le bouton synchro sur l'interface

