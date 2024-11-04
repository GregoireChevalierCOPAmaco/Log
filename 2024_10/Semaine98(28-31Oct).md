**29 Octobre**
- [ ] ESL
    - [ ] ESL-147 Mettre à jour les ESLs en fournissant un fichier en entrée
        - [ ] ESL-152 Créer le front pour recharger toutes les ESLs
            - [ ] Merge de la branche 133 dans ma 147   
                - [x] ```git pull origin ESL-133```   
                - [ ] Résolution des conflits
            - [ ] Créer la logique front pour recharger toutes les ESLs
                - [x] Voir la doc fabric pour les objets JSON, regarder le champ text $price$ et le changer
                (récupérer les infos en appelant les services depuis le front plutot que d'utiliser http)
                - csv : récupération du prix & barcode
                - barcode : récupération du store_article
                - store_article : récupération de l'ESL via liaison
                - esl_id : créer une route GET template by eslId
                - template : modifier le champ prix
                - générer l'image
                - transformer en base64 & envoyer
                - [ ] Modifier trackChangesAndCollectData pour récupérer le templateId
                - [ ] Récupérer le template en question
                - [ ] Créer la methode update price dans les templates, prenant en paramètre d'entrée le templateId & le nouveau prix, pour générer l'image
                - [ ] Générer l'image, utiliser le service base64 et envoyer à l'ESL
                - [ ] développer la logique de création de template d’après les infos récupérées du csv
                - [ ] développer l' esl.service.ts frontend et créer sa méthode update (voir avec template/update & la conversion en base 64)
            - [ ] ESL-151 Créer la logique back pour recharger toutes les ESLs
                - [ ] Modifier l'update-esl.dto
                - [ ] développer l' esl.service.ts backend et créer sa méthode update
        - [ ] ESL-159 Gérer le rechargement en masse des ESL (charge)


**30 Octobre**
- [ ] ESL
    - [ ] ESL-147 Mettre à jour les ESLs en fournissant un fichier en entrée
        - [ ] ESL-152 Créer le front pour recharger toutes les ESLs
            - [ ] Merge de la branche 133 dans ma 147   
                - [x] ```git pull origin ESL-133```   
                - [ ] Résolution des conflits
            - [ ] Créer la logique front pour recharger toutes les ESLs
                - [x] Voir la doc fabric pour les objets JSON, regarder le champ text $price$ et le changer
                (récupérer les infos en appelant les services depuis le front plutot que d'utiliser http)
                - [x] csv : récupération du prix & barcode
                - [x] barcode : récupération du store_article
                - [x] store_article : récupération de l'ESL via liaison
                - [x] esl_id : créer une route GET template by eslId
                - [x] template : modifier le champ prix
                - [ ] Générer l'image, utiliser le service base64 et envoyer à l'ESL
                - [ ] transformer en base64 & envoyer
            - [ ] Tout refaire sur le back 
                - [ ] Enoyer les données du csv dans le back
                - [ ] Reprendre la logique dans le back 