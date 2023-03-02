D’où vient le besoin d’interconnexion?
- Hétérogénéité des technologies des réseaux (Même si les technos convergent)
- Différents propriétaires/gérants (réseaux d'entreprise -> VPN <=> Interconnexion de réseaux de même protocole mais avec différents propriétaires)

(contexte interco nv 2)
Pour encapsuler (On suppose que le client final soit suffisament intelligent pour installer la techo et la comprendre, démodulation) => Même protocole sur les 2 réseaux; != Réseaux = Traduction
Traduction, algo pour chaque protocole, décohérence des headers => Perte de signalisation et possiblement de l'inversion => Pré-configuration forte nécessaire, **AUCUN SCALING POSSIBLE, DONC ON NE PEUT PAS TRAVERSER PLUSIEURS RESEAUX**.
On fait remonter ce problème de niveau 2 vers le niveau 3


### IP UN OUTIL D’INTERCONNEXION

IP propose une adresse qui va représenter un équipement en donnant une information géographique, position dans le réseau et identifier de manière unique cet équipement. Une seule info donne plusieurs caractéristiques.
Limite de cet adresse :
- IPv4, tout est dit, un problème connu depuis 97 quand même
- Mobilité fout le bordel sur la cohérence de l'info de localisation -> Changer d'adresse IP

Routage -> Aiguiller les paquets
Protocole de routage -> Remplit la table

![[Pasted image 20230223143859.png]]

Pile de protocole routeur :


| A1  | R1  | R2  | B1  |   
|---|---|---|---|
|  IP | IP   | IP  |  IP |   
|  T1 | T1 T2  | T2 T3  | T3  |   

IP -> Solution par encapsulation extensive
*Coût de l'encapsulation/désencapsulation -> Overhead*
*Encapsulation inutile avec overhead d'infos inutiles (niveau 2 peut apporter des infos dont IP se fout)*
*IP ne sait que multiplexer entre protocoles, pas entre applications*

Homogénéité de réseaux assuré car IP à fait poids sur les autres solutions

On peut penser que nous n'avons besoin que d'IP, mais envoyer/recevoir des infos sur un support physique n'est pas simple (Problème de la résolution d'adresse MAC dans un contexte Ethernet, besoin d'ARP). IP doit quand même configurer des tunnels mais il ne veut pas imposer des implémentations sur les autres technos.

![[Pasted image 20230223144959.png]]

## Tunnels

Encaspulation
![[Pasted image 20230223151530.png]]
Illustration de Tor

IPv6 ne peut pas connaitre l'adresse IPv4, besoin d'une configuration (dynamique/statique).

On peut faire un tunnel pour un VPN, abstraire la réalité d'interco ou encore la sécurité.
On peut même utiliser BGP pour encapsuler du trafic.
