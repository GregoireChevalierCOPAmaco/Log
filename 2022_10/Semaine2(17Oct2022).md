*** LOG Gregoire Chevalier ***

**17 Octobre**
- [x] Résolution des problèmes de connexion WSL/Vscode
- [x] Trouver les fichiers relatifs au déploiement aws
    - [x] Observation de la branche "KW273_upload_file_s3"
        - [x] Check du fichier KMO_WEB/.idea/inspectionProfiles/Project_Default.xml
        - [x] Check du fichier KMO_WEB/.idea/KMO_WEB.iml
        - [x] Check du fichier KMO_WEB/.idea/modules.xml
        - [x] Check du fichier KMO_WEB/COP-SAV/src/app/pages/cop/user/user.component.ts
        - [x] Check du fichier KMO_WEB/kmo-back/environements/mysql/local.env avec ajout des lignes :
        ```
        AWS_ACCESS_KEY_ID = xxxxxxxxxxxxxxxx
        AWS_SECRET_ACCESS_KEY = xxxxxxxxxxxxxx
        ``` 
        (ids et key masqués pour sécurité)
        - [x] Check du fichier KMO_WEB/kmo-back/src/reports/S3UploadFile.service.ts
        - [x] Check du fichier KMO_WEB/kmo-back/src/reports/reports.controller.ts (lien avec et utilisation du service S3uploadFile)
        - [x] Check du fichier KMO_WEB/kmo-back/src/reports/reports.module.ts (lien avec le service S3uploadFile et lien comme provider)
        - [x] Check du fichier KMO_WEB/kmo-back/src/reports/reports.service.ts (déclaration dans le constructeur, et utilisation dans le cadre d'awaits)
        ```
        let buffer = Buffer.from(signReportDto.signBase.split(",")[1], "base64");
        await this.fileService.upload(buffer);
        ```
    - [x] Observation de la branche "KW275_S3_API_back"
        - [x] Check du fichier KMO_WEB/.idea/inspectionProfiles/Project_Default.xml
        - [x] Check du fichier KMO_WEB/kmo-app/src/app/services/PhotoService/photo.service.ts
        - [x] Check du fichier KMO_WEB/kmo-app/README.md
        - [x] Check du fichier KMO_WEB/COP-SAV/src/app/core/interfaces/IReport.ts
        ```
        epiPath: string;
        rpsPath: string;
        ```
        - [x] Check du fichier KMO_WEB/COP-SAV/src/app/core/services/ReportService/report.service.ts idem que plus haut.
- [x] Trouver ce à quoi correspond le terme 'Bucket' dans le contexte d'aws
    - [x] Check de (https://www.techtarget.com/searchaws/definition/AWS-bucket)
    - [x] Détail du concept dans un retour de veille.
- [x] Trouver ce à quoi correspond le terme 'buffer' dans le contexte d'aws
    - [x] Détail du concept dans un retour de veille.
- [ ] Trouver ce à quoi correspondent les termes 'epiPath' & 'rpsPath' 
    - [x] Check de (https://docs.nestjs.com/recipes/crud-generator)
    - [x] Lancement des containers
    - [x] Positionnement dans le dossier kmo-back
        - [x] Lancement de la commande :
        ```
        nest g resource
        ```
        Et sélection des options :
        - exteriorContacts (nom)
        - API Rest
        - oui à la génération d'entrypoints
        Retour de console :
        ```
         nest g resource
        ? What name would you like to use for this resource (plural, e.g., "users")? exteriorContacts
        ? What transport layer do you use? REST API
        ? Would you like to generate CRUD entry points? Yes
        CREATE src/exterior-contacts/exterior-contacts.controller.ts (1129 bytes)
        CREATE src/exterior-contacts/exterior-contacts.controller.spec.ts (678 bytes)
        CREATE src/exterior-contacts/exterior-contacts.module.ts (326 bytes)
        CREATE src/exterior-contacts/exterior-contacts.service.ts (765 bytes)
        CREATE src/exterior-contacts/exterior-contacts.service.spec.ts (531 bytes)
        CREATE src/exterior-contacts/dto/create-exterior-contact.dto.ts (41 bytes)
        CREATE src/exterior-contacts/dto/update-exterior-contact.dto.ts (209 bytes)
        CREATE src/exterior-contacts/entities/exterior-contact.entity.ts (32 bytes)
        UPDATE src/app.module.ts (2615 bytes)
        ```
    - [ ] Survol des fichiers créés
    - [ ] Faire un comparatif des fichiers créés avec les fichiers user pour avoir une référence.
    - [ ] Ajout de la nouvelle table à la db. Migration ?
        - [ ] Check de (https://wanago.io/2022/07/25/api-nestjs-database-migrations-typeorm/)
- [ ] Voir les interactions avec les services aws
    - [ ] S3
    - [ ] LightSail
    - [ ] EC2
- [ ] Créer une ensemble entité-classe-component-service-persistance
- [ ] Testing du projet
    - [ ] Aller voir (https://ionicframework.com/docs/angular/testing#general-testing-structure)
    - [ ] Définir les contraintes de test
    - [ ] Rechercher le framework de test le plus adapté aux problématiques du projet  
    - [ ] Réaliser des essais de tests
