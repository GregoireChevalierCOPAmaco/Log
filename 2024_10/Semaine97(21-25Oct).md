**21 Octobre**
- [ ] ESL
    - [ ] ESL-147 Mettre à jour les ESLs en fournissant un fichier en entrée
        - [ ] ESL-148 Créer une page pour l'insertion d'un fichier en entrée
            - [x] Prompt à GPT : 
            ```
            Currently, my project is setup as follows : 
            the angular frontend, the nestJs backend, the database, a python mqtt api, a python mqtt broker, all hosted on a server and deployed via docker containers orchestrated by docker compose.
            The app works well, as i am able to detect and interact with the ESLs (detect them, make a LED on the ESL blink, disable them and change the image).
            Right now what i want is to create an angular page that would offer me the possibility to upload a file (csv or excel for example) filled with store-article infos, and make logic to filter informations to change store-articles in database.
            Below are files to help : 
            ```
            avec fichiers fournis
            - [x] Ajout dans le fichier article-list.component.html
            de :
            ```
            <div class="file-upload-section">
                <input type="file" (change)="handleFileInput($event)" accept=".csv">
            </div>
            ```
            - [x] Ajout dans le fichier article-list.component.ts
            de :
            ```
            handleFileInput(event: Event): void {
                const inputElement  = event.target as HTMLInputElement; 
                if (inputElement .files && inputElement .files.length > 0) {
                const file = inputElement .files[0]; 
                if (file) {
                    this.parseCSV(file);
                }
                }
            }
            
            parseCSV(file: File): void {
                Papa.parse(file, {
                header: true,
                skipEmptyLines: true,
                complete: (result) => {
                    this.csvData = result.data;
                    this.uploadCSVData(this.csvData);
                }
                });
            }

            uploadCSVData(data: any[]): void {
                this.storeArticleService.updateArticlesFromCSV(data).subscribe(
                response => {
                    console.log('CSV data processed successfully:', response);
                    this.getArticle(this.currentPage); // Refresh the list
                },
                error => {
                    console.error('Error processing CSV data:', error);
                }
                );
            }
            ```
            - [x] Ajout dans le fichier store article service (front)
            de :
            ```
            updateArticlesFromCSV(data: any[]): Observable<any> {
                return this.httpClient.post(`${this.apiUrl}/upload-csv`, data);
            }
            ```
            - [x] Ajout dans le fichier store article service (back)
            de :
            ```
            async updateArticlesFromCSV(csvData: any[]): Promise<void> {
                try {
                for (const row of csvData) {
                    const { bar_code, name, price, origine } = row;

                    let article = await this.storeArticleRepository.findOne({ where: { bar_code } });

                    if (article) {
                    article.name = name;
                    article.price = price;
                    article.origine = origine;
                    await this.storeArticleRepository.save(article);
                    } else {
                    const newArticle = this.storeArticleRepository.create({
                        bar_code,
                        name,
                        price,
                        origine,
                    });
                    await this.storeArticleRepository.save(newArticle);
                    }
                }
                } catch (error) {
                throw new HttpException('Failed to process CSV data', 500);
                }
            }
            ```
            - [x] Ajout dans le fichier store article controller (back)
            de :
            ```
            @Post('upload-csv')
            async uploadCSV(@Body() csvData: any[]): Promise<void> {
                return this.storeArticlesService.updateArticlesFromCSV(csvData);
            }
            ```
            - [x] Update des fichiers de test
        - [x] ESL-149 Extraire les données du fichier entrée
        - [ ] ESL-150 Créer un bouton pour mettre à jour toutes les ESLs concernées par un changement depuis la page d' soumission de fichier entrée
        - [ ] ESL-151 Créer la logique back pour recharger toutes les ESLs
        - [ ] ESL-152 Créer la logique front pour recharger toutes les ESLs
        - [ ] ESL-156 Filtrer les données du fichier en entrée pour exclure la màj des store-articles pas liés à une ESL
        - [ ] ESL-157 Filtrer les données du fichier en entrée pour pointer à l'utilisateur les ESLs n'étant pas liées à un article


**22 Octobre**
- [ ] ESL
    - [ ] ESL-147 Mettre à jour les ESLs en fournissant un fichier en entrée
        - [ ] ESL-150 Créer un bouton pour mettre à jour toutes les ESLs concernées par un changement depuis la page d' soumission de fichier entrée
        - [ ] ESL-151 Créer la logique back pour recharger toutes les ESLs
        - [ ] ESL-152 Créer la logique front pour recharger toutes les ESLs
        - [ ] ESL-156 Filtrer les données du fichier en entrée pour exclure la màj des store-articles pas liés à une ESL
        - [ ] ESL-157 Filtrer les données du fichier en entrée pour pointer à l'utilisateur les ESLs n'étant pas liées à un article