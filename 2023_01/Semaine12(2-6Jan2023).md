**2 Janvier**
- [x] Réinstallation du poste de travail
- [x] MàJ du système windows
- [ ] Survol des tâches en cours 
- [x] Remise à jour sur les mails
    - [x] Récupération des IMEI envoyés
    - [x] Prise de connaissance des mails pascal
    Réunion Kevin design ecran tft (UI/UX)
- [ ] Récupérer les IMEI des 3 tablettes monteurs en utilisation
    - [ ] Alexandre WANDRE
    - [x] Frederic LIGER 
    - [x] Nicolas RUXER
    - [x] Update de l'excel en conséquence
- [x] Purge des dockers
- [x] Rebuild des dockers
- [x] Poursuite de l'inventaire des tablettes
    - [x] Nouvel arrivage de 3 tablettes
        - [x] Déterminer la provenance (utilisateur) de chaque tablette
        - [x] Identifier l'IMEI
        - [x] Identifier le n° SIM
        - [x] Faire l'état des lieux
- [x] Faire un mail à Mevin Govinda pour devis 8 tablettes
- [x] Rangement du bureau en prévision de l'audit décathlon, première passe
- [ ] Kmo tentatives de connexion au localhost 4200
    - [x] Connexion avec l'identifiant admin
    - [ ] Connexion avec un user créé en base
        - [ ] Renvoie Erreur du serveur. La console des outils de developpement browser affiche :
        ```
        Failed to load resource: the server responded with a status of 400 (Bad Request)
        ```
- [ ] Kmo amazon SES service
    - [ ] Méthode pret à facturer 
    - [ ] Faire le template avec lien
    - [ ] Trouver où appeler les méthodes
- [ ] Pesner à retester la tablette de Baugue


