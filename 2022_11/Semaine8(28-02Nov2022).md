**28 Novembre** 
- [x] Résolution du conflit pour merge COP-sav sur develop
    - [x] Fichier kmo-app/src/app/pages/settings/settings.page.ts, conflit sur deux lignes identiques ?? :
    ```
        changeVehicleUsed($event) {
    <<<<<<< KW-297_tests_database
        this.toastService.toast('Vous utilisez maintenant le véhicule ' + $event.target.value, 'success', "top");
    =======
        this.toastService.toast('Vous utilisez maintenant le véhicule ' + $event.target.value, 'success', 'top');
    >>>>>>> develop
        this.localStorageService.set('vehicle', $event.target.value);
    }
    ```
- [ ] Projet lidl sav tablettes : Structurer les infos & donner de la visibilité
    - [ ] Partie tablettes & monteurs
        - [ ] Structurer les infos
            - [ ] Mettre à jour les documents excel
                - [ ] Listing monteurs : demander à Nicolas ses coordonnées
                - [ ] Fichier ListingUsers.xls
                    - [x] Création d'un fichier snapshot backup
                    - [x] Suppression des 4 monteurs partis de la liste
                    - [x] Mise en bleu des monteurs dont l'adresse mail est valide et utilisée
                    - [x] Récupérer n°téléphone de Behili/Zmiri/Guillet/Amejoud/Lajon. Récupéré & rensigné
            - [ ] Téléphoner à chaque monteur ayant potentiellement une tablette pour savoir ce qu'ils en ont fait ?
                - [ ] Pour les monteurs de rang 1, s'assurer qu'ils ont une adresse cop valide && leur dire de les utiliser le cas échéant.
                - [ ] Préparation de la liste des monteurs à appeler
            - [x] Prise de connaissance du doc C:/Users/gchevalier/Desktop/Lidl-sav-tablettes/docs%20mail/TUTO-mail-monteur.pdf
            - [ ] Combien de tablettes ont été achetées
            - [ ] Combien de tablettes sont en circulation
                - [ ] 19 ou 29 ? Trouver si la relance de 10 a été faite ou pas ?
            - [x] Qui à une tablette
                - [x] Check du fichier tablette-cop _11_2020.xls
                - [x] Au 12/11/2020, à priori 29 tablettes ont été données
            - [ ] Où est chaque tablette
            - [ ] Récupérer les contrats orange en cours
            - [ ] Récupérer les attestations de récupération de tablettes
            - [ ] Gérer les tablettes
                - [x] Créer un excel
                - [x] Identifier les tablettes du bureau
                    - [x] Renseigner le modèle de tablette 
                    - [x] Renseigner l'état de la tablette 
                    - [x] Renseigner les pinID 
                    - [x] Renseigner les codes de verrouillage 
                - [ ] Gérer les tablettes dans la nature
                    - [ ] Renseigner le modèle de tablette 
                    - [ ] Renseigner l'état de la tablette 
                    - [ ] Renseigner les pinID 
                    - [ ] Renseigner les codes de verrouillage 
    - [ ] Partie Users accès & droits
        - [ ] Accéder à la bdd
            - [ ] Lister les users & leurs droits
        - [ ] Accéder au déploiement
            - [ ] Lister les users & leurs droits
        - [ ] Structurer les infos
            - [ ] Mettre à jour les documents excel
- [ ] Démarche obtention contrat 4G Orange : L'objectif est de se faire présenter des offres de formules pour connecter du matériel en magasin à internet (via clé 4G par exmple)
    - [x] Prise de rendez vous Mévin Govinda, réunion teams mardi 29 14h30


