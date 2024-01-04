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
- [ ] Redémarrage atmos beta
    - [ ] Redéploiment du docker certificat ssl
- [ ] Push des changements sur develop
    - lint
    - rename de l'uptime
    - ajout de closedtime