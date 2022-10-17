*** LOG Gregoire Chevalier ***

**17 Octobre**
- [x] Résolution des problèmes de connexion WSL/Vscode
- [ ] Trouver les fichiers relatifs au déploiement aws
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
    - [ ] Observation de la branche "KW275_S3_API_back"
        - [x] Check du fichier KMO_WEB/.idea/inspectionProfiles/Project_Default.xml
        - [x] Check du fichier KMO_WEB/kmo-app/src/app/services/PhotoService/photo.service.ts
        - [x] Check du fichier KMO_WEB/kmo-app/README.md
        - [x] Check du fichier KMO_WEB/COP-SAV/src/app/core/interfaces/IReport.ts
        ```
        epiPath: string;
        rpsPath: string;
        ```
        - [x] Check du fichier KMO_WEB/COP-SAV/src/app/core/services/ReportService/report.service.ts idem que plus haut.
    - [ ] Trouver ce à quoi correspond le terme 'Bucket' dans le contexte d'aws
    - [ ] Trouver ce à quoi correspond le terme 'buffer' dans le contexte d'aws
    - [ ] Trouver ce à quoi correspondent les termes 'epiPath' & 'rpsPath' 
    - [ ] Voir les interactions avec les services aws
        - [ ] S3
        - [ ] LightSail
        - [ ] EC2
- [ ] Testing du projet
    - [ ] Aller voir (https://ionicframework.com/docs/angular/testing#general-testing-structure)
    - [ ] Définir les contraintes de test
    - [ ] Rechercher le framework de test le plus adapté aux problématiques du projet  
    - [ ] Réaliser des essais de tests
