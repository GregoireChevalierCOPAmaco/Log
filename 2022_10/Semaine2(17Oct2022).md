*** LOG Gregoire Chevalier ***

**17 Octobre**
- [x] Résolution des problèmes de connexion WSL/Vscode
- [x] Trouver les fichiers relatifs au déploiement aws
    - [x] Observation de la branche "KW273_upload_file_s3"
        - [x] Check du fichier KMO_WEB/.idea/inspectionProfiles/Project_Default.xml
        - [x] Check du fichier KMO_WEB/.idea/KMO_WEB.iml
        - [x] Check du fichier KMO_WEB/.idea/modules.xml
        - [x] Check du fichier KMO_WEB/COP-SAV/src/app/pages/cop/user/user.component.ts
        - [x] Check du fichier KMO_WEB/kmo-back/environements/mysql/local.env avec ajout des lignes :
        ```
        AWS_ACCESS_KEY_ID = xxxxxxxxxxxxxxxx
        AWS_SECRET_ACCESS_KEY = xxxxxxxxxxxxxx
        ``` 
        (ids et key masqués pour sécurité)
        - [x] Check du fichier KMO_WEB/kmo-back/src/reports/S3UploadFile.service.ts
        - [x] Check du fichier KMO_WEB/kmo-back/src/reports/reports.controller.ts (lien avec et utilisation du service S3uploadFile)
        - [x] Check du fichier KMO_WEB/kmo-back/src/reports/reports.module.ts (lien avec le service S3uploadFile et lien comme provider)
        - [x] Check du fichier KMO_WEB/kmo-back/src/reports/reports.service.ts (déclaration dans le constructeur, et utilisation dans le cadre d'awaits)
        ```
        let buffer = Buffer.from(signReportDto.signBase.split(",")[1], "base64");
        await this.fileService.upload(buffer);
        ```
    - [x] Observation de la branche "KW275_S3_API_back"
        - [x] Check du fichier KMO_WEB/.idea/inspectionProfiles/Project_Default.xml
        - [x] Check du fichier KMO_WEB/kmo-app/src/app/services/PhotoService/photo.service.ts
        - [x] Check du fichier KMO_WEB/kmo-app/README.md
        - [x] Check du fichier KMO_WEB/COP-SAV/src/app/core/interfaces/IReport.ts
        ```
        epiPath: string;
        rpsPath: string;
        ```
        - [x] Check du fichier KMO_WEB/COP-SAV/src/app/core/services/ReportService/report.service.ts idem que plus haut.
- [x] Trouver ce à quoi correspond le terme 'Bucket' dans le contexte d'aws
    - [x] Check de (https://www.techtarget.com/searchaws/definition/AWS-bucket)
    - [x] Détail du concept dans un retour de veille.
- [x] Trouver ce à quoi correspond le terme 'buffer' dans le contexte d'aws
    - [x] Détail du concept dans un retour de veille.
- [ ] Trouver ce à quoi correspondent les termes 'epiPath' & 'rpsPath' 
- [x] Créer une ensemble entité-classe-component-service-persistance
    - [x] Check de (https://docs.nestjs.com/recipes/crud-generator)
    - [x] Lancement des containers
    - [x] Positionnement dans le dossier kmo-back
        - [x] Lancement de la commande :
        ```
        nest g resource
        ```
        Et sélection des options :
        - exteriorContacts (nom)
        - API Rest
        - oui à la génération d'entrypoints
        Retour de console :
        ```
         nest g resource
        ? What name would you like to use for this resource (plural, e.g., "users")? exteriorContacts
        ? What transport layer do you use? REST API
        ? Would you like to generate CRUD entry points? Yes
        CREATE src/exterior-contacts/exterior-contacts.controller.ts (1129 bytes)
        CREATE src/exterior-contacts/exterior-contacts.controller.spec.ts (678 bytes)
        CREATE src/exterior-contacts/exterior-contacts.module.ts (326 bytes)
        CREATE src/exterior-contacts/exterior-contacts.service.ts (765 bytes)
        CREATE src/exterior-contacts/exterior-contacts.service.spec.ts (531 bytes)
        CREATE src/exterior-contacts/dto/create-exterior-contact.dto.ts (41 bytes)
        CREATE src/exterior-contacts/dto/update-exterior-contact.dto.ts (209 bytes)
        CREATE src/exterior-contacts/entities/exterior-contact.entity.ts (32 bytes)
        UPDATE src/app.module.ts (2615 bytes)
        ```
    - [x] Survol des fichiers créés
    - [x] Faire un comparatif des fichiers créés avec les fichiers user pour avoir une référence.
        - [x] Modification du fichier exterior-contact.entity.ts de :
        ```
        export class ExteriorContact {}
        ```
        à :
        ```
        import {
        BeforeInsert,
        Column,
        Entity,
        JoinTable,
        PrimaryGeneratedColumn
        } from "typeorm";
        
        @Entity()
        export class ExteriorContact {

            @PrimaryGeneratedColumn("uuid")
            id: string;

            @Column({ nullable: false })
            nom: string;

            @Column({ nullable: false })
            prenom: string;

            @Column({ nullable: false , unique: true})
            telephone: string;
            
            @BeforeInsert()
            phoneNumberFormat() { 
                this.telephone = this.telephone.replace(/(.{2})(?!$)/g, "$1.");
            }

        }
        ```
        - [x] Modification du fichier create-exterior-contact.dto.ts de :
        ```
        export class CreateExteriorContactDto {}
        ```
        à 
        ```
        import { ApiProperty } from "@nestjs/swagger";

        export class CreateExteriorContactDto {
            @ApiProperty()
            nom: string;

            @ApiProperty()
            prenom: string;

            @ApiProperty()
            telephone: string;
        }
        ```
        - [x] Modification du fichier update-exterior-contact.dto.ts de :
        ```
        import { PartialType } from '@nestjs/swagger';
        import { CreateExteriorContactDto } from './create-exterior-contact.dto';

        export class UpdateExteriorContactDto extends PartialType(CreateExteriorContactDto) {}
        ```
        à 
        ```
        import { PartialType } from '@nestjs/swagger';
        import { CreateExteriorContactDto } from './create-exterior-contact.dto';
        import { ApiProperty } from "@nestjs/swagger";

        export class UpdateExteriorContactDto extends PartialType(CreateExteriorContactDto) {

            @ApiProperty()
            nom: string;

            @ApiProperty()
            prenom: string;

            @ApiProperty()
            telephone: string;

        }
        ```
        - [x] Modification du fichier exterior-contact.controller.ts, ajout des lignes :
        ```
        import { ApiQuery, ApiTags } from '@nestjs/swagger';

        @ApiTags('Exterior-contacts')
        ```
        - [x] Modification du fichier exterior-contact.module.ts, ajout des lignes :
        ```
        import { TypeOrmModule } from '@nestjs/typeorm';
        import { ExteriorContact } from './entities/exterior-contact.entity';

        @Module({
        imports: [TypeOrmModule.forFeature([ExteriorContact])],
        ...
        exports: [ExteriorContactsService, TypeOrmModule]
        ```
        - [x] Modification du fichier exterior-contacts.service.ts, définition des return du crud pour passage de message template :
        ```
        create(createExteriorContactDto: CreateExteriorContactDto) {
            return 'This action adds a new exteriorContact';
        }
        ```
        à du tangible.
        - [x] Import des dépendances nécessaires
        - [x] Définition du constructor
        - [x] Écriture des fonctions (! = async ?) : async est nécessaire pour les users afin d'attendre d'avoir créé le premier user admin en dur pour lancer la db à laquelle se connecter. Nécessaire pour autre chose ? oui car attente réponse du service. Passage pour exemple de :
            ```
             async create() {
                return 'This action adds a new exteriorContact';
            }
            ```
            à :
            ```
              async create(createExteriorContactDto: CreateExteriorContactDto) {
                const exteriorContact = await this.exteriorContactRepository.create(createExteriorContactDto);
                
                console.log('This action adds a new exteriorContact');
                return this.exteriorContactRepository.save(exteriorContact).catch(err => {
                throw new HttpException({
                    message: err.message
                }, HttpStatus.BAD_REQUEST);
                });
            }
            ```
    - [x] Ajout de la nouvelle table à la db. Migration ?
        - [x] Check de (https://wanago.io/2022/07/25/api-nestjs-database-migrations-typeorm/)
        - [x] Redémarrage des containers suffisant
    - [x] Test de création d'exterior-content avec postman : fonctionnel.
    - [x] Adaptation du formulaire front
    - [x] Lier le formulaire précédemment créé à l'entité
        - [x] Check de (https://stackoverflow.com/questions/69242002/database-update-problem-in-angular-nestjs)
        - [x] Dans le fichier   , changement de :
        ```
          async test() {
            this.testService.test(this.testForm.value);
        }
        @HostListener('keydown.enter',['$event'])
        testEnventEnter(event:KeyboardEvent){

            this.testService.test(this.testForm.value).subscribe(res=>{
            console.log(res);
            })

        }
        ```
        à : 
        ```
        async test() {
            this.testService.test(this.testForm.value).subscribe(res=>{
            console.log(res)
            })
        }
        @HostListener('keydown.enter',['$event'])
        testEnventEnter(event:KeyboardEvent){

            this.testService.test(this.testForm.value).subscribe(res=>{
            console.log(res);
            })

        }
        ```
        Le changement fait fonctionner l'ensemble et arriver les données en base. Mais je sais pas pourquoi juste rajouter subscribe(res) donne ce comportement 
        ¯\ _ (ツ)_/¯
- [ ] Voir les interactions avec les services aws
    - [ ] S3
        - [x] Check de (https://levelup.gitconnected.com/upload-files-to-s3-using-angular-and-nestjs-64721d815d18)
        - [x] Check de (https://docs.aws.amazon.com/AmazonS3/latest/userguide/creating-bucket.html)
    - [ ] LightSail
    - [ ] EC2
- [ ] Testing du projet
    - [ ] Aller voir (https://ionicframework.com/docs/angular/testing#general-testing-structure)
    - [ ] Définir les contraintes de test
    - [ ] Rechercher le framework de test le plus adapté aux problématiques du projet  
        - [x] Check de (https://geekflare.com/fr/javascript-unit-testing/)
    - [ ] Réaliser des essais de tests
  
**18 Octobre**
- [x] Réunion de projet 
    - [x] Récap dans le fichier de synthèse de réunion
    - [x] Assignation des premières tâches
- [ ] Voir les interactions avec les services aws
    - [ ] S3
        - [ ] Poursuite de documentation
    - [ ] LightSail
    - [ ] EC2
- [ ] Testing du projet
    - [x] Définir les contraintes de test
    - [x] Rechercher le framework de test le plus adapté aux problématiques du projet  
        - [x] Check de (https://geekflare.com/fr/javascript-unit-testing/)
        - [x] JEST semble le plus approprié : 
            - Natif pour les projets Angular & Nest
            - Démarrage rapide et faible en ressources
            - Syntaxe simplifiée
            - Usage de snapshots pour tester plus facilement les gros objets
    - [x] Documentation : aller voir (https://jestjs.io/docs/getting-started) & (https://docs.nestjs.com/fundamentals/testing#testing)
    - [ ] Réaliser des essais de tests
        - [x] Setup de Jest dans le projet
            - [x] Lancement à la racine du projet de : 
            ```
            npm i --save-dev @nestjs/testing
            ```
        - [x] Run de ```npm test```, retour console : 
        ```
        Test Suites: 17 failed, 3 passed, 20 total
        Tests:       17 failed, 4 passed, 21 total
        Snapshots:   0 total
        Time:        18.544 s
        Ran all test suites.
        ```
        - [ ] Tester l'entité template exterior-contacts
            - [x] Run de ```npm test 'exterior-contacts'```, retour console : 
            ```
            FAIL  src/exterior-contacts/exterior-contacts.service.spec.ts (5.384 s)
            ● ExteriorContactsService › should be defined
            Nest can't resolve dependencies of the ExteriorContactsService (?). Please make sure that the argument ExteriorContactRepository at index [0] is available in the RootTestModule context.                 
            Potential solutions:  
            - If ExteriorContactRepository is a provider, is it part of the current RootTestModule?   
            - If ExteriorContactRepository is exported from a separate @Module, is that module imported within RootTestModule?                                            @Module({imports: [ /* the Module containing ExteriorContactRepository */ ]})

            FAIL  src/exterior-contacts/exterior-contacts.controller.spec.ts (5.529 s)
            ● ExteriorContactsController › should be defined

            Nest can't resolve dependencies of the ExteriorContactsService (?). Please make sure that the argument ExteriorContactRepository at index [0] is available in the RootTestModule context.

            Potential solutions:
            - If ExteriorContactRepository is a provider, is it part of the current RootTestModule?
            - If ExteriorContactRepository is exported from a separate @Module, is that module imported within RootTestModule?
            @Module({
                imports: [ /* the Module containing ExteriorContactRepository */ ]
            })

            Test Suites: 2 failed, 2 total
            Tests:       2 failed, 2 total
            Snapshots:   0 total
            Time:        6.18 s, estimated 16 s
            Ran all test suites matching /exterior-contacts/i.
            ```  
            - [x] Identification du problème : 
                - il faut ajouter les providers au RootTestModule, car Nest n'inclut pas automatiquement les services dans le test.
                - il faut s'assurer que l'argument ExteriorContactRepository at index [0] est accessible dans le contexte RootTestModule.  
                - [x] Passage de : 
                ```
                beforeEach(async () => {
                    const module: TestingModule = await Test.createTestingModule({
                    providers: [ExteriorContactsService],
                }).compile();
                ```
                à :
                ```
                beforeEach(async () => {
                    const module: TestingModule = await Test.createTestingModule({
                    providers: [
                        ExteriorContactsService,
                        {
                        provide: getRepository(ExteriorContact)
                        }
                    ],
                }).compile();
                ```
                - Retour d'erreur : 
                ```
                Type 'Repository<ExteriorContact>' is not assignable to type 'InjectionToken'.
                ```
                - [x] Check de (https://github.com/nestjs/nest/issues/363), (https://stackoverflow.com/questions/62942112/nest-cant-resolve-dependencies-in-the-roottestmodule-context-when-i-use-bazel-t), & (https://stackoverflow.com/questions/64716494/please-make-sure-that-the-argument-contactrepository-at-index-0-is-available-i) avec application des suggestions sans succès
                - [x] Résolution pour le fichier .service.spec.ts en changeant :
                ```
                beforeEach(async () => {
                    const module: TestingModule = await Test.createTestingModule({
                    providers: [ExteriorContactsService, 
                        { provide: getRepositoryToken(ExteriorContact), useValue: jest.fn()}
                    ],
                    }).compile();

                    service = module.get<ExteriorContactsService>(ExteriorContactsService);
                });
                ```
            - [x] Résolution pour le fichier .controller.spec.ts de l'erreur : 
            ```
            Nest could not find ExteriorContactsController element (this provider does not exist in the current context)
            ```
            L'erreur venait de la typo : 
            ```
            beforeEach(async () => {
                const module: TestingModule = await Test.createTestingModule({
                controllers: [ExteriorContactsService],
                ...
            ```
            Résolu en rectifiant par :
            ```
            beforeEach(async () => {
                const module: TestingModule = await Test.createTestingModule({
                    controllers: [ExteriorContactsController],
            ```
            - [ ] Tests unitaires
                - [x] Assurer que le retour findAll() des ExteriorContacts soit Defined
                - [x] Essai de retour findAll() des ExteriorContacts
                - [x] Check de (https://dev.to/walrusai/testing-database-interactions-with-jest-519n) 
                - [ ] Tester le type de données retourné par le form (ou voir validator ?)
                - [ ] Tester les accès à la db
            - [ ] Voir les capacités de mock offertes par jest/nest
            - [ ] Tests fonctionnels
            - [ ] Tests e2e
            - [ ] S'assurer que les données arrivent en base
        - [ ] **Tester l'entité Reports**
- [ ] Checker Obsidian pour la mise en oeuvre des logs en .md

**19 Octobre**
- [ ] Testing du projet
    - [ ] Tests unitaires
        - [x] Tester le type de données retourné par le form
            - [x] Survol des validators du front
                - [x] Check du fichier KMO_WEB\COP-SAV\src\app\pages\cop\user\user.component.ts . Les validateurs utilisés sont basiques et ne demandent qu'une valeur en retour, sauf pour email qui doit avoir le format mail :
                ```
                  initUser() {
                    this.userForm = this.fb.group({
                    firstname: ['', Validators.required],
                    lastname: ['', Validators.required],
                    email: ['', [Validators.required, Validators.email]],
                    password: ['', Validators.required],
                    roles: ['', Validators.required],
                    phoneNumber: ['', Validators.required],
                    vehicleRegistration:[null]

                    })
                }
                ```
                - [x] Check du fichier similaire sur branche à jour : code similaire
                - [x] Conclusion : il va falloir détailler les Validators ou faire des tests sur le retour du type de donnée. À voir avec l'équipe
        - [ ] Voir avec l'équipe le cas des validators/test de retour de données
        - [ ] Faire un essai de test de retour de type de donnée
            - [ ] Tester le champ firstname
                - [x] Tester le cas nb de char insuffisant
                    - [x] Produire le cas FAIL
                    - [x] Produire une exception
                        - [x] Check de (https://github.com/facebook/jest/issues/8279)
                        - [x] Création d'une class StringLengthError.ts & appel de l'error dans le test
                    - [x] Produire le cas PASS
                        - [x] Se documenter sur les exceptions jest
                    - [ ] Lier le test au retour de donnée du form
                - [ ] Tester le cas mauvais type de donnée
                    - [x] Documentation
                    - [x] Produire le cas FAIL
                    - [x] Produire une exception
                        - [x] Création d'une class StringTypeError.ts & appel de l'error dans le test
                    - [x] Produire le cas PASS
                    - [ ] Lier le test au retour de donnée du form
                - [ ] Tester le cas type-regex
                    - [x] Documentation (https://jestjs.io/docs/expect#tomatchregexp--string)
                    - [x] Petit tips pour déclarer une regex en ts : 
                    ```
                    const regex:RegExp = new RegExp(/[A-Za-zÀ-ÖØ-öø-ÿ]/) ;
                    ```
                    - [x] Produire le cas FAIL
                    - [x] Produire une exception
                        - [x] Création d'une class UnallowedCharactersError.ts & appel de l'error dans le test
                    - [x] Produire le cas PASS
                    - [ ] Lier le test au retour de donnée du form
                - [ ] Check de (https://jestjs.io/docs/asynchronous)
        - [x] Documentation sur les méthodes jest (https://jestjs.io/docs/api#methods)
            - [ ] Voir quelle forme conviendrait mieux que les if{}else{} actuellement dans mes tests
        - [ ] Exécution de séries d'un même test avec des jeux de données différents suivant le describe.each()
        - [ ] Tester les accès à la db
        - [ ] Voir les capacités de mock offertes par jest/nest
    - [ ] Tests fonctionnels
    - [ ] Tests e2e
    - [ ] S'assurer que les données arrivent en base
    - [ ] **Tester l'entité Reports**
- [ ] Voir les interactions avec les services aws
    - [ ] S3
        - [ ] Poursuite de documentation
    - [ ] LightSail
    - [ ] EC2
- [ ] Checker Obsidian pour la mise en oeuvre des logs en .md

