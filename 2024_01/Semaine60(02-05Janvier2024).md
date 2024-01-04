**2 Janvier**
- [x] Redémarrage windows & màjs
- [ ] Reprise à la suite de fin décembre
    - [ ] Reconnexion à l'instance
        - [x] Connexion à aws avec id root 613152473815
        - [x] Win+R cmd ```ssh -i "Predict-Beta.pem" ubuntu@ec2-3-76-238-223.eu-central-1.compute.amazonaws.com```
    - [ ] Reprise des KP du tableau Jira
        - [ ] Validation de KP-642 : Fix la corrélation temps depuis la dernière utilisation
- [ ] Reprendre la documentation de la fonction gestion uptime
- [x] Démarrage atmos beta pour tests guillaume


**4 Janvier**
- [x] Redémarrage atmos beta
    - [x] Redéploiment du docker certificat ssl
        - [x] Connexion à l'instance
            - [x] Création d'une clé paire
            - [x] ```sudo docker restart deploy-certbot-1```
        - [x] Résolution du problème
            - [x] Restart des containers ``` sudo docker restart atmos_proxy atmos_front atmos_back```
- [x] Push des changements sur develop
    - lint
    - rename de l'uptime
    - ajout de closedtime
- [x] Ajout de lignes aux tests fonctionnels : 
        - Modifier un magasin via l'interface d'administration
        - Dupliquer un magasin via l'interface d'administration
        - Dupliquer un magasin via l'interface d'administration *sans GW disponible*
        - Dupliquer un magasin via l'interface d'administration en assignant une GW déjà assignée
        - Désactiver un magasin
        - Réactiver un magasin désactivé
- [x] KP-664 : Créer une modale de modification de magasin, et supprimer la possibilité de réassigner une GW dedans
    - [x] Création de la branche : 
    ```
    git checkout -b feat/KP-664_create_editStore_modal
    ```
    - [x] Duplication des fichiers addstore
    - [x] Remplacement add par edit
    - [x] Ajout du component au app.module
    - [x] Viabilisation
        - [x] ligne 167 ```if (this.data.method == 'post'){``` changer post ? ou refaire un endpoint back qui ne nécessite pas le champ gateway
        - [x] Modification de l'UpdateStoreDTO en changeant gateway pour gw mac & ajoutant numberofcheckouts
    - [x] Modifs pour fair epasser les tests
    - [ ] Déployer sur la prod