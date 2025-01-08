**06 Janvier**
- [ ] ESL
    - [ ] Assessment du projet à la reprise
        - [x] Check des containers
            - [x] Le back a crash, trouver pourquoi et depuis quand
            - [x] utilisation des logs avec le flag -t pour les timestamps
            ```
            sudo docker logs esl_api -t
            ```
            - [x] Origine de l'erreur : 
            ```
            2024-12-27T23:08:20.223116047Z Error sending image to ESL: 
            Error communicating with ESL service(updateOnEsl())
            2024-12-27T23:08:20.229677372Z
            2024-12-27T23:08:20.229697489Z /app/src/esl/esl.service.ts:200
            ```
            - [x] L'erreur et le crash surviennent à cause d'une erreur dans le code de jacques ; un console log renvoie un type incorrect et fait échouer le trycatch l. 200-292 de l'esl service
    - [ ] ESL-237 Réparer la génération du code barre depuis $barcode$ dans le template
        - [x] Refonte de la fonction de génération de code barre : 
        ```
        async function generateBarcode2(barcodeValue: string): Promise<string> {
        console.log('barcodeValue:', barcodeValue);

        try {
            const barcodeCanvas = createCanvas(100, 20);
            const ctx = barcodeCanvas.getContext('2d');

            JsBarcode(barcodeCanvas, barcodeValue, {
            format: 'CODE128',
            width: 2.5,
            height: barcodeCanvas.height * 0.6,
            displayValue: false
            });
            
            const barcodeImage = barcodeCanvas.toDataURL();
            const barcode = await loadImage(barcodeImage);

            ctx.drawImage(barcode, 0, 0,100, 20);
            const finalImage = barcodeCanvas.toDataURL('image/png');
            const image64 = finalImage.split(',')[1];
            console.log('generated barcode base64 string : ', image64);

            return image64;
            
        } catch (error) {
            console.error('Error generating barcode:', error);
            throw new Error('Failed to generate barcode');
        }

        
        }
        ```
        - [ ] Refonte de l'intégration dans eslTemplateManagement
        - [x] Mise en pause, commit sur branche 241 : 
        ```
        "refactor(back): Redo the barcode generation function of store article service"
        ```
    - [ ] ESL-211 Graphique jour par jour du nombre d’ESLs en ligne
        - [x] Heure par heure
            - [x] Front
                - [x] HTML ajout de : 
                ```
                <div class="chart-card">
                    <h3 class="chart-title">Activité des ESLs par heure</h3>
                    <canvas id="eslHourlyActivityChart"></canvas>
                </div>
                ```
                - [x] Component ajout de :
                ```
                activeEslsByHour: number[] = [];

                  fetchActiveEslsByHour() {
                        const requests = [];
                        for (let i = 0; i < 24; i++) {
                        requests.push(this.eslService.getEslsActiveByHour(i));
                        }

                        forkJoin(requests).subscribe((results) => {
                        this.activeEslsByHour = results.map(esls => esls.length);
                        this.initializeHourlyActivityChart();
                        });
                    }

                    initializeActivityChart() {
                        new Chart('eslActivityChart', {
                        type: 'line',
                        data: {
                            labels: ['12 AM', '1 AM', '2 AM', '3 AM', '4 AM', '5 AM'],
                            datasets: [
                            {
                                label: 'Active ESLs',
                                data: [5, 10, 15, 20, 25, 30],
                                borderColor: 'rgba(75, 192, 192, 1)',
                                backgroundColor: 'rgba(75, 192, 192, 0.2)',
                            },
                            ],
                        },
                        options: {
                            responsive: true,
                            plugins: {
                            legend: { display: true },
                            },
                        },
                        });
                    }

                    initializeAssociationChart() {
                        new Chart('eslAssociationChart', {
                        type: 'pie',
                        data: {
                            labels: ['Associated', 'Unassociated'],
                            datasets: [
                            {
                                data: [this.associatedEsls, this.unassociatedEsls],
                                backgroundColor: ['#4CAF50', '#F44336'],
                            },
                            ],
                        },
                        options: {
                            responsive: true,
                            plugins: {
                            legend: { display: true },
                            },
                        },
                        });
                    }

                    initializeHourlyActivityChart() {
                        new Chart('eslHourlyActivityChart', {
                        type: 'line',
                        data: {
                            labels: Array.from({ length: 24 }, (_, i) => `${i}:00`),
                            datasets: [
                            {
                                label: 'Active ESLs',
                                data: this.activeEslsByHour,
                                borderColor: 'rgba(75, 192, 192, 1)',
                                backgroundColor: 'rgba(75, 192, 192, 0.2)',
                            },
                            ],
                        },
                        options: {
                            responsive: true,
                            plugins: {
                            legend: { display: true },
                            },
                        },
                    });
                }
                ```
                - [x] Service : 
                ```
                getEslsActiveByHour(hour: number): Observable<any[]> {
                    return this.httpClient.get<any[]>(`${this.route}/active-by-hour/${hour}`);
                }
                ```
            - [x] Back 
                - [x] Service : 
                ```
                async getEslsActiveByHour(hour: number): Promise<Esl[]> {
                    const startDate = new Date();
                    startDate.setHours(hour, 0, 0, 0);
                    const endDate = new Date();
                    endDate.setHours(hour + 1, 0, 0, 0);

                    return this.eslRepository
                    .createQueryBuilder('esl')
                    .where('esl.timestamp >= :startDate AND esl.timestamp < :endDate AND esl.store_article IS NOT NULL', { startDate, endDate })
                    .getMany();
                }
                ```
                - [x] controller : 
                ```
                @Get('active-by-hour/:hour')
                getEslsActiveByHour(@Param('hour') hour: number) {
                return this.eslService.getEslsActiveByHour(hour);
                }
                ```
            - [x] ```
        docker compose -f docker-compose.api.yml up --build  
        ```
        - [ ] Nombre par jour


