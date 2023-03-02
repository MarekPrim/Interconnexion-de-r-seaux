## Introduction

### Origine

MultiProtocol Label Switching
Fondé sur la commutation de label
Tout type de données : Eth, ATP, IP, etc
Fonctionne sur tout type de réseau

Introduit le meilleur des 2 mondes : 
Mode Datagramme d'IP -> Routage
Mode VC d'ATM -> Performance commutation cellule

Puissant outil d'ingéniérie de trafic

Conçue pour interconnecter deux réseaux distincts de manière simple & élégante

**Problème avec IP**

Comment acheminer le trafic vers chaque site de chaque client ?
Comment réserver des ressources pour garantir un service ?
Comment router un tel plan de routage ? -> Traduction d'adresse

Volonté d'attribuer des ressources de qualité de service pour certains services (Volonté financière).

IP ne permet pas de satisfaire ces besoins.


#### Origine de MPLS

IP et ATM étaient en concurrence. Cohabitation, besoin d'interconnexion.
But de faire de l'ATM natif -> LAN Emulation (Ethernet émulé sur de l'ATM), vu comme de l'Ethernet par l'OS (Driver), IP et Ethernet simulé, avec en plus ATM.
Pas besoin de changer d'applications et adaptation aux nouvelles apps.

IP regarde comment mettre de l'IP dans de l'ATM, mais ne cherche pas à faire chier tout le monde comme ATM.

**MPOA** -> MultiProtocol Over ATM, les données circulent via une encapsulation LLC/SNAP avec éventuellement une étiquette.
Le réseau est structuré en IP mais on veut communiquer entre 2 stations ATM. Les routeurs route en IP mais ont des cartes ATM. Si 1 station envoie un paquet IP sur ATM, le paquet va être reconstruit/transformé depuis des cellules/paquet. C'est vraiment tout pourri si tout le réseau est en ATM. ATM ne pourra pas utiliser le multipath ou la QoS car IP prend toujours le même chemin.
MPOA se fonde sur le routage IP pour déterminer le chemin (plan de controle), la mise en place de la communication entre deux MPC (clients) peut ne pas passer par les routeurs décidés par IP (eq. VC ATM dans le plan de données -> Permet de découper et recoler une seule fois).

### Succès

ATM était très cher par rapport à IP et a perdu la bataille.
Les routeurs IP sont on par avec les perfs d'un commutateur ATM.
MPLS est resté et s'est adapté à d'autres technos qu'ATM avec une utilisation intelligente de ses capacités de classes de trafic, de classifications en entrée de réseau et de commutation simple dans le coeur réseau; le tout sans remettre en cause IP.
Il peut encapsuler plusieurs protocoles -> Mise en place de VPN
Peut être étendu pour gérer des technologies très variés -> *Generalized MPLS [22]*


### Principes de bases

Tous les avantages de IP et du mode VC dans IP sans toucher au fonctionnement de IP 

![[Pasted image 20230224165749.png]]

Paquet de Site A1 vers Site A2 :
 - Routons les en r-1 dans un "conduit"
 - Commutons les en fonction de ce conduit sur r-2, r-3 et r-4
 - Routons les vers Site A2 en sortie de conduit

Dans un conduit, les paquets ont un label pour commuter sans router avec IP.
![[Pasted image 20230224170102.png]]


Tout les paquets vont suivre le même chemin, seront dans l'ordre et de savoir qu'ils sont ensembles -> Avantages du ***circuit virtuel***
Pas vraiment d'avantage niveau performance

![[Pasted image 20230224170457.png]]

