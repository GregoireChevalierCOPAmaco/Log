**20 Janvier**
- [ ] ESL
    - [ ] ESL-230 Créer un menu de changement des infos d'une ESL dans l'application web   
        - [ ] Correction des problèmes de [(ngModel)]
- [ ] Remettre develop sur la prod
- [x] Entretien candidat Artem Tatkov


appétance front/back ?
portfolio : perso ou hébergeur ? expérience bout en bout ? conception-déploiment
place dans une équipe ?

Artem Tatkov
2e année UHA 4.0 
pas motorisé mais possibilitré de se faire emmener ponctuellement, sinon train.
accent léger mais élocution excellente
percute vite
exp chez perflinker : intégration, templating & code en PHP
fil rouge, création d'une API. POO Utilisée avec Java sous framework
projet badgeuse, docker, angular
Projet le plus récent NestJs + prisma
Pas cantonné à une techno, semble bien s'adapter à la demande.
Aucun mal à développer un propos. 
Bien structuré dans sa manière de parler. Très enthousiaste, on sent la passion. Peronnalité plutôt forte, préfère lead à priori. 
Manque peut-être un peu d'écoute → a très envie de s'exprimer, de proposer (& stress lié à l'entretien). A canaliser dans la communication ? N'hésite pas à poser des questions s'il en a


master en vue, alternance prévue.
question code quality :
Conventions de nommage, linting, tests unitaires, pas de code redondant, bonnes idées sur les pratiques à mettre en place. 
question kmo Video: demande le cahier des charges, dialogue avec le client, définit le MVP, organise les tâches en fonction de la priorité & backlog
appétance back mais pas de problème avec le front

Bilan très positif pour ma part, de loin le meilleur profil selon moi


- [ ] Entretien candidat Lilyan Muller

voiture ? mobilité / déménagement
appétance front/back ?
expérience bout en bout ? conception-déploiment
place dans une équipe ?
explique les "sites dynamiques" de ton cv
mise en ligne? du projet thisIsVal

premier ressenti un peu mou. N'a pas regardé où était l'entreprise. 
Se décrit comme ayant eu l'occasion de "maîtriser" git & docker ... maitrise ?
présentation perso très succincte ; pas mal de "euh". Mobilité : a permis & voiture & envisage de déménager. Veut être embauché juste après le stage. 4 de ses 6 mois de stage seront conventionnés, pas les 2 restants.

question code quality :
répond à côté avec MVC,
Documentation & commentaire. Stresse sur la question, l'a avoué. Pas de connaissances de tests unitaires, nommage & linter mais a déja utilisé une CI
KMO video :
- recherche d'infos sur le hardware & comment communiquer avec
- rédaction du cahier des charges.
pas trop mal

Utilise Ts, pas de soucis pour la stack
Appétance front & UX/UI mais pas de soucis avec le back
Plutôt suiveur mais sait s'adapter et être proactif

expérience mise en ligne "ThisIsVal" stressante selon lui mais a su faire face.

Peu loquace, mais s'exprime correctement. A déja travaillé en équipe.

potentiellement prometteur mais manque de maturité. Pourrait être exploitable si on investit assez de temps. A les bases mais peu de pratique. Interrogation sur son autonomie sur le long terme
Retour Anthony très différent : plutot convaincu par sa volonté de s'autoformer (?) et sa curiosité vis a vis des technos et tendances. Anthony d'accord sur le fait qu'il manque un peu de maturité mais trouve sa candidature pertinente.
Terrain d'entente : il a la stack et saurait bosser si encadré.


- [ ] ESL
- [ ] Remettre develop sur la prod
    - [x] Connexion à ```ssh esl@192.168.1.252``` avec pw comme d'hab
    - [x] Purge des dockers containers & volume : 
    ```
    sudo docker volume prune --filter all=1
    sudo docker system prune -a
    ```
    - [x] Arrêt des containers concernés :
    ```
    sudo docker stop esl_front esl_api
    sudo docker remove esl_front esl_api
    ```
    - [x] Checkout sur develop, pull et rebuild :
    ```
    git checkout develop
    nano environments.ts
    sudo docker compose  --env-file .env.prod -f docker-compose.prod.yml up --build
    ```
    - [ ] ESL-230 Créer un menu de changement des infos d'une ESL dans l'application web   
        - [ ] Correction des problèmes de [(ngModel)]



**22 Janvier**
- [ ] ESL
    - [x] Fix dashboard error : 
    ```
    Error: Canvas is already in use. Chart with ID '1' must be destroyed before the canvas with ID 'eslHourlyActivityChart' can be reused.
    ``` 
    - [ ] Modification dashboard component, ajout de  : 
    ```
    OnInit()
    
    private eslActivityChart: Chart | null = null;
    private eslHourlyActivityChart: Chart | null = null;
    private eslDailyActivityChart: Chart | null = null;
    private eslAssociationChart: Chart | null = null;
     ...

    initializeHourlyActivityChart() {
    if (this.eslHourlyActivityChart) {
      this.eslHourlyActivityChart.destroy();
    }
    ```


**23 Janvier**
- [ ] ESL
    - [ ] ESL-248_refreshESL_onButtonClick
        - [ ] Refonte du store-article service backend pour inclure un force refresh
            - [x] Front end, ajout du bouton hmtl, reprise du component edit store article & ajout d'une méthode dans le service  :
            ```
            <div>
            <button (click)="forceRefresh()" class="w-full inline-flex justify-center rounded-md border border-transparent shadow-sm px-4 py-2 bg-orange-600 text-base font-medium text-white hover:bg-orange-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 sm:text-sm">
                Force Refresh
            </button>
            </div>
            ```
            ```
            export class EditStoreArticlesComponent implements OnInit {
            article: any;
            editForm: FormGroup;
            editedArticle: any = {};
            eslId: string = '';

            constructor(
            private fb: FormBuilder,
            private storeArticleService: StoreArticleService,

            forceRefresh(): void {
                this.storeArticleService.forceRefresh(this.editedArticle).subscribe(
                response => {
                    console.log('Force refresh successful', response);
                },
                error => {
                    console.error('Error forcing refresh', error);
                }
                );
            }
            ```
            ```
            forceRefresh(article: any): Observable<any> {
                console.log(article);
                
                return this.httpClient.post(`${this.apiUrl}/force-refresh`, article);
            }
            ```
        - [ ] Backend
            - [ ] Récupérer via endpoint le storeArticle
            - [ ] Get son ESL associée s'il en a une
            - [ ] résolution de l'erreur : 


**24 Janvier**
- [ ] ESL
    - [x] Reprise et recréation de templates
- [ ] Entretiens en physique
    - [ ] Artem - 14h
```
Arrive en avance de 35 minutes dû aux transports en commun
Parle très vite, a tendance à mâcher un peu ses mots?
Pas de mobile atm, comment réagirait si on le mettait sur un projet mobile : connait flutter, saurait globalement par où commencer, mais verra ça en 3e année
question qu'est-ce qu'il a compris du digital : qu'on travaille sur atmos ? pas clair, n'a pas su trop en parler ou dire qu'il n'avait pas compris/ pas su s'exprimer
a du répondant ; on est là pour apprendre, cherche à comprendre comment ça marcherait de bosser sur mobile
est conscient du côté montée en compétence nécessaire, se sent prêt à relever le challenge si on lui laisse le temps.
projets personnels : portfolio en vueJs, a dans l'idée de dev une app de case-opening
quelle pluvalue pense-t-il apporter à l'équipe : 
travail d'équipe, autonomie, force de proposition. Pas spécialement de préférence entre travail équipe/seul par manque d'exp dans le sujet ?
Plus tard c'est quoi l'objectif ? : freelance ou création de boîte ou aller chez un gros de la tech. Se voit toujours dans le dev, et le fullstack plus précisément.
Aspect web, les frameworks sont connus. A fait beaucoup de PHP aussi, notamment avec Laravel, chez Perflinker (marketing digital)
a pas l'air d'aimer se répéter, une phrase qui revenait souvent : "Comme je l'ai déjà dit à ..."
Attentes pour le stage : accumuler l'expérience dans le domaine, petit intérêt dans le retail car se demande ce qu'il y a derrière, cherche en général à savoir comment sont faites les choses.
Personnalité facile à communiquer avec. Semble gérer le stress
date début de stage : 17 février +- 2 semaines
a conscience qu'on va pas lui trouver un logement, mais est prêt à chercher ; a les pieds sur terre
```
    - [ ] Adam  - 16h