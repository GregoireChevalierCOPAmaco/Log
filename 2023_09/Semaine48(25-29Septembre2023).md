**25 Septembre**
- [ ] Mise à jour Kélio
- [ ] KP-490 : Fix l'affichage des magasins sur /cop pour pouvoir afficher les magasins au-delà de 10
    - [x] Création de gateways dans la base locale pour arriver à 12
    ```
        INSERT INTO public.gateway (mac, datetime, "diskUsage", "temperatureCpu", "freeRam", services)
        VALUES ('te:st:gr:eg:01', '2023-03-01 16:48:43.000000', 101, 29, 30,
        '{
            "ATMOS": {
            "active": true,
            "memory_usage": "46.6M",
            "mac_declared": "te:st:gr:eg:01",
            "extras": {
                "operational_rate": 98.66666666666667
            }
            },
            "RTunnel": {
            "active": true,
            "memory_usage": "11.8M"
            }
        }'),
        ('te:st:gr:eg:02', '2024-03-01 16:48:43.000000', 2323, 29, 30, '{
            "ATMOS": {
            "active": true,
            "memory_usage": "46.6M",
            "mac_declared": "te:st:gr:eg:02",
            "extras": {
                "operational_rate": 98.66666666666667
            }
            },
            "RTunnel": {
            "active": true,
            "memory_usage": "11.8M"
            }
        }'),
        ('te:st:gr:eg:03', '2023-02-22 09:45:48.347000', 13, 39.444, 81.52787453215763, '{
            "ATMOS": {
            "active": true,
            "memory_usage": "46.6M",
            "mac_declared": "te:st:gr:eg:03",
            "extras": {
                "operational_rate": 98.66666666666667
            }
            }
        }'),
        ('te:st:gr:eg:04', '2023-05-07 16:48:43.000000', 101, 29, 30,
        '{
            "ATMOS": {
            "active": true,
            "memory_usage": "38.6M",
            "mac_declared": "te:st:gr:eg:04",
            "extras": {
                "operational_rate": 98.66666666666667
            }
            },
            "RTunnel": {
            "active": true,
            "memory_usage": "11.8M"
            }
        }'),
        ('te:st:gr:eg:05', '2023-05-17 17:48:43.000000', 101, 29, 30,
        '{
            "ATMOS": {
            "active": true,
            "memory_usage": "41.53",
            "mac_declared": "te:st:gr:eg:05",
            "extras": {
                "operational_rate": 98.66666666666667
            }
            },
            "RTunnel": {
            "active": true,
            "memory_usage": "21.8M"
            }
        }'),
        ('te:st:gr:eg:06', '2023-05-01 16:48:43.000000', 101, 29, 30,
        '{
            "ATMOS": {
            "active": false,
            "memory_usage": "47.5M",
            "mac_declared": "te:st:gr:eg:06",
            "extras": {
                "operational_rate": 98.66666666666667
            }
            },
            "RTunnel": {
            "active": true,
            "memory_usage": "11.8M"
            }
        }');
    ```
    - [x] Création de magasins dans la base locale pour arriver à 12
    ```
    INSERT INTO public.store (id, address, city, "postalCode", "isStoreOpen", "numberOfCheckouts", "gatewayMac", "openingHours", brand, "isStoreActive") VALUES ('081439dd-cfda-49e7-85d8-15c6c25c6123', '7 rue du frêne', 'Nordhouse', '67100', true, 2, 'te:st:gr:eg:01', '{"fri": ["08:30", "20:00"], "mon": ["08:30", "20:00"], "sat": ["08:30", "20:00"], "sun": ["10:00", "13:00"], "thu": ["08:30", "20:00"], "tue": ["08:30", "20:00"], "wed": ["08:30", "20:00"]}', 'Lidl',true);
    INSERT INTO public.store (id, address, city, "postalCode", "isStoreOpen", "numberOfCheckouts", "gatewayMac", "openingHours", brand, "isStoreActive") VALUES ('9de16383-c4d8-4616-bc4c-a26795f28456', '8 rue du bouleau', 'Nordhouse', '67100', true, 2, 'te:st:gr:eg:02', '{"fri": ["08:30", "20:00"], "mon": ["08:30", "20:00"], "sat": ["08:30", "20:00"], "sun": ["10:00", "13:00"], "thu": ["08:30", "20:00"], "tue": ["08:30", "20:00"], "wed": ["08:30", "20:00"]}', 'Norma',true);
    INSERT INTO public.store (id, address, city, "postalCode", "isStoreOpen", "numberOfCheckouts", "gatewayMac", "openingHours", brand, "isStoreActive") VALUES ('7d1a9808-ccf1-4136-8c8b-b80020ac0789', '9 rue du marronnier', 'Nordhouse', '67100', true, 3, 'te:st:gr:eg:03', '{"fri": ["08:30", "20:00"], "mon": ["08:30", "20:00"], "sat": ["08:30", "20:00"], "sun": ["10:00", "13:00"], "thu": ["08:30", "20:00"], "tue": ["08:30", "20:00"], "wed": ["08:30", "20:00"]}', 'Lidl',true);
    INSERT INTO public.store (id, address, city, "postalCode", "isStoreOpen", "numberOfCheckouts", "gatewayMac", "openingHours", brand, "isStoreActive") VALUES ('081439dd-cfda-49e7-85d8-15c6c25c6000', '7 rue du bambou', 'Nordhouse', '67100', true, 2, 'te:st:gr:eg:04', '{"fri": ["08:30", "20:00"], "mon": ["08:30", "20:00"], "sat": ["08:30", "20:00"], "sun": ["10:00", "13:00"], "thu": ["08:30", "20:00"], "tue": ["08:30", "20:00"], "wed": ["08:30", "20:00"]}', 'Lidl',true);
    INSERT INTO public.store (id, address, city, "postalCode", "isStoreOpen", "numberOfCheckouts", "gatewayMac", "openingHours", brand, "isStoreActive") VALUES ('9de16383-c4d8-4616-bc4c-a26795f28111', '8 rue du pêcher', 'Nordhouse', '67100', true, 2, 'te:st:gr:eg:05', '{"fri": ["08:30", "20:00"], "mon": ["08:30", "20:00"], "sat": ["08:30", "20:00"], "sun": ["10:00", "13:00"], "thu": ["08:30", "20:00"], "tue": ["08:30", "20:00"], "wed": ["08:30", "20:00"]}', 'Norma',true);
    ```
    - [x] Update du sql discord
    - [ ] Résolution de l'affichage 10+ stores
    - [x] Faire passer les tickets jira à faire dans le prochain sprint depuis le backlog

