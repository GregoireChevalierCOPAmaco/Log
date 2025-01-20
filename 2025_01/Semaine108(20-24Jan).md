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