**29 Novembre** 
- [ ] Projet lidl sav tablettes : Structurer les infos & donner de la visibilité
    - [ ] Partie tablettes & monteurs
        - [ ] Structurer les infos
            - [x] Check des informations des 4 tablettes du bureau
            - [ ] Mettre à jour les documents excel
                - [ ] Listing monteurs : demander à Nicolas ses coordonnées
                - [ ] Fichier ListingUsers.xls
                - [ ] Fichier Copie ListingMonteurs.xls
                    - [ ] Numéro F.Loriot non attibué, trouver le num de remplacement
                    - [ ] Trouver numéro de T.Diess
                    - [x] Trouver numéro de JM.Ackermann pas la peine, ne fait plus partie de l'effectif
                    - [x] Trouver numéro de A.Avenel pas la peine, ne fait plus partie de l'effectif
                    - [x] Trouver numéro de JS.Baugue pas la peine, ne fait plus partie de l'effectif
                    - [x] Trouver numéro de D.Bedel pas la peine, ne fait plus partie de l'effectif
                    - [x] Trouver numéro de F.Bardera pas la peine, ne fait plus partie de l'effectif
                    - [x] Trouver numéro de S.Chocron pas la peine, ne fait plus partie de l'effectif
                    - [x] Trouver numéro de M.Ferroudj. Pas la peine, décédé.
                    - [x] Trouver numéro de D.Jonchere pas la peine, ne fait plus partie de l'effectif
                    - [x] Trouver numéro de D.Lentiez pas la peine, ne fait plus partie de l'effectif
                    - [x] Trouver numéro de J.Vienne pas la peine, ne fait plus partie de l'effectif
            - [x] Téléphoner à chaque monteur ayant potentiellement une tablette pour savoir ce qu'ils en ont fait ?
                - [x] Pour les monteurs de rang 1, s'assurer qu'ils ont une adresse cop valide && leur dire de les utiliser le cas échéant.
                - [x] Préparation de la liste des monteurs à appeler
                - [x] Ont répondu :
                    - A.Suhr, aurait rendu à Luis qui aurait transmis à A.Wandres
                    - A.Charles, tablette possédée, fonctionnelle & utilisée
                    - D.Hainque, tablette possédée, écran cassé, sera ramenée par un collègue
                    - G.Baradian, tablette possédée
                    - R.Guartieri, aurait renvoyé la tablette ici
                    - R.Bordet, tablette possédée, fonctionnelle & utilisée
                    - R.Christiano, arrêt de travail, tablette transmise à l'intérimaire de W.DeCottonier
                    - R.Privat, tablette possédée, fonctionnelle & utilisée
                    - S.Blampain, jamais eu de tablette
                    - W.DeCottonier, 3 tablettes dans son équipe, toutes fonctionnelles & utilisées
                - [x] N'ont pas répondu & mail envoyé :
                    - A.Sy
                    - C.Daibissaram
                    - C.Deschamps
                    - C.Carabeau
                    - F.Ledrole
                    - F.Ritter
                    - G.Guillet
                    - J.Ivanoff
                    - L.Amejoud / Répondu au mail envoyé
                    - N.Bordron
                    - R.Leroy
                    - S.Zrirak
            - [ ] Combien de tablettes ont été achetées
            - [ ] Combien de tablettes sont en circulation
                - [ ] 19, 29, 34, 36 ? Trouver si la relance de 10 a été faite ou pas ?
            - [ ] Qui à une tablette
                - [x] MàJ du fichier listing monteurs.xls
                - [x] MàJ du fichier suivi tablettes.xls
                - [x] Retour mail de Lachene Amejoud qui l'aurait transmise à Arnaud Charles por retour à Nordhouse
                    - [ ] Lien entre les monteurs & numéro de tablette pour état du matériel
                    - [ ] Téléphoner à WANDRES pour numéro imei tablette (anciennement celle de SUHR ?)
                    - [ ] Téléphoner à LIGER pour numéro imei tablette
                    - [ ] Téléphoner à RUXER pour numéro imei tablette
            - [ ] Où est chaque tablette
                - [ ] Màj du fichier tablette-cop.xls
            - [ ] Récupérer les contrats orange en cours
            - [ ] Récupérer les attestations de récupération de tablettes
            - [ ] Gérer les tablettes
                - [x] Créer un excel
                - [x] Identifier les tablettes du bureau
                    - [x] Renseigner le modèle de tablette 
                    - [x] Renseigner l'état de la tablette 
                    - [x] Renseigner les pinID 
                    - [x] Renseigner les codes de verrouillage 
                - [ ] Gérer les tablettes dans la nature
                    - [ ] Renseigner le modèle de tablette 
                    - [ ] Renseigner l'état de la tablette 
                    - [ ] Renseigner les pinID 
                    - [ ] Renseigner les codes de verrouillage 
- [ ] Résolution du problème de mail pro R.Privat
    - [x] Echange de mail pour récupération pjs
    - [ ] Voir pourquoi pw incorrect ?
        - [x] Renvoi de mail pour essai avec mdp 123Cop@67, attente de réponse
    - [ ] Partie Users accès & droits
        - [ ] Accéder à la bdd
            - [ ] Lister les users & leurs droits
        - [ ] Accéder au déploiement
            - [ ] Lister les users & leurs droits
        - [ ] Structurer les infos
            - [ ] Mettre à jour les documents excel