**3 Janvier**
- [ ] Kmo tentatives de connexion au localhost 4200 
    - [ ] Voir si les changements apportés aux routes n'interfèrent pas avec la connexion. 
        - Plongée dans le code
    - [x] Check de (https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/400#:~:text=The%20HyperText%20Transfer%20Protocol%20(HTTP,%2C%20or%20deceptive%20request%20routing).) & (https://stackoverflow.com/questions/56435467/how-to-fix-failed-to-load-resource-the-server-responded-with-a-status-of-400)
    - [x] Application du correctif adapté
    - [x] Echec, poursuite des recherches
        - [ ]
- [x] Gros rangement accueil & stockage bureau pour l'audit décathlon, de mercredi
- [x] Repasse avec Kevin sur les textes en anglais du site
- [x] Appel avec Mevin pour précisions devis & droits
- [x] Voir avec François Anthony pour activation du lien pour les drois d'accès au compte client orange
- [ ] Voir la démarche à suivre dans l'échange de mail
- [ ] Kmo amazon SES service
    - [ ] Méthode pret à facturer 
    - [ ] Faire le template avec lien
        - [ ] Refactoriser le code pour minimiser les lignes
    - [ ] Trouver où appeler les méthodes
- [ ] Pesner à retester la tablette de Baugue


**4 Janvier**
- [x] Penser à retester la tablette de Baugue
    - [x] La tablette ne s'allume plus. (batterie HS ?)
- [x] Voir la démarche à suivre dans l'échange de mail
    - [x] François a suivi la procédure. Affaire à suivre
    - [ ] Trouver pourquoi c'est François ANTHONY à qui le mail de confirmation a été envoyé
- [ ] Reprise du passage sur l'observation d'atmos
    - [x] Passage sur keycloak
        - [x] Reprise du code
            - [x] Essais en environnement test
            - [ ] Essais en environnement dev
        - [x] Reprise de doc
- [x] Prise de connaissance du document pour la projection à BPI
    - [ ] Parcourir le fichier pptx de présentation
- [ ] Kmo amazon SES service
    - [ ] Méthode pret à facturer
    - [ ] Faire un service de templates à appeler pour alléger le fichier
        - [ ] Faire le template de la fonction createSendPasswordEmailCommand
        - [ ] Faire le template de la fonction createSendReportEmailCommand
        - [ ] Faire le template de la fonction createSendTicketEmailCommand
        - [ ] Faire le template de la fonction createSendModifiedTicketEmailCommand
        - [ ] Faire le template de la fonction createSendDeletedTicketEmailCommand
        - [ ] Faire le template de la fonction createSendUnassignedTicketEmailCommand
        - [ ] Faire le template de la fonction méthode prêt à facturer
            - [ ] Trouver le lien à renseigner pour rediriger vers ...
            - [ ] Intégrer le lien
- Audit Décathlon & alarme incendie
    - [ ] Faire le template avec lien
        - [ ] Refactoriser le code pour minimiser les lignes
    - [ ] Trouver où appeler les méthodes
        - [x] Appel de la fonction createSendPasswordEmailCommand
        - [x] Appel de la fonction createSendReportEmailCommand
        - [ ] Appel de la fonction createSendTicketEmailCommand
        - [ ] Appel de la fonction createSendModifiedTicketEmailCommand
        - [ ] Appel de la fonction createSendDeletedTicketEmailCommand
        - [ ] Appel de la fonction createSendUnassignedTicketEmailCommand
        - [ ] Appel de la fonction méthode prêt à facturer

**5 Janvier**
- [ ] Trouver pourquoi c'est François ANTHONY à qui le mail de confirmation a été envoyé
- [ ] Voir avec Pascal pour l'activation de la sim "clé 4G"
- [ ] Reprise du passage sur l'observation d'atmos
    - [x] Passage sur keycloak
        - [x] Reprise du code
            - [x] Essais en environnement test
            - [ ] Essais en environnement dev
        - [x] Reprise de doc
    - [ ] Parcourir le fichier pptx de présentation
- [ ] Kmo amazon SES service : 
    - [x] Méthode pret à facturer
        - [x] Ajout des paramètres 
        ```
        fromAddress,
        technicianAddress,
        afterSalesAddress,
        redirectionLink,
        ticket,
        bill
        ```
    - [x] Faire un service de templates à appeler pour alléger le fichier
        - [x] Test d'import de fichier html, retour d'erreur :
        ```
        Cannot find module './test.hmtl' or its corresponding type declarations
        ```
        - [x] Suivi de doc (https://docs.nestjs.com/techniques/mvc#template-rendering)
        - [x] Installation handlebars
            - [X] Dans kmo-back, :
            ```
            npm install --save hbs
            ```
            - [x] Conflit entre NestExpressApplication (prôné pour hbs) et notre application INestApplication
        - [x] Import du template de test
            - [x] Abandon du templating
    - [x] Mise en pause de la création de la méthode createReadyToBillEmailCommand() pour cause de : le prêt à facturer n a pas été codé
    - [x] Fichier ticket-service.ts
        - [x] Import du service amazon ses et déclaration : 
        ```
		private readonly amazonSesService: AmazonSesService
        ```
        - [x] Suppression des appels aux méthodes { MailerService } from '@nestjs-modules/mailer';
        ligne 266 : 
        ```
        this.mailerService
        .sendMail({
            to: user.email,
            from: 'a.wilhelm@cop-amaco.com',
            subject: 'Nouveau Ticket',
            html:
                '<p>Un nouveaux ticket vient de vous être assigné </strong></p>' +
                '<p><strong>Pour le : </strong></p>' +
                ticket.dateOfIntervention +
                "<p><strong>A l'adresse :  </strong></p>" +
                ticket.store.city +
                ' ' +
                ticket.store.address,
        })
        ```
        && Suppression des déclarations et imports lignes 14 & 31
        - [x] Application des méthodes du service ses-mailing
    - [x] Fichier reports-service.ts
        - [x] Suppression des appels aux méthodes { MailerService } from '@nestjs-modules/mailer';
        Lignes 14, 26 & m0 lignes de méthode lignes 40-73
        - [x] Application des méthodes du service ses-mailing
        - [x] Suppression des déclarations & imports dans le fichier reports.module.ts 
        - [x] Suppression des MAILEROPTIONS, imports et paramètres dans le fichier reports.module.ts 
        - [x] Suppression des MAILEROPTIONS, imports et paramètres dans le fichier reports.controller.spec.ts 
    - [x] Fichier pieces.module.ts
        - [x] Suppression des déclarations & imports dans le fichier 
        - [x] Suppression des MAILEROPTIONS, imports et paramètres
    - [x] Fichier pieces.controller.spec.ts
        - [x] Suppression des déclarations & imports dans le fichier 
        - [x] Suppression des MAILEROPTIONS, imports et paramètres
    - [x] Fichier users.controller.spec.ts
        - [x] Suppression des appels aux méthodes { MailerService } from '@nestjs-modules/mailer';
    - [x] Cleaner les imports de MailerService
        - [x] auth.module.ts
            - [x] Suppression des MailerService, MAILEROPTIONS, imports et paramètres
        - [x] regional-management.controller.spec.ts
            - [x] Suppression des MailerService, MAILEROPTIONS, imports et paramètres
        - [x] statistics.service.spec.ts
            - [x] Suppression des MailerService, MAILEROPTIONS, imports et paramètres
            - [x] Suppression de import { getRepository } from 'typeorm';
        - [x] statistics.controller.spec.ts
            - [x] Suppression des MailerService, MAILEROPTIONS, imports et paramètres & provide usevalue jest.fn()
        - [x] stores.controller.spec.ts
            - [x] Suppression des MailerService, MAILEROPTIONS, imports et paramètres
        - [x] tickets.controller.spec.ts
            - [x] Suppression des MailerService, MAILEROPTIONS, imports et paramètres
        - [x] tickets.service.spec.ts
            - [x] Suppression des MailerService, MAILEROPTIONS, imports et paramètres
        - [x] users.module.ts
            - [x] Suppression des MailerService, MAILEROPTIONS, imports et paramètres
        - [x] users.service.spec.ts
            - [x] Suppression des MailerService, MAILEROPTIONS, imports et paramètres
        - [x] vehicles.controller.spec.ts
            - [x] Suppression des MailerService, MAILEROPTIONS, imports et paramètres
    - [ ] Lancement des tests back pour assurance que le tout fonctionne encore comme attendu
        - [x] Résolution de l'erreur : 
        ```
        Nest can't resolve dependencies of the TicketsService (UserRepository, TicketRepository, ?). Please make sure that the argument AmazonSesService at index [2] is available in the TicketsModule context.
        kmo-api        |
        kmo-api        | Potential solutions:
        kmo-api        | - If AmazonSesService is a provider, is it part of the current TicketsModule?
        kmo-api        | - If AmazonSesService is exported from a separate @Module, is that module imported within TicketsModule? 
        ```
        Ajout de l'import et du provider d'AmazonSESService dans le fichier tickets.modules.ts
        - [x] Résolution de l'erreur : 
        ```
        ERROR [TypeOrmModule] Unable to connect to the database. Retrying (4)...
        Error: ER_ACCESS_DENIED_ERROR: Access denied for user ''@'172.19.0.1' (using password: NO)
        ```
    - [x] Docker prune -a -f
    - [ ] Rebuild docker & relance des tests back pour assurance que le tout fonctionne encore comme attendu
    - [ ] reports.service.spec.ts
        - [ ] Refaire les tests avec les appels à amazonSES
    - [ ] users.controller.spec.ts
        - [ ] Refaire les tests avec les appels à amazonSES
    - [ ] Virer le MailerModule de l'app.module.ts


**6 Janvier**
- [x] Préparation à la réunion
    - [x] Parcourir le fichier pptx de présentation
- [x] Réunion 2h + avec BPI.
- [ ] Trouver pourquoi c'est François ANTHONY à qui le mail de confirmation a été envoyé
- [ ] Voir avec Pascal pour l'activation de la sim "clé 4G"
- [ ] Kmo amazon SES service : 
    - [ ] Lancement des tests back pour assurance que le tout fonctionne encore comme attendu
        - [ ] Résolution de l'erreur : 
        ```
        ERROR [TypeOrmModule] Unable to connect to the database. Retrying (4)...
        Error: ER_ACCESS_DENIED_ERROR: Access denied for user ''@'172.19.0.1' (using password: NO)
        ```
        - [x] Changement dans le fichier app.module.ts : renseignement des valeurs locales en dur :
        ```
        TypeOrmModule.forRoot({
			type: 'mariadb',
			host: 'KMO-mysql', //process.env.MYSQL_HOST,
			port: 3306,
			username: 'KMO',//process.env.MYSQL_USER, mettre les valeurs en dur ?
			password: 'KMO15022022', //process.env.MYSQL_PASSWORD,
			database: 'KMO', //process.env.MYSQL_DATABASE,
			entities: [Checkout, User, Store, Data],
			autoLoadEntities: true,
			synchronize: false,
		}),
        ```
        - [x] Rebuild du docker, et erreur :
        ```
         ERROR [TypeOrmModule] Unable to connect to the database. Retrying (1)...
        Error: getaddrinfo ENOTFOUND KMO-mysql
        ```
        - [x] Modification du champ host en kmo-mysql au lieu de KMO-mysql
    - [x] Rebuild docker & relance des tests back pour assurance que le tout fonctionne encore comme attendu
        - [ ] Rapports d'erreur : 8 failed
            - Huit erreurs de  Cannot find module 'src/ses-mailing/amazon-ses.service' from ... dû à la présence de src.
        - [ ] Remplacement du chemin en dur de import { AmazonSesService } from 'src/ses-mailing/amazon-ses.service'; à ... ./../ses-mailing... sur les fichiers
            - [ ] reports/reports.service.ts & fichiers associés
            - [ ] tickets/tickets.service.ts & fichiers associés
        - [ ] Résolution du test  users/users.controller.spec.ts
            ```
            ● Test suite failed to run

                src/users/users.controller.spec.ts:18:78 - error TS2554: Expected 3 arguments, but got 4.

                18   usersService = new UsersService(userRepository, jwtService, mailerService, amazonService);
            ```
    - [ ] reports.service.spec.ts
        - [ ] Refaire les tests avec les appels à amazonSES
    - [ ] users.controller.spec.ts
        - [ ] Refaire les tests avec les appels à amazonSES
    - [ ] Virer le MailerModule de l'app.module.ts