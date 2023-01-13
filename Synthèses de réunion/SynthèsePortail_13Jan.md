**Synthèse Portail Global**
  
    
*13 Janvier*
L'objectif de la réunion était de me mettre en tête l'idée générale derrière la construction du projet digital :
Avoir un portail d'accès général sous forme de point d'entrée login qui donne accès *selon qui est connecté* à :
- KMO Vidéo
    - Mon magasin
    - Administration des utilisateurs de l'app
- ATMOS
    - Mon magasin
    - Administration des utilisateurs de l'app
- FLEG
- KMO Assist
    - Mon magasin
    - Administration des utilisateurs de l'app
- Portail d'aministration COP
    - Administration des utilisateurs du protail global

Reste à voir si le keycloak servira à supporter toute la partie administration, gestion des rôles, utilisateurs, mailing & automatisation, ou s'il ne servira qu'à assurer la partie connexion & utilisateurs
Penser à définir une convention de nommage dans le cas où tous les utilisateurs (& rôles associés) de toutes les applications seraient gérables via keycloak

| Utilisateur       | Description                  | Accès Portail Admin COP | Modifs Portail Admin COP | Accès ATMOS | Accès Mon magasin ATMOS | Modifs Admin ATMOS | Accès KMO Assist | Modifs Admin KMO Assist | Accès FLEG |
| ----------------- | ---------------------------- | ----------------------- | ------------------------ | ----------- | ----------------------- | ------------------ | ---------------- | ----------------------- | ---------- | 
| Superadmin        | Équipe digitale COP          | Oui                     | Oui                      | Oui         | Oui                     | Oui                | Oui              | Oui                     | Oui        |
| Admin COP         | Référent COP (Pascal)        | Oui                     | Non                      | Non         | Non                     | Non                | Oui              | Non                     | Oui        |
| Technicien        | Monteur COP                  | ?                       | Non                      | Non         | Non                     | Non                | Oui              | Non                     | Non        |
| Admin ATMOS       | Référent admin chez NORMA    | Non                     | Non                      | Oui         | Oui                     | Oui                | Non              | Non                     | Non        |
| Manager ATMOS     | Gérant de magasin NORMA      | Non                     | Non                      | Oui         | Oui                     | Non                | Non              | Non                     | Non        |
| User ATMOS        | Utilisateur de base NORMA    | Non                     | Non                      | Oui         | Non                     | Non                | Non              | Non                     | Non        |
| Admin VIDEO       | Référent admin client VIDEO  | Non                     | Non                      | Non         | Non                     | Non                | Non              | Non                     | Non        |
| Manager VIDEO     | Gérant de magasin VIDEO      | Non                     | Non                      | Non         | Non                     | Non                | Non              | Non                     | Non        |
| User VIDEO        | Utilisateur de base VIDEO    | Non                     | Non                      | Non         | Non                     | Non                | Non              | Non                     | Non        |
| Admin FLEG        | Référent admin client FLEG   | Non                     | Non                      | Non         | Non                     | Non                | Non              | Non                     | Oui        |
| Manager FLEG      | Gérant de magasin FLEG       | Non                     | Non                      | Non         | Non                     | Non                | Non              | Non                     | Oui        |
| User FLEG         | Utilisateur de base FLEG     | Non                     | Non                      | Non         | Non                     | Non                | Non              | Non                     | Oui        |