- [ ] Démarche obtention contrat 4G Orange : L'objectif est de se faire présenter des offres de formules pour connecter du matériel en magasin à internet (via clé 4G par exmple)
    - [x] Rendez vous Mévin Govinda, réunion teams 14h30
        - [x] Préparation de l'entretien
        - [x] Retour rdv :
            - 36 Lignes mobiles réparties sur 2 comptes de facturation 34 (626 82 518)/ 2 (612 44 027)
            - Connexion internet par clé 4G : forfait 5Go 4G bloqué ou 100 Go flex 5G puis débit réduit
            - Délai de livraison 72h après passage commande
            - Voir doc technique de la ou les solutions
    - [ ] Chercher et démarcher pour offre clé 4G concurrence afin de négocier

**30 Novembre** 
- [ ] Projet lidl sav tablettes : Structurer les infos & donner de la visibilité
    - [ ] Partie tablettes & monteurs
        - [ ] Structurer les infos
            - [ ] Mettre à jour les documents excel
                - [ ] Listing monteurs : demander à Nicolas ses coordonnées
                - [x] Fichier ListingUsers.xls
                - [ ] Fichier Copie ListingMonteurs.xls
                    - [ ] Numéro F.Loriot non attibué, trouver le num de remplacement
                    - [x] Numéro S.Zrirak non attibué, trouver le num de remplacement
                    - [ ] Trouver numéro de T.Diess
            - [x] Repasse téléphoner à chaque monteur ayant potentiellement une tablette pour savoir ce qu'ils en ont fait ?
                - [x] Préparation de la liste des monteurs à appeler
                - [x] Ont répondu :
                    - F.Ritter, tablette cassée lors de chantier, amenée pour check à un samsung point service, puis jetée car irrécupérable
                    - G.Guillet, all ok
                    - C.Carabeau, pas de tablette, 1 pour 2 avec N.Bordron
                    - N.Bordron, ok tablette, faire mail sur perso pour résolution mail pro
                - [x] N'ont pas répondu  :
                    - A.Sy, messagerie vocale immdt.
                    - C.Daibissaram, messagerie vocale immdt.
                    - C.Deschamps, bon numéro ? vraiment ?
                    - J.Ivanoff
                    - R.Leroy, messagerie vocale immdt
                    - S.Zrirak
                - [x] Màj du fichier suivi tablettes.xls en conséquence
            - [x] Voir quoi faire pour le cas F.Ritter
                - [x] Envoyer mail à Pascal Bonetto avec Ritter en copie
                - [x] Voir la fin de l'échange entre Bonetto & Ritter
            - [ ] Combien de tablettes ont été achetées
            - [ ] Combien de tablettes sont en circulation
                - [ ] 19, 29, 34, 36 ? Trouver si la relance de 10 a été faite ou pas ?
            - [ ] Qui à une tablette
                    - [ ] Lien entre les monteurs & numéro de tablette pour état du matériel
                    - [ ] Téléphoner à WANDRES pour numéro imei tablette (anciennement celle de SUHR ?)
                        - [x] Récupération numéro complet ; celui renseigné dans le excel a 9 chiffres
                            - [x] Mail aux RH & rectification dans le excel liste users
                    - [ ] Téléphoner à LIGER pour numéro imei tablette
                    - [ ] Téléphoner à RUXER pour numéro imei tablette
            - [ ] Où est chaque tablette
                - [ ] Màj du fichier tablette-cop.xls
            - [ ] Récupérer les contrats orange en cours
            - [ ] Récupérer les attestations de récupération de tablettes
            - [ ] Gérer les tablettes
                - [x] Créer un excel
                - [x] Identifier les tablettes du bureau
                    - [x] Renseigner le modèle de tablette 
                    - [x] Renseigner l'état de la tablette 
                    - [x] Renseigner les pinID 
                    - [x] Renseigner les codes de verrouillage 
                - [ ] Gérer les tablettes dans la nature
                    - [ ] Renseigner le modèle de tablette 
                    - [ ] Renseigner l'état de la tablette 
                    - [ ] Renseigner les pinID 
                    - [ ] Renseigner les codes de verrouillage 
- [x] Résolution du problème de mail pro R.Privat
    - [x] Traitement en cours
    - [x] Frank a reset le pw à COP67
