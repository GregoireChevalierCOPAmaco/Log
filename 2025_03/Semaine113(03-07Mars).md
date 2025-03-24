**03 Mars**
- [x] Check mails
- [ ] ESL
    - [x] Connexion au serveur 
    ```
    ssh esl@192.168.1.252
    ```
    - [x] Pull, et rédéploiement des containers
    - [x] Vérification sur la prod des tickets en validation
    - [ ] Poursuite des tickets
        - [ ] ESL-269 Ajouter une interface de changement de style à l'application web
            - [x] Modif du fichier html
            ```
            <div class="associate-container">
            <h2>Définir le style de l'ESL</h2>
            <form (submit)="onSetStyleSubmit($event)">
                <div>
                <label for="idEsl">ESL ID:</label>
                <input
                    type="text"
                    id="idEsl"
                    [(ngModel)]="idEsl"
                    name="idEsl"
                    readonly
                />
                </div>

                <div>
                <label for="styleName">Style Name:</label>
                <input
                    type="text"
                    id="styleName"
                    [(ngModel)]="styleName"
                    name="styleName"
                    required
                />
                </div>

                <button type="submit" [disabled]="!styleName">Définir le style</button>
            </form>
            </div>
            ```
            - [x] Modif du fichier component
            ```
            onSetStyleSubmit(event: Event): void {
                event.preventDefault();

                if (this.idEsl && this.styleName) {
                this.eslService.setStyleOnEsl(this.idEsl, this.styleName).subscribe({
                    next: () => alert('Style set successfully!'),
                    error: (err) => alert('Failed to set style: ' + err.message),
                });
                }
            }
            ```
            - [x] Modif du fichier esl service front
            ```
            setStyleOnEsl(idEsl: string, styleName: string): Observable<void> {
                return this.httpClient.patch<void>(`${this.route}/set-style/${idEsl}`, { styleName });
            }
            ```
            - [x] Modif du fichier controller
            ```
            @Patch('set-style/:id')
            setStyleOnEsl(
                @Param('id') id: string,
                @Body('styleName') styleName: string,
            ) {
                return this.eslService.SetStyleOnEsl(id, styleName);
            }
            ```
            - [x] Modif du fichier esl service front
            ```
            async SetStyleOnEsl(id_esl: string, styleName: string): Promise<void> {
                const esl = await this.eslRepository.findOne({where: {id_esl}});
                if (!esl) {
                throw new HttpException('ESL not found', HttpStatus.NOT_FOUND);
                }

                const template = await this.templateService.getTemplateByStyleName(styleName);
                if (!template) {
                throw new HttpException('Template not found', HttpStatus.NOT_FOUND);
                }
                const style = await this.styleService.getStyleByName(styleName);

                esl.style = style;
                await this.eslRepository.save(esl);
            }
            ```
            - [x] Lint, test, commit & push
        - [ ] ESL-271 Au clic du bouton Sauvegarder de l'édition de template, envoyer un snackbar de confirmation et / ou rediriger vers le menu
            - [x] Modifs du fichier edit-template component
            ```
            constructor(
                private route: ActivatedRoute, 
                private templateImageService: TemplateImageService, 
                private  templateService: TemplateService,    
                private snackBar: MatSnackBar,
                private router: Router
            )
            ...
            public updateCanvasState() {
                const canvasJSON = this.canvas.toJSON();
                // Assurez-vous que l'arrière-plan est blanc
                this.canvas.backgroundColor = '#FFFFFF';
                const canvaUpload = {
                objects: canvasJSON.objects,
                width: this.width,
                height: this.height,
                version: canvasJSON.version,
                esl_id: '',
                name: this.name
                };
                console.log(canvaUpload);

                // Enregistrer l'état dans la base de données
                return this.templateService.updateTemplate(this.id,canvaUpload).subscribe(result => {
                console.log('uploaded',result);
                this.snackBar.open('Le template a été modifié', 'Close', {
                    duration: 3000,
                }).afterDismissed().subscribe(() => {
                    this.router.navigate(['/template-list']);
                });
                }, (error: any) => {
                console.log(error);
                });
            }
            ```
            - [x] Lint, test, commit & push
        - [ ] ESL-280 Ajouter un bouton de force refresh dans l'interface de management de l'ESL
            - [x] Modif du fichier html
            ```
            <div class="associate-container">
            <h2>Recharger l'image</h2>
            <form (submit)="forceRefresh()">

                <button type="submit" [disabled]="!idEsl">Force Refresh</button>
            </form>
            </div>
            ```
            - [x] Modif du fichier component
            ```
            forceRefresh(){
                if (this.idEsl) {
                const article = this.storeArticleService.getArticleByEslId(this.idEsl);
                this.storeArticleService.forceRefresh(article).subscribe({
                    next: () => alert('Force refresh completed successfully!'),
                    error: (err) => alert('Failed to force refresh: ' + err.message),
                });
                }
            }
            ```
            - [x] Modif du fichier store article service frontend
            ```
            getArticleByEslId(id: string){
                return this.httpClient.get<any>(`${this.apiUrl}/esl/${id}`);
            }
            ```
            - [x] Modif du fichier store article service backend
            ```
            async getStoreArticleByEslId(eslId: string): Promise<StoreArticle> {
                try {
                const esl = await this.Esl.findOne({ where: { id_esl: eslId }, relations: ['store_article'] });
                if (!esl || !esl.store_article) {
                    throw new HttpException('Store Article not found for the given ESL ID', 404);
                }
                return esl.store_article;
                } catch (e) {
                throw new HttpException('Failed to fetch store article by ESL ID', 500);
                }
            }
            ```
            - [x] Modif du fichier store article controller
            ```
            @Post('force-refresh')
            async forceRefresh(@Body() StoreArticle: any): Promise<any> {
                console.log(StoreArticle);
                
                return await this.storeArticlesService.forceRefresh(StoreArticle);
            }

            @Get('esl/:eslId')
            async getStoreArticleByEslId(@Param('eslId') eslId: string): Promise<StoreArticle> {
                return await this.storeArticlesService.getStoreArticleByEslId(eslId);
            }
            ```
        - [ ] ESL-293 Gérer le error handling de updateOnEsl() dans le ESL service pour éviter le crash du back s'il y a erreur
            - [x] Modif du fichier esl service backend : 
            ```
            async updateOnEsl(id_esl: string, updateTemplateDto: UpdatePictureEslDto) {
                try {
                const esl = await this.eslRepository.findOne({ where: { id_esl } });
                if (!esl) {
                    console.error(`ESL with id ${id_esl} not found.`);
                    return { success: false, message: 'ESL not found' };
                }
            
                esl.disabled = false;
                await this.eslRepository.save(esl);
            
                console.log('Sending update to ESL:', id_esl);
                
                try {
                    const response = await this.httpService
                    .post('http://mqtt_api:5002/esl_set_image', {
                        id_esl: id_esl,
                        image: updateTemplateDto.picture,
                    })
                    .toPromise();
            
                    if (response.status !== HttpStatus.OK) {
                    console.error(`Failed to send image to ESL. Status: ${response.status}`);
                    return { success: false, message: 'Failed to send image to ESL' };
                    }
            
                    return { success: true, message: 'Image sent successfully', data: response.data };
                } catch (httpError) {
                    console.error('Error sending request to ESL service:', httpError.message || httpError);
                    return { success: false, message: 'Error communicating with ESL service' };
                }
            
                } catch (error) {
                console.error('Unexpected error in updateOnEsl():', error.message || error);
                return { success: false, message: 'Unexpected error occurred' };
                }
            }
            ```
            - [x] Modif du fichier test : 
            ```
            describe('updateOnEsl', () => {
            it('should enable an ESL and send an update request', async () => {
                const idEsl = 'ID1';
                const updateTemplateDto = { picture: 'image-data' };
                const mockEsl = { id_esl: idEsl, disabled: true } as Esl;
            
                jest.spyOn(repository, 'findOne').mockResolvedValue(mockEsl);
                jest.spyOn(repository, 'save').mockResolvedValue(mockEsl);
                jest.spyOn(httpService, 'post').mockReturnValue(of({ status: 200, data: 'success' } as AxiosResponse));

                const result = await service.updateOnEsl(idEsl, updateTemplateDto);

                expect(repository.save).toHaveBeenCalledWith({ ...mockEsl, disabled: false });
                expect(result).toEqual({ success: true, message: 'Image sent successfully', data: 'success' });
            });

            it('should return an error object if the ESL is not found', async () => {
                jest.spyOn(repository, 'findOne').mockResolvedValue(null);

                const result = await service.updateOnEsl('ID1', { picture: 'image-data' });

                expect(result).toEqual({ success: false, message: 'ESL not found' });
            });

            it('should handle HTTP request failure gracefully', async () => {
                const idEsl = 'ID1';
                const updateTemplateDto = { picture: 'image-data' };
                const mockEsl = { id_esl: idEsl, disabled: true } as Esl;
            
                jest.spyOn(repository, 'findOne').mockResolvedValue(mockEsl);
                jest.spyOn(repository, 'save').mockResolvedValue(mockEsl);
                jest.spyOn(httpService, 'post').mockImplementation(() => {
                throw new Error('Request failed');
                });

                const result = await service.updateOnEsl(idEsl, updateTemplateDto);

                expect(result).toEqual({ success: false, message: 'Error communicating with ESL service' });
            });

            it('should handle unexpected errors gracefully', async () => {
                jest.spyOn(repository, 'findOne').mockImplementation(() => {
                throw new Error('Unexpected error');
                });

                const result = await service.updateOnEsl('ID1', { picture: 'image-data' });

                expect(result).toEqual({ success: false, message: 'Unexpected error occurred' });
            });
            });
            ```
        - [ ] ESL-268 Afficher le nom du style des ESLs assoicées
            - [x] Modif du fichiereslcontroller
            ```
            @Get('/style/:id')
            @ApiOperation({ summary: 'Get style by ESL ID' })
            async getStyleByEslId(@Param('id') id: string) {
            return this.eslService.getStyleByEslId(id);
            }
            ```
            - [x] Modif du fichier esl service backend
            ```
            async getStyleByEslId(id: string): Promise<{ styleName: string } | null> {
            const esl = await this.eslRepository.findOne({
                where: { id },
                relations: ['style'], // Assuming there's a relation between ESL and Style
            });

            return esl ? { styleName: esl.style?.name || null } : null;
            }

            ```
            - [x] Modif du fichier esl service frontend
            ```
            getStylesForEsls(ids: string[]): Observable<{ idEsl: string, style: any }[]> {
            return this.httpClient.post<{ idEsl: string, style: any }[]>(`${this.route}/styles`, { ids });
            }
            ```
            - [x] Modif du fichier esl table component
            ```
            fetchStyle(idEsl: string) {
            this.eslService.getStyleByEslId(idEsl).subscribe(
                (response) => {
                console.log(`Style for ESL ${idEsl}:`, response.styleName);
                },
                (error) => {
                console.error('Error fetching style:', error);
                }
            );
            }
            ```
        - [ ] ESL-267 Ajouter un filtre pour trier les ESLs associées 
