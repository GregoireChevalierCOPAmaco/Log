*** LOG Gregoire Chevalier ***

**10 Octobre**
- [x] Prise en main du matériel
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
    - [x] Installation de docker desktop
        - [x] Check de (https://docs.docker.com/desktop/windows/wsl/#:~:text=The%20docker%20CLI%20and%20UI,both%20v1%20or%20v2%20mode.)
        - [x] Téléchargement et installation du Linux kernel update package.
        - [x] Link de docker cli dans ubuntu : use wsl2 engine via docker desktop
    - [x] Installation de flux
- [x] Check de (https://www.docker.com/blog/announcing-docker-sbom-a-step-towards-more-visibility-into-docker-images/) Article docker sur la transparence des images : sbom
- [ ] Création d'un projet template docker pour push ssh
    - [x] Création du repo
    - [x] Création de clé ssh ubuntu
    - [x] Création de clé ssh windows
    - [ ] Début du projet docker
        - [x] Commande : 
        ```
        docker run -d -p 80:80 docker/getting-started
        ```
- [ ] Survol des projets en cours sur le github https://github.com/COP-AMACO
- [x] Veille techno dockerfile
    - [x] Check de (https://www.silicon.fr/31-bonnes-pratiques-securiser-conteneurs-docker-163501.html)

**11 Octobre**
- [ ] Immersion dans le projet KMO_Web
    - [x] Check du projet avec Anthony
    - [x] Synthèse écrite
    - [ ] Check du projet KMO_Web sur Github
        - [x] Check de la branche fix
        - [x] Check de la branche develop
            - [x] Prise en compte du readme
        - [ ] Check de la branche connect-to-IR
        - [ ] Pull en local de la branche connect-to-IR
        - [ ] Installations requises
- [ ] Installation d'Angular
- [ ] Installation de TypeScript
- [ ] Installation de Tailwind