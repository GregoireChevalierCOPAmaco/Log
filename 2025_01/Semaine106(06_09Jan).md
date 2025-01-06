**06 Janvier**
- [ ] ESL
    - [ ] Assessment du projet à la reprise
        - [x] Check des containers
            - [x] Le back a crash, trouver pourquoi et depuis quand
            - [x] utilisation des logs avec le flag -t pour les timestamps
            ```
            sudo docker logs esl_api -t
            ```
            - [x] Origine de l'erreur : 
            ```
            2024-12-27T23:08:20.223116047Z Error sending image to ESL: 
            Error communicating with ESL service(updateOnEsl())
            2024-12-27T23:08:20.229677372Z
            2024-12-27T23:08:20.229697489Z /app/src/esl/esl.service.ts:200
            ```
    - [ ] ESL-237 Réparer la génération du code barre depuis $barcode$ dans le template