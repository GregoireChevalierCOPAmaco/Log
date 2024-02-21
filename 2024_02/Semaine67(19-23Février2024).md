**19 Février**
- [ ] Poursuite user story KP-705
    - [x] KP 706 Affichage visuel de caisse en maintenancee sur l'interface de store
    - [x] KP-750 fix le comportement de détection de trapdoor event pour ne prendre que les 30 dernières secondes dans le cas du reload
        - [x] Ajout de : 
        ```
        this.eventService.getLatestTrapdoorEvent().subscribe({
            next: (data) => {
                if (data && data.id) {
                if (data && data.datetime) {

                    const trapdoorDate = new Date(data.datetime);
                    const currentDate = new Date();
                    const currentUtcDate = new Date(
                    currentDate.getUTCFullYear(),
                    currentDate.getUTCMonth(),
                    currentDate.getUTCDate(),
                    currentDate.getUTCHours(),
                    currentDate.getUTCMinutes(),
                    currentDate.getUTCSeconds(),
                    currentDate.getUTCMilliseconds()
                    );

                    const trapdoorUtcDate = new Date(
                    trapdoorDate.getUTCFullYear(),
                    trapdoorDate.getUTCMonth(),
                    trapdoorDate.getUTCDate(),
                    trapdoorDate.getUTCHours(),
                    trapdoorDate.getUTCMinutes(),
                    trapdoorDate.getUTCSeconds(),
                    trapdoorDate.getUTCMilliseconds()
                    );          

                    const timeDifference = currentUtcDate.getTime() - trapdoorUtcDate.getTime();

                    const timeDifferenceInSeconds = (timeDifference / 1000);
                    
                    if (timeDifferenceInSeconds < 30) {
                    this.latestTrapdoorEventId = data.id;
                    this.eventService.getKmoBoxMacByEventId(this.latestTrapdoorEventId).subscribe({
                        next: (data) => {
                        if (data && data.kmoBoxMac) {
                            this.kmoBoxService.setMaintenanceLevelToCurative(data.kmoBoxMac).subscribe({
                            next: (response) => {
                                console.log("Maintenance level set to Curative successfully:", response);
            
                                if (this.refreshFlag) {
                                this.refreshFlag = false;
                                setTimeout(() => {
                                    this.refreshFlag = true;
                                    
                                    this.router.routeReuseStrategy.shouldReuseRoute = () => false;
                                    const currentUrl = this.router.url;
                                    this.router.navigateByUrl('/', { skipLocationChange: true }).then(() => {
                                    this.router.navigate([currentUrl]);
                                    });
                                }, 25000); 
                                }
                            },
                            error: (error) => {
                                console.error("Failed to set maintenance level to Curative:", error);
                            },
                            });
                        } else {
                            console.error("Invalid data received for kmoBoxMac:", data);
                        }
                        },
                        error: (err) => {
                        throw err;
                        },
                    });
                    }else {
                    console.log("Last trapdoor event is older than 30 seconds");              
                    }
                
                }
                
                } else {
                console.error("Invalid data received for latestTrapdoorEventId:", data);
                }
            },
            error: (err) => {
                throw err;
            },
            });
        ```
        à checkDysfunctionalCheckouts() de cop.components
    - [x] Validation corrélation données sur place au 12
        - [x] Passage au 12 pour voir le comportement
        - [x] Détection du fait que le snackbar ne s'affiche pas
        - [x] Dû au fait que les 3600 secondes et le UTC ont la même fonction, à savoir rétablir l'équilibre entre le temps bdd et le temps appli
        - [x] Retrait du 3600
        - [x] Commit
    - [ ] KP 745 Ajouter un code coloré dans la page de magasin pour traduire l'ouverture/fermeture de caisse
- [x] Suppression des events en base prod
- [x] Suppression des caisses non associées en base prod


**20 Février**
- [x] Poursuite user story KP-705
    - [x] Lint
    - [x] Tests
    - [x] Push
    - [x] PR
    - [x] Déploiement en prod
- [ ] KP 745 Ajouter un code coloré dans la page de magasin pour traduire l'ouverture/fermeture de caisse
- [x] Créer un nouveau client keycloak
- [x] Créer une instance vierge et déployer dessus
    - [x] Connexion 
    - [x] Installer git (déjà installé)
    - [ ] Installer docker ```sudo snap install docker```
    - [x] Lier git et le serveur par clé ssh git
        - ```ssh-keygen```
        - cat  /home/ubuntu/.ssh/id_rsa.pub
        - Ajout dans la liste des clés de github
        - clone du repo ```git clone git@github.com:COP-AMACO/KMO_PREDICT.git```
    - [x] cd 
    - [x] checkout develop_demoEuroCIS
    - [x] Modifs du .env.prod
    - [x] sudo docker
    - [x]  sudo  docker compose --env-file .env.prod -f docker-compose-prod.yml up --always-recreate-deps --build
    - [x] network net declared as external, but could not be found, :
    ```
    sudo docker network create net
    ```
    - [x] docker de certification :
    ```
    sudo  docker compose --env-file .env.prod -f docker-compose-cert.yml up --build
    ```
    - [x] Ouvrir ports 80 & 443 sur groupes de sécurité amazon aws (entrant & sortant)
    - [x] Ouvrir port 5432 pour db aussi
    - [x] Relance docker cert
    - [x] changer les envs prod dans le front et back
    - [x] Recréer les docker
    - [x] Réassignation des bonnes routes à la base de démo
    - [x] Créer un magasin 
    - [x] Assigner GW
    - [x] Créer caisse
- [x] Créer un user demoCOP avec tous les droits


**21 Février**
- [ ] Terminer préparatifs pour EuroCIS
    - [ ] KP-745 Ajouter un code coloré dans la page de magasin pour traduire l'ouverture/fermeture de caisse
    - [ ] KP-762 Changer le thème de login KC
    - [ ] KP-765 Changer les libellés de caisse
    - [ ] KP-766 Traduire l'application en anglais
    - [ ] KP-767 Supprimer la mat-tab "message d'erreur" pour le salon
    - [ ] KP-768 Retirer l'apparition de la snackbar "Trapdoor OK" quand il n'y a pas eu d'event de trapdoor dans les 30 secondes