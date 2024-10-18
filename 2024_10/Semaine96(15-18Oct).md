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
    - [ ] ESL-153 Ajouter la propriété timestamp_rssi aux entités la nécessitant pour matcher le code de Jacques
        - [ ] Création de la propriété dans les entités
        - [ ] Rajout des mentions à timestamp_rssi dans les fichiers : 
            - [ ] 
            - [ ] 
            - [ ] 
            - [ ] 