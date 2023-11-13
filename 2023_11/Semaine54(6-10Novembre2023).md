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
    - [x] Display dans la modale de caisse le nombre d'events de trappe par semaine
        - [x] Back
            - [x] events.controller : 
            ```
              @Get('trapdoor-events-in-last-week/:kmoBoxMac')
                async getTrapdoorEventsInLastWeek(@Param('kmoBoxMac') kmoBoxMac: string) {
                const oneWeekAgo = new Date();
                oneWeekAgo.setDate(oneWeekAgo.getDate() - 7);

                const trapdoorEvents = await this.eventsService.getTrapdoorEventsInLastWeek(kmoBoxMac, oneWeekAgo);

                return trapdoorEvents;
            }
            ```
            - [x] events.service : 
            ```
            async getTrapdoorEventsInLastWeek(kmoBoxMac: string, startTime: Date): Promise<Event[]> {
                const trapdoorEvents = await this.eventRepository.find({
                where: {
                    type: TypeEvent.TRAPDOOR_TRIGGERED,
                    kmoBox: {
                    mac: kmoBoxMac,
                    },
                    datetime: MoreThanOrEqual(startTime.toISOString()),
                },
                });
            
                return trapdoorEvents;
            }
            ```
        - [x] Front :
            - [x] events.service : 
            ```
            getTrapdoorEventsInLastWeek(kmoBoxMac: string): Observable<any[]> {
            return this.httpclient.get<any[]>(`${this.apiUrl}/trapdoor-events-in-last-week/${kmoBoxMac}`);
            }
            ```
            - [x] cop-dialog.component :
            ```
            this.eventsService.getTrapdoorEventsInLastWeek(this.data.mac).subscribe({
                next: (events) => {
                    this.trapdoorTriggeredEventCount = events.length;
                },
                error: (err) => {
                    this.snackBar.open(err, 'Ok');
                },
                });
            }
            ```
        - [x] Linting
        - [x] Tests
- [ ] Reprise sujets Anthony
    - [ ] Sécurisation API
        - [ ] Auth keycloak
        

**9 Novembre**
- [ ] Check des mails
- [x] Mise à jour Jira
- [x] Créer la logique pour afficher la dernière fois que la caisse a été allumée
    - [x] Créer la route backend
    - [x] Relier la logique sur le front
    ```
    this.eventsService.getLatestOpenedEvent(kmoBoxMac).subscribe({
      next: (latestOpenedEvent) => {
        if (latestOpenedEvent) {
          this.lastOpenedDate = latestOpenedEvent.datetime.slice(0,10);
        } else {
          this.lastOpenedDate = "Aujourd'hui";
        }
      }
    });
    ```
    - [x] Display sur la modale
        - [x] Linting
        - [x] Tests
- [ ] MàJ de predict prod
    - [ ] git pull origin develop, retour : 
    ```
    git@github.com: Permission denied (publickey).
    fatal: Could not read from remote repository.

    Please make sure you have the correct access rights
    and the repository exists.
    ```
    - [x] Ajout de clé ssh
    ```
    sudo ssh-keygen -t ed25519 -C "g.chevalier@cop-amaco.com"
    ```
    - [x] Ajout aux clés ssh du github
    ```
    sudo cat /root/.ssh/id_ed25519.pub
    ```
    mais sans succes, pull renvoie toujours : 
    ```
    git@github.com: Permission denied (publickey).
    fatal: Could not read from remote repository.

    Please make sure you have the correct access rights
    and the repository exists.
    ```
    Bah au final c'est anthony qui a pull develop -_-
- [ ] KP-585 Flagging de gateway en HS après x jours de non utilisation 
    - [ ] Création de la logique
- [ ] Reprise sujets Anthony
    - [ ] Sécurisation API
        - [ ] Auth keycloak        


**10 Novembre**
- [ ] Check des mails
- [x] Mise à jour Jira
- [ ] Mise en marche du "magasin de tests" en réel
    - [x] Purge de la db de prod
    - [x] Détection du bug denon assignation d'un storeId à une GW lors de la création d'un magasin via l'interface
    - [x] Résolution : 
        - [x] Changement de gateway à gatewayMac dans les fichiers back de store.service & create-store.dto
        - [x] Changement de gateway à gatewayMac dans les fichiers front add-store.component store.ts & post.store.interface
        - [ ] Lint
        - [ ] Tests
    - [ ] Détection du bug de non assignation d'une GW à une KMOBOX