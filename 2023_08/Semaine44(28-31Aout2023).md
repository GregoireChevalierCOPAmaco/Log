**25 Aout**
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