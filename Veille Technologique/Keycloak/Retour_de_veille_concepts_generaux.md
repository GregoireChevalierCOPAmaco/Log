Keycloak basics 
- Identity Access Management =
    - Auth du user
    - Droits, accès et privilèges du user
    - Gestion de par qui et comment les informations peuvent être modifiées
    - Administration, gestion des infos users, des sessions, etc ...
- Identity Management a 4 fonctions principales :
    - Pure Identity Function, qui ne prend pas en compte le CRUD d'users
    - User Access Function (log-on), qui utilise une 'smart card' et ses données associées pour connecter l'user à un service ou une vue
    - Service Function, qui fournit un système de services personnalisé selon l'utilisateur ou le device concerné
    - Identity Federation, qui permet d'authentifier un user sans connaître son mot de passe sur le principe de la fédération d'identités
- Schema de requête basique d'une app sous keycloak :
User request to app -> Redirection vers l'auth keycloak -> Redirection vers la page de login -> Entrée des informations de login -> Submit des infos de login au keycloak -> Validation des informations de login par le keycloak -> Redirection vers l'application -> Display de la page requestée -> Request de token de l'application au keycloak -> Validation du token par keycloak -> Retour du token à l'application
- Keycloak utilise le SSO, Single Sign On, pour permettre à un user d'accéder à de multiples applications avec un seul compte user sans avoir à constamment log in & out des différentes applications (grâce au token)
- Le SSO se base sur Lightweight Directory Access Protocol (LDAP) & bases de données LDAP supportées par directory servers
- Le SSO peut être utilisé avec les cookies dans le cadre de réseau IP, mais seulement si les différentes apps partagent le même domaine DNS parent