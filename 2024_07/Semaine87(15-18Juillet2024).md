**15 Juillet**
- [x] Arrivée et mise au point avec Loïc et Franck sur COPACO (sujet de discussion : stocks)
- [x] Reprise ESL avec :
    - [x] ESL-88 Changer la taille de police des templates
        - [x] Modifications au front : 
        - ajout de fontsize? à l'interface du templateData
        - modifications des templates pour y ajouter la fontsize
        - modification de la logique du component et de updateCanvas()
        - [x] Modifications au back : 
        - ajout de fontsize? à l'entity templateData
        - ajout de fontSize aux DTOs relatifs
        - ajustements de la logique dans le service
    - [x] Lint, résolution des tests, commit & push


**16 Juillet**
- [x] Reprise ESL avec :
    - [x] ESL-85 Rendre les champs de l'éditeur de templates réactifs
        - [x] À GPT : 
        ```
        fichiers icic
        [...]

        this is the current state of my app's files. I am pretty happy about it.
        Now i want one more thing : to have the canvas display in real time the text an user would fill in the fields : [(ngModel)]="text1",  [(ngModel)]="text2" & [(ngModel)]="text3"
        So the wanted behaviour would be : select a template using the selector would setup canvas width, height & content datas' position&fontSize but the text inputted in the fileds could change dynamically on the fly
        ```
        - [x] Changement de la fonction updateCanvas() dans le TemplateEditorComponent : 
        ```
        updateCanvas(): void {
            const canvas = this.canvas.nativeElement;
            const ctx = this.ctx;
            ctx?.clearRect(0, 0, canvas.width, canvas.height);

            const selectedTemplate = this.templates[this.selectedTemplateIndex];

            const textData = [
            { text: this.text1, positionX: selectedTemplate.datas[0].positionX, positionY: selectedTemplate.datas[0].positionY, fontSize: selectedTemplate.datas[0].fontSize },
            { text: this.text2, positionX: selectedTemplate.datas[1].positionX, positionY: selectedTemplate.datas[1].positionY, fontSize: selectedTemplate.datas[1].fontSize },
            { text: this.text3, positionX: selectedTemplate.datas[2].positionX, positionY: selectedTemplate.datas[2].positionY, fontSize: selectedTemplate.datas[2].fontSize }
            ];

            textData.forEach((data) => {
            if (data.fontSize) {
                ctx!.font = `${data.fontSize}px Arial`;
            }
            ctx?.fillText(data.text, data.positionX, data.positionY);
            });
        }
        ```
        - [x] Ajustement du test associé : 
        ```
        it('should update canvas with text contents from selected template on updateCanvas', () => {    
            component.selectedTemplateIndex = 1; 
            component.ngOnInit(); 

            component.text1 = templates[component.selectedTemplateIndex].datas[0].content;
            component.text2 = templates[component.selectedTemplateIndex].datas[1].content;
            component.text3 = templates[component.selectedTemplateIndex].datas[2].content;

            const canvas = component.canvas.nativeElement;
            const ctx = canvas.getContext('2d') as CanvasRenderingContext2D;
            jest.spyOn(ctx, 'clearRect');
            jest.spyOn(ctx, 'fillText');

            component.updateCanvas();

            templates[component.selectedTemplateIndex].datas.forEach((data, index) => {
            const text = [component.text1, component.text2, component.text3][index];
            expect(ctx.fillText).toHaveBeenCalledWith(text, data.positionX, data.positionY);
            });
        });
        ```
- [x] Mise à jour Jira
    - [x] Création de tickets : 
    - ESL-89 Reprendre et terminer la branche 83 d'Anthony
    - ESL-90 Écrire et faire passer les tests unitaires sur les nouvelles fonctionnalités
    - ESL-91 Fix l'affichage du contenu dans la page
    - ESL-92 Voir avec Kévin pour mettre en place une charte graphique rapide et efficace
- [ ] ESL-89 Reprendre et terminer la branche 83 d'Anthony
    - [ ] ESL-90 Écrire et faire passer les tests unitaires sur les nouvelles fonctionnalités
        - [x] checkout sur la branche, et ```git pull origin develop```
        - [x] Run des tests pour assessment ; 
        front : 
        ```
        Test Suites: 2 failed, 10 passed, 12 total
        Tests:       3 failed, 28 passed, 31 total
        ```
        back :
        ```
        Test Suites: 3 failed, 6 passed, 9 total
        Tests:       2 failed, 23 passed, 25 total
        ```
