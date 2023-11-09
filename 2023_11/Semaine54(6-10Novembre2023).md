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
- [ ] Mise à jour Jira
- [ ] Créer la logique pour afficher la dernière fois que la caisse a été allumée
    - [ ] Créer la route backend
    - [ ] Relier la logique sur le front
    - [ ] Display sur la modale
- [ ] Reprise sujets Anthony
    - [ ] Sécurisation API
        - [ ] Auth keycloak