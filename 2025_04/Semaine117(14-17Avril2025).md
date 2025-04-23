**14 Avril**
- [x] Onboarding de paul
    - [x] Visite de l'entreprise
    - [x] Présentation des projets
    - [ ] Mise en route app bureau co avec Kévin
- [ ] ESL
    - [ ] ESL-287 Ajouter un bouton aperçu pour pop-up une prévisualisation de l'ESL dans la ESL table
        - [ ] Refonte de la fonction du service template back
            problématique : 
            ```
            The thing is the esl_id property never gets filled so this function does not work. On the other hand, i know there can only be ONE template per STYLE for a given set of dimensions (width & height). So my idea is to :
            - use eslId to get the associated style if there is one
            - use eslId to retrieve the ESL's width & height
            - the search for a template that matches both style (via styleName property) and the width&height
            - then get that template
            ```
            - [x] Récupération de l'esl :
            ```
            const esl = await this.eslService.findEslByIdEsl(eslId);
            if (!esl) {
            throw new HttpException(`ESL with ID ${eslId} not found`, 404);
            }   
            ```
            - [x] Récupération du style associé :
            ```
            let styleName = null;
                if (esl.style) {
                const style = await this.styleService.get({ id: esl.style.id });
                if (style) {
                    styleName = style.name;
                }
            }
            ```
            - [x] Récupérer le template :
            ```
            const { width, height } = esl;

            const template = await this.templateModel.findOne({
            styleName,
            width,
            height,
            }).exec();

            if (!template) {
            throw new HttpException(`Template not found for ESL ID ${eslId}`, 404);
            }

            return template;
            ```
        - [x] Rebuild du docker et tests
            - [x] Résolution de la R2NullInjection error : 
            ```
            Error: Nest can't resolve dependencies of the StoreArticlesService (HttpService, StoreArticleRepository, EslRepository, ?, EslTagRepository, RequestLogService, RequestLogRepository). Please make sure that the argument dependency at index [3] is available in the StoreArticlesModule context.
            ```
            mettre des ```forwardRef(() => ...Module)``` dans les imports des modules pour éviter les dépendances circulaires.
            Réponse détaillée : 
            You're injecting EslService and StyleService into TemplateService, and both EslModule and StyleModule import TemplateModule, so you now have a loop
            fix : ajouter des forwardref dans le constructor du service : 
            ```
            @Injectable()
            export class TemplateService {
            constructor(
                @InjectModel(Template.name) private templateModel: Model<Template>,

                @Inject(forwardRef(() => EslService))
                private readonly eslService: EslService,

                @Inject(forwardRef(() => StyleService))
                private readonly styleService: StyleService
            ) {}
            ```
        OK on récupère bien le lien vers le template (http://localhost:3000/Templates/canvas/12 ) ce qui créée une 500 vu que la route n'existe pas.
        - [x] Modif de la fonction 
        ```
        async getTemplateByEslId(eslId: string): Promise<Template> {
            console.log("getTemplateByEslId");
            
            const esl = await this.eslService.findEslByIdEsl(eslId);
            console.log("esl : " + esl);
            
            if (!esl) {
            throw new HttpException(`ESL with ID ${eslId} not found`, 404);
            }
            
            let styleName = null;
            console.log("styleName : " + styleName);
            if (esl.style) {
            const style = await this.styleService.get({ id: esl.style.id });
            if (style) {
                styleName = style.name;
            }
            }

            const { width, height } = esl;

            const template = await this.templateModel.findOne({
            styleName,
            width,
            height,
            }).exec();
            console.log(template);
            

            if (!template) {
            throw new HttpException(`Template not found for ESL ID ${eslId}`, 404);
            }

            return template;
        }
        ```
        le back retourne : 
        ```
        getTemplateByEslId
        esl : [object Object]
        styleName : null
        null
        ```
        d'où le null sur le template.
        - [x] Ajout dans l'esl service de la relation style dans la function findEslByIdEsl : 
        ```
        findEslByIdEsl(id_esl: string): Promise<Esl> {
            return this.eslRepository.findOne(
            {where: {id_esl},
            relations: ['style']}
            );
        }
        ```
    - [ ] ESL-291 Au clic d'un bouton, faire clignoter toutes les ESLs en batterie faible pendant 5 minutes
        - [x] backend
            - [x] création de la function dans le service : 
            ```
            async blinkLowBatteryEsls() {
                try {
                const lowBatteryEsls = await this.eslRepository.find({
                    where: { power: LessThan(25) },
                });
            
                const color = '#F0738C';
                const blinkPromises = lowBatteryEsls.map(esl => {
                    const route = `${this.apiEsl}/esl_blink`;
                    return this.httpService.post(route, { id_esl: esl.id_esl, color })
                    .toPromise()
                    .catch(error => {
                        console.error(`Failed to blink ESL with id_esl: ${esl.id_esl}`, error);
                        throw new HttpException(`Failed to blink ESL with id_esl: ${esl.id_esl}`, HttpStatus.INTERNAL_SERVER_ERROR);
                    });
                });
            
                await Promise.all(blinkPromises);
                } catch (error) {
                console.error('Error in blinkLowBatteryEsls function:', error);
                throw new HttpException('Failed to blink low battery ESLs', HttpStatus.INTERNAL_SERVER_ERROR);
                }
            }
            ```
            - [x] création de la route dans le controller : 
            ```
            @Get('/blink-low-battery')
            async blinkLowBatteryEsls() {
                return this.eslService.blinkLowBatteryEsls();
            }
            ```
            - [x] création des tests : 
            ```
            describe('findEslByIdEsl', () => {
                it('should return an ESL by id_esl', async () => {
                const idEsl = 'ID1';
                const esl = { id_esl: idEsl } as Esl;
            
                jest.spyOn(repository, 'findOne').mockResolvedValue(esl);
            
                expect(await service.findEslByIdEsl(idEsl)).toEqual(esl);
                expect(repository.findOne).toHaveBeenCalledWith({
                    where: { id_esl: idEsl },
                    relations: ['style'], 
                });
                });
            });  
            ```
    - [x] checkout develop
    - [x] pull develop & re run des tests
    - [x] ESL-306 Dashboard: Nombre d’ESLs
        - [x] Modif du component dashboard
        ```
        ngOnInit() {
            this.eslService.getEslsBrokenCount().subscribe((count) => {
            this.numberOfBrokenEsls = count;
            });
            this.eslService.getEslsWithoutStoreArticle().subscribe((esls) => {
            this.numberOfEslUnassigned = esls.length - this.numberOfBrokenEsls;
            this.numberOfEslsWithoutStoreArticle = esls.length;
            });
            this.eslService.getEsls().subscribe((esls) => {
            this.numberOfEsl = esls.length  - this.numberOfBrokenEsls;
            });
            this.eslService.getEslsActive().subscribe((esls) => {
            this.numberOfEslActive = esls.length;
            });
            this.requestlogService.getLast24HoursFail().subscribe((logs) => {
            this.numberOfRequestFailed = logs.length;
            this.dataRequestFailed = logs;
            });

            this.eslService.getEslsWithoutStoreArticle().subscribe((unassociated) => {
            this.unassociatedEsls = unassociated.length;
            this.numberOfEslUnassigned = this.unassociatedEsls - this.numberOfBrokenEsls;;

            this.eslService.getEsls().subscribe((allEsls) => {
                this.associatedEsls = allEsls.length - this.unassociatedEsls;
                this.numberOfEsl = allEsls.length - this.numberOfBrokenEsls;;

                this.initializeAssociationChart();
            });
        });
        ```
    - [x] ESL-331 Reprendre les boutons d'action des lignes de la table storeArticles
        - [x] html
        ```
        <td>
          <button class="bg-blue-500 border-4 border-blue-500 rounded-sm" (click)="editArticle(article)">
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6 text-gray-100">
              <path stroke-linecap="round" stroke-linejoin="round" d="M16.862 3.487a2.25 2.25 0 1 1 3.182 3.182L7.5 19.313 3 21l1.688-4.5L16.862 3.487z" />            </svg>
          </button>
          <button class="bg-blue-500 border-4 border-blue-500 rounded-sm" (click)="duplicateArticle(article)">
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6 text-gray-100">
              <path stroke-linecap="round" stroke-linejoin="round" d="M15.75 17.25v3.375c0 .621-.504 1.125-1.125 1.125h-9.75a1.125 1.125 0 0 1-1.125-1.125V7.875c0-.621.504-1.125 1.125-1.125H6.75a9.06 9.06 0 0 1 1.5.124m7.5 10.376h3.375c.621 0 1.125-.504 1.125-1.125V11.25c0-4.46-3.243-8.161-7.5-8.876a9.06 9.06 0 0 0-1.5-.124H9.375c-.621 0-1.125.504-1.125 1.125v3.5m7.5 10.375H9.375a1.125 1.125 0 0 1-1.125-1.125v-9.25m12 6.625v-1.875a3.375 3.375 0 0 0-3.375-3.375h-1.5a1.125 1.125 0 0 1-1.125-1.125v-1.5a3.375 3.375 0 0 0-3.375-3.375H9.75" />
            </svg>
          </button>
          <button class="ml-1 bg-red-500 border-4 border-red-500 rounded-sm" (click)="deleteArticle(article.id)">
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6 text-gray-100">
              <path stroke-linecap="round" stroke-linejoin="round" d="m14.74 9-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 0 1-2.244 2.077H8.084a2.25 2.25 0 0 1-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 0 0-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 0 1 3.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 0 0-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 0 0-7.5 0" />
            </svg>
          </button>
        </td>
        ```
        - [x] Component : 
        ```
        duplicateArticle(article: any): void {
            this.router.navigate(['/create-store-article'], { state: { article } });
        }
        ```
    - [x] ESL-333 Créer le component de création de store article
        - [x] ng create component create-store-article
        - [x] html
        ```
        <div class="container">
            <h2>Create Store Article</h2>
            <form [formGroup]="createForm" (ngSubmit)="onSubmit()" class="space-y-6">
            <div>
                <label for="bar_code" class="block text-sm font-medium text-gray-700">Code barre</label>
                <input type="text" id="bar_code" formControlName="bar_code" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm">
            </div>
            <div>
                <label for="name" class="block text-sm font-medium text-gray-700">Nom</label>
                <input type="text" id="name" formControlName="name" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm">
            </div>
            <div>
                <label for="price" class="block text-sm font-medium text-gray-700">Prix</label>
                <input type="number" id="price" formControlName="price" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm">
            </div>
            <div>
                <label for="origine" class="block text-sm font-medium text-gray-700">Origine</label>
                <input type="text" id="origine" formControlName="origine" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm">
            </div>
            <div>
                <label for="price_per_kilo" class="block text-sm font-medium text-gray-700">Prix au Kilo</label>
                <input type="number" id="price_per_kilo" formControlName="price_per_kilo" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm">
            </div>
            <div>
                <button type="submit" class="w-full inline-flex justify-center rounded-md border border-transparent shadow-sm px-4 py-2 bg-indigo-600 text-base font-medium text-white hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 sm:text-sm">
                Créer
                </button>
            </div>
            </form>
        </div>
        ```
        - [x] Component
        ```
        import { Component, OnInit } from '@angular/core';
        import { FormBuilder, FormGroup, FormsModule, ReactiveFormsModule, Validators } from '@angular/forms';
        import { StoreArticleService } from '../../services/store-article.service';
        import { Router, ActivatedRoute } from '@angular/router';
        import { CommonModule } from '@angular/common';

        @Component({
        selector: 'app-create-store-article',
        templateUrl: './create-store-article.component.html',
        styleUrls: ['./create-store-article.component.scss'], 
        standalone: true,
        imports: [
            FormsModule,
            CommonModule,
            ReactiveFormsModule
        ]
        })
        export class CreateStoreArticleComponent implements OnInit {
        createForm: FormGroup;

        constructor(
            private fb: FormBuilder,
            private storeArticleService: StoreArticleService,
            private router: Router,
            private route: ActivatedRoute
        ) {
            this.createForm = this.fb.group({
            bar_code: ['', Validators.required],
            name: ['', Validators.required],
            price: ['', Validators.required],
            origine: ['', Validators.required],
            price_per_kilo: ['']
            });
        }

        ngOnInit(): void {
            const articleData = history.state.article;
            if (articleData) {
            this.createForm.patchValue(articleData);
            }
        }

        onSubmit(): void {
            if (this.createForm.valid) {
            this.storeArticleService.createArticle(this.createForm.value).subscribe(
                response => {
                console.log('Article created successfully', response);
                this.router.navigate(['/store-article']);
                },
                error => {
                console.error('Error creating article', error);
                }
            );
            }
        }
        }
        ```
        !!! r Résolution du NG8002: Can't bind to 'formGroup' since it isn't a known property of 'form' en important : 
        ```
        @Component({
        selector: 'app-create-store-article',
        templateUrl: './create-store-article.component.html',
        styleUrls: ['./create-store-article.component.scss'], 
        standalone: true,
        imports: [
            FormsModule,
            CommonModule,
            ReactiveFormsModule
        ]
        })
        ```
        - [x] Modif du service 
        ```
        createArticle(data: any): Observable<any> {
            return this.httpClient.post(`${this.apiUrl}`, data);
        }
        ```
        - [x] app routing
        ```
        { path: 'create-store-article', component:CreateStoreArticleComponent },
        { path: '**', redirectTo: '/dashboard', pathMatch: 'full' },
        ```
        !!! i Importance de mettre le path: '**' sous peine qu'il prenne le pas sur les autres routes