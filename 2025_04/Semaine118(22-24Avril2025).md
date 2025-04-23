**23 Avril**
- [ ] ESL
    - [ ] ESL-292 Créer une icône batterie faible sur le coin à droite des ESL
        - [x] Question a voir avec l'equipe : vaut-il mieux décomposer les services de 500+ lignes en plusieurs petits services ou pas ?
        - [ ] Génération de l'icone lowbattery :
         ```
         private async generateLowBatteryIcon(width: number, height: number): Promise<string> {
            const canvas = createCanvas(width, height);
            const ctx = canvas.getContext('2d');
        
            if (ctx) {
            ctx.fillStyle = 'white';
            ctx.fillRect(0, 0, width, height);
            ctx.fillStyle = 'red';
            ctx.font = '16px Arial';
            ctx.fillText('LOW', 5, 15);
        
            const iconImage = canvas.toDataURL('image/png');
            const base64String = iconImage.split(',')[1];
            console.log(base64String);
            
            return base64String;
            }
        
            throw new Error('Failed to generate low battery icon');
        }
        ```
        - [ ] cron & update : 
        ```
        @Cron(CronExpression.EVERY_5_MINUTES)
        async handleLowPowerEsls() {
            console.log("handleLowPowerEsls");
            
            try {
            const lowPowerEsls = await this.eslRepository.find({
                where: { power: LessThan(20) },
            });

            console.log("lowPowerEsls : ");
            console.log(lowPowerEsls);
            

            for (const esl of lowPowerEsls) {
                await this.updateEslWithLowBatteryIcon(esl);
            }
            } catch (error) {
            console.error('Error handling low power ESLs:', error);
            }
        }

        async updateEslWithLowBatteryIcon(esl: Esl) {
            console.log("updateEslWithLowBatteryIcon");
            
        try {
            const template = await this.templateService.getTemplateByEslId(esl.id_esl);
            if (!template || !Array.isArray(template.objects) || template.objects.length === 0) {
            console.warn(`Template not found or invalid for ESL ID ${esl.id_esl}`);
            return;
            }

            const fabricCanvas = new StaticCanvas(null);
            fabricCanvas.setDimensions({ width: template.width, height: template.height });
            fabricCanvas.backgroundColor = '#FFFFFF';
            fabricCanvas.loadFromJSON(template.objects, () => {
            this.generateLowBatteryIcon(20, 20).then((lowBatteryIcon) => {
                const iconImage = new Image();
                iconImage.src = lowBatteryIcon;
                iconImage.onload = () => {
                const ctx = fabricCanvas.getContext();
                ctx.drawImage(iconImage, 0, 0, 20, 20);

                const updatedImage = fabricCanvas.toDataURL({
                    format: 'png',
                    multiplier: 1, 
                });

                this.httpService
                    .post('http://mqtt_api:5002/esl_set_image', {
                    id_esl: esl.id_esl,
                    image: updatedImage,
                    })
                    .toPromise()
                    .then(() => {
                    console.log(`Updated ESL ${esl.id_esl} with low battery icon`);
                    })
                    .catch((error) => {
                    console.error(`Error updating ESL ${esl.id_esl} with low battery icon:`, error);
                    });
                };
            });
            });
        } catch (error) {
            console.error(`Error updating ESL ${esl.id_esl} with low battery icon:`, error);
        }
        }
        ```
        - [x] Rebuild du docker back : crash ave erreur : 
        ```
        Error: Package subpath './*' is not defined by "exports" in /app/node_modules/fabric/package.json
        ```
        dû à l'import : 
        ```
        import { StaticCanvas } from 'fabric/*';
        ```
        Résolution avec ```import * as fabric from 'fabric';```
        pour référence, la suggestion :
        ```
        import { fabric } from 'fabric';
        ```
        ne fonctionne pas
        - [x] Refonte de l'update pour fit le nouvel import : 
        ```
        async updateEslWithLowBatteryIcon(esl: Esl) {
        try {
            const template = await this.templateService.getTemplateByEslId(esl.id_esl);
            if (!template || !Array.isArray(template.objects) || template.objects.length === 0) {
            console.warn(`Template not found or invalid for ESL ID ${esl.id_esl}`);
            return;
            }

            const canvas = new fabric.Canvas(null, { width: template.width, height: template.height });
            canvas.backgroundColor = '#FFFFFF'; // Set background color directly

            // Load the existing template
            const objects = await fabric.util.enlivenObjects(template.objects);
            objects.forEach((obj) => {
            if (obj instanceof fabric.Object) {
                canvas.add(obj);
            }
            });

            // Generate the low battery icon
            const lowBatteryIcon = await this.generateLowBatteryIcon(20, 20);
            const img = await fabric.Image.fromURL(lowBatteryIcon);
            img.scaleToWidth(20);
            img.scaleToHeight(20);
            canvas.add(img);

            // Render the canvas to a base64 image
            const updatedImage = canvas.toDataURL({
            format: 'png',
            multiplier: 1, // Add the required multiplier property
            });

            // Update the ESL with the new image
            await this.httpService
            .post('http://mqtt_api:5002/esl_set_image', {
                id_esl: esl.id_esl,
                image: updatedImage,
            })
            .toPromise()
            .then(() => {
                console.log(`Updated ESL ${esl.id_esl} with low battery icon`);
            })
            .catch((error) => {
                console.error(`Error updating ESL ${esl.id_esl} with low battery icon:`, error);
            });
        } catch (error) {
            console.error(`Error updating ESL ${esl.id_esl} with low battery icon:`, error);
        }
        }
        ```
