- [ ] Réunion(s ?) de briefing sur Copaco, l'erm de nicolas decornet
    - [x] Réunion Copaco 26/04, matinée
    Niveau process de prod, demander à Fédé plutôt que Denis, qui est moins objectif
    Besoin de savoir quelques correspondances entre la base mysql et la base hyperfile (hfsql) de clipper
    Centre de contrôle hfsql accessible depuis le bureau à distance (?)
    Fédé reste maître de la base hfsql, et nous au digital on reprend la base mysql
    une sauvegarde automatique de la base de données clipper se fait tous les jours, zippée pour plus de praticité
    En cas de rollback nécessaire, plutôt cibler les fichiers à remplacer que de reload toute la base. (Fichiers .FIC dans le serveur /C/Serveur_HF/BDD_Backup/... avec cop et amaco ,distincte)
    commandes client ou fournisseur, ça reste côté clipper, pas copaco
    Fédérico s'en va en mars/avril prochain.
    Structure du code du projet copaco ; nicolas a fait évoluer sa manière de coder en fonction de ce qu'il a assimilé sur les méthodes (de production ?). Serait idéal d'avoir ses notes et insights là-dessus
    Si stabilisé, charge de travail (maintenance & debug) estimée à 1/2 à 1 journée de travail.
    nicolas a dit faire beaucoup de tests. Il y a des procédures, des cadres de tests ou c'est informel ?
    Pour l'instant, informel. L'équipe va normalement créer un document qui explique les relations et dépendances entre les modules de copaco
    fonctionnalités à développer
    - création des données techniques
    - création de comptes
    - gestion des droits de compte
    - gestion des créations et réassignations de marchandises
    - adaptation à amaco de copaco
    but de copaco est d'etre le reflet des flux physiques
    Andzrei et Fédé seront amenés à utiliser l'outil, ils connaissent un peu et ont déjà vu à quelques reprises l'app.
    ===
    Description des flux
    Travail à l'affaire / travail au catalogue , cop c'est un bordel entre les deux, même si plutôt travail à l'affaire en ce moment.
    **___** Besoin -> Bureau d'études (assemblage et hiérarchie des pièces : nomenclature) logiciel utilisé : créo, versionné avec windchill. Quand c'est validé, ça arrive aux méthodes
    **___** Méthodes -> Traduire les nomenclatures qui viennent du BE et de l'industrialiser, à savoir comment usiner et sur quelles machines. Traduction du plan dans l'entreprise et assignation aux différents pôles de l'atelier, et ajout des gammes (quels moyens matériels). Méthodes = intégrer les étapes réalisées sur le terrain en prenant matière première, temps machine & machine utilisée & composants nécessaires
    **___** Ordonnancement : Traduire nomenclature et gammes côté production. Pour chaque article est décidé l'ordre d'importance (réserver sur stock, soit commander soit lancer prod interne). Sur copaco, l'icone calculette permet d'algorithmer le travail d'ordonnancement actuellement fait par Deins & Fédé
    **___** Achats -> pas développé dans la réunion
    **___** Ordres de fabrication -> en cas d'arborescence, les O.F sont réservés entre eux, exemple, les composants de la pièce petit-enfant et enfant seront réservées/achetées/produites pour la pièce enfant.
    Quand les O.F et les réservations sont générées, il y a une vue "lancement atelier". Chaque chef d'atelier veut lancer les o.f qui concernet son atelier. Dans la vue, un dropdown permet de trier par atelier (ex. tolerie, peinture etc) et affiche tout ce qui concerne l'atelier en question trié par gamme. Plusieurs opérations (découpe, pliage, peinture ... peuvent être assignée à une même o.f). La vue mpontre ce qui est en attente de fabrication. Dans la même vue, à droite est la partie qui est actuellement en production, avec un code coloré pour :
    - 0, vient d'etre généré
    - 1, vient d'etre lancé, le chef d'atelier a déclenché
    - 2, faisable, les étapes précédentes ont été faites (découpe et pliage avant peinture par ex)
    - 3 démarré (assigné par un utilisateur d'atelier), en cours d'exécution
    - 4 clôturé 