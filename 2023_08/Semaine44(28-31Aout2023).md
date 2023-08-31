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
- [ ] KP-429, Ajouter un retour message au changement de gateway
    - [x] Retour sur la branche locale
    - [ ] Ajouter 5 gateways à la base locale pour tests
    - [ ] Voir si la réassignation de gateway se fait bien
    - [ ] Ajouter une snackbar positive si la reass se fait bien
    - [ ] Ajouter une snackbar négative si la reass rencontre une erreur