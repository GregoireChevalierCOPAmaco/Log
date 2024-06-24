**Réunion COPACO 24 Juin**

Synchro des commandes clients → manuel
Quand on fait des mouvements de stocks dans copaco ça se fait aussi dans clipper.
Il y a plusieurs synchros à plusieurs endroits
Copaco lancé en prod que pour hornbach, et la synchro du CBN se fait via la moulinette de Fédé :
   - fichier excel qui se connecte à clipper et qui va regarder tous les enfants d'une commande et déterminer en fonciton des derniers trim/semestres la consommation mensuelle des pièces nécessaires pour toujours avoir un stock minimum et ne pas être en rupture
   - la moulinette de Fédé se connecte à clipper et à Copaco
   - basé sur l'historique des ventes et les commandes actuelles
Dans le cas où Denis va faire de la merde, comment on va justifier que ça vient de lui et pas de nous ? A priori, voir Fédé et vérifier le trace des mouvements faits par qui et quand.
En cas de commande non validée à chaque étape, mais déjà expédiée, comment on fait ? Tout valider de force fausserait les quantités de stocks

Suivi de commandes dans clipper : 
-

états de commandes : 
devis 
commande (généré mais pas prêt)
fabrication
soldé (expédié)


comment savoir où est le code concerné lorsqu'il y a un souci ; 
aller voir le fichier ajax qui gère la page en question, mais des fois y a pas d'ajax
bonne chance
$pref dans les fichiers indique l'arborescence (préfixe), et ramène à la racine
quand il y a inventaire en paramètre, ça implique de ne pas tenir compte de la qté existante et de repartir de la nouvelle valeur
michel est un sac à merde
A.R.C = Accusé-Réception de Commande