**26 Septembre**
- [ ] Mise à jour Kélio
- [x] KP-490 : Fix l'affichage des magasins sur /cop pour pouvoir afficher les magasins au-delà de 10
    - [x] Résolution de l'affichage 10+ stores
        - [ ] Trifouillage du mat-paginator
        - [x] ```git reset --hard```
        - [x] ```git pull origin develop```
        - [x] ```docker compose --env-file .env.dev -f docker-compose.dev.yml up --renew-anon-volumes --always-recreate-deps --build```
        - [x] Reprise sur des bases saines
        Ne marchait pas UNIQUEMENT à cause de :
        ```
        @ViewChild(MatSort) sort!: MatSort;
        @ViewChild(MatPaginator) paginator!: MatPaginator;
        ```
        à remplacer par : 
        ```
        private paginator!: MatPaginator;
        private sort!: MatSort;
        ```
        pour rendre fonctionnel.
        PAGINATOR DE SES MOOOOOOOORTS ! 
        A changer aussi : 
        ```
          getStores() {
            this.storeService.getStores(this.page, this.pageSize,this.query).subscribe({
            next: (data) => {
                this.dataSource.data = data.items;
                this.pageSize = data.meta.itemsPerPage;
                this.length = data.items.length;
            },
            error: (err) => err,
            });
        }
        ```
        en : 
        ```
            getStores() {
            this.storeService.getStores(this.page, this.pageSize,this.query).subscribe({
            next: (data) => {
                this.dataSource.data = data.items;
                this.pageSize = data.meta.itemsPerPage;
                this.length = data.meta.totalItems;
            },
            error: (err) => err,
            });
        }
        ```
- [ ] kp-488 Rediriger l'user sur la vue du magasin au clic dans les listes de magasins de cards sur la vue /cop
    - [ ] Création de branche & switch

**27 Septembre** 
- [x] kp-488 Rediriger l'user sur la vue du magasin au clic dans les listes de magasins de cards sur la vue /cop
    - [x] ajout du html : 
    ```
    <mat-card 
      (click)="openDialog(box)"
      [style.background-color]="box.maintenanceLevel === 'Preventive' ? 'rgb(253 230 138)' : (box.maintenanceLevel === 'Curative' ? 'rgb(220 38 38)' : 'inherit')"
      class="hover:opacity-75 text-center w-42 h-32"
    >
    ```
    - [x] Changement de tous les 'Predictive' du projet en 'Preventive' pour harmonisation & fonctionnement global
    - [x] 
    ```
    git add .  
    git commit -m "feat(front): Added dynamic color change on kmo status change for cards in store view"
    git push --set-upstream origin feat/KP-488_display_checkoutCards_coloured_by_status  
    ```
    - [x] PR github
- [ ] Mise à jour Jira
- [ ] Mise à jour document excel tests


**29 Septembre**
- [ ] Réunion SAV Pascal/Adrien/Béatrice
    - [ ] Récupération éventuelle des SIM dans les magasins NORMA, à réinjecter dans 
    - [ ] Récupérer des tablettes pour redispatcher aux chefs d'équipe
    - [ ] Envoyer duplicata à l'adresse mail du magasin, comment obtenir l'adresse mail ?
    - [ ] Offrir la possiblité au monteur dans le rapport (serrure / sangle ...) de préciser, gauche/droite, avant/arrière ...