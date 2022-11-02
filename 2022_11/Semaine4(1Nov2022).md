*** LOG Gregoire Chevalier ***

**31 Octobre**
- [x] Cleanup des fichiers de test
    - [x] Suppression des commentaires
    - [x] Suppression des console.log()
    - [x] Run des tests pour s'assurer de leur passage
- [x] Push sur KM-297_testsGreg
    - [x] Check que je suis sur la bonne branche en local
    - [x] Commit 
        - [x] Résolution d'erreur de code liées au linting
    - [x] Push
    - [x] Merge
        - [x] Pour note : l'env a été changé pour l'url de prod pour les tablettes :
        ```
        export const environment = {
            production: true,
           - apiUrl: 'http://3.126.39.182:3000/',
           + apiUrl: 'http://18.197.225.248:3000/',
        };
        ```
- [x] Voir comment s'organisent les relations via les methodes de CRUD pour les entités
    - [x] Les relations sont gérées par les décorators @Unique, @ManyToOne, etc ... dans les fichiers entités. **Logiquement, si les services & controllers sont testés, leurs relations sont viables ?**
    - [x] Check de (https://dkzeb.medium.com/unit-testing-in-ts-jest-with-typeorm-entities-ad5de5f95438) & (https://stackoverflow.com/questions/70092175/mocking-relationships-in-typeorm-nestjs)
- [x] Voir comment s'organisent les relations entre les Reports et les entités reliées
    - [x] Check de KMO_WEB\kmo-back\src\reports\entities\report.entity.ts
    - [x] Check de (https://www.valentinog.com/blog/jest-coverage/) sur le code coverage
- [x] Reprise du testing
- [x] Survol des fichiers de test de Reports
- [ ] Appliquer les tests à l'entité Reports
    - [x] Mock du CRUD
    - [x] Test du Create
    - [ ] Test de l'envoi du mail lors du create
    - [x] Test du Read
    - [ ] Test de l'Update
        - [ ] Résolution des erreurs créées
    - [ ] Test du Delete
        - [ ] Test du  CRUD avec relation à un store, un ticket
- [ ] Tests e2e
- [ ] Penser à donner un justificatif de nouveau domicile à hubert
  
  
**2 Novembre**
- [x] Reprise du testing 
- [ ] Appliquer les tests à l'entité Reports 
    - [x] Test du Read
    - [x] Test de l'Update
        - [x] Résolution des erreurs créées
            - [x] Résolution de : 
            ```
            No overload matches this call.
            Overload 1 of 4, '(entities: { id?: string; reference?: string; clipperRef?: string; date?: ........... }
        - [x] Console log de report & updateReport. Retour de log :
        ```
        undefined
        at update (reports/reports.service.spec.ts:387:12)

        UpdateResult { generatedMaps: [], raw: [], affected: 2 }
        ```
        - [x] Récupérer le report au lieu d'un undefined
        at update (reports/reports.service.spec.ts:388:12) : 
        changement du paramètre fourni dans le findOne(), passage de new report reference à report reference. Cela retourne le report
- [x] Redémarrage pour résolution des problèmes de push du log
- [x] Passage sur le log sous windows != wsl
- [x] git pull & màj
    - [x] Test du Delete
    - [ ] Répondre à la question : est-ce normal de pouvoir créer des reports de même noms ? -> unique sur le champ reference ?
    - [ ] Test de l'envoi du mail lors du create
        - [x] Check de (https://www.npmjs.com/package/jest-smtp) & (https://github.com/tglink/jest-email-reporter)
        - [x] Survol des fonctions relevant de l'envoi de mail 
        - [x] Check des fichiers relevant de mailer.service.ts
        - [x] En gros : si l'objet createReportDto == true dans la requête de création, on passe dans la boucle d'envoi de mail :
        ```
        const store = await this.StoreRepository.findOne(createReportDto.store);
			this.mailerService
				.sendMail({
					to: 'g.chevalier@cop-amaco.com',
					from: 'copdevbot@gmail.com',
					subject:
						'Le rapport ' +
						createReportDto.reference +
						" à besoin d'un second rendez-vous",
					html:
						'<h1>Bonjour</h1>\n' +
						'<p>Le rapport </strong>' +
						createReportDto.reference +
                        .....
						createReportDto.observationProblem,
				})
				.then((success) => {
					console.log(success);
				})
				.catch((err) => {
					console.log(err);
				});
        ```
        - [x] Résolution de l'erreur : 
            ```
            TypeError: Cannot read properties of undefined (reading 'sendMail')
            ```
            - [x] Check de (https://stackoverflow.com/questions/70737323/jest-error-typeerror-cannot-read-properties-of-undefined-reading-send), (https://salesforce.stackexchange.com/questions/372425/uncaught-in-promise-typeerror-cannot-read-properties-of-undefined-reading-b) & (https://github.com/nest-modules/mailer/blob/master/lib/mailer.service.spec.ts)
            - [x] Uilisation des tests en snapshot comme renseigné dans (https://github.com/nest-modules/mailer/blob/master/lib/mailer.service.spec.ts) 
            - [x] Check de (https://jestjs.io/docs/snapshot-testing) pour référence
        - [x] Mail service mock-testé
        - [ ] Tester en réel le mailing ? Pas sûr que ce soit nécessaire...
    - [ ] Test du  CRUD avec relation à un store, un ticket
        - [x] Création d'un store
        - [x] Création d'un ticket
        - [x] Création des liens report-ticket-store
            - [x] Le lien se fait entre report & store mais pas entre report & ticket... -> insérer :
            ```
            @ManyToOne(() => Ticket, (ticket) => ticket.id, { onDelete: 'CASCADE' })
	        ticket: Ticket;
            ```
            au create-report.dto.ts ??? ou voir s'il est logique ou non d'assigner le ticket à la création du report.... ?
            - [x] Insertion du ticket comme many to one dans le CreateReportDTO
        - [x] Test du Create
            - [x] Lien avec store
            - [x] Lien avec ticket
        - [x] Test du Read
            - [x] Lien avec store
            - [x] Lien avec ticket
        - [ ] Test de l'Update
        - [ ] Test du Delete
- [ ] Tests e2e
- [ ] Penser à donner un justificatif de nouveau domicile à hubert