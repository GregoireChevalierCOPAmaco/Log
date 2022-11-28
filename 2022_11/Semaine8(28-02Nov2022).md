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

- [ ] Parler avec Guillaume de la structure/architecture portail saas (keycloak) des projets pour uniformisation & harmonisation des projets en cours & à venir
- [ ] Penser à donner un justificatif de nouveau domicile à hubert