- [x] Réunion prospect ESLs croissantville
```
un genre d'entrepot plus que boutique, très rural, entreposé sur des palettes. Roots quoi.
Mise en relation avec les stocks et l'ERP ?
Une centaine d'ESLs prévues
400 m²
Comment on peut avancer niveau délai, technique, besoins matériels ?
qui s'occupe de fournir quoi ? (pc, bases, réseau, PDA, accès pc-réseau le cas échéant)
Comment on peut récupérer les infos ERP au besoin.
Réseau : fibré mais parfois des coupures ? 
prix : élément primordial, le moins cher sera le mieux pour le client. 
```
- [x] Check mails
- [ ] ESL
    - [ ] Reprise ESL-268 Afficher le nom du style des ESLs assoicées
        - [x] Modif du fichiereslcontroller
        ```
        @Get('styles')
        @ApiOperation({ summary: 'Get the style for each ESL' })
        getStylesForEsls() {
            return this.eslService.getStylesForEsls();
        }
        ```
        - [x] Modif du fichier esl service backend
        ```
        async getStylesForEsls(): Promise<{ eslId: string; styleName: string }[]> {
            const esls = await this.eslRepository.find({ relations: ['style'] });
            return esls.map(esl => ({ eslId: esl.id_esl, styleName: esl.style?.name || 'No Style' }));
        }
        ```
        - [x] Modif du fichier esl service frontend
        ```
        getStylesForEsls(): Observable<{ eslId: string; styleName: string }[]> {
            return this.httpClient.get<{ eslId: string; styleName: string }[]>(`${this.route}/styles`);
        }
        ```
        - [x] Modif du fichier esl table component
        ```
        filterEsls(): void {
            if (this.searchTerm.trim() != '') {
            this.eslService.getEslspaginate(1,10,this.searchTerm.toLowerCase()).subscribe((data) => {
                if (Array.isArray(data.items)) {
                console.log(data.items);
                this.getStylesForEsls();
        
                
                this.filteredEsls = data.items;
                } else {
                console.error(data.items);
                }
            }, (error) => {
                console.log(error);
            });
            }else {
            
            this.eslService.getEslspaginate(this.currentPage,10,' ').subscribe((data) => {
                if (Array.isArray(data.items)) {
                console.log(data.items);
                console.log("datas");
                
                this.getStylesForEsls();

                
                this.filteredEsls = data.items;
                console.log(this.filteredEsls[0]);
                console.log(this.filteredEsls[0].style?.name);
                if (this.showOnlyAssociatedEsls) {
                    this.filteredEsls = this.filteredEsls.filter(esl => esl.store_article?.id !== null);
                }
                } else {
                console.error(data.items);
                }
            } , (error) => {
                console.log(error);
            });
            }
            
        }

        getStylesForEsls(): void {
            this.eslService.getStylesForEsls().subscribe((styles) => {
            this.filteredEsls.forEach(esl => {
                const style = styles.find(s => s.eslId === esl.id_esl);
                esl.style = style ? { name: style.styleName } : null;
            });
            this.cdr.markForCheck();
            }, (error) => {
            console.error(error);
            });
        }
        ```
    - [ ] ESL-272 Fix la barre de recherche de la page storeArticle table
        - [x] Modif du fichier article list component
        ```
        filterArticles(): void {
            if (!this.searchTerm.trim()) {
            this.filteredArticles = [...this.articles]; 
            return;
            }
            
            this.filteredArticles = this.articles.filter(article => 
            article.name.toLowerCase().includes(this.searchTerm.toLowerCase()) ||
            article.bar_code.toLowerCase().includes(this.searchTerm.toLowerCase()) ||
            article.origine.toLowerCase().includes(this.searchTerm.toLowerCase()) ||
            article.price.toString().includes(this.searchTerm)
            );
        }
        ```
        + formsModule dans les imports pour résoudre l'error de can't bind to ngModel
        - [x] Modif du fichier html
        ```
       <div class="mt-2.5 absolute right-10 top-5">
        <input 
        placeholder="Recherche" 
        type="text" 
        name="first-name" 
        id="first-name" 
        autocomplete="given-name" 
        class="w-full rounded-md border-0 px-3.5 py-2 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
        [(ngModel)]="searchTerm"
        (input)="filterArticles()"
        >
        </div>
        ```
    - [ ] ESL-267 Ajouter un filtre pour trier les ESLs associées 