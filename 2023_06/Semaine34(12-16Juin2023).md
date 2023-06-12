**9 Juin**
- [ ] Mise en prod
    - [x] Connexion sur AWS & run de l'instance test i-0ff387a4c39282058
    - [x] Login à l'instance avec :
    ```
    ssh -i "Keycloak.pem" ec2-user@ec2-3-70-202-53.eu-central-1.compute.amazonaws.com
    ```
    - [x] Check de l'espace disque restant dans l'instance : ```df -h``` , retour console : 
    ```
    [ec2-user@ip-172-31-29-175 ~]$ df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        4.0M     0  4.0M   0% /dev
    tmpfs           475M     0  475M   0% /dev/shm
    tmpfs           190M  2.8M  188M   2% /run
    /dev/xvda1      8.0G  4.6G  3.4G  58% /
    tmpfs           475M     0  475M   0% /tmp
    tmpfs            95M     0   95M   0% /run/user/1000
    ```
    - [x] Finir l'install des projets sur le serveur avec npm i 
        - [x] Front 
        - [x] Back
        - [x] Racine
    - [ ] Configurer Keycloak pour la prod
    - [ ] Lancer le tout combiné