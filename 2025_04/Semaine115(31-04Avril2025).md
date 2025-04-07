**31 Mars**
- [ ] ESL
    - [ ] ESL-307 Afficher les chiffres des unités plus grand que le chiffre des décimales des prix dans les templates
        - [x] prompt 
        ```
        eslTemplateManagement
        ...
        this is my updated function.
        1) is it correct ?
        2) is there a method to isolate the decimals from the rest of the $price$ ?
        ```
        réponse : 
        ```
        for (const obj of template.objects) {
            if (obj.type === 'IText') {
                if (obj.text === '$price$') {
                const priceParts = storeArticle.price.toFixed(2).split('.');
                obj.text = `${priceParts[0]}<sup>${priceParts[1]}</sup>`; // Isolate decimals
                console.log('Price updated in object:', obj.text);
                priceFieldFound = true;
        ```
    - [ ] ESL-314-315 Ajouter les tags d'unité de mesure & de prix avant promo
        - [x] entité store article : 
        ```
        import {Column, Entity, JoinColumn, OneToOne, PrimaryGeneratedColumn, UpdateDateColumn} from 'typeorm';
        import {Esl} from '../../esl/entities/esl.entity';

        @Entity()
        export class StoreArticle {
            @PrimaryGeneratedColumn()
            id:number

            @Column()
            name:string

            @Column({nullable:true, type:'float'})
            price:number

            @Column({nullable:true, type:'float'})
            preDiscountPrice:number

            @Column()
            bar_code:string

            @Column({nullable:true})
            origine: string

            @Column({nullable:true, type:'float'})
            price_per_kilo : number

            @UpdateDateColumn({ type: 'timestamp' })
            updatedAt: Date;

            @Column({ nullable: true, type: 'varchar', length: 10 })
            currency: string; //Varchar 10 to support € $ signs

            @Column({nullable:true})
            measurementUnit: string

            @OneToOne(()=> Esl,(esl)=>esl.store_article,{nullable:true})
            @JoinColumn()
            esl:Esl

        }
        ```
        - [x] store article service backend : 
        ```
        eslTemplateManagement
        ...
        let priceFieldFound = false;
        let currencyFieldFound = false;
        let measurementUnitFieldFound = false;
        // const barcodeFieldFound = false;

            const fabricCanvas = new StaticCanvas(null);
            fabricCanvas.setDimensions({ width: template.width, height: template.height });
            fabricCanvas.backgroundColor = '#FFFFFF';

            for (const obj of template.objects) {
                if (obj.type === 'IText') {
                if (obj.text === '$price$') {
                    obj.text = storeArticle.price.toFixed(2).toString();
                    console.log('Price updated in object:', obj.text);
                    priceFieldFound = true;
                } else if (obj.text === '$currency$') {
                    obj.text = storeArticle.currency.toString();
                    console.log('Currency updated in object:', obj.text);
                } else if (obj.text === '$preDiscountPrice$') {
                    obj.text = storeArticle.preDiscountPrice.toFixed(2); // Ensure two decimal places
                    console.log('Pre-discount price updated in object:', obj.text);
                } else if (obj.text === '$measurementUnit$') {
                    obj.text = storeArticle.measurementUnit.toString();
                    console.log('Measurement unit updated in object:', obj.text);
                } else if (obj.text === '$origin$') {
                    obj.text = storeArticle.origine.toString();
                    console.log('Origin updated in object:', obj.text);
                } else if (obj.text === '$name$') {
                    obj.text = storeArticle.name.toString();
                    console.log('Name updated in object:', obj.text);
                } else if (obj.text === '$pkg$') {
                    obj.text = storeArticle.price_per_kilo.toString();
                    console.log('Price per kilo updated in object:', obj.text);
                } 
                } 
                // console.log("hash de référence : ↓");
                
                // console.log(this.generateHash(this.base64Ref));
                
                if (obj.type === 'Image' && this.isImageSame(obj.src,this.generateHash(this.base64Ref))) {
                try {
                    const barcodeImageBase64 = await generateBarcode2(storeArticle.bar_code,esl.width,esl.height);

                    obj.src = barcodeImageBase64;
                    obj.scaleX =1;
                    obj.scaleY =1;
                    
                    // barcodeFieldFound = true;
                } catch (err) {
                    console.error('Error adding barcode image to canvas:', err.message);
                }
                }
            }

            if (!priceFieldFound) {
                console.warn(`No "$price$" field found in template for ESL ${esl.id_esl}`);
                requestLogData.code = '404';
                requestLogData.success = false;
                requestLogData.storeArticleId = storeArticle.id;
                requestLogData.eslId = esl.id_esl;
                requestLogData.message = `No "$price$" field found in template for ESL ${esl.id_esl}`;
                await this.requestLogService.create(requestLogData);
                return results;
            }

            if (!currencyFieldFound) {
                console.warn(`No "$currency$" field found in template for ESL ${esl.id_esl}`);
                requestLogData.code = '404';
                requestLogData.success = false;
                requestLogData.storeArticleId = storeArticle.id;
                requestLogData.eslId = esl.id_esl;
                requestLogData.message = `No "$currency$" field found in template for ESL ${esl.id_esl}`;
                await this.requestLogService.create(requestLogData);
                return results;
            }

            if (!measurementUnitFieldFound) {
                console.warn(`No "$measurementUnit$" field found in template for ESL ${esl.id_esl}`);
                requestLogData.code = '404';
                requestLogData.success = false;
                requestLogData.storeArticleId = storeArticle.id;
                requestLogData.eslId = esl.id_esl;
                requestLogData.message = `No "$measurementUnit$" field found in template for ESL ${esl.id_esl}`;
                await this.requestLogService.create(requestLogData);
                return results;
            }

            // Génération et envoi de l'image
            await this.generateAndSendImageToEsl(esl.id_esl, template, storeArticle.bar_code);

            } catch (error) {
            console.error('Error in eslTemplateManagement:', error.message);
            requestLogData.message = error.message || 'Failed to process ESL template management';
            requestLogData.code = '500';
            requestLogData.success = false;
            requestLogData.storeArticleId = storeArticle.id;
            await this.requestLogService.create(requestLogData);

            }

            // Création du log
            try {
            await this.requestLogService.create(requestLogData);
            } catc  h (logError) {
            console.error('Failed to log request:', logError);
            }

            return results;
        }
        ```
    - [ ] ESL-312 Reformatage de la date de dernière mise à jour dans la liste des produits. Sous forme "date"
        - [x] import de commonModule dans le component
        - [x] modif du html : 
        ```
        <td class="px-4 py-4 text-sm text-gray-500 whitespace-nowrap">{{ article.updatedAt | date: 'dd-MM-yyyy'}}</td>
        ```
        - [x] Ajout dans le mock des test files :
        ```
        preDiscountPrice: 120,
        measurementUnit: "L",
        ```
    - [ ] ESL-314-315 Ajout de measurementUnit & preDiscountPrice
        - [x] Update de l'entity
        ```
        @Column({nullable:true, type:'float'})
        preDiscountPrice:number

        @Column({nullable:true})
        measurementUnit: string
        ```
        - [x] Modif du DTO create backend : 
        ```
        @ApiProperty()
        measurementUnit: string;
    
        @ApiProperty()
        preDiscountPrice: number;
        ```
        - [x] Modif du DTO update backend : 
        ```
        @ApiProperty()
        measurementUnit: string;
    
        @ApiProperty()
        preDiscountPrice: number;
        ```
        - [x] Modif du service backend : 
        ```
        async processCsvData(csvData: any[]): Promise<any> {
            const results = [];
            const requestLogData: CreateRequestLogDto = {
            eslId: '',
            storeArticleId: null,
            success: false,
            code: '',
            message: '',
            };

            for (const row of csvData) {
            const { bar_code: barCode, price, name, measurementUnit, preDiscountPrice } = row;
        

            if (!barCode || !price || !name || price === undefined) {
                console.warn(`Missing bar_code, price or other informations in row:`, row);
                continue;
            }

            try {
                let storeArticle = await this.findOneByBarcode(barCode);

                if (!storeArticle) {
                storeArticle = await this.create({
                    bar_code: barCode,
                    name: row.name,
                    price: row.price,
                    origine: row.origine,
                    price_per_kilo: row.price_per_kilo,
                    currency: row.currency || '€',
                    measurementUnit: measurementUnit || '',
                    preDiscountPrice: preDiscountPrice || 0,
                });
                } else {
                if (
                    storeArticle.price !== row.price ||
                    storeArticle.name !== row.name ||
                    storeArticle.origine !== row.origine ||
                    storeArticle.price_per_kilo !== row.price_per_kilo||
                    storeArticle.currency !== row.currency||
                    storeArticle.measurementUnit !== measurementUnit ||
                    storeArticle.preDiscountPrice !== preDiscountPrice
                ) {
                    storeArticle.price = row.price;
                    storeArticle.name = row.name;
                    storeArticle.origine = row.origine;
                    storeArticle.price_per_kilo = row.price_per_kilo;
                    storeArticle.currency = row.currency || '€';
                    storeArticle.measurementUnit = measurementUnit || '';
                    storeArticle.preDiscountPrice = preDiscountPrice || 0;
                    await this.storeArticleRepository.save(storeArticle);
                    if (storeArticle.esl) {
                    await this.eslTemplateManagement(storeArticle);
                    } else {
                    requestLogData.storeArticleId = storeArticle.id;
                    requestLogData.message = 'No ESL associated with store article';
                    requestLogData.code = '500';
                    await this.requestLogService.create(requestLogData);
                    }
                }
                }
            } catch (error) {
                console.error(error);
                results.push({ barCode, status: 'Error', error: error.message });
            }
            }

            return results;
        }
        ```
        - [x] Modif des test files to fit entity
        - [x] lint
        - [x] Commit & push
    - [ ] ESL-286 Changer les labels du tableau ESL table pour avoir des metrics utiles et virer le technique
        - [x] Modif du html : 
        ```
        <thead>
            <tr>
                <th
                scope="col"
                class="px-4 py-3.5 text-sm font-normal text-left text-gray-200"
                >
                ID ESL
                </th>
                <th
                scope="col"
                class="px-4 py-3.5 text-sm font-normal text-left text-gray-200"
                >
                Style associé
                </th>
                <th
                scope="col"
                class="px-4 py-3.5 text-sm font-normal text-left text-gray-200"
                >
                Hauteur
                </th>
                <th
                scope="col"
                class="px-4 py-3.5 text-sm font-normal text-left text-gray-200"
                >
                Largeur
                </th>
                <th
                scope="col"
                class="px-4 py-3.5 text-sm font-normal text-left text-gray-200"
                >
                Connexion
                </th>
                <th
                scope="col"
                class="px-4 py-3.5 text-sm font-normal text-left text-gray-200"
                >
                Niveau de batterie
                </th>
                <th
                scope="col"
                class="px-4 py-3.5 text-sm font-normal text-left text-gray-200"
                >
                Opération
                </th>
            </tr>
        ```
    - [ ] ESL-276 Ajouter un bouton "ajouter champ ..." par champ optionnel lors de la création de template
        - [ ] Ajout du drop down avec choix multiples de champs optionnels non modifiables à ajouter si ceux-ci ne sont pas déja présents.
            - [x] Modif du html : 
            ```
            <div class="mt-2">
                <p class="text-2xl">Champs optionnels</p>
                <div class="ml-2">
                    <label for="optionalFieldsSelect">Champs optionnels :</label>
                    <select id="optionalFieldsSelect" (change)="addOptionalField($event)">
                    <option *ngFor="let field of optionalFields" [value]="field" [disabled]="isFieldUsed(field)">
                        {{ field }}
                    </option>
                    </select>
                </div>
            </div>
            ```
            - [x] Modif du component : 
            ```
            public optionalFields = [
                '$origin$',
                '$preDiscountPrice$',
                '$measurementUnit$',
                '$origin$',
                '$price_per_kilo$',
                '$currency$',
                ];

                private usedFields: Set<string> = new Set();

                ...

                addOptionalField(event: Event): void {
                    const selectElement = event.target as HTMLSelectElement;
                    const selectedField = selectElement.value;
                    if (selectedField && !this.usedFields.has(selectedField)) {
                    this.addTextFieldpredefined(selectedField, 200, 200, true);
                    this.usedFields.add(selectedField);
                    }
                }

                isFieldUsed(field: string): boolean {
                    return this.usedFields.has(field);
            }
            ```
            - [x] Refonte du fichier de test : 
            ```
            imports
            jest.mock('fabric', () => ({
            ...jest.requireActual('fabric'),
            Canvas: jest.fn().mockImplementation(() => ({
                add: jest.fn(),
                setDimensions: jest.fn(),
                toJSON: jest.fn().mockReturnValue({ objects: [], version: '' }),
                toDataURL: jest.fn().mockReturnValue('data:image/png;base64,'),
                renderAll: jest.fn(),
                requestRenderAll: jest.fn(),
                discardActiveObject: jest.fn(),
                animate: jest.fn(),
                getActiveObject: jest.fn(),
                remove: jest.fn(),
            })),
            IText: jest.fn().mockImplementation((text, options) => ({
                text,
                ...options,
                set: jest.fn((key, value) => {
                if (key === 'fontFamily') {
                    options.fontFamily = value;
                } else if (key === 'fill') {
                    options.fill = value;
                }
                }),
            })),
            }));

            describe('CreateNewTemplateComponent', () => {
            let component: CreateNewTemplateComponent;
            let fixture: ComponentFixture<CreateNewTemplateComponent>;
            // let templateServiceMock: jest.Mocked<TemplateService>;
            // let mockCanvas: jest.Mocked<fabric.Canvas>;

            const activatedRouteStub = {
                queryParams: of({
                name: 'TestTemplate',
                width: '200',
                height: '100'
                })
            };

            beforeEach(async () => {
                const templateService = {
                createTemplate: jest.fn().mockReturnValue(of({}))
                } as unknown as jest.Mocked<TemplateService>;

                const base64Service = {
                createTemplate: jest.fn().mockReturnValue(of({}))
                } as unknown as jest.Mocked<Base64imageConverterService>;

                await TestBed.configureTestingModule({
                imports: [
                    CreateNewTemplateComponent,
                    BrowserAnimationsModule, 
                    MatSnackBarModule 
                ],
                providers: [
                    { provide: ActivatedRoute, useValue: activatedRouteStub },
                    { provide: TemplateService, useValue: templateService },
                    { provide: Base64imageConverterService, useValue: base64Service },
                ]
                }).compileComponents();

                fixture = TestBed.createComponent(CreateNewTemplateComponent);
                component = fixture.componentInstance;
                // templateServiceMock = TestBed.inject(TemplateService) as jest.Mocked<TemplateService>;
                const mockAnimate = jest.fn().mockReturnValue({
                    finished: Promise.resolve()
                    });
                    Element.prototype.animate = mockAnimate;
                // mockCanvas = component['canvas'] as jest.Mocked<fabric.Canvas>;
                fixture.detectChanges();
            });

            it('should create the component', () => {
                expect(component).toBeTruthy();
            });

            it('should add a new text field to the canvas', () => {
                const addTextFieldSpy = jest.spyOn(component as any, 'addTextField');
                component.addNewTextField();
                expect(addTextFieldSpy).toHaveBeenCalledWith('Nouveau texte', 50, 50);
            });

            it('should add predefined fields to the canvas', () => {
                const addTextFieldpredefinedSpy = jest.spyOn(component as any, 'addTextFieldpredefined');
                component.ngAfterViewInit();
                expect(addTextFieldpredefinedSpy).toHaveBeenCalledWith('$price$', 100, 100, true);
                expect(addTextFieldpredefinedSpy).toHaveBeenCalledWith('$currency$', 140, 100, true);
            });

            it('should change the font of the selected text object', () => {
                const changeFontSpy = jest.spyOn(component, 'changeFont');
                const mockEvent = { target: { value: 'Arial' } };
            
                const mockActiveObject = {
                type: 'i-text',
                set: jest.fn(),
                };
                jest.spyOn(component['canvas'], 'getActiveObject').mockReturnValue(mockActiveObject as any);
            
                component.changeFont(mockEvent);
                expect(changeFontSpy).toHaveBeenCalledWith(mockEvent);
                expect(mockActiveObject.set).toHaveBeenCalledWith('fontFamily', 'Arial');
            });

            it('should change the color of the selected object', () => {
                const changeColorSpy = jest.spyOn(component, 'changeColor');
                const mockEvent = { target: { value: '#ff0000' } };
            
                const mockActiveObject = {
                set: jest.fn(),
                };
                jest.spyOn(component['canvas'], 'getActiveObject').mockReturnValue(mockActiveObject as any);
            
                component.changeColor(mockEvent);
                expect(changeColorSpy).toHaveBeenCalledWith(mockEvent);
                expect(mockActiveObject.set).toHaveBeenCalledWith('fill', '#ff0000');
            });

            it('should delete the selected object if it is not predefined', () => {
                const deleteSelectedObjectSpy = jest.spyOn(component, 'deleteSelectedObject');
                component['canvas'].getActiveObject = jest.fn().mockReturnValue({ isPredefined: false } as any);
                component.deleteSelectedObject();
                expect(deleteSelectedObjectSpy).toHaveBeenCalled();
                expect(component['canvas'].remove).toHaveBeenCalled();
            });

            it('should not delete the selected object if it is predefined', () => {
                const deleteSelectedObjectSpy = jest.spyOn(component, 'deleteSelectedObject');
                component['canvas'].getActiveObject = jest.fn().mockReturnValue({ isPredefined: true } as any);
                component.deleteSelectedObject();
                expect(deleteSelectedObjectSpy).toHaveBeenCalled();
                expect(component['snackBar'].open).toHaveBeenCalledWith('Cannot delete predefined field', 'Close', {
                horizontalPosition: 'right',
                verticalPosition: 'top',
                duration: 2000,
                });
            });

            it('should add a square to the canvas', () => {
                component.addSquare();
                expect(component['canvas'].add).toHaveBeenCalledWith(expect.objectContaining({
                type: 'rect',
                left: 100,
                top: 100,
                width: 100,
                height: 100,
                fill: component.selectedColor,
                }));
            });

            it('should add a circle to the canvas', () => {
                component.addCircle();
                expect(component['canvas'].add).toHaveBeenCalledWith(expect.objectContaining({
                type: 'circle',
                left: 200,
                top: 100,
                radius: 50,
                fill: component.selectedColor,
                }));
            });

            it('should add a triangle to the canvas', () => {
                component.addTriangle();
                expect(component['canvas'].add).toHaveBeenCalledWith(expect.objectContaining({
                type: 'triangle',
                left: 100,
                top: 50,
                width: 150,
                height: 100,
                fill: component.selectedColor,
                }));
            });

            it('should toggle bold style on the selected text object', () => {
                const toggleBoldSpy = jest.spyOn(component, 'toggleBold');
            
                const mockActiveObject = {
                type: 'i-text',
                fontWeight: 'normal',
                set: jest.fn(),
                };
                jest.spyOn(component['canvas'], 'getActiveObject').mockReturnValue(mockActiveObject as any);
            
                component.toggleBold();
                expect(toggleBoldSpy).toHaveBeenCalled();
                expect(mockActiveObject.set).toHaveBeenCalledWith('fontWeight', 'bold');
            });

            it('should toggle strikethrough style on the selected text object', () => {
                const toggleStrikethroughSpy = jest.spyOn(component, 'toggleStrikethrough');
            
                const mockActiveObject = {
                type: 'i-text',
                linethrough: false,
                set: jest.fn(),
                };
                jest.spyOn(component['canvas'], 'getActiveObject').mockReturnValue(mockActiveObject as any);
            
                component.toggleStrikethrough();
                expect(toggleStrikethroughSpy).toHaveBeenCalled();
                expect(mockActiveObject.set).toHaveBeenCalledWith('linethrough', true);
            });
            });
            ```
        - [x] Commit, push & PR
    - [ ] ESL-256 Fix la désactivation des ESLs depuis le front
        - [x] console logging dans le front : 
            - [x] génération de l'image en base 64
            - [x] passage dans esl service updateOnEslAndDisable avec payload ok
            - [x] Retour d'erreur : 
            ```
            Failed to disable ESL: Http failure response for http://localhost:4200/api/esl/picturedisable/145031a2: 404 Not Found
            ```
            - [x] Modification de la route dans le service front : 
            ```
            return this.httpClient.patch<void>(`${this.route}/picturedisable/${idEsl}`, payload);
            ```
            - [x] Test dans le swagger avec  le payload
            - [x] Retour d'erreur : 
            ```
            502 Error: Bad Gateway
            "statusCode": 502,
            "message": "Error communicating with ESL service(updateOnEslAndDisable())"
            ```
            avec l'aide de GPT, j'ai l'info que c'est dans le container mqtt_api que ça se passe, et après check des logs, je trouve :
            ```
            ERROR:ESLBridgeServer:Failed to execute esl_set_image with error
            Traceback (most recent call last):
            File "/app/ESLBridgeServer.py", line 70, in route_esl_set_image
                esl_process.esl_image(request.json)
            File "/app/ESLBridgeServerProcess.py", line 108, in esl_image
                self.check_esl_exists(request_data["id_esl"])
            File "/app/ESLBridgeServerProcess.py", line 184, in check_esl_exists
                raise KeyError("EslNotFoundError")
            KeyError: 'EslNotFoundError'
            Stack (most recent call last):
            File "/usr/lib/python3.10/threading.py", line 973, in _bootstrap
                self._bootstrap_inner()
            File "/usr/lib/python3.10/threading.py", line 1016, in _bootstrap_inner
                self.run()
            File "/usr/lib/python3.10/threading.py", line 953, in run
                self._target(*self._args, **self._kwargs)
            File "/usr/lib/python3.10/concurrent/futures/thread.py", line 83, in _worker
                work_item.run()
            File "/usr/lib/python3.10/concurrent/futures/thread.py", line 58, in run
                result = self.fn(*self.args, **self.kwargs)
            File "/usr/local/lib/python3.10/dist-packages/gunicorn/workers/gthread.py", line 281, in handle
                keepalive = self.handle_request(req, conn)
            File "/usr/local/lib/python3.10/dist-packages/gunicorn/workers/gthread.py", line 333, in handle_request
                respiter = self.wsgi(environ, resp.start_response)
            File "/usr/local/lib/python3.10/dist-packages/flask/app.py", line 1498, in __call__
                return self.wsgi_app(environ, start_response)
            File "/usr/local/lib/python3.10/dist-packages/flask/app.py", line 1473, in wsgi_app
                response = self.full_dispatch_request()
            File "/usr/local/lib/python3.10/dist-packages/flask/app.py", line 880, in full_dispatch_request
                rv = self.dispatch_request()
            File "/usr/local/lib/python3.10/dist-packages/flask/app.py", line 865, in dispatch_request
                return self.ensure_sync(self.view_functions[rule.endpoint])(**view_args)  # type: ignore[no-any-return]
            File "/app/ESLBridgeServer.py", line 72, in route_esl_set_image
                logger.exception(
            ```
            - [x] Reprise des bases en local (ipconfig, http://192.168.1.161:8080/#/setting, et modif de la host address)
            - [x] trim la génération de base64 dans le front pour enlever le début ('data:image.png,base64,')
    - [ ] ESL-263 Fix la dissociation du storeArticle de son ESL
        - [x] Réajustement de la route du service esl front pour match le controller endpoint back : 
        'dissociate-esl/:id'
    - [ ] ESL-273 Dissocier l'eslId du storeArticle associé en même temps que la dissociation storeArticleID de l'ESL
        - [ ] Créer la fonction qui set NULL au champ storeArticle.esl
        - [ ] Appeler la fonction quand on call dissociate(esl)
        - [ ] Gérer la case sensitive pour harmoniser les actions