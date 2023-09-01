**28 Aout**
- [ ] Reprise du tableau des tests fonctionnels 
- [ ] KP-424, Réparer le triage des magasins sur /administration, le fix ne fonctionne pas en prod
    - [x] Retour en local
    - [x] Switch sur la branche
    - [x] Pull origin pour être à jour 
    - [ ] Tests du code
    - [ ] Rajouter la searchbar
        - [x] Ajout dans le fichier administration.component.html de  :
        ```
        <div class="my-6">
            <form  class="" #searchFrom="ngForm">
            <input matInput type="text" name="query" [(ngModel)]="query" class="absolute p-3 w-52 h-9 right-10 border-gray-400 border-2 shadow  hover:bg-gray-100" placeholder="Rechercher un magasin">
            <button (keydown)="searchEventListener($event)" class="invisible" ></button>
            </form>
        </div>
        ```
        - [x] Ajout dans le fichier administration.component.ts de : 
        ```
        // eslint-disable-next-line @typescript-eslint/no-unused-vars
        searchEventListener(event : KeyboardEvent){
            this.getStores();
        }
        ```
        - [ ] Connexion à pgadmin avec les ids
        - predict-pg
        - predict16022023
    - [ ] Réparer le code de la table

    **29 Aout**
- [ ] Reprise du tableau des tests fonctionnels 
- [x] KP-424, Réparer le triage des magasins sur /administration, le fix ne fonctionne pas en prod
    - [x] Connexion à pgadmin avec les ids (avec l'app locale, pas le browser)
        - predict-pg/localhost
        - predict16022023
    - [x] Réparer le code de la table
        - [x] Résolution de l'erreur 400 sur /admin administration.component.ts:53     GET http://localhost:3001/stores/paginate?page=undefined&limit=undefined&searchTerm=undefined 400 (Bad Request) : 
        changement dans administration.component.ts de :
        ```
        sort: MatSort | null = new MatSort;
        page!: number;
        pageSize!: number;
        query!: string;
        length!: number;
        pageIndex!: number;
        ```
        à 
        ```
        length!: number;
        pageIndex!: number;
        public query= '';
        page = 1;
        pageSize = 10;
        ```
        - [x] Faire fonctionner la table comme attendu (n'affiche pas les stores atm)
        Changement de :
        ```
        @ViewChild(MatPaginator) paginator!: MatPaginator;

        sort: MatSort | null = new MatSort;
        ```
        à
        ```
        @ViewChild(MatPaginator) paginator!: MatPaginator;
        @ViewChild(MatSort) sort!: MatSort;
        ```
        - [x] Remettre dans /admin les actions de caisse
        - [x] Déplacer le code dans app-tab-store
        - [x] Ajouter la searchbar
        
**31 Aout**
- [ ] Reprise du tableau des tests fonctionnels 
- [x] KP-429, Ajouter un retour message au changement de gateway
    - [x] Retour sur la branche locale
    - [x] Ajouter 5 gateways à la base locale pour tests
        - [x] Reprise du SQL et modifications : 
        ```
        INSERT INTO public.gateway (mac, datetime, "diskUsage", "temperatureCpu", "freeRam", services)
        VALUES ('23:a9:dz:ff:d2:cd', '2023-05-07 16:48:43.000000', 101, 29, 30,
        '{
          "ATMOS": {
            "active": true,
            "memory_usage": "38.6M",
            "mac_declared": "23:a9:dz:ff:d2:cd",
            "extras": {
              "operational_rate": 98.66666666666667
            }
          },
          "RTunnel": {
            "active": true,
            "memory_usage": "11.8M"
          }
        }'),
        ('25:a9:fb:ff:d2:cd', '2023-05-17 17:48:43.000000', 101, 29, 30,
            '{
            "ATMOS": {
                "active": true,
                "memory_usage": "41.53",
                "mac_declared": "25:a9:fb:ff:d2:cd",
                "extras": {
                "operational_rate": 98.66666666666667
                }
            },
            "RTunnel": {
                "active": true,
                "memory_usage": "21.8M"
            }
            }'),
            ('24:a9:fb:ff:d2:cd', '2023-05-01 16:48:43.000000', 101, 29, 30,
            '{
            "ATMOS": {
                "active": false,
                "memory_usage": "47.5M",
                "mac_declared": "24:a9:fb:ff:d2:cd",
                "extras": {
                "operational_rate": 98.66666666666667
                }
            },
            "RTunnel": {
                "active": true,
                "memory_usage": "11.8M"
            }
            }'),
            ('24:a9:fb:ff:d2:bc', '2024-04-01 16:48:43.000000', 2323, 29, 30, '{
                "ATMOS": {
                "active": true,
                "memory_usage": "34.6M",
                "mac_declared": "24:a9:fb:ff:d2:bc",
                "extras": {
                    "operational_rate": 98.66666666666667
                }
                },
                "RTunnel": {
                "active": true,
                "memory_usage": "11.8M"
                }
            }'),
            ('24:a9:fb:ff:d2:ef', '2023-02-24 09:45:48.347000', 13, 39.444, 81.52787453215763, '{
                "ATMOS": {
                "active": true,
                "memory_usage": "26.6M",
                "mac_declared": "24:a9:fb:ff:d2:ef",
                "extras": {
                    "operational_rate": 98.66666666666667
                }
                }
            }');

        INSERT INTO public.kmo_box (mac, datetime, uptime, "firmwareVersion","checkoutNumber", "gatewayMac")
        VALUES ('01:00:5E:xx:xx:xx', '2023-03-01 16:48:43.000000', 10, 1.4,1,
                '22:a9:fb:ff:d2:bc'),
            ('01:00:5E:xx:xx:33', '2023-03-02 13:44:01.000000', 3, 3,2, '22:a9:fb:ff:d2:bc'),
            ('01:00:5E:xx:xx:34', '2023-08-02 14:44:01.000000', 6, 2.3,2, '25:a9:fb:ff:d2:cd'),
            ('01:00:5E:xx:xx:35', '2023-07-02 15:44:01.000000', 5, 3.5,2, '24:a9:fb:ff:d2:bc'),
            ('01:00:5E:xx:xx:36', '2023-06-02 16:44:01.000000', 4, 2,1, '24:a9:fb:ff:d2:ef');

        INSERT INTO public.store (id, address, city, "postalCode", "isStoreOpen", "numberOfCheckouts", "gatewayMac", "openingHours", brand, "isStoreActive") VALUES ('081439dd-cfda-49e7-85d8-15c6c25c65ca', '7 rue du chêne', 'Nordhouse', '67100', true, 3, '22:a9:fb:ff:d2:cd', '{"fri": ["08:30", "20:00"], "mon": ["08:30", "20:00"], "sat": ["08:30", "20:00"], "sun": ["10:00", "13:00"], "thu": ["08:30", "20:00"], "tue": ["08:30", "20:00"], "wed": ["08:30", "20:00"]}', 'Lidl',true);
        INSERT INTO public.store (id, address, city, "postalCode", "isStoreOpen", "numberOfCheckouts", "gatewayMac", "openingHours", brand, "isStoreActive") VALUES ('9de16383-c4d8-4616-bc4c-a26795f286ae', '8 rue du chêne', 'Nordhouse', '67100', true, 3, '22:a9:fb:ff:d2:bc', '{"fri": ["08:30", "20:00"], "mon": ["08:30", "20:00"], "sat": ["08:30", "20:00"], "sun": ["10:00", "13:00"], "thu": ["08:30", "20:00"], "tue": ["08:30", "20:00"], "wed": ["08:30", "20:00"]}', 'Norma',true);
        INSERT INTO public.store (id, address, city, "postalCode", "isStoreOpen", "numberOfCheckouts", "gatewayMac", "openingHours", brand, "isStoreActive") VALUES ('7d1a9808-ccf1-4136-8c8b-b80020ac0ec4', '9 rue du chêne', 'Nordhouse', '67100', true, 3, '22:a9:fb:ff:d2:ef', '{"fri": ["08:30", "20:00"], "mon": ["08:30", "20:00"], "sat": ["08:30", "20:00"], "sun": ["10:00", "13:00"], "thu": ["08:30", "20:00"], "tue": ["08:30", "20:00"], "wed": ["08:30", "20:00"]}', 'Lidl',true);

        INSERT INTO public.event (id, type, duration, datetime, state, "stateValue", motor, "kmoBoxMac")
        VALUES ('993051ef-1a3e-4781-b60f-1ea7faf7da89', 'pedal', 22.6, '2023-02-22 09:45:48.347000', 'up', null, null,
                '01:00:5E:xx:xx:xx'),
            ('75cb0af5-849d-4ce6-b794-a0585136bd8c', 'motor_flag', null, '2023-02-22 09:45:48.347000', 'value', 0.225, '2',
                '01:00:5E:xx:xx:xx'),
            ('6ee4988c-8e7b-4c10-820c-07b48fd8c574', 'cpu_temperature_changed', null, '2023-02-22 09:45:48.347000',
                'thermal_switch', null, '2', '01:00:5E:xx:xx:xx'),
            ('cbee4323-3f71-4fda-ba96-cc9c122aa5a2', 'power_intensity_changed', null, '2023-02-22 09:45:48.347000',
                'thermal_switch', null, '2', '01:00:5E:xx:xx:xx'),
            ('993051ef-1a3e-4781-b60f-1ea7faf7da83', 'ir', 22.6, '2023-03-02 13:43:30.000000', 'down', null, '1',
                '01:00:5E:xx:xx:xx')
       ```
       - [x] Query successful
    - [x] Voir si la réassignation de gateway se fait bien
        - [x] Retour local, erreurs : 
            - Page not found
            - No access to CORS
            - Failed to load ressource: net
        - [x] Résolution
            - [x] Export du realm
            - [x] Rebuild des containers
            - [x] Modification du realm → saas
            - [x] Réimportation du realm
    - [x] Ajouter une snackbar positive si la reass se fait bien
    - [x] Ajouter une snackbar négative si la reass rencontre une erreur -->> bah en fait anthony l'avait déjà fait
- [x] Màj Prod pour avancer dans les tests fonctionnels
    - [x] Connexion à la prod 
    - [x] Dans le dossier du projet, ```git pull origin develop --no-rebase```
    - [x] Arrêt & suppression des containers
    - [x] Rebuild des dockers ```sudo  docker compose --env-file .env.dev -f docker-compose-prod.yml up --always-recreate-deps --build```
    - [x] Ajout des gateways & kmo boxes dans la db de prod  

            
**1er Septembre**
- [x] Check des mails
- [ ] Reprise du tableau des tests fonctionnels 
    - [ ] Reprise des tests de l'application
    - [ ] Ajout de ticket Jira pour chaque issue