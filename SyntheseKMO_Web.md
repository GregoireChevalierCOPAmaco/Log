***Synthese KMO_Web***  
11 Octobre 2022  

KMO_Web est une application à vocation de gestion et suivi de SAV pour les interventions de caisses chez les clients tels que LIDL, Aldi, ...

Application Web
Application mobile

Stack Technique : 
- Angular
- Nest
- NodeJS - TypeScript
- Mysql
- Tailwind
- Docker
- Hébergement Amazon
- Doc API accessible sur le port 3000 via swagger une fois les containers lancés

Système d'authentification, puis redirection dashboard
Système de tickets par enseigne
Les tickets synthétisent les interventions et leurs IMS, feuilles d'intervention, et détails associés
Le ticket et son rapport d'intervention sont accessibles à Évelyne au SAV. Elle s'occupe de l'assignaiton aux techniciens, de la commande de pièces, du stock ...
L'application gère le suivi de la flotte de véhicule, répertoriés par plaque d'immatriculation, et leur réservation par les techniciens
L'application gère le suivi des stocks de pièces par véhicule
Flow différents selon le client (Lidl != Aldi qui comprend du bricolage dans le magasin en plus des interventions caisses)
!! En cas de tests de tickets template, envoyer sur l'adresse mail de Théo pour éviter notification au technicien !!
!! Ne pas mettre de pièce à commander dans les rapports tests car cela envoie une notification par mail au SAV !!

api, port 3000, doc, swagger = liste api
endpoint ?