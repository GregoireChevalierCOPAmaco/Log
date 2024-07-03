**1er Juillet**
- [x] Arrivée et brief Loïc
- [ ] Reprise ESLs
    - [ ] ESL-62 Créer un menu de sélection pour les templates
        - [x] Ajout du sélector et modif du service : 
        ```
            createTemplateImage(text1: string, text2: string, text3: string, image: File, templateType: string): Promise<string> {
            return new Promise((resolve, reject) => {
            const canvas = document.createElement('canvas');
            canvas.width = 360;
            canvas.height = 240;
            const ctx = canvas.getContext('2d');
        
            if (ctx) {
                // Set background color to white
                ctx.fillStyle = '#ffffff';
                ctx.fillRect(0, 0, canvas.width, canvas.height);

                const img = new Image();
                img.onload = () => {
                ctx.drawImage(img, 0, 0, 180, 240);

                ctx.fillStyle = '#000000'; // Set text color to black
                ctx.font = '16px Arial'; // Set font size and type

                if (templateType === 'template1') {
                    ctx.fillText(text1, 190, 50);
                    ctx.fillText(text2, 190, 100);
                    ctx.fillText(text3, 190, 150);
                } else if (templateType === 'template2') {
                    ctx.fillText(text1, 10, 50);
                    ctx.fillText(text2, 10, 100);
                    ctx.fillText(text3, 10, 150);
                } else if (templateType === 'template3') {
                    ctx.fillText(text1, 50, 30);
                    ctx.fillText(text2, 50, 90);
                    ctx.fillText(text3, 50, 150);
                }
        
                const url = canvas.toDataURL('image/jpeg');
                resolve(url);
                };
        
                img.onerror = () => {
                reject('Error loading image');
                };

                img.src = URL.createObjectURL(image);
            } else {
                reject('Error getting canvas context');
            }
            });
        }
        ```
        mais erreur : 
        ```
        ✘ [ERROR] NG8002: Can't bind to 'ngModel' since it isn't a known property of 'select'. [plugin angular-compiler]    
        esl_front     |
        esl_front     |     src/app/template-image-creator/template-image-creator.component.html:7:10:
        ```
        - [x] ```git reset --hard```
        - [x] Essai autrement : utilisation de la table template du backend pour 
            - offrir la possibilité à l'user de définir les coordonnées x,y des champs texte et de l'image (et reload au submit)
            - enregistrer ledit template créé en base avec un nom de template
            - sélectionner un template existant en base depuis un selector
    - [ ] Test de la table des esls
        - [ ] Ajout d'esls en base
            - [ ] Connexion via adminer à (localhost:8080)
    - [ ] Résolution du bug de non affichage des esls dans la table
        - [x] Modif du component : 
        ```
        getEsls(page: number): void {
            this.eslService.getEsls(page, this.pageSize).subscribe(
            response => {
                console.log(response);
                this.esls = response;
                this.totalItems = response.length;
                this.cdr.markForCheck();
            },
            error => {
                console.error('Error fetching ESLs:', error);
            }
            );
        }
        ```
        - [x] Modif du service front : 
        ```
        getEsls(page: number, limit: number): Observable<any[]> {
            return this.httpClient.get<any[]>(`${this.route}?page=${page}&limit=${limit}`);
        }  
        ```

**3 Juillet**
- [ ] Suite ESLs
    - [ ] Lien de la creation de template au backend
        - [x] Switch de branche 
        ```
        git checkout -b feat/ESL-68_link_template_creation_toBackend
        ```
        