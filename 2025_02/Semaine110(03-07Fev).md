**3 Février**
- [ ] ESL
    - [x] ESL-257
        - [x] Suppression des liens & icônes dans la sidebar
    - [x] Redéploiement
        - [x] fix des tests frontend
        - [x] Repush 
    - [x] ESL-253 Enlever la possibilité de rezise le code bar
    - [x] ESL-259 enlever le $barcode$ des templates et vérifier que il n'est plus necessaire dans le back
- [ ] Veille techno
    - [x] (https://refactoring.guru/design-patterns/adapter)

**4 Février**
- [ ] ESL
    - [x] Installation du setup à la maison
        - [x] Base
        - [x] Pc
        - [x] Lancement des dockers
        - [x] Unauthorized
    - [ ] Poursuite des tickets


    
**5 Février**
- [ ] ESL
    - [x] Réinstallation du setup au bureau
        - [x] Base
        - [x] Pc
        - [x] Lancement des dockers
        - [x] Unauthorized
        - [x] Connexion à github & docker
        - [x] Lancement du script install.sh, retour : 
        ```
        denied
        ```
    - [ ] Poursuite des tickets
        - [ ] ESL-255 faire en sorte que l'on ne puisse pas poste un template si il existe déjà un template de la même taille dans le style
        - [ ] ESL-256 Fix la désactivation des ESLs depuis le front
        - [ ] ESL-258 Ajouter un onHover pour foncer les onglets du menu au survol


        0661873285


**07 Février**
- [ ] ESL
    - [ ] Poursuite des tickets
        - [x] ESL-260 Rajouter des titres au dessus de chaque tableau
            - [x] Dashboard component, rename des labels de charts & des titres associés
        - [ ] ESL-255 faire en sorte que l'on ne puisse pas poster un template si il existe déjà un template de la même taille dans le style
            - [x] prompt :
            ```
            fichiers templates backend,
            i want to create a route and method that gets all templates matching the provided parameters of width AND height
            ```
            - [ ] Récupérer tous les templates associés à une dimension d'ESL
            - [ ] Récupérer tous les templates liés à un style :
                - [x] Controller 
                    ```
                    @Get('filter-by-dimensions')
                        @ApiOperation({ summary: 'Get templates by width and height' })
                        async findByWidthAndHeight(
                            @Query('width') width: number,
                            @Query('height') height: number,
                        ) {
                            return this.templateService.findByWidthAndHeight(Number(width), Number(height));
                        }
                        @Get('filter-by-dimensions-and-style')
                        @ApiOperation({ summary: 'Get templates by width and height' })
                        async findByWidthHeightAndStyle(
                            @Query('width') width: number,
                            @Query('height') height: number,
                            @Query('style') style: string,
                        ) {
                            return this.templateService.findByWidthHeightAndStyle(Number(width), Number(height), String(style));
                        }
                    ```
                    - [x] Service backend : 
                    ```
                      async findByWidthAndHeight(width: number, height: number): Promise<Template[]> {
                        return this.templateModel.find({ width, height }).exec();
                    }

                    async findByWidthHeightAndStyle(width: number, height: number, styleName:string): Promise<Template[]> {
                        return this.templateModel.find({ width, height, styleName }).exec();
                    }
                    async existsByWidthHeightAndStyle(width: number, height: number, styleName: string): Promise<boolean> {
                        const template = await this.templateModel.findOne({ width, height, styleName }).exec();
                        return !!template; // Returns true if found, false otherwise
                    }
                    ```
                    - [x] Kill du container et rebuild avec 
                    ```
                    docker compose  --env-file .env.dev -f docker-compose.api.yml up --build
                    ```
            - [x] Connection au front
                - [x] template service
                ```
                checksIfExistsByWidthHeightAndStyle(width: number, height: number, styleName: string): Observable<boolean> {
                    return this.http.get<boolean>(`${this.baseUrl}/exists-by-dimensions-and-style`, {
                    params: { width: width.toString(), height: height.toString(), style: styleName }
                    });
                }
                ```
                - [x] Update du component : 
                ```
                 constructor(
                    private router: Router,
                    private styleService: StyleService,
                    private eslService: EslService,
                    private templateService: TemplateService,
                    private snackBar: MatSnackBar
                ) {}

                OnSubmit() {
                    const styleName = this.styleNameControl.value;
                    const [width, height] = this.templateSize.value.split('x').map(Number);

                    this.templateService.checksIfExistsByWidthHeightAndStyle(width, height, styleName)
                    .subscribe((exists) => {
                    if (exists) {
                        // Show snackbar and block submission
                        this.snackBar.open('Un template existe déjà pour ce style avec ces dimensions', 'Close', {
                        duration: 3000, // Duration in milliseconds
                        panelClass: ['error-snackbar'] // Custom styling (optional)
                        });
                    } else {
                        // Proceed with submission logic
                        this.proceedWithSubmit(styleName);
                    }
                    });
                }

                proceedWithSubmit(styleName: string){
                    // If it's a new style, then we'll try to create it in db
                    if (
                    !this.styles
                        .map((s) => s.name.toLowerCase())
                        .includes(styleName?.toLowerCase())
                    ) {
                    this.styleService.create(styleName).subscribe({
                        next: () => {
                        this.redirectToCreate();
                        },
                        // eslint-disable-next-line @typescript-eslint/no-unused-vars
                        error: (error) => {
                        this.redirectToCreate();

                        console.error();
                        },
                    });
                    } else {
                    this.redirectToCreate();
                    }
                }
                ```
        - [ ] ESL-256 Fix la désactivation des ESLs depuis le front