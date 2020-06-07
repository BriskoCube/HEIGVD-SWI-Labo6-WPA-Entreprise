- [Livrables](#livrables)

- [Échéance](#%c3%89ch%c3%a9ance)

- [Quelques éléments à considérer](#quelques-%c3%a9l%c3%a9ments-%c3%a0-consid%c3%a9rer-pour-les-parties-2-et-3)

- [Travail à réaliser](#travail-%c3%a0-r%c3%a9aliser)

# Sécurité des réseaux sans fil

## Laboratoire 802.11 Sécurité WPA Entreprise

__A faire en équipes de deux personnes__

### Objectif :

1.	Analyser les étapes d’une connexion WPA Entreprise avec une capture Wireshark
2.	__(optionnel)__ Implémenter une attaque WPE (Wireless Pwnage Edition) contre un réseau WPA Entreprise
1.  __(optionnel)__ Implémenter une attaque GTC Dowgrade contre un réseau WPA Entreprise


## Quelques éléments à considérer pour les parties 2 et 3 :

Les parties 2 et 3 sont optionnelles puisque vous ne disposez pas forcement du matériel nécessaire pour les réaliser.

En principe, il devrait être possible de démarrer vos machines en Kali natif (à partir d'une clé USB, avec une distro live par exemple) ou d'employer une autre version de Linux. Si vous n'avez pas une interface WiFi USB externe, __vous ne pouvez pas faire ces parties dans une VM Linux__. 

Dans le cas où vous arriverais à tout faire pour démarrer un Linux natif, il existe toujours la possibilité que votre interface WiFi ne puisse pas être configurée en mode AP, ce qui à nouveau empêche le déroulement des parties 2 e 3.

Ces deux parties sont vraiment intéressantes et __je vous encourage à essayer de les faire__, si vous avez les ressources. Malheureusement je ne peux pas vous proposer un bonus si vous les faites, puisqu'il risque d'y avoir des personnes qui n'auront pas la possibilité de les réaliser pour les raisons déjà expliquées.

Si toutes les équipes rendent le labo complet, il sera donc corrigé entièrement et les parties 2 et 3 seront considérées pour la note.

Si vous vous lancez dans ces deux parties, voici quelques informations qui peuvent vous aider :

- Solution à l’erreur éventuelle « ```Could not configure driver mode``` » :

```
nmcli radio wifi off
rfkill unblock wlan
```
-	Pour pouvoir capturer une authentification complète, il faut se déconnecter d’un réseau et attendre 1 minute (timeout pour que l’AP « oublie » le client) 
-	Les échanges d’authentification entreprise peuvent être facilement trouvés utilisant le filtre d’affichage « ```eap``` » dans Wireshark


## Travail à réaliser

### 1. Analyse d’une authentification WPA Entreprise

Dans cette première partie, vous allez analyser [une connexion WPA Entreprise](files/auth.pcap) avec Wireshark et fournir des captures d’écran indiquant dans chaque capture les données demandées.

- Comparer [la capture](files/auth.pcap) au processus d’authentification donné en théorie (n’oubliez pas les captures d'écran pour illustrer vos comparaisons !). En particulier, identifier les étapes suivantes :
	
	- Requête et réponse d’authentification système ouvert
	
	> ![open_1](img/open_1.PNG)
	>
	> 
	>
	> ![open_2](img/open_2.PNG)
	
	
	
	  - Requête et réponse d’association (ou reassociation)
	
	> ![assoc_1](img/assoc_1.PNG)
	>
	> 
	>
	> ![assoc_2](img/assoc_2.PNG)
	
	
	
	- Phase d’initiation. Arrivez-vous à voir l’identité du client ?
	
	> Le client utilise le nom d'utilisateur: `einet\joel.gonin` 
	>
	> ![req_ident](img/req_ident.PNG)
	>
	> 
	>
	> ![resp_ident](img/resp_ident.PNG)
	
	
	
	- Négociation de la méthode d’authentification entreprise
	
	>
	> ![start](img/start.PNG)
	>
	> 
	>
	> ![nak](img/nak.PNG)
	>
	> 
	>
	> ![start_peap](img/start_peap.PNG)
	
	
	
	- Phase hello:
	
	  > ![tlsc_hello](img/tlsc_hello.PNG)
	  >
	  > 
	  >
	  > ![tlsc_hello](img/tlsc_hello.PNG)
	  >
	  > 
	  >
	  > ![tlss_hello](img/tlss_hello.PNG)
	
	  
	
	  
	
	  - Version TLS
	
	  > La version de TLS utilisé est la v1
	
	  
	
	  - Suites cryptographiques et méthodes de compression proposées par le client et acceptées par l’AP
	
	  >Suites proposées par le client
	  >
	  >![`cipher suites`](img/cipher_suites.PNG)
	  >
	  >
	  >
	  >Suite cryptographique utilisée: `TLS_RSA_WITH_AES_256_CBC_SHA`
	  >
	  >
	  >
	  >Le client propose `null` comme méthode de compression. Et le serveur choisis la seul méthode possible: `null`
	
	  
	
	  - Nonces
	
	  > Client: `16e24a729c4b60609b8ce482014ac38f1e9cb8cf2bf8fd30bf8995f1`
	  >
	  > Serveur: `003b6c2676ffd79814e56c065e5b0c39cb26600148ca1e9b3e8af83426d46e11`
	
	  
	
	  - Session ID
	
	  > Client: `9f1bbf1e90b88366a836db08d659f906a637ac31920e06f622762ca6c522a64f`
	  >
	  > Serveur: `ad41641ec2a7d1d5a9f6586c05703a8cbdbf6ef0053ad517f6e69b286804f5f2`
	
	  
	
	- Phase de transmission de certificats
	
	  - Echanges des certificats
	
	  > ![certificats](img/certificats.PNG)
	
	  
	
	  - Change cipher spec
	
	  >Point depuis le quel les échanges passent en chiffré
	  >
	  >![change_cipher](img/change_cipher.PNG)
	
	  
	
	  - Authentification interne et transmission de la clé WPA (échange chiffré, vu comme « Application data »)
	
	  > ![appdara](img/appdara.PNG)
	
	  
	
	  - 4-way handshake
	
	  > ![4way](img/4way.PNG)
	
	

### Répondez aux questions suivantes :

![full_exchange](img/full_exchange.PNG)



> **_Question :_** Quelle ou quelles méthode(s) d’authentification est/sont proposé(s) au client ?
>
> **_Réponse :_** EAP-TLS est proposé au client.

---

> **_Question:_** Quelle méthode d’authentification est finalement utilisée ?
>
> **_Réponse:_** La méthode finalement utilisée est le protocole EAP-PEAP. On peut voir que l'AP propose EAP-TLS mais le client demande EAP-PEAP.
>
> ![nak_tls_eap](img/nak_tls_eap.PNG)

---

> **_Question:_** Lors de l’échange de certificats entre le serveur d’authentification et le client :
> 
> - a. Le serveur envoie-t-il un certificat au client ? Pourquoi oui ou non ?
> 
> **_Réponse:_** Oui, le client envoie son certificat au client (*packet 21498 et les fragments*). Le certificat du serveur est nécessaire au client pour qu'il puisse authentifier le serveur.
> 
> - b. Le client envoie-t-il un certificat au serveur ? Pourquoi oui ou non ?
> 
> **_Réponse:_** Non, le client n'envoie pas de certificat au serveur. Ceci est du au fait que la méthode finalement choisie (EAP-PEAP) utilise comme vu ci-dessus une authentification du serveur par le client avec certificat mais à l'inverse une authentification "interne" est effectuée (MS-CHAPv2 par exemple) avec le username et le mot de passe du client (cette authentification interne est protégée par le tunnel TLS). 

---

### 2. (__Optionnel__) Attaque WPA Entreprise (hostapd)

Les réseaux utilisant une authentification WPA Entreprise sont considérés aujourd’hui comme étant très surs. En effet, puisque la Master Key utilisée pour la dérivation des clés WPA est générée de manière aléatoire dans le processus d’authentification, les attaques par dictionnaire ou brute-force utilisés sur WPA Personnel ne sont plus applicables. 

Il existe pourtant d’autres moyens pour attaquer les réseaux Entreprise, se basant sur une mauvaise configuration d’un client WiFi. En effet, on peut proposer un « evil twin » à la victime pour l’attirer à se connecter à un faux réseau qui nous permette de capturer le processus d’authentification interne. Une attaque par brute-force peut être faite sur cette capture, beaucoup plus vulnérable d’être craquée qu’une clé WPA à 256 bits, car elle est effectuée sur le compte d’un utilisateur.

Pour faire fonctionner cette attaque, il est impératif que la victime soit configurée pour ignorer les problèmes de certificats ou que l’utilisateur accepte un nouveau certificat lors d’une connexion.

Pour implémenter l’attaque :

- Installer ```hostapd-wpe``` (il existe des versions modifiées qui peuvent peut-être faciliter la tâche... je ne les connais pas. Dans le doute, utiliser la version originale). Lire la documentation du site de l’outil ou d’autres ressources sur Internet pour comprendre son utilisation
- Modifier la configuration de ```hostapd-wpe``` pour proposer un réseau semblable au réseau de l’école ou le réseau de votre préférence, sachant que dans le cas d'une attaque réelle, il faudrait utiliser le vrai SSI du réseau de la cible
- Lancer une capture Wireshark
- Tenter une connexion au réseau (ne pas utiliser vos identifiants réels)
- Utiliser un outil de brute-force (```john```, par exemple) pour attaquer le hash capturé (utiliser un mot de passe assez simple pour minimiser le temps)

### Répondez aux questions suivantes :

> **_Question :_** Quelles modifications sont nécessaires dans la configuration de hostapd-wpe pour cette attaque ?
> 
> **_Réponse :_** 

---

> **_Question:_** Quel type de hash doit-on indiquer à john pour craquer le handshake ?
> 
> **_Réponse:_** 

---

> **_Question:_** Quelles méthodes d’authentification sont supportées par hostapd-wpe ?
> 
> **_Réponse:_**


### 3. (__Optionnel__) GTC Downgrade Attack avec [EAPHammer](https://github.com/s0lst1c3/eaphammer) 

[EAPHammer](https://github.com/s0lst1c3/eaphammer) est un outil de nouvelle génération pour les attaques WPA Entreprise. Il peut en particulier faire une attaque de downgrade GTC, pour tenter de capturer les identifiants du client en clair, ce qui évite le besoin de l'attaque par dictionnaire.

- Installer ```EAPHammer```. Lire la documentation du site de l’outil ou d’autres ressources sur Internet pour comprendre son utilisation
- Modifier la configuration de ```EAPHammer``` pour proposer un réseau semblable au réseau de l’école ou le réseau de votre préférence. Le but est de réaliser une GTC Downgrade Attack.
- Lancer une capture Wireshark
- Tenter une connexion au réseau


### Répondez aux questions suivantes :

> **_Question :_** Expliquez en quelques mots l'attaque GTC Downgrade
> 
> **_Réponse :_** 

---

> **_Question:_** Quelles sont vos conclusions et réflexions par rapport à la méthode hostapd-wpe ?
> 
> **_Réponse:_** 


## Livrables

Un fork du repo original . Puis, un Pull Request contenant :

-	Captures d’écran + commentaires
-	Réponses aux questions

## Échéance

Le 1 juin 2020 à 23h59