**2 Janvier**
- [x] Réinstallation du poste de travail
- [x] MàJ du système windows
- [ ] Survol des tâches en cours 
- [x] Remise à jour sur les mails
    - [x] Récupération des IMEI envoyés
    - [x] Prise de connaissance des mails pascal
    Réunion Kevin design ecran tft (UI/UX)
- [ ] Récupérer les IMEI des 3 tablettes monteurs en utilisation
    - [ ] Alexandre WANDRE
    - [x] Frederic LIGER 
    - [x] Nicolas RUXER
    - [x] Update de l'excel en conséquence
- [x] Purge des dockers
- [x] Rebuild des dockers
- [x] Poursuite de l'inventaire des tablettes
    - [x] Nouvel arrivage de 3 tablettes
        - [x] Déterminer la provenance (utilisateur) de chaque tablette
        - [x] Identifier l'IMEI
        - [x] Identifier le n° SIM
        - [x] Faire l'état des lieux
- [x] Faire un mail à Mevin Govinda pour devis 8 tablettes
- [x] Rangement du bureau en prévision de l'audit décathlon, première passe
- [ ] Kmo tentatives de connexion au localhost 4200
    - [x] Connexion avec l'identifiant admin
    - [ ] Connexion avec un user créé en base
        - [ ] Renvoie Erreur du serveur. La console des outils de developpement browser affiche :
        ```
        Failed to load resource: the server responded with a status of 400 (Bad Request)
        ```
- [ ] Kmo amazon SES service
    - [ ] Méthode pret à facturer 
    - [ ] Faire le template avec lien
    - [ ] Trouver où appeler les méthodes
- [ ] Pesner à retester la tablette de Baugue


**3 Janvier**
- [ ] Kmo tentatives de connexion au localhost 4200 
    - [ ] Voir si les changements apportés aux routes n'interfèrent pas avec la connexion. 
        - Plongée dans le code
    - [x] Check de (https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/400#:~:text=The%20HyperText%20Transfer%20Protocol%20(HTTP,%2C%20or%20deceptive%20request%20routing).) & (https://stackoverflow.com/questions/56435467/how-to-fix-failed-to-load-resource-the-server-responded-with-a-status-of-400)
    - [x] Application du correctif adapté
    - [x] Echec, poursuite des recherches
        - [ ]
- [x] Gros rangement accueil & stockage bureau pour l'audit décathlon, de mercredi
- [x] Repasse avec Kevin sur les textes en anglais du site
- [x] Appel avec Mevin pour précisions devis & droits
- [x] Voir avec François Anthony pour activation du lien pour les drois d'accès au compte client orange
- [ ] Voir la démarche à suivre dans l'échange de mail
- [ ] Kmo amazon SES service
    - [ ] Méthode pret à facturer 
    - [ ] Faire le template avec lien
        - [ ] Refactoriser le code pour minimiser les lignes
    - [ ] Trouver où appeler les méthodes
- [ ] Pesner à retester la tablette de Baugue


**4 Janvier**
- [x] Penser à retester la tablette de Baugue
    - [x] La tablette ne s'allume plus. (batterie HS ?)
- [x] Voir la démarche à suivre dans l'échange de mail
    - [x] François a suivi la procédure. Affaire à suivre
    - [ ] Trouver pourquoi c'est François ANTHONY à qui le mail de confirmation a été envoyé
- [ ] Reprise du passage sur l'observation d'atmos
    - [x] Passage sur keycloak
        - [x] Reprise du code
            - [x] Essais en environnement test
            - [ ] Essais en environnement dev
        - [x] Reprise de doc
- [x] Prise de connaissance du document pour la projection à BPI
    - [ ] Parcourir le fichier pptx de présentation
- [ ] Kmo amazon SES service
    - [ ] Méthode pret à facturer
    - [ ] Faire un service de templates à appeler pour alléger le fichier
        - [ ] Faire le template de la fonction createSendPasswordEmailCommand
        - [ ] Faire le template de la fonction createSendReportEmailCommand
        - [ ] Faire le template de la fonction createSendTicketEmailCommand
        - [ ] Faire le template de la fonction createSendModifiedTicketEmailCommand
        - [ ] Faire le template de la fonction createSendDeletedTicketEmailCommand
        - [ ] Faire le template de la fonction createSendUnassignedTicketEmailCommand
        - [ ] Faire le template de la fonction méthode prêt à facturer
            - [ ] Trouver le lien à renseigner pour rediriger vers ...
            - [ ] Intégrer le lien
- Audit Décathlon & alarme incendie
    - [ ] Faire le template avec lien
        - [ ] Refactoriser le code pour minimiser les lignes
    - [ ] Trouver où appeler les méthodes
        - [x] Appel de la fonction createSendPasswordEmailCommand
        - [x] Appel de la fonction createSendReportEmailCommand
        - [ ] Appel de la fonction createSendTicketEmailCommand
        - [ ] Appel de la fonction createSendModifiedTicketEmailCommand
        - [ ] Appel de la fonction createSendDeletedTicketEmailCommand
        - [ ] Appel de la fonction createSendUnassignedTicketEmailCommand
        - [ ] Appel de la fonction méthode prêt à facturer

**4 Janvier**
- [ ] Trouver pourquoi c'est François ANTHONY à qui le mail de confirmation a été envoyé
- [ ] Voir avec Pascal pour l'activation de la sim "clé 4G"
- [ ] Reprise du passage sur l'observation d'atmos
    - [x] Passage sur keycloak
        - [x] Reprise du code
            - [x] Essais en environnement test
            - [ ] Essais en environnement dev
        - [x] Reprise de doc
    - [ ] Parcourir le fichier pptx de présentation
- [ ] Kmo amazon SES service
    - [ ] Méthode pret à facturer
    - [ ] Faire un service de templates à appeler pour alléger le fichier
        - [ ] Faire le template de la fonction createSendPasswordEmailCommand
        - [ ] Faire le template de la fonction createSendReportEmailCommand
        - [ ] Faire le template de la fonction createSendTicketEmailCommand
        - [ ] Faire le template de la fonction createSendModifiedTicketEmailCommand
        - [ ] Faire le template de la fonction createSendDeletedTicketEmailCommand
        - [ ] Faire le template de la fonction createSendUnassignedTicketEmailCommand
        - [ ] Faire le template de la fonction méthode prêt à facturer
            - [ ] Trouver le lien à renseigner pour rediriger vers ...
            - [ ] Intégrer le lien
- Audit Décathlon & alarme incendie
    - [ ] Faire le template avec lien
        - [ ] Refactoriser le code pour minimiser les lignes
    - [ ] Trouver où appeler les méthodes
        - [x] Appel de la fonction createSendPasswordEmailCommand
        - [x] Appel de la fonction createSendReportEmailCommand
        - [ ] Appel de la fonction createSendTicketEmailCommand
        - [ ] Appel de la fonction createSendModifiedTicketEmailCommand
        - [ ] Appel de la fonction createSendDeletedTicketEmailCommand
        - [ ] Appel de la fonction createSendUnassignedTicketEmailCommand
        - [ ] Appel de la fonction méthode prêt à facturer