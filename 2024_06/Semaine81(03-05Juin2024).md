**03 Juin**
- [ ] Poursuite projet ESLs
    - [ ] Créer un service de transformation d'image en base 64
        - [x] ```git checkout -b feat/ESL-8_create_base64_converter_service```
        - [x] Création du dossier services
        - [x] Création du fichier
        - [x] ```npm i```
    - [ ] Créer le component Angular d'import d'image
    - [x] Routing vers le component
        - [x] ```ng generate module app-routing --flat --module=app``` 
- [x] Test de l'app
    - [x] ```ng serve```retour : 
    ```
    Node.js version v18.12.1 detected.
    The Angular CLI requires a minimum of v18.13.

    Please update your Node.js
    ```
    - [x] Désinstallation de node
    - [x] Résinstallation de node lts
    - [ ] Résolution de l'erreur : 
    ```
    Error: NG04002: Cannot match any routes. URL Segment: 'image-converter'
    ```