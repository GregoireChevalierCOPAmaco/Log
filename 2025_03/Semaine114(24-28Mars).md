- [ ] Retour de vacances
    - [x] Nettoyage du poste de travail
    - [x] Check des mails et tri
    - [x] Catchup Discord
    - [x] Replongée dans le projet
    - [x] Reprise des commits & PRs
    - [x] Prise de connaissance du figma
    - [x] Passage aux RHs pour infos paternité
    - [x] Reprise des tickets
- [ ] ESL
    - [x] Repasse sur les nouveaux tickets avec Anthony
    - [ ] PR ESL-272 fix storeArticles searchbar
    - [ ] ESL-267 Filtrer les ESLs de la table par associées ou non au clic d'un toggle
        - [x] Comprendre pourquoi le clic de la checkbox n'a aucune influence : 
        après ajout de logs, lignes 196&199 de l'esl-table component, il semble que la checkbox ne modifie jamais le ```this.showOnlyAssociatedEsls```en true
- [ ] Réunion ESL
```
synchronisation action utilisateur-retour UI : 
- retour de franck négatif sur le manque d'info de ce qui se passe une fois qu'on a appuyé sur un bouton. Faire une animation qui tourne pour dire qu'il se passe quelque chose ? Ajouter une user story "Retour UI des actions utilisateurs"
- impossible atm de changer un produit par un autre ; inhérent au bug de dissociation esl-storeArticle
- les ESLs 5'8 & 7'5 n'affichent pas la même image disabled que les autres. A voir
- harmoniser les uppercase & lowercase dans l'app mobile
- mettre un hover & un tooltip sur les modales du dashboard
- ajouter des tooltips au dessus des boutons d'action de la table des storeArticles
- ajouter les infos nombre en rayon, nombre en stock et les display dans le template de l'esl (à terme, développer un connecteur)
- intégrer les notions de champs optionnels dans le csv 
- par style, afficher toutes les dimensions et séparer en deux groupes les templates existants pour les dimensions et ceux créables pour les dimensions qui n'en ont pas encore
- effort à faire sur comment on peut valider 1000 styles/templates/générations d'images via csv plutôt qu'essayer de faire un style qui va fit à 1000 ESLs
- 
```

REFAIRE LE JIRA


- [x] Réunion AWS ERP
```
but : faire suite à la demande de revue d'architecture
Yasmine boutin, architecte chez aws
Dernière réunion avec franck ; sujets evoqués : projets, erp, etc
Description des projets existants
question de l'upscaling, automatiquement ça n'existe pas, mais il existe des scripts pour allouer plus de mémoire
demande anthony ; upscale pour augmenter l'instance aux pics d'utilisations
si possibilité de regrouper les gateway (adresage ip) alors on peut contenairiser pour aller taper sur un sous groupe qui se déploierait sur une autre instance ? 
cognito : gestion de couche d'auth par dessus une application sur EC2 ou amplify (host app web simple)
ils ont un keycloak-like
ECS (pourrait aider sur plusieurs aspects), service de contenairs managés. Selon elle, on gagnerait beaucoup à monter en compétence dessus. Le prochain 
ecsworkshop.com
ECS permet de deployer des clusters de docker
loadbalancer/api gateway/cognito
Immersion Days (virtuellement ou sur place à la defense) 1/2 ou journée sur un service particuler. Parties théorique + pratique. Gratuit et sans limite, pas d'obligation de participer à toute la journée.
Activation Days, sensiblement différent
docker facilement transportable sur d'autres projets
suggestion de montée en compétence sur les services suivants : 
https://ecsworkshop.com/
https://ecsworkshop.com/
- amplify
- self service KYC Amplify Amazon Ai (https://catalog.us-east-1.prod.workshops.aws/workshops/2b414187-7e55-4729-a998-c698d9429876/en-US)
- cognito / JWT (https://catalog.workshops.aws/wyld-pets-cognito/en-US)
- 
Replanifier une réunion une fois qu'on s'est informés pour reparler des solutions existantes à notre disposition
Summit AWS, topics suggérés : 
-
-
-

point après le summit pour debrief le summit & parler architecture
envoyer un mail a yasmine quand on a fait notre planning pour le summit


résumé réunion : 
la consultante compétente a pas été mise au courant du déroulé. On a passé la première demi heure à réexpliquer les projets et leur état respectifs.
La consultante nous a orientés vers des services qui peuvent nous être utiles
on va planifier nos sessions au summit elle va nous aiguiller, on la verra aussi sur place
RDV apres le summit pour discuter de l'architecture en fonction des services qu'on a étudié
```