- [ ] Voir avec Jacques pour connecter la base ESL à mon pc
    - [x] ipconfig (en étant sur wifi prod) : 
    ```
    Carte réseau sans fil Wi-Fi :

    Suffixe DNS propre à la connexion. . . : cop-amaco.local
    Adresse IPv6 de liaison locale. . . . .: fe80::31b6:5290:e407:4d02%15
    Adresse IPv4. . . . . . . . . . . . . .: 192.168.1.140
    Masque de sous-réseau. . . . . . . . . : 255.255.255.0
    Passerelle par défaut. . . . . . . . . : 192.168.1.254
    ```
    - [x] Suivi de la minidoc sur le channel ESL de discord
    - [x] Ouverture du pourt 1884 au traffic entrant et sortant (suivi de  : https://fr.wikihow.com/ouvrir-des-ports)
- [x] Réunion ESLs, ce qu'il en filtre : 
    ```
    ESLs : voir pour après le poc 
    - avoir les metrics esl connectées/déconnectées + filtrage
    - ajouter le champ nom pour la sauvegarde du template
    - prévoir l'ajout de csv pour update les prix (fake pour poc, en réel pour la v2) / bouton connecter à la base de données (bullshit connecteur SAP)
    ```


**17 Juillet**
- [ ] ESL-89 Reprendre et terminer la branche 83 d'Anthony
    - [ ] ESL-90 Écrire et faire passer les tests unitaires sur les nouvelles fonctionnalités
        - [x] store articles controller, ajout dans les providers du mock de streArticleRepo 
        ```
        beforeEach(async () => {
            const module: TestingModule = await Test.createTestingModule({
            controllers: [StoreArticlesController],
            providers: [
                StoreArticlesService,
                {
                provide: getRepositoryToken(StoreArticle),
                useClass: Repository,
                },
            ],
        }).compile();
        ```
        & écriture des autres tests : 
        ```
        describe('StoreArticlesController', () => {
            let controller: StoreArticlesController;
            let service: StoreArticlesService;

            const mockStoreArticle: StoreArticle = {
                id: 1,
                name: 'Article 1',
                price: 100,
                bar_code: '123456789',
                origine: 'France',
                updatedAt: new Date(),
                esl: null,
            };

            beforeEach(async () => {
                const module: TestingModule = await Test.createTestingModule({
                controllers: [StoreArticlesController],
                providers: [
                    StoreArticlesService,
                    {
                    provide: getRepositoryToken(StoreArticle),
                    useClass: Repository,
                    },
                ],
                }).compile();

                controller = module.get<StoreArticlesController>(StoreArticlesController);
                service = module.get<StoreArticlesService>(StoreArticlesService);
            });

            it('should be defined', () => {
                expect(controller).toBeDefined();
            });

            describe('create', () => {
                it('should create a new store article', async () => {
                const createDto: CreateStoreArticleDto = {
                    name: 'Article 1',
                    price: 100,
                    bar_code: '123456789',
                    origine: 'France',
                };
                const result = { ...mockStoreArticle };

                jest.spyOn(service, 'create').mockImplementation(async () => result);

                expect(await controller.create(createDto)).toBe(result);
                });
            });

            describe('findAll', () => {
                it('should return paginated store articles', async () => {
                const result: Pagination<StoreArticle> = {
                    items: [mockStoreArticle],
                    meta: {
                    totalItems: 1,
                    itemCount: 1,
                    itemsPerPage: 10,
                    totalPages: 1,
                    currentPage: 1,
                    },
                };

                jest.spyOn(service, 'findAllPaginaite').mockImplementation(async () => result);

                expect(await controller.findAll(1, 10)).toBe(result);
                });
            });

            describe('update', () => {
                it('should update a store article', async () => {
                const updateDto: UpdateStoreArticleDto = {
                    name: 'Updated Article',
                    price: 120,
                    bar_code: '987654321',
                    origine: 'Germany',
                    esl: null,
                };
                const result = { ...mockStoreArticle, ...updateDto };

                jest.spyOn(service, 'update').mockImplementation(async () => result);

                expect(await controller.update('1', updateDto)).toBe(result);
                });
            });

            describe('remove', () => {
                it('should remove a store article', async () => {
                const result = { ...mockStoreArticle };

                jest.spyOn(service, 'remove').mockImplementation(async () => result);

                expect(await controller.remove('1')).toBe(result);
                });
            });
        }); 
        ```
        - [x] store articles service : 
        écriture des tests : 
        ```
        import { Test, TestingModule } from '@nestjs/testing';
        import { StoreArticlesService } from './store-articles.service';
        import { StoreArticle } from './entities/store-article.entity';
        import { Repository } from 'typeorm';
        import { getRepositoryToken } from '@nestjs/typeorm';
        import { CreateStoreArticleDto } from './dto/create-store-article.dto';
        import { UpdateStoreArticleDto } from './dto/update-store-article.dto';
        import { IPaginationOptions, Pagination } from 'nestjs-typeorm-paginate';

        describe('StoreArticlesService', () => {
        let service: StoreArticlesService;
        let repository: Repository<StoreArticle>;

        const mockStoreArticleRepository = {
            save: jest.fn(),
            createQueryBuilder: jest.fn().mockReturnThis(),
            findOne: jest.fn(),
            remove: jest.fn(),
            leftJoinAndSelect: jest.fn().mockReturnThis(),
            paginate: jest.fn(),
        };

        const mockStoreArticle: StoreArticle = {
            id: 1,
            name: 'Article 1',
            price: 100,
            bar_code: '123456789',
            origine: 'France',
            updatedAt: new Date(),
            esl: null,
        };

        beforeEach(async () => {
            const module: TestingModule = await Test.createTestingModule({
            providers: [
                StoreArticlesService,
                {
                provide: getRepositoryToken(StoreArticle),
                useValue: mockStoreArticleRepository,
                },
            ],
            }).compile();

            service = module.get<StoreArticlesService>(StoreArticlesService);
            repository = module.get<Repository<StoreArticle>>(getRepositoryToken(StoreArticle));
        });

        it('should be defined', () => {
            expect(service).toBeDefined();
        });

        describe('create', () => {
            it('should create a new store article', async () => {
            const createDto: CreateStoreArticleDto = {
                name: 'Article 1',
                price: 100,
                bar_code: '123456789',
                origine: 'France',
            };

            jest.spyOn(repository, 'save').mockResolvedValue(mockStoreArticle);

            const result = await service.create(createDto);
            expect(result).toEqual(mockStoreArticle);
            expect(repository.save).toHaveBeenCalledWith(createDto);
            });
        });

        describe('findOne', () => {
            it('should return a single store article', async () => {
            const result = await service.findOne(1);
            expect(result).toBe(`This action returns a #1 storeArticle`);
            });
        });

        describe('update', () => {
            it('should update a store article', async () => {
            const updateDto: UpdateStoreArticleDto = {
                name: 'Updated Article',
                price: 120,
                bar_code: '987654321',
                origine: 'Germany',
                esl: null,
            };

            const updatedArticle = { ...mockStoreArticle, ...updateDto };

            jest.spyOn(repository, 'findOne').mockResolvedValue(mockStoreArticle);
            jest.spyOn(repository, 'save').mockResolvedValue(updatedArticle);

            await service.update(1, updateDto);
            expect(repository.findOne).toHaveBeenCalledWith({ where: { id: 1 } });
            });
        });

        describe('remove', () => {
            it('should remove a store article', async () => {
            jest.spyOn(repository, 'findOne').mockResolvedValue(mockStoreArticle);
            jest.spyOn(repository, 'remove').mockResolvedValue(mockStoreArticle);

            const result = await service.remove(1);
            expect(result).toEqual(mockStoreArticle);
            expect(repository.findOne).toHaveBeenCalledWith({ where: { id: 1 } });
            expect(repository.remove).toHaveBeenCalledWith(mockStoreArticle);
            });
        });
        });
        ```
        - [x] esl controller : 
        retake des tests : 
        ```
        import { Test, TestingModule } from '@nestjs/testing';
        import { EslController } from './esl.controller';
        import { EslService } from './esl.service';
        import { CreateEslDto } from './dto/create-esl.dto';
        import { UpdateEslDto } from './dto/update-esl.dto';
        import { Pagination } from 'nestjs-typeorm-paginate';
        import { Esl } from './entities/esl.entity';
        import { Observable, Subscription } from 'rxjs';
        import { StoreArticle } from '../store-articles/entities/store-article.entity'; 

        describe('EslController', () => {
        let controller: EslController;
        let service: EslService;

        const mockStoreArticle: StoreArticle = {
            id: 1,
            name: 'Article 1',
            price: 100,
            bar_code: '123456789',
            origine: 'France',
            updatedAt: new Date(),
            esl: null,
        };

        beforeEach(async () => {
            const module: TestingModule = await Test.createTestingModule({
            controllers: [EslController],
            providers: [
                {
                provide: EslService,
                useValue: {
                    create: jest.fn(),
                    findAll: jest.fn(),
                    findAllPaginate: jest.fn(),
                    findOne: jest.fn(),
                    update: jest.fn(),
                    blink: jest.fn(),
                    remove: jest.fn(),
                },
                },
            ],
            }).compile();

            controller = module.get<EslController>(EslController);
            service = module.get<EslService>(EslService);
        });

        it('should be defined', () => {
            expect(controller).toBeDefined();
        });

        describe('create', () => {
            it('should create an ESL', async () => {
            const createEslDto: CreateEslDto = { supplier: 'Supplier1', id_esl: 'ID1', width: 100, height: 200, version_name: 'v1.0' };
            const createdEsl = { id: '1', ...createEslDto, store_article: mockStoreArticle };

            jest.spyOn(service, 'create').mockResolvedValue(createdEsl as any);

            const result = await controller.create(createEslDto);

            expect(result).toEqual(createdEsl);
            expect(service.create).toHaveBeenCalledWith(createEslDto);
            });
        });

        describe('findAll', () => {
            it('should return all ESLs', async () => {
            const mockEsls = [{ id: '1', id_esl: 'ID1', supplier: 'Supplier1', width: 100, height: 200, version_name: 'v1.0', store_article: mockStoreArticle }];

            jest.spyOn(service, 'findAll').mockResolvedValue(mockEsls as any);

            const result = await controller.findAll();

            expect(result).toEqual(mockEsls);
            expect(service.findAll).toHaveBeenCalled();
            });
        });

        describe('findAllPaginate', () => {
            it('should return paginated ESLs', async () => {
            const page = 1;
            const limit = 10;
            const mockPagination: Pagination<Esl> = {
                items: [{
                id: '1', id_esl: 'ID1', supplier: 'Supplier1', width: 100, height: 200, version_name: 'v1.0',
                picture: '',
                power: 0,
                average_rssi: 0,
                adv_group: 0,
                router_mac: '',
                timestamp: '',
                store_article: mockStoreArticle,
                }],
                meta: { itemCount: 1, totalItems: 1, itemsPerPage: limit, totalPages: 1, currentPage: page },
            };

            jest.spyOn(service, 'findAllPaginate').mockResolvedValue(mockPagination);

            const result = await controller.findAllPaginate(page, limit);

            expect(result).toEqual(mockPagination);
            expect(service.findAllPaginate).toHaveBeenCalledWith({ page, limit });
            });
        });

        describe('blink', () => {
            it('should send a blink request', async () => {
            const id = '1';
            const color = 'red';
            const mockResponse = { message: 'Blink request sent successfully' };
        
            const mockSubscription: Subscription = {
                unsubscribe: () => {},
                closed: false,
                add: () => {},
                remove: () => {},
                unsubscribeOn: () => {},
                constructor: () => {},
                hasOwnProperty: () => {},
                isPrototypeOf: () => {},
                propertyIsEnumerable: () => {},
                toLocaleString: () => {},
                toString: () => {},
                valueOf: () => {}
            } as any; 
            
            jest.spyOn(service, 'blink').mockReturnValue(mockSubscription);
        
            const mockObservable = new Observable(observer => {
                observer.next(mockResponse);
                observer.complete();
            });
        
            let result: any; 
        
            mockObservable.subscribe({
                next: data => {
                result = data; 
                },
                error: err => {
                throw err; 
                }
            });
        
            await controller.blink(id, color);
        
            expect(result).toEqual(mockResponse);
        
            expect(service.blink).toHaveBeenCalledWith(id, color);
            });
        });

        it('should return an ESL by id', async () => {
            const id = '1';
            const mockEsl: Esl = {
            id: '1',
            id_esl: 'ID1',
            supplier: 'Supplier1',
            width: 100,
            height: 200,
            version_name: 'v1.0',
            picture: '',
            power: 0,
            average_rssi: 0,
            adv_group: 0,
            router_mac: '',
            timestamp: '',
            store_article: mockStoreArticle,
            };

            (service.findOne as jest.Mock).mockResolvedValue(mockEsl);

            const result = await controller.findOne(id);

            expect(result).toEqual(mockEsl);
            expect(service.findOne).toHaveBeenCalledWith(id);
        });

        describe('update', () => {
            it('should update an ESL', async () => {
            const id = '1';
            const updateEslDto: UpdateEslDto = { supplier: 'Updated Supplier' };
            const updatedEsl = { id: '1', id_esl: 'ID1', width: 100, height: 200, version_name: 'v1.0', ...updateEslDto, store_article: mockStoreArticle };
        
            
            service.update = jest.fn().mockResolvedValue(updatedEsl);
        
            const result = await controller.update(id, updateEslDto);
        
            expect(result).toEqual(updatedEsl);
            expect(service.update).toHaveBeenCalledWith(+id, updateEslDto);
            });
        });
        

        describe('remove', () => {
            it('should remove an ESL', () => {
            const id = '1';
            const message = `This action removes a #${id} esl`;
        
            
            jest.spyOn(service, 'remove').mockImplementation(() => message);
        
            const result = controller.remove(id);
        
            expect(result).toEqual(message);
            expect(service.remove).toHaveBeenCalledWith(+id);
            });
        });
        });
        ```

**18 Juillet**
- [ ] ESL-89 Reprendre et terminer la branche 83 d'Anthony
    - [x] ESL-90 Écrire et faire passer les tests unitaires sur les nouvelles fonctionnalités
        - [x] EslTableComponent spec : 
        modifications au fichier de test : 
        ```
        it('should fetch ESLs on initialization', () => {
            const mockResponse = {
            items: [{ "id": 1, "name": "ESL 1" }],
            meta: { totalItems: 1 }
            };
            httpClientSpy.get.mockReturnValue(of(mockResponse));

            component.ngOnInit();
            expect(httpClientSpy.get).toHaveBeenCalledWith(`${eslService.route}/paginate/?page=${component.currentPage}&limit=${component.pageSize}`);
            expect(component.esls).toEqual(mockResponse.items);
            expect(component.totalItems).toEqual(mockResponse.meta.totalItems);
        });

        it('should update ESLs on page change', () => {
            const mockResponse = {
            items: [{ "id": 1, "name": "ESL 1" }],
            meta: { totalItems: 1 }
            };
            httpClientSpy.get.mockReturnValue(of(mockResponse));

            const newPage = 2;
            component.onPageChange(newPage);

            expect(httpClientSpy.get).toHaveBeenCalledWith(`${eslService.route}/paginate/?page=${newPage}&limit=${component.pageSize}`);
            expect(component.esls).toEqual(mockResponse.items);
            expect(component.totalItems).toEqual(mockResponse.meta.totalItems);
        });
        ```
        - [x] ArticleListComponent spec : 
        écriture de tests : 
        ```
        import { ComponentFixture, TestBed } from '@angular/core/testing';
        import { HttpClientTestingModule } from '@angular/common/http/testing';
        import { of } from 'rxjs';
        import { HttpClient } from '@angular/common/http';
        import { ArticleListComponent } from './article-list.component';
        import { StoreArticleService } from '../../services/store-article.service';

        describe('ArticleListComponent', () => {
        let component: ArticleListComponent;
        let fixture: ComponentFixture<ArticleListComponent>;
        let storeArticleService: StoreArticleService;
        let httpClientSpy: { get: jest.Mock };

        beforeEach(async () => {
            httpClientSpy = {
            get: jest.fn().mockReturnValue(of({ items: [], meta: { totalItems: 0 } })),
            };

            await TestBed.configureTestingModule({
            imports: [ArticleListComponent, HttpClientTestingModule],
            providers: [
                StoreArticleService,
                { provide: HttpClient, useValue: httpClientSpy }
            ],
            }).compileComponents();

            fixture = TestBed.createComponent(ArticleListComponent);
            component = fixture.componentInstance;
            storeArticleService = TestBed.inject(StoreArticleService);
        });

        it('should create', () => {
            expect(component).toBeTruthy();
        });

        it('should initialize with empty articles array and pagination variables', () => {
            expect(component.articles).toEqual([]);
            expect(component.currentPage).toEqual(1);
            expect(component.pageSize).toEqual(10);
            expect(component.totalItems).toEqual(0);
        });

        it('should fetch articles on initialization', () => {
            const mockResponse = {
            items: [{ "id": 1, "title": "Article 1" }],
            meta: { totalItems: 1 }
            };
            httpClientSpy.get.mockReturnValue(of(mockResponse));

            component.ngOnInit();
            expect(component.articles).toEqual(mockResponse.items);
        });

        it('should update articles on page change', () => {
            const mockResponse = {
            items: [{ "id": 1, "title": "Article 1" }],
            meta: { totalItems: 1 }
            };
            httpClientSpy.get.mockReturnValue(of(mockResponse));

            const newPage = 2;
            component.onPageChange(newPage);

            expect(component.articles).toEqual(mockResponse.items);
        });
        });
        ```
    - [ ] ESL-93 Fix l'affichage des ESL par page & la pagination
- [ ] Réunion COPACO avec pascal denis fede loic franck
```
fede soulève la possibilité que les stocks soient vérolés par l'utilisation de copaco
utilisation de moulinette pour trouver les références réservées par commande
service achat sous effectif et pas fiable selon pascal avec (?) conflit au sein du service 
"on est partis pour supprimer copaco et retourner à l'état d'avant" - pascal -
problème soulevé par loic ; thierry se sert des douchettes et de copaco pour faire les inventaires, que faire à ce sujet
suggestion pascal : faire un inventaire physique d'abord, validé par fede pour avoir la vue stock + réservations plutôt que juste compter ce que j'ai sous les yeux
franck pas emballé de laisser 10% de copaco juste pour tirer profit d'une ou deux fonctionnalités -> inventaire annuel par exemple. 
Le problème est que pascal voudrait utiliser copaco pour l'inventaire tournant pour garder les stocks à jour (relativement). 
Or, ce module n'existe pas encore dans copaco, ou n'est pas fiable/utilisable en l'état
Check sur une douchette si on a accès à la version de clipper mobile (pas payée selon pascal - mais possiblement incluse dans le bundle de service de maintenance)
fede : essayer de migrer clipper sur des SSD depuis les HDD actuellement en usage
franck veut temporiser et tester avant de le faire ↑
discussions avec le commercial pour un environnement de test ; le commercial a répondu qu'il faut une licence pour déployer un nouveau serveur. Quel est l'état des licences ?
Vœu pieu de franck de tisser plus de liens avec Clipper quitte à travailler avec.
Franck va prendre rdv avec le support clipper pour poser les questions à poser avec fede, denis et loic ; utilise-t-on bien clipper comme il a été prévu, comment peut-on se faire accompagner, trouver les bonnes questions à poser.
fede propose de developper un copaco v2 pour "juste" faire module après module (détaché) et déployer au compte goutte
fede propose donc de faire développer de nouvelles interfaces pour clipper par le pôle dev
consensus général sur le fait qu'il faut échanger plus avec clipper. Franck suggère de d'abord faire part de la dissatisfaction que l'on a au sein de l'entreprise de l'utilisation  qu'on en a avant de demander à payer des formations pour attendrir les futures négociations
Denis est 100% satisfait de clipper dans le spectre de prod uniquement. Pour la logistique, pas trop, "pour la logistique il y a tout à faire". Planning de prod pas ouf non plus
demander à clipper si on a la possibilité de dialoguer en mqtt - ou autre - pour récupérer les données & developper notre propre verrue pour afficher les données voulues dans des fenêtres dédiées
clipper est sur nos serveurs, pas en cloud
tester impérativement le module copaco d'inventaire de fin d'année
réinventorier 120 reférences hornbach pour repasser sur clipper et lâcher copaco
actuellement, la version mobile de clipper sur les douchettes permet de scanner un code barre pour afficher la qté actuelle. si on ajoute en entrée une qté, elle va écraser la valeur précédente, ce qui va fausser la qté totale si on ajoute en entrée les qtés d'une même référence à x endroits différents.
```
- [ ] ESL-89 Reprendre et terminer la branche 83 d'Anthony
    - [ ] Fix l'affichage du contenu dans la page