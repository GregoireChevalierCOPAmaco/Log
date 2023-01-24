https://letsencrypt.org/fr/getting-started/
24/01

Pour activer le HTTPS sur votre site web, vous devez obtenir un certificat (un type de fichier) auprès d’une autorité de certification (AC). Let’s Encrypt est une AC. Afin d’obtenir un certificat pour le domaine de votre site web auprès de Let’s Encrypt, vous devez apporter la preuve que vous avez le contrôle du domaine. Avec Let’s Encrypt, vous faites cela en utilisant un logiciel qui utilise le protocole ACME qui fonctionne généralement sur votre hôte web.

Pour déterminer la méthode qui vous conviendra le mieux, vous devez savoir si vous avez un accès shell access (également appelé accès SSH) à votre hôte web. Si vous gérez votre site web entièrement via un panneau de contrôle comme cPanel, Plesk, ou WordPress, il y a de fortes chances que vous n’ayez pas d’accès shell. Vous pouvez demander à votre fournisseur d’hébergement d’en être sûr.

Certificats racine
Nos certificats racine sont maintenus hors ligne en toute sécurité. Nous délivrons des certificats d’entité finale aux souscripteurs des certificats intermédiaires dans la section suivante. Pour une compatibilité supplémentaire lorsque nous soumettons notre nouveau Root X2 à divers programmes racine, nous l’avons également signé de manière croisée à partir de Root X1.

Certificats intermédiaires
Dans des circonstances normales, les certificats émis par Let’s Encrypt proviendront de “R3”, un intermédiaire RSA. À l’heure actuelle, l’émission depuis “E1”, un intermédiaire ECDSA, est possible uniquement pour les clés d’abonnés ECDSA pour les comptes autorisés. À l’avenir, l’émission depuis “E1” sera disponible pour tout le monde.

Nos autres intermédiaires (“R4” et “E2”) sont réservés à la reprise en cas de catastrophe et ne seront utilisés que si nous perdons notre capacité à émettre des problèmes avec nos principaux intermédiaires. Nous n’utilisons plus les intermédiaires X1, X2, X3 et X4.

IdenTrust a signé la signature croisée de nos intermédiaires RSA pour une compatibilité supplémentaire.

Voir aussi https://certbot.eff.org/ avec https://certbot.eff.org/instructions?ws=nginx&os=ubuntufocal pour tuto