- [ ] Résolution du problème de mail pro N.Bordron
    - [x] Mail envoyé sur adresse perso
    - [x] Traitement en cours
        - [ ] Trouver la cause du problème : ne peut pas dissocier mail de sa tablette/ ne peut se déconnecter
        - [ ] Voir avec Camille Rossi à ce sujet
- [ ] Partie Users accès & droits
    - [ ] Accéder à la bdd
        - [ ] Lister les users & leurs droits
    - [ ] Accéder au déploiement
        - [ ] Lister les users & leurs droits
    - [ ] Structurer les infos
        - [ ] Mettre à jour les documents excel
- [x] Récupération de la tablette 352 807 082 442 874 de la main de bonetto.
- [x] Démarche obtention contrat 4G Orange : L'objectif est de se faire présenter des offres de formules pour connecter du matériel en magasin à internet (via clé 4G par exmple)
    - [x] Chercher et démarcher pour offre clé 4G concurrence afin de négocier
    - [x] Relancer Mevin a 16h pour réponses devis & ids plateforme
- [ ] Est-ce que la clé 4G fournit internet via usb ou émet du wifi ??


**1er Décembre** 
- [ ] Projet lidl sav tablettes : Structurer les infos & donner de la visibilité
- [ ] Démarche obtention contrat 4G Orange 
    - [ ] Gestion d'équipement de test
        - [ ] Objectif : obtenir un jeu d'un routeur et une clé usb pour tests en réel
        - [ ] Communication avec M.Govinda pour obtention d'un jeu de test :
            - forfait équilibre 4G 100 Go (5G pas nécessaire atm, voir plus tard)
            - clé 4G
            - routeur + eth : airbox ou flybox ? besoin de manipulation, plug and play ? Configuration ?
        - [x] Doublage de l'appel par un mail, mise de frank en cc
        - [x] Appel avec M.Govinda : bon de commande à venir dans la journée
        - [ ] voir la question de la sécurité
        - [ ] voir pour retour de la solution non acceptée ... à faire dans l'échange de mail
    - [ ] Est-ce que la clé 4G fournit internet via usb ou émet du wifi ??
- [x] Réunion catch up avec Théo
    - [x] Mise à niveau du projet chez Théo
    - [x] Test de reproduction des erreurs R3NullInjector (43 tests failed)
        - [x] Utiliser npm test au lieu de ng test fait fail les tests à cause de httpclient
        - [x] Suppression du projet en local et réinstallation complète
        - [x] Git clone
        - [x] Git Checkout
        - [x] cd kmo-back
            - [x] npm i --legacy-peer-deps
            - [x] npm test --detectOpenHandles : erreur
            - [x] Résolution de l'erreur : 
            ```
            Error: Jest: Got error running globalSetup - C:\Users\gchevalier\cleankmo2R3NI\KMO_WEB\kmo-back\node_modules\jest-mysql\setup.js, reason: Cannot find module 'mysql'
            ```
            - [x] Grosse install bien sale de ```npm install mysql --legacy-peer-deps``` dans le dossier back & les tests passent comme attendu
        - [x] cd COP-sav
            - [x] ng test --detect-open-handles --coverage --test-name-pattern="Report" résultat :
            ```
            Test Suites: 2 failed, 45 skipped, 4 passed, 6 of 51 total
            Tests:       47 skipped, 26 passed, 73 total
            ```        

    - [ ] Partie tablettes & monteurs
        - [ ] Structurer les infos
            - [ ] Mettre à jour les documents excel
                - [ ] Listing monteurs : demander à Nicolas ses coordonnées
                - [ ] Fichier ListingUsers.xls
                - [ ] Fichier Copie ListingMonteurs.xls
                    - [ ] Numéro F.Loriot non attibué, trouver le num de remplacement 
                    - [ ] Trouver numéro de T.Diess
            - [ ] Repasse téléphoner à chaque monteur ayant potentiellement une tablette pour savoir ce qu'ils en ont fait ?
                - [ ] Préparation de la liste des monteurs à appeler
                - [ ] Ont répondu : 
                    - Pascal Lajon, n'a jamais eu de tablette
                - [ ] N'ont pas répondu  : 
                - [ ] Màj du fichier suivi tablettes.xls en conséquence 
            - [ ] Combien de tablettes ont été achetées
            - [ ] Combien de tablettes sont en circulation 
            - [ ] Qui à une tablette
                    - [ ] Lien entre les monteurs & numéro de tablette pour état du matériel
                    - [ ] Téléphoner à WANDRES pour numéro imei tablette (anciennement celle de SUHR ?) 
                    - [ ] Téléphoner à LIGER pour numéro imei tablette
                    - [ ] Téléphoner à RUXER pour numéro imei tablette
            - [ ] Où est chaque tablette
                - [ ] Màj du fichier tablette-cop.xls
            - [ ] Récupérer les contrats orange en cours
            - [ ] Récupérer les attestations de récupération de tablettes
            - [ ] Gérer les tablettes
                - [x] Créer un excel
                - [x] Identifier les tablettes du bureau
                    - [x] Renseigner le modèle de tablette 
                    - [x] Renseigner l'état de la tablette 
                    - [x] Renseigner les pinID 
                    - [x] Renseigner les codes de verrouillage 
                - [ ] Gérer les tablettes dans la nature
                    - [ ] Renseigner le modèle de tablette 
                    - [ ] Renseigner l'état de la tablette 
                    - [ ] Renseigner les pinID 
                    - [ ] Renseigner les codes de verrouillage  