LSP -> Circuit unidirectionnel avec un numéro (eq. VCI VPI ATM)
LSR -> Routeur de commutation de label (font aussi du routage du plan de controle sur de l'IPv4,v6 et Ethernet), 2 types :
- Entrée -> I-LER : ingress LER ou *Label Edge Router*
- Coeur
- Sortie -> E-LER : Egress LER
**Comme un circuit est unidirectionnel, il faut 2 circuits pour communiquer mais cela permet de les faire passer par des chemins différents**
FEC -> Classe d'équivalence = Définit les propriétés communes des paquets

#### Plan de données
##### Label
L'information en fonction de laquelle un "paquet MPLS" est commuté est un label :
- Négocié entre deux LSR (ou LER)
- Portée locale
Peut utiliser des mécanismes existants -> VPI/VCI, DLCI, ...
Shim label entre L2 et L3 :
![[Pasted image 20230224171353.png]]

Entête de 4 octets :
- 20 bits de label -> 1M de label entre chaque équipement
- 3 bit qui sont utilisés pour un peu tout (surtout QoS)
- S -> Dernier
- TTL -> TTL
MPLS se place donc entre Layer 2 et Layer 3 (eq. Ethernet / MPLS / IP )

##### Empillement label

On peut imbriquer des circuits dans d'autres circuits -> Problème de taille (1M d'entrées entre **chaque noeud**, lourd dans le plan données/controle) et gestion/administration plus simple (LSP par client, LSP par QoS).
On enchaine les labels et le bit S permet de savoir si on est en fond de pile.
On traite toujours le label en sommet de pile.


##### Fonctionnement d'un LSR

*Ingress* LSR :
- Insère les paquets dans un LSP suite à une décision de routage (par exemple)
- Utilise le label négocié avec le LSR en aval
- Le label est eventuellement empilé (PUSH)

LSR de coeur :
- Commute les paquets selon leur label
- Les place sur la voie de sortie correspondante au Next Hop
- SWAP la valeur du label

*Egress* LSR :
- Extrait les paquets du LSP
- Injection dans le système correspondant (eg. routés)
- Le label est supprimé (POP)

##### Gestion du TTL

Gestion cohérente à IP
Seul le label en haut de pile est traité
En cas de TTL à 0, le message ICMP est envoyé dans le ***sens des données*** (ICMP à l'émetteur, *Destination Unreacheable*) -> Ce paquet  est envoyé vers la destination pour le renvoyer dans l'autre sens via à un autre LSP quand un routeur sera capable de renvoyer ce paquet (obligatoire car sinon le destinataire doit nous répondre dans quasiment 100% des cas).
Exemple : On recopie le TTL d'IP et à la sortie du LSP, on attribue au champ TTL du paquet IP sortant la valeur du champ TTL du paquet MPLS reçu.
![[Pasted image 20230224172943.png]]
(On suppose que le client fait aussi du MPLS)

Problème de l'encapsulation, on perd des champs TTL. Donc on décrémente, on encapsule et on récupère à la destination le champ TTL pour le mettre dans l'encapsulant. A destination, on remet le champ TTL dans l'encapsulé (eg. IP)


##### Penultimate Hop Popping (PHP)

Ne pas confondre avec PHP Hypertext Processing.

Penultième -> Avant-dernier
Quand un paquet arrive dans l'avant-dernier des routeurs d'un LSP. Il doit parcourir la table de commutation et la table de routage (important si il est à la confluence de plusieurs LSP).


... -> SWAP -> POP 

On peut faire qqe chose entre le SWAP et le POP si on sait qu'on est l'avant-dernier alors on peut envoyer le paquet désencapsulé directement car le dernier LSR comprendra forcément ce protocole.
Pourquoi ne pas faire encore celui d'avant et etc... ; donc pourquoi ça ne marche qu'avec l'avant-dernier -> On se projette encore sur le chemin, on ne route pas ce paquet et on obéit encore au chemin établi sur le LSP; Si c'est avant, on perd l'information et on sera obligé de router.
On perd les 3 bits expérimentaux et donc potentiellement de l'information (QoS), IMPLICIT NULL -> Label 3 permet de savoir qu'on est le pénultième.
Les labels sont négociés d'aval en aval, le dernier saura qu'il parle avec le pénultième et impose 3.
EXPLICIT NULL -> Label 0, le LER le supprime mais ne fait absolument aucune recherche (ne fait pas de POP, ni de désencapsulation; Communication classique).
La valeur 0 veut donc dire qu'il faut désencapsuler directement sans chercher dans la table MPLS en conservant les bits EXP.


##### Table de correspondance

![[Pasted image 20230224175431.png]]


NHLFE -> Dit au LSR comment traiter le paquet
![[Pasted image 20230224175518.png]]

ILM -> Permet de savoir comment traiter un paquet MPLS arrivant (correspondance label d'entrée et une NHLFE; sinon le paquet est routé [sur le e-LER])

FTN -> FEC (Forwarding Equ. Class) to NHLFE

![[Pasted image 20230224175657.png]]

