- [x] Check mails
- [ ] ESL
    - [ ] Poursuite des tickets
        - [ ] ESL-194 Définir la politique de sauvegarde des données et son automatisation
            - [ ] Tests de sauvegarde et restauration de la base de données mongo
                - [x] Sur le serveur, essai de la commande de sauvegarde : 
                ```
                sudo docker exec -t esl-mongodb mongodump --out /
                ```
                Retour d'erreur : 
                ```
                Failed: error creating intents to dump: error getting database names: (Unauthorized) command listDatabases requires authentication
                ```
                Ajout des ids de connexion à la mongodb :
                ```
                sudo docker exec -t esl-mongodb mongodump --out /backup --username esl --password Esl22102024 --authenticationDatabase admin
                ```
                Retour succès : 
                ```
                2025-02-26T09:06:41.036+0000    writing admin.system.users to /admin/system.users.bson
                2025-02-26T09:06:41.038+0000    done dumping admin.system.users (1 document)
                2025-02-26T09:06:41.038+0000    writing admin.system.version to /admin/system.version.bson
                2025-02-26T09:06:41.039+0000    done dumping admin.system.version (2 documents)
                2025-02-26T09:06:41.040+0000    writing esl.templates to /esl/templates.bson
                2025-02-26T09:06:41.042+0000    done dumping esl.templates (11 documents)
                ```
                Les fichiers de sauvegarde sont faits dans le container. Commande pour les décaler su rle linux : 
                ```
                sudo docker cp esl-mongodb:/admin /backup
                ```
                - [x] Essai différemment ; avec mongodump :
                ```
                sudo docker exec -t esl-mongodb mongodump --out /dump --username esl --password Esl22102024 --authenticationDatabase admin
                ```
                &
                ```
                sudo docker cp esl-mongodb:/dump /path/to/backup
                ```
                - [ ] Résolution du problème de copie des fichiers depuis le container vers la machine host linux
        - [ ] ESL-264  Ajouter un retour snackbar au clic de faire clignoter une ESL
            - [x] esl-table component, modification de la fonction blinkESL : 
            ```
            blinkEsl(id_esl: string, color: string) {
            this.eslService.blinkEsl(id_esl, color).subscribe({
                next: async () => {
                console.log(`ESL with ID ${id_esl} is blinking with color ${color}`);
                this.snackBar.open(`ESL with ID ${id_esl} is blinking with color ${color}`, 'Close', {
                    duration: 2000,
                });
                },
                error: async (error) => {
                console.error(error);
                this.snackBar.open('Failed to blink ESL', 'Close', {
                    duration: 2000,
                });
                },
            });
            }
            ```
            - [x] EslListDialogComponent, modification de la fonction blinkESL : 
            ```
            blinkEsl(id_esl: string, color: string) {
                this.eslService.blinkEsl(id_esl, color).subscribe({
                next: async () => {
                    console.log(`ESL with ID ${id_esl} is blinking with color ${color}`);
                    this.snackBar.open(`ESL with ID ${id_esl} is blinking with color ${color}`, 'Close', {
                    duration: 2000,
                    });
                },
                error: async (error) => {
                    console.error(error);
                },
                });
            }
            ```
            - [x] UnassignedEslsModalComponent, modification de la fonction blinkESL : 
            ```
            blinkEsl(id_esl: string, color: string) {
                this.eslService.blinkEsl(id_esl, color).subscribe({
                next: async () => {
                    console.log(`ESL with ID ${id_esl} is blinking with color ${color}`);
                    this.snackBar.open(`ESL with ID ${id_esl} is blinking with color ${color}`, 'Close', {
                    duration: 2000,
                    });
                },
                error: async (error) => {
                    console.error(error);
                },
                });
            }
            ```
            - [x] Run du linting et tests
        - [ ] ESL-265 Virer le bouton Editer de la table des store articles
            - [x] Retrait des boutons dans le template
        - [ ] ESL-261 Centrer la barre d'actions d'objet dans l'interface de création de template
            - [x] Modifications du create new template component :
            ```
            ...
            private positionToolbar(selected: any): void {
                if (this.toolbar && selected) {
                const toolbarEl = this.toolbar.nativeElement;
                const canvasRect = this.canvas.getElement().getBoundingClientRect(); // Position absolue du canvas

                // Positionner la barre d'outils au-dessus de la bounding box en tenant compte de l'offset du canvas
                toolbarEl.style.display = 'block';
                toolbarEl.style.position = 'absolute';
                toolbarEl.style.left = `${
                    canvasRect.left + selected.left - toolbarEl.offsetWidth
                }px`; // Ajustement de -5 pour une petite marge à gauche
                toolbarEl.style.top = `${
                    canvasRect.top + selected.top - toolbarEl.offsetHeight - 80
                }px`; // Décalage supplémentaire en hauteur
                }
            }
            ```
            - [x] Modifications du edit template component :
            ```
            ...
            private positionToolbar(selected: any): void {
                if (this.toolbar && selected) {
                const toolbarEl = this.toolbar.nativeElement;
                const canvasRect = this.canvas.getElement().getBoundingClientRect(); // Position absolue du canvas

                // Positionner la barre d'outils au-dessus de la bounding box en tenant compte de l'offset du canvas
                toolbarEl.style.display = 'block';
                toolbarEl.style.position = 'absolute';
                toolbarEl.style.left = `${
                    canvasRect.left + selected.left - toolbarEl.offsetWidth
                }px`; // Ajustement de -5 pour une petite marge à gauche
                toolbarEl.style.top = `${
                    canvasRect.top + selected.top - toolbarEl.offsetHeight - 80
                }px`; // Décalage supplémentaire en hauteur
                }
            }
            ```
        - [ ] ESL-270 Enlever les décimales des labels de charts dans le dashboard
            - [x] Modifications du dashboard component, ajout des options tick & scale aux charts concernées : 
            ```
            ...
            
                options: {
                    responsive: true,
                    plugins: {
                    legend: { display: true },
                    },
                    scales: {
                    y: {
                        beginAtZero: true,
                        ticks: {
                        stepSize: 1,
                        callback: function(value) {
                            return Number.isInteger(value) ? value : null;
                        }
                        }
                    }
                    }
                },
                });
            }
            ```
        - [ ] ESL-266 Faire un bouton de suppression dans la table des store articles
            - [x] Modif du store articles module pour éviter les R3 injection error : 
            ```
            import {forwardRef, Module} from '@nestjs/common';
            import { StoreArticlesService } from './store-articles.service';
            import { StoreArticlesController } from './store-articles.controller';
            import {TypeOrmModule} from '@nestjs/typeorm';
            import {StoreArticle} from './entities/store-article.entity';
            import {Esl} from '../esl/entities/esl.entity';
            import {HttpModule} from '@nestjs/axios';
            import {EslTag} from '../esl-tag/entities/esl-tag.entity';
            import { TemplateModule } from '../Templates/template.module';
            import { StyleModule } from '../style/style.module';
            import { RequestLogModule } from '../request-log/request-log.module';
            import { RequestLog } from 'src/request-log/entities/request-log.entity';

            @Module({
            imports: [
                HttpModule,
                TypeOrmModule.forFeature([StoreArticle,Esl,EslTag,RequestLog]),
                TemplateModule,
                forwardRef(() => RequestLogModule), // Ajoutez forwardRef ici
                forwardRef(() => StyleModule), // Remplacez StyleService par StyleModule

            ],
            controllers: [StoreArticlesController],
            providers: [StoreArticlesService], // Fournir uniquement les services nécessaires
            exports: [StoreArticlesService], // Exportez le service si d'autres modules en ont besoin
            })
            export class StoreArticlesModule {}

            ```
            - [x] Modif du store articles service pour éviter les R3 injection error : 
            ```
            @Injectable()
            export class StoreArticlesService {
            constructor(
                private readonly httpService: HttpService,
                @InjectRepository(StoreArticle) private storeArticleRepository: Repository<StoreArticle>,
                @InjectRepository(Esl) private Esl: Repository<Esl>,
                private templateService: TemplateService,
                @InjectRepository(EslTag) private EslTag: Repository<EslTag>,
                private requestLogService: RequestLogService,
                @InjectRepository(RequestLog) private requestLogRepository: Repository<RequestLog>
            ) {}
            base64Ref:string ="data:image/png;base64,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";

            async updateArticlesFromCSV(csvData: CreateStoreArticleDto[]): Promise<StoreArticle[]> {
                const updatedArticles: StoreArticle[] = [];
                try {
                for (const row of csvData) {
                    // eslint-disable-next-line @typescript-eslint/naming-convention
                    const { bar_code: barCode, name, price, origine, price_per_kilo } = row;

                    if (!barCode || !name || price === undefined) {
                    console.error(row);
                    continue;
                    }

                    const article = await this.storeArticleRepository.findOne({ where: { bar_code: barCode } });

                    if (article) {
                    article.name = name;
                    article.price = price;
                    article.origine = origine;
                    article.price_per_kilo = price_per_kilo;
                    await this.storeArticleRepository.save(article);
                    updatedArticles.push(article);
                    } else {
                    const newArticle = this.storeArticleRepository.create({
                        bar_code: barCode,
                        name,
                        price,
                        origine,
                        price_per_kilo,
                    });

                    await this.storeArticleRepository.save(newArticle);
                    updatedArticles.push(newArticle);
                    }
                }
                } catch (error) {
                console.error(error.message);
                throw new HttpException('Failed to process CSV data', 500);
                }

                return updatedArticles;
            }


            async remove(id: number) {
                try {
                const article = await this.storeArticleRepository.findOne({ where: { id }, relations: ['esl'] });
                console.log("passage dans remove avec article : " + article);
            
                if (!article) {
                    throw new HttpException('Article not found', 404);
                }

                await this.requestLogRepository.delete({ storeArticle: { id } });
            
                if (article.esl) {
                    article.esl.store_article = null;
                    await this.Esl.save(article.esl);
                }
            
                await this.storeArticleRepository.remove(article);
                return article;
                } catch (e) {
                throw new HttpException('Failed to remove store article', 500);
                }
            }
            ```
