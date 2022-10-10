**10 Ocotbre**

Sur le réseau wifi prod : accès aux mails et serveur de prod / git push ssh impossible, connexion à discord impossible
Sur le réseau wifi guest : accès à discord & git push ssh /  connexion aux mails et serveur de prod impossible

*Dockerfile* : Lister les arguments multi-lignes par ordre alphanumérique permettra une relecture plus facile du fichier. Cela facilitera la maintenance du ficher et permet également d'éviter la duplication des packages.

Une barre oblique inverse \ permet également de revenir à la ligne.
Toujours combiner apt-get update et apt-get install dans la même ligne.
Utiliser apt-get update seul dans un RUN entrainera des problèmes de cache et de version de paquets.
Toujours supprimer le cache APT après votre installation. Cela réduira le poids de l'image car il ne sera pas stocké dans le layer. 
(https://gist.github.com/marvell/7c812736565928e602c4)
```
apt-get clean autoclean
apt-get autoremove --yes
rm -rf /var/lib/{apt,dpkg,cache,log}/
```