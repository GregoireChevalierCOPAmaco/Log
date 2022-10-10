*** LOG Gregoire Chevalier ***

**10 Octobre**
- [ ] Prise en main du matériel
    - [x] Changement du mot de pass utilisateur windows
    - [x] Installation du setup Git
        - [x] Installation de gitSK 2.38 64k
        - [x] Création du profil Github
        - [x] Création du repo log
        - [x] Premier push
    - [x] Installation de discord
        - [x] Création du compte discord
    - [x] Installation de Linux
        - [x] Check de (https://learn.microsoft.com/fr-fr/windows/wsl/install)
        - [x] Dans powershell admin, utilisation de la commande 
        ```
        wsl --install -d Ubuntu-20.04
        ```
        - [x] Paramétrage d'Ubuntu
            - [x] Unix username : gregoirechevaliercopamaco
            - [x] Unix pw : procédure habituelle
    - [x] Installation du plugin wsl dans vscode
    - [x] Installation de vscode dans linux WSL (automatique au lancement de wsl via vscode)
    - [x] Vérifier les accès à Github via linux WSL (ssh/?) (https ok)
        - [x] Configuration ubunut-git :  git config --global user.email "you@example.com" / git config --global user.name "Your Name"
    - [x] Installation de Postman
        - [x] Téléchargement
        - [x] Configuration
        - [x] Validation email
    - [x] Intégration au github copamaco
    - [x] Installation de node
        - [x] Check de (https://www.educative.io/answers/how-to-install-nodejs-on-ubuntu)
        - [x] Installation des paquets avec 
        ```
        sudo apt-get install curl
        curl -sL https://deb.nodesource.com/setup_16.17.1 | sudo -E bash -
        sudo apt-get install nodejs
        ```
        - [x] Résolution de l'erreur "E: Unable to locate package npm avec"
        ```
        apt-get update
        sudo apt-get install nodejs
        ```
    - [ ] Installation de docker desktop
    - [ ] Installation de docker cli dans ubuntu