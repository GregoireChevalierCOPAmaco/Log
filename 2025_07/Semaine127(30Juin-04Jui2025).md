**30 Juin**
- [ ] ESL
    - [x] checkout, pull develop & ```git checkout -b fix/ESL-377+_various_frontend_fixes```
    - [ ] ESL-377 Appliquer les modifications mineures relatives à l'interface générale
    - Sidebar :  harmoniser Esl en ESLs
    - Recentrer les alertes au milieu de l'écran
    - Afficher les erreurs utilisateur sur chaque page
    - Mettre les textes de snackbar de clignotement en français
    - Surligner la page active dans la sidebar
    - Renommer “Liste des templates” en “Styles & templates”
aéxb    ```
    <a link class="flex" mat-list-item [routerLink]="['/dashboard']" routerLinkActive="active-link" [routerLinkActiveOptions]="{ exact: true }"
        <div class="flex hover:bg-gray-700">
            <svg xmlns="http://www.w3.org/2000/svg" height="40px" viewBox="0 -960 960 960" width="40px" class="size-6" fill="#FFFFFF"><path d="M513.33-580v-260H840v260H513.33ZM120-446.67V-840h326.67v393.33H120ZM513.33-120v-393.33H840V-120H513.33ZM120-120v-260h326.67v260H120Zm66.67-393.33H380v-260H186.67v260ZM580-186.67h193.33v-260H580v260Zm0-460h193.33v-126.66H580v126.66Zm-393.33 460H380v-126.66H186.67v126.66ZM380-513.33Zm200-133.34Zm0 200ZM380-313.33Z"/></svg>
            <!-- <span class="link-label text-white">Dashboard</span> -->
            <span class="link-label text-white">Tableau de bord</span>
        </div>
    </a>
    ```
    scss
    ```
    .active-link {
        background-color: $cop-blue-grey-hover;
        font-weight: bold;
        border-left: 4px solid $cop-blue;
        .link-label {
            color: $cop-blue;
        }
        svg {
            fill: $cop-blue;
        }
    }
    ```
    - [ ] ESL-378 Fix le 1/0 de la pagination de la table ESLs quand la table est vide
    - [ ] ESL-427 Fix les typos du management
    - [ ] ESL-383 Doubler la pagination en haut et en bas sur les pages storeArticleTable, ESLTable
    - [ ] ESL-389 Faire en sorte d'afficher le message d'erreur à chaque fois qu'on tente d'uploader une image aux mauvaises dimensions , et pas juste la première fois
    - [ ] ESL-395 Rediriger vers la page StoreArticle et refresh au submit du csv
    - [ ] ESL-384 Renommer le bouton ForceRefresh de la storeArticleTable pour mieux exprimer le fait qu'on va rafraichir les ESLs et pas les articles de la table
    - [ ] ESL-398 Création de template : l'absence du champ $name$ doit lever une erreur utilisateur
    - [ ] ESL-423 Création de template : créer un système de copier-coller pour les objets du canvas
    - [ ] ESL-422 Création de template : créer des raccourcis claviers pour la suppression d'objets du canvas