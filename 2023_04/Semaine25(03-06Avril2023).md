**3 Avril**
- [x] Re check billets aws summit
- [x] Transfert billets train mail perso
- [ ] Poursuite keycloak
    - [ ] KP-139 : Suppression d'un magasin
        - [x] Ajout du code back & front
            - [x] Front : 
                - [x] administration.component.ts :
                ```
                deActivateStore(){
                    console.log("deActivateStore() called");
                    
                    const targetStore: StoreInterface = { ...this.store, isstoreactive: false };
                    console.log(targetStore);
                    this.storeService.updateStore(this.storeId[0], targetStore).subscribe({
                    next: (data) => {
                        console.log('Store deactivated successfully!');
                    },
                    error: (err) => console.log(err),
                    });
                }
                ```
                - [x] store.interface.ts :
                ```
                ...
                isstoreactive: boolean;
                ...
                ```
                - [x] administration.component.html :
                ```
                <div class="example-button-row">
                    <button mat-stroked-button (click)="deActivateStore()"> Désactiver le magasin</button>
                </div>
                ```
            - [x] Back : 
                - [x] update-store.dto.ts :
                ```
                    @ApiProperty()
                    @IsString()
                    id: string;

                    @ApiProperty()
                    @IsString()
                    @Length(0, 30)
                    address: string;
                
                    @ApiProperty()
                    @IsString()
                    @Length(0, 20)
                    city: string;
                
                    @ApiProperty()
                    @IsEnum(BrandEnum)
                    brand: BrandEnum;
                
                    @ApiProperty()
                    @IsPostalCode('FR')
                    postalCode: string;
                
                    @ApiProperty()
                    @IsBoolean()
                    isStoreOpen: boolean;
                
                    @ApiProperty()
                    @IsNumber()
                    @Min(1, {
                    message: 'Le magasin doit avoir au moins 1 caisse',
                    })
                    numberOfCheckouts: number;
                
                    @ApiProperty()
                    openingHours: Record<Day, Hours>;
                
                    @ApiProperty()
                    @IsMACAddress()
                    gateway: Gateway;
                    
                    @ApiProperty()
                    @IsBoolean()
                    isstoreactive: boolean;
                ```
                - [x] store.entity.ts :
                ```
                    @Column()
                    isstoreactive: boolean;
                ```
        - [x] Requête ok, mais erreur 400
        - [ ] Résolution du problème de 400 lors de l'update : 
        ```
        HttpErrorResponse {headers: HttpHeaders, status: 400, statusText: 'Bad Request', url: 'http://localhost:3001/stores/9de16383-c4d8-4616-bc4c-a26795f286ae', ok: false, …}
        error
        : error
        : "Bad Request"
        message
        : Array(2)
        0
        : "property openingHours should not exist"
        1
        : "gateway must be a MAC Address"
        length
        : 2
        [[Prototype]]
        : Array(0)
        statusCode
        : 400
        ...
        ```
        pour référence
            - [x] Ajout dans leupdate-store.dto.ts ligne 52 de :
            ```
            @IsNotEmpty()
            ```
            && ligne 56 :
            ```
            @IsDefined()
            ```
            - [x] Suppression du décorateur ```@IsMACAddress()``` ligne 58 du fichier update-store.dto.ts
            - [x] Changement de ```patch``` en ```put``` dans les fichiers store.service.ts && store.controller.ts respectivement aux lignes 31 & 46
        - [x] Lier le bouton suppression à la suppression dans le back
        - [x] Associer la désactivation du store en db & l'affichage des stores dans le front
        - [x] Afficher tous les stores par ligne et proposer un bouton suppression au bout de la ligne
        - [x] Afficher tous les stores par ligne et proposer un bouton réactivation au bout de la ligne
            - [x] Cacher l'affichage du bouton desactiver en cas de boolean désactivé
                - [x] Set la variable isStoreActive en fonction de la réponse
                - [ ] Lier la ligne affichant chaque store à l'id correspondant : actuellement c'est l'id du storeId de l'attribut de l'user connecté qui est pris
            - [ ] Refactorer le code avec le service de store existant
            - [ ] Lier l'état isActive du store à son bouton associé dans la page administration
        - [ ] Écriture des tests
        - [ ] Restriction de l’accès à la page aux users ayant le rôle (pas nécessaire, la deletion (désactivation) ne sera accesible qu'aux devs cop)
- [x] mdp orange business ARD : procédure habituelle + orangebusiness
- [ ] Accéder au visionnage des lignes en cours chez Orange
- [ ] Reprise projet vierge & pull branche théo pour voir