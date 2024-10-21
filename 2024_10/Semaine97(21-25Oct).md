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
        - [ ] ESL-149 Extraire les données du fichier entrée
        - [ ] ESL-150 Créer un bouton pour mettre à jour toutes les ESLs concernées par un changement depuis la page d' soumission de fichier entrée
        - [ ] ESL-151 Créer la logique back pour recharger toutes les ESLs
        - [ ] ESL-152 Créer la logique front pour recharger toutes les ESLs
        - [ ] ESL-156 Filtrer les données du fichier en entrée pour exclure la màj des store-articles pas liés à une ESL
        - [ ] ESL-157 Filtrer les données du fichier en entrée pour pointer à l'utilisateur les ESLs n'étant pas liées à un article