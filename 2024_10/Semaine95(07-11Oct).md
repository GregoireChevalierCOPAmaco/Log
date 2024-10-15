**07 Octobre**
- [x] Check des mails
- [ ] ESL
    - [ ] ESL-144 Résoudre le bug d'affichage de la liste des ESLs : ERR_BLOCKED_BY_CLIENT
    - [ ] ESL-140 Brancher les barres de recherches au back (voir code predict)
        - [x] Check du code de predict
        - [x] Développement de la searchbar
            - [x] fichier html : 
            ```
            ```


**08Octobre**
- [x] Réunion en physique avec Erwann
Erwann Brion 19 ans 20soon bachelor CDA à la CCI jusqu'en juin, mais possibilité d'aller jusqu'au 30 septembre → à voir
dispo de suite
Bac pro systemes réseau puis redirection dev 
petite tension durant l'entretien qu'il n'y avait pas en distantiel
outil de doc en ligne pour l'entreprise d'alternance, point global avec tous les futurs users de l'app, puis suivi régulièrement en équipes
tu as déployé ton portfolio toi même, est ce que tu touches un peu aux hébergeurs, docker 



- [x] Entretien Arthur Frisch
bac g 2022 math numerique sciences informatiques (python un peu de réseau, binaire)
fan de jv, quels jv. Important pour lui de mettre en pratique ce qui est dit à l'école 
(
    alt. chez comptalib équipe de 10 personnes sur l'ERP : . Tests unitaires & end to end
    front vueJs, nest pour le back, dev & maintenance
)
notions de déploiement ? pas chez comptalib, mais a manipulé la CI, les tests & le linting. Usage courant de git, participation aux reviews
déjà utilisé docker ? oui & docker compose.
aisance d'expression, pas de phrases préfaites ou apprises. Pas de mal à se faire comprendre. A une appétance pour faire les choses bien, niveau code, nommage, structure & organisation. on sent la passion dans son discours
pas d'appétance particulière front/back mais plus attiré par la logique métier 
Applique le principe de séparation, a connaissance de SOLID, KISS de nom mais c'est tout 
c'est quoi la place que tu prends d'instinct dans un groupe ? (une fois acclimaté) : aime pas forcément être au centre des débats. N'hésite pas à "contredire" et à donner son avis. Entre deux. Aime challenger sa vision des choses. Va pas hésiter à apporter un retour
Prend des notes, on sent l'intérêt. Excité par le côté iot, aime le concret d'avoir du physique qui évolue sous ses yeux. 
Se sent à la hauteur pour l'intégration niveau technique, mais prêt à se laisser guider, pas inquiet à ce sujet. Prêt à bosser en autonomie (l'a pas dit mais on le sent). Bonne mentalité face aux échecs
Petite exp sur DART avec Flutter. Pas d'exp sur le mobile en général
En formation pour l'équivalent bachelor bac + 3 avec vue sur le master
possible de commencer début novembre → fin septembre
a déja refusé une autre alternance (techno propriétaire apple filemaker + questions chelous comme : "jusqu'à combien d'heures par jour vous pouvez travailler ?")


**11 Octobre**
- [ ] ESL
    - [ ] ESL-142 Fix l'erreur AxiosError: Request failed with status code 500 et le crash du back au clic de la désactivation de l'ESL
        - [x] Suivi du processus d'apparition de l'erreur : 
            - ESL : C4D0284A 
                - blink OK
                - disable OK (fait apparaître le code barre et le message sur l'ESL)
                - scan OK, assignation de l'image OK
                - envoi de l'image à l'ESL : message de succès dans l'app mobile, mais pas de changement d'image & crash du back avec le message : 
                ```
                /app/node_modules/axios/lib/core/settle.js:19
                    reject(new AxiosError(
                        ^
                AxiosError: Request failed with status code 500
                    at settle (/app/node_modules/axios/lib/core/settle.js:19:12)
                    at IncomingMessage.handleStreamEnd (/app/node_modules/axios/lib/adapters/http.js:599:11)
                    at IncomingMessage.emit (node:events:531:35)
                    at endReadableNT (node:internal/streams/readable:1696:12)
                    at processTicksAndRejections (node:internal/process/task_queues:82:21)
                    at Axios.request (/app/node_modules/axios/lib/core/Axios.js:45:41)
                    at processTicksAndRejections (node:internal/process/task_queues:95:5)
                ```
            - [x] Même processus en mettant l'idesl en minuscule & majuscule
            - [ ] Recherche sur l'erreur AxiosError: Request failed with status code 500
            - [ ] ESL-146 Empêcher le crash du back lors de la levée de l'erreur Axios 500