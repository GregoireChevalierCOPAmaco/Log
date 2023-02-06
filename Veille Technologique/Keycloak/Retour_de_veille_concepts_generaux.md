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
Benefits of using SSO
Reduce risk for 3rd party sites to access.
Reduce password debility from the different username and password combinations.
Reduce time spent for re-entering passwords for the same identity.
Reduce IT help desk calls for passwords, therefore, IT costs are also reduced.
Keycloak
Keycloak is an open source identity and access management solution which mainly aims at applications and services. Users can authenticate with Keycloak rather than individual applications. So, the applications don’t have to deal with login forms, authenticating users and storing users. Once logged-in to Keycloak, users don’t have to login again to access different applications. Same thing is applicable to sign-out. Keycloak offers everything a sophisticated user management tool needs – without having to log on repeatedly with every login and into every system-as well as system security, social logins, support for mobile apps and integration into other solutions. Keycloak have implementations to LDAP and Active Directory as well.

Password Policies
Each new realm created has no password policies associated with it while users can create as short, as long, as complex, as insecure a password, as they want. Simple settings are fine for development or learning Keycloak, but unacceptable in production environments.

OTP
Keycloak has a number of policies you can set up for your FreeOTP or Google Authenticator One-Time Password generator. There are two types:

TOTP(Time based OTP)
HOTP(Counter based OTP)
Client Certificate
A client authentication certificate is a certificate used to authenticate clients during an SSL handshake, users who access a server by exchanging the client authentication certificate. A client certificate would typically contain pertinent information like a digital signature, expiration date, name of client, name of CA (Certificate Authority), revocation status, SSL/TLS version number, serial number, and possibly more, all structured using the X.509 standard. Very popular web browsers like Firefox, Chrome, Safari, and Internet Explorer can readily support client certificates.
If an application is enabled with client certificate authentication, only users who attempt to connect from clients loaded with the right client certificates will succeed. Even if a legitimate user attempts to connect with the right username and password, if that user isn’t on a client application loaded with the right client certificate, that user will not be granted access. In fact, if that user’s connecting from a Web browser, the login page (where he’s supposed to enter his username and password) might not even load at all like the one shown below.
A server certificate is sent from the server to the client at the start of a session and is used by the client to authenticate the server. A client certificate, on the other hand, is sent from the client to the server at the start of a session and is used by the server to authenticate the client.
Keycloak Working Procedure
On a complete system secured with keycloak:
A user clicks from a public page to navigate to protected area within the application. The link to this protected area is in the application settings in keycloak admin console.

The user will be redirected indeed to the keycloak authentication page. After providing username and password, keycloak redirects the user back to the application again with a code that is valid to a very short span of time.
The application communicates this code to keycloak along with the application ID and the application secret, then keycloak replies with the Access token, ID token, and a Refresh token. Your application will need only one of these tokens to see which claims the user has, and according to the claims, the user will be granted or denied access to the requested protected URL(s).