- [ ] ESL
    - [ ] ESL-267 Filtrer les ESLs de la table par associées ou non au clic d'un toggle
        - [x] Comprendre pourquoi le clic de la checkbox n'a aucune influence : 
        après ajout de logs, lignes 196&199 de l'esl-table component, il semble que la checkbox ne modifie jamais le ```this.showOnlyAssociatedEsls```en true
        - [x] Checkbox réagit comme attendu mais la table ne display pas juste les ESLs associées.
        - [ ] Appel de  this.getStylesForEsls(); dans filterEsls dans filterEsls :
        ```
        filterEsls(): void {
            if (this.showOnlyAssociatedEsls) {
            this.eslService.getEslsActive().subscribe((data) => {
                if (Array.isArray(data)) {
                this.filteredEsls = data;
                this.getStylesForEsls();
                } else {
                console.error(data);
                }
            }, (error) => {
                console.error(error);
            });
            } else if (this.searchTerm.trim() !== '') {
            this.eslService.getEslspaginate(1, 10, this.searchTerm.toLowerCase()).subscribe((data) => {
                if (Array.isArray(data.items)) {
                this.filteredEsls = data.items;
                this.getStylesForEsls();
                } else {
                console.error(data.items);
                }
            }, (error) => {
                console.error(error);
            });
            } else {
            this.eslService.getEslspaginate(this.currentPage, 10, ' ').subscribe((data) => {
                if (Array.isArray(data.items)) {
                this.filteredEsls = data.items;
                this.getStylesForEsls();
                } else {
                console.error(data.items);
                }
            }, (error) => {
                console.error(error);
            });
            }
        }
        ```
    - [x] ESL-282 Appliquer un style par défaut à l'initialisation des ESLs
        - [x] esl service back, fonction async fetchAndUpdateData() : 
        ```
        // Create the Default style if not already existing
                        let defaultStyle = await this.styleService.getStyleByName('Default');
                        if (!defaultStyle) {
                          defaultStyle = await this.styleService.create({ name: 'Default' });
                        }

                        // Assign the Default style to the ESL
                        esl.style = defaultStyle;
                        await this.eslRepository.save(esl);
        ```
    - [ ] ESL-283 Ajouter la concaténation du sigle de devise après le prix dans la création de template
        - [x] entité storeArticle, ajout de la property currency
        ```
        @Column({ nullable: true, type: 'varchar', length: 10 })
        currency: string; //Varchar 10 to support € $ signs
        ```
        - [x] html, ajout de :
        ```
         <div class="mt-2">
            <p class="text-2xl">Devise</p>
            <div class="ml-2">
                <label for="currencySelect">Devise :</label>
                <select id="currencySelect" (change)="changeCurrency($event)">
                <option value="€">€</option>
                <option value="$">$</option>
                <option value="£">£</option>
                <option value="¥">¥</option>
                </select>
            </div>
        </div>
        ```
        - [x] angular component
        ```
        ...
        selectedObject: any = null;
        selectedColor: string = '#000000'; // Couleur par défaut : noir
        selectedCurrency: string = '€';
        public predefinedFields = [
            { text: '$price$', left: 100, top: 100, isPredefined: true },
            { text: '$currency$', left: 140, top: 100, isPredefined: true },
            // { text: '$barcode$', left: 0, top: 0, isPredefined: true },
        ];
        ...
        public changeCurrency(event: any): void {
            this.selectedCurrency = event.target.value;
            this.updateCurrencyOnCanvas();
        }

        private updateCurrencyOnCanvas(): void {
            const objects = this.canvas.getObjects();
            objects.forEach((obj) => {
            if (obj.type === 'i-text' && (obj as fabric.IText).text === '$currency$') {
                (obj as fabric.IText).set('text', this.selectedCurrency);
            }
            });
            this.canvas.renderAll();
        }
        ```
        - [x] update article dto 
        ```
        @ApiProperty()
        currency: string;
        ```
        - [x] create article DTO
        ```
        @ApiProperty()
        currency: string;
        ```
        - [x] storeArticle service backend, ajout des properties currency dans create & update et changement de eslTemplateManagement
        ```
        ...
        async eslTemplateManagement(storeArticle: StoreArticle) {
        ...
        let priceFieldFound = false;
        // const barcodeFieldFound = false;
        let currencyFieldFound = false;

        const fabricCanvas = new StaticCanvas(null);
        fabricCanvas.setDimensions({ width: template.width, height: template.height });
        fabricCanvas.backgroundColor = '#FFFFFF';

        for (const obj of template.objects) {
            if (obj.type === 'IText') {
            if (obj.text === '$price$') {
                obj.text = storeArticle.price.toString();
                console.log('Price updated in object:', obj.text);
                priceFieldFound = true;
            } else if (obj.text === '$currency$') {
                obj.text = storeArticle.currency.toString();
                console.log('Currency updated in object:', obj.text);
                currencyFieldFound = true;
        ...
        ```
        - [x] esl controller test file, changement du mock
        ```
        describe('EslController', () => {
        let controller: EslController;
        let service: EslService;

        const mockStoreArticle: StoreArticle = {
            id: 1,
            name: 'Article 1',
            price: 100,
            bar_code: '123456789',
            origine: 'France',
            price_per_kilo: 1,
            updatedAt: new Date(),
            esl: null,
            currency: '€'
        };
        ```
        - [x] storeArticle service testfile : rajout de , currency: '€' partout où il y en a besoin
        ```
        ```
        - [x] storeArticle controller testfile : rajout de , currency: '€' partout où il y en a besoin
        ```
        ```
        - [x] Run du lint
        - [x] Commit, push & PR
    - [x] Remise en marche du setup base-ESLs
        - [x] Brancher les bases
        - [x] Trouver les adresses IP (ip variables)
            - [ ] installer nmap GUI
            - [ ] target : 192.168.1.0/24
            - [ ] ping scan dans le dropdown
            - [ ] chercher Dongguan et aller à l'ip renseignée par scan report
            NE PAS INTENSIVE SCAN sous peine d'alerter toute la boite de risque d'attaque malveillante
            sniffer n'est pas autorisé
            sniffer c'est mauvais pour la santé
    

    FAIRE UNE WISHLIST des besoins réseau pour mercredi (machines de tests, pas pcs)