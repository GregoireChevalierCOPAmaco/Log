**6 Novembre**
- [ ] Check des mails
- [ ] Mise à jour Jira
- [ ] Reprise sujets Anthony
    - [x] Déploiement Predict
        - [x] Récupération des infos d'Anthony
            - [x] Connection à l'instance
                - [x] Erreur : ```@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @```
                - [x] Suppression du fichier /ssh/known hosts et reconnexion : 
                ```
                Warning: Permanently added 'ec2-52-58-79-10.eu-central-1.compute.amazonaws.com' (ED25519) to the list of known hosts.
                ubuntu@ec2-52-58-79-10.eu-central-1.compute.amazonaws.com: Permission denied (publickey).
                ```
                - [x] Récupération de la public key 
                - [x] ```sudo apt upgrade```et reboot de l'instance
            - [x] Relance de Docker : 
                - [x] Résolution 
                ```
                ERROR [internal] booting buildkit  
                http: invalid Host header
                ```
                - [x] Check de () et application : 
                ```
                sudo snap refresh docker --channel=latest/edge
                ```
                reprise de la dernière version edge de docker
            - [x] Relance du Docker cert **apres** avoir up les apps 
            - [x] Réassigner l'adresse ip sur aws
    - [ ] Sécurisation API
        - [ ] Auth keycloak


**7 Novembre**
- [ ] Check des mails
- [ ] Mise à jour Jira
- [ ] Reprise sujets Anthony
    - [ ] Sécurisation API
        - [ ] Auth keycloak