**08 Janvier**
- [ ] ESL
    - [ ] ESL-211 Graphique jour par jour du nombre d’ESLs en ligne
        - [x] Back
            - [x] Création de l'entité ActiveESLsHourly
            ```
            import { Entity, Column, PrimaryGeneratedColumn } from 'typeorm';

            @Entity()
            export class ActiveESLsHourly {
            @PrimaryGeneratedColumn('uuid')
            id: string;

            @Column({ type: 'timestamp', default: () => 'CURRENT_TIMESTAMP' })
            datetime: Date;

            @Column()
            activeEslsCount: number;
            }

            ```
            - [x] Création du service ActiveESLsHourly
            ```
            import { Injectable, HttpException } from '@nestjs/common';
            import { InjectRepository } from '@nestjs/typeorm';
            import { MoreThanOrEqual, Repository } from 'typeorm';
            import { ActiveESLsHourly } from './entities/active-esls-hourly.entity';
            import { EslService } from '../esl/esl.service';
            import { Cron, CronExpression } from '@nestjs/schedule';

            @Injectable()
            export class ActiveESLsHourlyService {
            constructor(
                @InjectRepository(ActiveESLsHourly)
                private activeEslsHourlyRepository: Repository<ActiveESLsHourly>,
                private eslService: EslService,
            ) {}

            async create(activeEslsHourly: ActiveESLsHourly) {
                try {
                return await this.activeEslsHourlyRepository.save(activeEslsHourly);
                } catch (e) {
                throw new HttpException(e, 500);
                }
            }

            async findAll(): Promise<ActiveESLsHourly[]> {
                try {
                return await this.activeEslsHourlyRepository.find();
                } catch (e) {
                throw new HttpException(
                    'Failed to fetch ActiveESLsHourly (ActiveESLsHourlyService/findAll())',
                    500,
                );
                }
            }

            @Cron(CronExpression.EVERY_HOUR)
            async fetchAndStoreActiveEsls() {
                try {
                const activeEsls = await this.eslService.getEslactiveLastHour();
                const activeEslsHourly = new ActiveESLsHourly();
                activeEslsHourly.activeEslsCount = activeEsls.length;
                await this.create(activeEslsHourly);
                } catch (e) {
                console.error('Error fetching and storing active ESLs:', e);
                }
            }

            async getActiveEslsLastFiveDays(): Promise<ActiveESLsHourly[]> {
                try {
                const fiveDaysAgo = new Date();
                fiveDaysAgo.setDate(fiveDaysAgo.getDate() - 5);
                return await this.activeEslsHourlyRepository.find({
                    where: { datetime: MoreThanOrEqual(fiveDaysAgo) },
                    order: { datetime: 'ASC' },
                });
                } catch (e) {
                throw new HttpException(
                    'Failed to fetch active ESLs for the last five days',
                    500,
                );
                }
            }
            }
            ```
            - [x] Création du controller ActiveESLsHourly
            ```
            import { Controller, Get, Post, Body } from '@nestjs/common';
            import { ActiveESLsHourlyService } from './active-esls-hourly.service';
            import { ActiveESLsHourly } from './entities/active-esls-hourly.entity';
            import { ApiTags } from '@nestjs/swagger';

            @ApiTags('active-esls-hourly')
            @Controller('active-esls-hourly')
            export class ActiveESLsHourlyController {
            constructor(private readonly activeEslsHourlyService: ActiveESLsHourlyService) {}

            @Post()
            create(@Body() createActiveEslsHourlyDto: ActiveESLsHourly) {
                return this.activeEslsHourlyService.create(createActiveEslsHourlyDto);
            }

            @Get()
            findAll() {
                return this.activeEslsHourlyService.findAll();
            }

            @Get('last-five-days')
            getActiveEslsLastFiveDays() {
                return this.activeEslsHourlyService.getActiveEslsLastFiveDays();
            }
            }
            ```
        - [x] Refonte du front :
            [x] html
            ```
            <div class="grid grid-cols-4 px-40 gap-4">
                <div class="front-card">
                    <p class="title-front-card">Nombre total d'ESLs</p>
                    <p class="front-card-text-green p-4">{{this.numberOfEsl}}</p>
                </div>
                <div class="front-card">
                    <p class="title-front-card">ESLs actives et associées</p>
                    <p>(pour l'heure passée)</p>
                    <p class="front-card-text-green">{{this.numberOfEslActive}} </p>
                </div>
                <div class="front-card">
                    <p class="title-front-card">ESLs non associées</p>
                    <p class="front-card-text-red p-4">{{this.numberOfEslUnassigned}} </p>
                </div>
                <div class="front-card">
                    <p class="title-front-card">Total des Articles</p>
                    <p class="front-card-text-green">{{ this.totalArticles }}</p>
                </div>
                <div class="front-card" (click)="openFailedRequestModal()">
                    <p class="title-front-card">Erreur survenue</p>
                    <p>(pour la journée passée)</p>
                    
                    <p class="front-card-text-red">{{ this.numberOfRequestFailed}}</p>
                </div>
                <div class="front-card" (click)="openLowPowerEslModal()">
                    <p class="title-front-card">ESL en batterie faible</p>
                    <p>(inferieur à 20%</p>
                    <p class="front-card-text-red">{{ this.lowPowerEsls.length}}</p>
                </div>
                </div>

                <div class="charts-section grid grid-cols-1 sm:grid-cols-2 gap-6">
                <div class="chart-card">
                    <h3 class="chart-title">Activité des ESLs sur la durée</h3>
                    <canvas id="eslActivityChart"></canvas>
                </div>
                <div class="chart-card">
                    <h3 class="chart-title">Activité des ESLs par heure</h3>
                    <canvas id="eslHourlyActivityChart"></canvas>
                </div>
                <div class="chart-card">
                    <h3 class="chart-title">Activité des ESLs par jour</h3>
                    <canvas id="eslDailyActivityChart"></canvas>
                </div>
                <div class="chart-card">
                    <h3 class="chart-title">ESLs associées vs non associées</h3>
                    <canvas id="eslAssociationChart" class="fixed-size-chart"></canvas>
                </div>
            </div>
            ```
            - [x] Component : 
            ```
            import {Component, inject, OnInit} from '@angular/core';
            import {EslService} from '../../services/esl.service';
            import { StoreArticleService } from '../../services/store-article.service';
            import Chart from 'chart.js/auto';
            import {RequestLog, RequestLogService} from '../../services/request-log.service';
            import {MatDialog} from '@angular/material/dialog';
            import {RequestFailDetailComponent} from './request-fail-detail/request-fail-detail.component';
            import {LowPowerEslDialogComponent} from './low-power-esl-dialog/low-power-esl-dialog.component';

            @Component({
            selector: 'app-dashboard',
            standalone: true,
            imports: [],
            templateUrl: './dashboard.component.html',
            styleUrls: ['./dashboard.component.scss']
            })
            export class DashboardComponent implements OnInit {
            readonly dialog = inject(MatDialog);
            numberOfEslUnassigned: number = 0;
            numberOfEsl: number = 0;
            numberOfEslActive: number = 0;
            totalArticles = 0;
            associatedEsls = 0;
            unassociatedEsls = 0;
            recentlyUpdatedEsls = 0;
            activeEslsByHour: number[] = [];
            activeEslsByDay: number[] = [];

            numberOfRequestFailed = 0;
            dataRequestFailed :RequestLog[]= [];
            lowPowerEsls :any = []

            constructor(
                private eslService: EslService,
                private storeArticleService: StoreArticleService,
                private requestlogService: RequestLogService
            ) {
            }

            openFailedRequestModal(){
                this.dialog.open(RequestFailDetailComponent , {
                    width: '80%',
                    height: '700px',
                    maxWidth: 'none',
                data: {logs: this.dataRequestFailed}
                    });
                }
                openLowPowerEslModal(){
                    this.dialog.open(LowPowerEslDialogComponent , {
                        width: '80%',
                        height: '700px',
                        maxWidth: 'none',
                        data: {esl: this.lowPowerEsls}
                    });

                }
            ngOnInit() {
                this.eslService.getEslsWithoutStoreArticle().subscribe((esls) => {
                this.numberOfEslUnassigned = esls.length;
                });
                this.eslService.getEsls().subscribe((esls) => {
                this.numberOfEsl = esls.length;
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
                this.numberOfEslUnassigned = this.unassociatedEsls;

                this.eslService.getEsls().subscribe((allEsls) => {
                    this.associatedEsls = allEsls.length - this.unassociatedEsls;
                    this.numberOfEsl = allEsls.length;

                    this.initializeAssociationChart();
                });
                });

                this.eslService.getEslLowPower().subscribe((lowPowerEsls: any) => {
                this.lowPowerEsls = lowPowerEsls;
                });
                this.eslService.getEslsActive().subscribe((recentlyUpdated) => {
                this.recentlyUpdatedEsls = recentlyUpdated.length;
                this.numberOfEslActive = recentlyUpdated.length;
                });

                this.storeArticleService.getArticlesFormatted(1, 10000).subscribe((response: { items: any[], meta: any }) => {
                const articles = response.items;
                this.totalArticles = articles.length;
                });

                this.initializeActivityChart();
                this.fetchActiveEslsByHour();
                this.fetchActiveEslsByDay();
            }

            fetchActiveEslsByHour() {
                this.eslService.getEslsActiveByHour().subscribe((results) => {
                this.activeEslsByHour = results.map(result => result.activeEslsCount);
                this.initializeHourlyActivityChart();
                });
            }

            fetchActiveEslsByDay() {
                this.eslService.getEslsActiveLastFiveDays().subscribe((results) => {
                this.activeEslsByDay = results.map(result => result.activeEslsCount);
                this.initializeDailyActivityChart();
                });
            }

            initializeActivityChart() {
                new Chart('eslActivityChart', {
                type: 'line',
                data: {
                    labels: ['12 AM', '1 AM', '2 AM', '3 AM', '4 AM', '5 AM'],
                    datasets: [
                    {
                        label: 'Active ESLs',
                        data: [5, 10, 15, 20, 25, 30],
                        borderColor: 'rgba(75, 192, 192, 1)',
                        backgroundColor: 'rgba(75, 192, 192, 0.2)',
                    },
                    ],
                },
                options: {
                    responsive: true,
                    plugins: {
                    legend: { display: true },
                    },
                },
                });
            }

            initializeAssociationChart() {
                new Chart('eslAssociationChart', {
                type: 'pie',
                data: {
                    labels: ['Associated', 'Unassociated'],
                    datasets: [
                    {
                        data: [this.associatedEsls, this.unassociatedEsls],
                        backgroundColor: ['#4CAF50', '#F44336'],
                    },
                    ],
                },
                options: {
                    responsive: true,
                    plugins: {
                    legend: { display: true },
                    },
                },
                });
            }

            initializeHourlyActivityChart() {
                new Chart('eslHourlyActivityChart', {
                type: 'line',
                data: {
                    labels: Array.from({ length: 24 }, (_, i) => `${i}:00`),
                    datasets: [
                    {
                        label: 'Active ESLs',
                        data: this.activeEslsByHour,
                        borderColor: 'rgba(75, 192, 192, 1)',
                        backgroundColor: 'rgba(75, 192, 192, 0.2)',
                    },
                    ],
                },
                options: {
                    responsive: true,
                    plugins: {
                    legend: { display: true },
                    },
                },
                });
            }

            initializeDailyActivityChart() {
                new Chart('eslDailyActivityChart', {
                type: 'line',
                data: {
                    labels: Array.from({ length: 5 }, (_, i) => `Day ${i + 1}`),
                    datasets: [
                    {
                        label: 'Active ESLs',
                        data: this.activeEslsByDay,
                        borderColor: 'rgba(75, 192, 192, 1)',
                        backgroundColor: 'rgba(75, 192, 192, 0.2)',
                    },
                    ],
                },
                options: {
                    responsive: true,
                    plugins: {
                    legend: { display: true },
                    },
                },
                });
            }
            }
            ```