- [ ] Résolution du problème de mail pro N.Bordron
    - [x] Mail envoyé sur adresse perso
    - [x] Traitement en cours
        - [ ] Trouver la cause du problème : ne peut pas dissocier mail de sa tablette/ ne peut se déconnecter
        - [ ] Voir avec Camille Rossi à ce sujet
- [ ] Partie Users accès & droits, voir avec Frank
    - [ ] Accéder à la bdd
        - [ ] Lister les users & leurs droits
    - [ ] Accéder au déploiement
        - [ ] Lister les users & leurs droits
    - [ ] Structurer les infos
        - [ ] Mettre à jour les documents excel 


**2 Décembre** 
- [ ] Projet lidl sav tablettes : Structurer les infos & donner de la visibilité
- [ ] Démarche obtention contrat 4G Orange 
    - [ ] Gestion d'équipement de test
        - [ ] Objectif : obtenir un jeu d'un routeur et une clé usb pour tests en réel
        - [x] Communication avec M.Govinda pour obtention d'un jeu de test :
            - forfait équilibre 4G 100 Go (5G pas nécessaire atm, voir plus tard)
            - clé 4G
            - routeur + eth : airbox ou flybox ? besoin de manipulation, plug and play ? Configuration ?
        - [x] Retour à Mevin Govinda pour aspect test et demandes retour solution non retenue
            - [x] Rédaction du brouillon pour validation Frank
            - [x] Confirmation et envoi du mail après retouches mineures
- [ ] Résolution des erreurs post update docker
    - [x] Résolution de l'erreur :
    ```
    [Nest] 22032  - 01/12/2022 16:23:34   ERROR [TypeOrmModule] Unable to connect to the database. Retrying (3)...
    TypeError: this.mysql.createPool is not a function
    ``` 
    Le message en complément de l'erreur (TypeORMError: Entity metadata for Vehicle#pieceVehicle was not found. Check if you specified a correct entity object and if it's connected in the connection options.) renseignait sur les entités à déclarer dans l'argument entities de la connexion co, ligne 29 de rapports.e2e-spec.ts  
    Résoluition comme suit : 
    ```
    entities: [
			Checkout, 
			User, 
			Store, 
			Data, 
			RegionalManagement,
			Ticket,
			TicketHistory,
			Vehicle,
			PieceVehicle,
			Piece
		]
    ```
    - [x] Résolution de l'erreur :
    ```
    ReportsService › Connection to db › should return the instance of Report which name is report reference

    expect(received).toBeTruthy()

    Received: undefined
    ``` 
    Rectifictation du findOne ligne 529 : 
    ```
    .findOne({ reference: 'report test magasin ticket 2345' });
    ```
    - [ ] Résolution de l'erreur :
    ```
    Nest can't resolve dependencies of the UsersService (UserRepository, JwtService, ?). Please make sure that the argument MailerService at index [2] is available in the UsersModule context.
    ``` 
    -> besoin de créer 2 instances de *store* avec id  1 & 2 pour faire passer les tests. Voir pour automatiser si non créés ?

- [ ] Parler avec Guillaume de la structure/architecture portail saas (keycloak) des projets pour uniformisation & harmonisation des projets en cours & à venir
- [ ] Penser à donner un justificatif de nouveau domicile à hubert