- [x] Réunion mensuelle des projets digitaux
    - [x] Bureau connecté : Frank mentionne un problème d'isolement dans le boîtier qui le fait exploser ou leak du 220V.
    Voir pour mettre sur pied un premier jet physique pour appuyer la com'
    - [x] Migrations en interne : Clipper, migration sur la nouvelle version. Le process galère un peu
                                : 4cad, 2e étape, tout se passe plutôt bien (dev sous windchill pour automatiser le transfert de données fichiers entre B.E & méthodes)
    - [x] Délégation à Jacque pour assister au lancement de la sliding gate ITAB dans un magasin auchan
    - [x] Guillaume
        - [x] Travaille pour automatiser le systeme ATMOS
        Objectif de parser & convertir les catalogues NORMA en annonces audio via I.A. (utilisation de cohérence cyclique)
        - [ ] Voir avec kévin pour marketer la solution
        - [x] Videosurveillance des caisses, devis en cours par pascal
        - [x] FLEG, mettre des écrans & écrans ESLs
    - [x] Anthony
        - [x] Nayax, bloqué pour cause de doc inexistante et code d'exemple non fonctionnel. Le boîtier semble être ok.
        - [x] Mise en place de la licence developpeur Apple. Vérification de l'entreprise par la firme pour voir si on est éligible. En attente.
        - [x] ESLs : refonte complète de l'app mobile sur le flow et le visuel. Egalement refacto partielle du code pour être à jour. On arrive plutôt sur la fin 
    - [x] Moi
        - [x] ESLs : Résumé des bugfix & fonctionnalités, puis planning futur avec le visuel pour tendre vers le figma
        - [x] ESLs : refacto des services ? Peut valoir le coup d'utiliser un outil pour déterminer le cout en performance du passage dans un gros fichier par rapport à un plus petit.
    - [x] Kévin
        - [x] Bureau connecté. Une précommande, part en espagne, sinon rien d'autre. Beaucoup d'impressions sur la campagne marketing web, 42k impressions pour 883 clics soit ~2.14% pas mal
    - [x] Paul
        - [X] Flasher, OK, attente de la licence Apple
        - [X] App bureau connecté, attente de la maquette figma
    - [x] Jacques
        - [x] UI interface sur l'écran du bureau connecté, ajout de toasters
        - [x] Moteur du bureau connecté. Marche mieux mais tests de fiabilité à faire (contrôle du moteur)
        - [x] Radar pour cabine decathlon