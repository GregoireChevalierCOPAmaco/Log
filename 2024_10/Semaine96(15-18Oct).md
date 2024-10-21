**15 Octobre**
- [ ] ESL
    - [ ] ESL-142 Fix l'erreur AxiosError: Request failed with status code 500 et le crash du back au clic de la désactivation de l'ESL
        - [ ] ESL-146 Empêcher le crash du back lors de la levée de l'erreur Axios 500
            - [x] Implémentation de try-catches dans les services backend
                - [X] EslService
                - [x] StoreArticlesService
                - [x] TemplateService 
            - [x] Gestion des erreurs potentielles pour updateOnEslAnd & updateOnEslAndDisable dans le TemplateService : 
            ```
            ... const response = await this.httpService.post('http://mqtt_api:5002/esl_set_image', {
                id_esl: id_esl,
                image: updateTemplateDto.picture,
            }).toPromise();
        
            if (response.status !== HttpStatus.OK) {
                throw new HttpException('Failed to send image to ESL(updateOnEsl())', HttpStatus.INTERNAL_SERVER_ERROR);
            }
        
            return response.data;
            } catch (e) {
            if (e.isAxiosError) {
                throw new HttpException('Error communicating with ESL service(updateOnEsl())', HttpStatus.BAD_GATEWAY);
            }
            throw new HttpException(e.message || 'Error updating ESL(updateOnEsl())', HttpStatus.INTERNAL_SERVER_ERROR);
            }
            ```

**18 Octobre**
- [ ] ESL
    - [x] ESL-153 Ajouter la propriété timestamp_rssi aux entités la nécessitant pour matcher le code de Jacques
        - [x] Création de la propriété dans les entités
        - [x] Rajout des mentions à timestamp_rssi dans les fichiers : 
            - [x] EslService
            - [x] EslEntity
            - [x] GetUpdateEslDTO
            - [x] StoreArticleEntity
            - [x] StoreArticleService test file
            - [x] StoreArticleController test file
        - [x] Rebuild des containers en dev
        - [ ] Rebuild des containers en prod
        - [ ] Supprimer les volumes non utilisées sur docker depuis docker23 : 
        ```
        docker volume prune --filter all=1
        ```
- [ ] Regarder s'il est possible d'ouvrir sur tablettes des plans 3D (step, stl & autres), voir si les monteurs / la prod / l'atelier peuvent ouvrir creo view sur pc / creo view express pour afficher des plans en décochant le sparties non souhaitées et voir le transfert de fichiers depuis pc jusqu'à tablettes (voir avec 4cad aussi ?)
    - [x] Le mdp des tablettes est bonetto en chiffres, soit 2663886