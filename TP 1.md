<u>Exercice 1</u>

1) Une architecture de réseau est une description/spécification des protocoles et de leurs interactions les uns avec les autres. N'est pas comprise l'implentation logicielle/matérielle.  L'implentation est laissé aux utilisateurs qui ont une motivation économique.
2) Les intérêts d'un modèle de réseau hiérarchique (en couche hiérarchisée en niveau) sont :
	- Sur une machine donnée, une entité ne dialogue qu'avec une entité d'un niveau immédiatement au-dessus/en-dessous. Une communication entre 2 machines impliques une communication horizontale
	- La simplicité
	- Evolutivité : On peut changer un seul protocole sur une couche selon les besoins en ne s'occupant que des interfaces directement inférieures/supérieures

3) Un protocole est une description de l'ensemble des échanges valides entre entités homologues. Derrière un protocole, on trouve des standarts (I3E)/avis (UIT)/recommendations (IETF)/normes (AFNOR/ISO) (dénominations variables selon l'organisme). N'est toujours pas inclus son implemtation, la partie algorithmique n'est pas normalisée.
	Un service est un ensemble de primitives, une primitive étant une fonction demandée par un niveau à un autre. Parmi les services classiques, connexion;déconnexion;envoi/reception de données;etc.
	Un point d'accès au service est une interface logicielle/matérielle qui permet à un service d'interagir avec un autre service situé sur la même couche. Le modèle OSI ne précise pas comment on l'implémente.

<u>Exercice 2</u>

1) Machine A / N+1 vers N : N_CONNECT.request
   La couche N de A va alors dérouler son protocole d'ouverture de connexion, en préparant un message N_PDU.demande_connexion à destination de la couche N de B (avec @ dst, SDU-1 == PDU) (1). Ce message n'est pas encore envoyé, il faut regarder le service proposé par N-1 (N-1 sans connexion). Pour envoyer ce message, on va activer N-1_DATA.request de A  (2)en lui fournissant l'adresse du destinataire, la N-1_SDU(=N_PDU) et d'éventuels paramètres de qualité de service.
   La couche N-1 de A va communiquer avec N-1 de B en envoyant la N-1_PDU.données (3) avec sans doute un identifiant de message, des codes d'erreurs, etc.
   Quand le service N-1 de B reçoit ce PDU, elle déroule son protocole pour s'assurer que ce message est correct et avoir la "marche à suivre". Elle envoie (N-1)\_DATA.indication (4) à N de B. Elle peut aussi envoyer une (N-1)\_PDU.données à (N-1) (3) de A (Wifi est sans connexion mais à des accusés de réception).
Entre N-1 et N, sens montant de N-1: Indication/Confirmation; Sens descendant de N : Requete/Réponse (voir p.5 du cours).
   N de B recoit ce message et déroule son protocole. Elle renvoie un message N_CONNECT.indication à N+1 de B (4). N+1 va répondre avec un message N_CONNECT.reponse (5) (OK ou NOK, mais on répond toujours quelque chose). Quand N recoit ce message, elle engage le processus d'acceptation de processus en envoyant un message N-1\_DATA.request. (N-1) de B envoie (N-1)\_PDU.données vers A, (N-1) de A remonte un message (N-1)\_DATA.indication à N.
   N va prévenir N+1 que c'est OK avec un message N_CONNECT.confirmation. On pourrait ensuite faire un triple handshake comme TCP avec un acquitté de reception de l'acceptation de connexion. La couche N de A envoie (N-1).\_DATA.request. La couche N-1 de A envoie (N-1)\_PDU.données. La couche N-1 de B remonte (N-1)\_DATA.indication pour indiquer que l'acceptation de connexion a été reçue.

2) Dans le cas d'une couche N-1 avec connexion, le déroulé de la demande de connexion est identique mais la couche N-1 négocie la connexion quand il recoit la demande la connexion de niveau N avec (N-1)\_CONNECT.request de N vers N-1. Entre les deux N-1, N-1\_PDU.demande_connexion. De N-1 B à N B, (N-1)\_CONNECT.indication. De N B à N-1 B, (N-1)\_CONNECT.response. N-1 B à N-1 A avec (N-1)\_cibbexion_conf. De N-1 A vers N A, (N_1)\_CONNECT.confirmation


3/4) 
   De N+1 à N, N_DATA.request. 
   De N à N-1, (N-1)\_DATA.request. 
   De N-1 A à N-1 B, (N-1)\_PDU.données. 
   ***De N-1 B à N-1 A, (N-1)\_PDU.confirmation***
   De N-1 à N, N-1_DATA.indication. 
   De N à N+1, N_DATA.indication. 
   De N à N-1, (N-1)\_DATA.request. 
   De N-1 B à N-1 A, N-1_PDU.données. 
   De N-1 à N, N-1_DATA.indication.

5)  De N+1 à N, N_DISCONNECT.request.
   De N à N-1, N-1.DATA.request
   De N-1 A à N-1 B, N-1_PDU.données.
   De N-1 B à N B, N_DATA.indication.
   De N à N+1, N_DISCONNECT.indication.
   On peut considérer qu'il y a eu déconnexion mais on risque de gaspiller des ressources si le message de déconnexion est perdu.
   Acquitement de la déconnexion :
   De N à N-1, N-1_DATA.request.
   De N-1 B à N-1 A, N-1_PDU.données.
   De N-1 A à N A, N-1_DATA.indication.
   De N à N-1, N-1_DISCONNECT.request
   De N-1 A à N-1 B, N-1_DISCONNECT.request
   Réponse de N-1 B, N-1_DISCONNECT.confirm