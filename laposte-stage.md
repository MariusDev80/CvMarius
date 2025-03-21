Stage

Outils, Tech utilisées :

- Jira (Gestion des Sprint, US, tickets (Anomalies, Incident, etc…))
- Teams pour les reunions et chatting
- Outlook pour la communication
- VS code avec :
    - NodeJS/Angular
- Eclipse avec :
    - Spring/Lombok/Maven
- Gitlab pour le versionning
- Bruno (API testing)
- Jenkins (deploiement des builds sur les differents environements)

Info Générales :

APIM = API Managment :

-> APIM Gestion = interface de gestion des API de la poste pour le service BSCC, le service du Siège : I-Team est sur AskSam qui est une autre interface. Les deux sont basées sur un socle commun : WSO2 API Manager qui est une plate-forme de gestion d'API. Cette plate-forme gère les API durant tout leur cycle de vie, de leur publication/deploiement à leur maintenance et leur retrait.

APIM Gestion permet la publication, l'abonement et le suivi des APIs mais aussi de faire des demandes support aussi bien pour les producteur d'API que les clients/abonnés qui les exploitent.

Affinage BackLog :
L'affinage du BackLog se déroule tout les lundi, les PO (Anne et Salim) proposent des US ou reviennent sur des anciennes qu'ils vont affiner avec l'équipe qui va donner sont point de vu de dev puis chaque US se voit attribuer une note après un accord commun de l'équipe. Cette note se fait par des outils de méthodes agiles comme Planning Poker ou chaque personne donne une note et si il y a des désacord les personne de la minorité expliquent leur note afin de trouver un accord. Cette note sert à évaluer la difficulté de la tâche.

Déroulement d'un Daily (15min) :
Le scrum master (Isabelle) dit les généralités si il y en a et les autres peuvent également en dire ensuite. Chaque personne parle de son avancée, les US sur lesquelles elle a travaillé et les blocages qu'elle a pu rencontrer ainsi que l'aide qu'elle peut avoir reçu des autres personnes de l'équipe. Elle désigne en suite la prochaine personne a parler.


Missions :

[APIMDEV-2621] US - Gestion Roles utilisateurs - I-Team :

A l'authentification sur APIM je souhaiterais gérer les roles de l'utilisateur afin de restreindre d'une manière differente ses accès selon le role afin d'éviter les doubles demandes via APIM Gestion et l'outil de l'équipe I-Team (AskSam).

Lorsque l'utilisateur se connecte, on prend son IdRh et on fait appel à l'API RefId qui va retourner les informations relatives à l'identité de la personne y compris l'id de sa branche ce qui permettra d'identifier les utilisateur appartenant à la branche d'I-Team (le Siège). Les utilisateurs normaux (non-admin) d'I-Team ne pouront pas avoir accès au demandes d'accostage, les utilisateurs administrateur de chez I-Team dans APIM-Gestion auront l'équivalent des droits d'un utilisateur normal se connectant à l'APIM, il aura accès au Analytics, Accostages et Retours Utilisateurs. La création d'un role admin_I-Team est donc nécéssaire.

Codes de branches :BSCC : 201
I-team : 203 

[APIMDEV-2675] US - I-team - Administrateur

En tant qu'administrateur I-team, je souhaiterais étendre les droits d'observation de mes demandes support à l'ensemble des autres administrateur I-team. Les administrateurs en observateurs auront les notifications de désactivées et pourront voir la demande dans la liste de leur demandes de support pour y accéder.

L'objectif est que l'équipe I-team n'ai pas a déclarer manuellement chaque observateur lors de la création d'une demande support.

Semaine 1 :

06/01 :

Installation, retrospéctive du dernier sprint avec l'equipe. Précision de certaines US pour le prochain sprint et evaluation du niveau de difficulté.

Après midi -> chiffrage et mise au point/explication de plusieurs US pour le prochain sprint 


07/01 :

Meeting + galette des rois
Daily scrum et explication de la mission et US.
Explication de ce que fait le pole dev : APIM, WSO2, XGate.

Mission (ce que j'ai compris au premier abord) : 
Deux equipes peuvent accéder au socle via deux interfaces differentes et gèrent chacunes des demandes consomateur/producteur differentes mais comme le socle est le même il y a des interferences (socle : WSO2). 

L'objectif est donc de restreindre l'accès de l'équipe i-team à l'interface de l'équipe BSCC (APIM Gestion) afin qu'il ne puissent consulter que les analyses de données et les retours utilisateurs. Uniquement les utilisateurs ayant des droit administrateurs pouront accéder au support d'APIM Gestion. 

Il faut donc permettre au admin d'APIM Gestion d'avoir un CRUD pour gérer les utilisateurs de i-team étant admin. Cependant il n'y a pas de moyen de différencier l'équipe à laquelle appartient un utilisateur quand il se connecte, il faudra donc passer par une API (RefId) pour la connaitre. 

08/01 :

Continuation de l'installation du poste et de l'environement de dev, front fonctionnel et DB créé.

09/01 :

Pull du back et mise en place d'eclipse, pas mal de problèmes autour d'eclipse.
Premiere approche du projet, c'est un projet avec une structure assez compliquée. Enormement de fichier et c'est assez dur de visualiser comment se font les request entre le front et le back ainsi que le back et les API/BDD. 

10/01 :

Mise au point et au clair de l'US APIMDEV-2621 qui la premiere que je dois faire. 
Ecriture du script de création de la table admin_i_team 
Installation de Bruno -> outil qui permet de tester les API

Semaine 2 :

13/01 :

Test de premieres requetes API avec bruno
Affinage backLog et daily 

14/01 :

Ajout d'un controller dans le back, test de request récupérées par le controller -> objectif étant de récupérer l'idrh d'une personne à son authentification.
Ajout d'un service qui va appeler l'API RefId de l'équipe I-Team afin de récupérer l'Identité lié à l'idrh envoyé par le front pour connaitre la branche du l'utilisateur.
Erreur lors de l'appel de l'API -> code erreur 900908 : Ressource Forbidden ; User is not authorized to access the Resource. API Subscription validation failed.
	-> L'utilisateur utilisé pour accéder à l'API n'est pas le bon ou c'est directement l'API qui rejète la requete 

15/01 :

Toujours un blocage sur RefId, ajout d'une methode qui servira à vérifier si l'utilisateur à le role ADMIN_Iteam
Création de la méthode qui vérifiera l'accès qu'aura l'utilisateur selon son role et sa branche. 
La branche n'étant pas encore récupérable, j'utilise des valeurs de test variabilisés dans le back.

16/01 :

Je récupère bien le niveau d'accès dans le front avec une requete envoyé au back cependant j'ai un problème. Je dois changer les conditions d'affichage des mat-cards (élément angular) mais pour cela il faut que je récupère l'idRh de l'utilisateur pour faire ma requete au back afin de récupérer l'information sur le niveau d'accès cependant en voulant appeler la fonction qui fait cet appel, ce que je fais à l'initialisation du composant qui contient les mat-cards, je me rends compte que le composant s'initialise un peu avant que l'utilisateur sois connecté. De ce que j'ai pu voir c'est à cause la fonction ngOnInit() (dans laquelle on fait les appels nécéssaires au bon fonctionnement du component) qui pré-charge le composant au lancement de l'app.

17/01 :

Je reprend sur le problème d'hier, j'ai d'abord essayé de faire une fonciton asynchrone pour régler ce problème mais elle bloquait tout lors de son appel et les appels devant être fait après ne se faisaient pas. J'ai donc essayé de trouver un autre moyen de faire qu'avec une fonction asynchrone, j'ai fait mon appel au back directement au même endroit que l'authentification de l'utilisateur et qui va sauvegarder la valeur retournée dans une variable. L'authentification se faisant dans le context, j'avais déjà accès au context dans le composant. J'ai trouvé dans angular un moyen de faire une variable utilisable dans un composant, ce que j'ai fait car l'affichage de celui ci dépend d'une condition sur l'état de l'utilisateur (se déclenche une fois connecté).

L'après midi j'ai rebasé ma branche sur la branche APIMDEV-2735 pour le back et le front car il y a eu de changement de version pour angular. Problèmes sur Eclipse suite au rebasage de la branche que j'ai réussi à régler en fin de journée avant de partir.

Semaine 3 :

20/01 :

Fin de sprint et rétrospéctive + voeux du directeur.
Toujours dans l'attente de l'API RefId pour pouvoir continuer l'US, je vais donc surement commencer à faire des TU (Tests Unitaires) sur les éléments ajoutés au back. 
Point avec Xavier, je vais essayer de gérer les accès pour le type Restricted-Access parce que actuellement il ne voit pas les cards sur le /home mais via les URL peut accéder au autres pages, il faut donc l'empecher d'y arriver.

21/01 :

J'avance sur la gestion des accès du type Restricted-Access coté back. 
Quelques news pour RefID, le ticket a été pris en charge par ITEAM et on devrait pouvoir l'utiliser dans 4 à 5 jours.
Lancement du Sprint + Deploimenent sur l'environnement d'accostage dans l'après midi. 

22/01 :

La gestion des accès au niveau du front marche, les utilisateurs en restricted-access ne peuvent pas accéder aux urls des la pages /accostage/… 
Merge de la branche Develop dans ma branche pour mettre a jour avec les modifications faites par les autres US mergé précédement sur Develop.
Mise en place de la conf des test pour le front, lancement des TU : 192 COMPLETED, 1 FAILED

23/01 :

Point sur le stage, Daily, MEP X-GATE/APIM Gestion/APIM Store sur l'environement d'accostage 
J'essaye de régler le problème du TU qui ne marchais pas hier. 
J'ai avancé sur les TU mais il y a d'autres probèmes que je n'arrive pas encore à régler.

24/01 :

J'ai fini 2 TU, celui du RouteAdminGuard et celui du RouteConsultationGuard.
En fouillant dans les TU d'un autre projet j'ai remarqué un problème dans le RouteGuard qui n'empeche pas des utilisateur normaux d'atteindre des routes normalement reservées au admins. Meme si la page ne charge pas les données elle est quand meme accessible ce qui ne devrait pas arriver (le guard devrait renvoyer l'utilisateur vers ./home ou une page d'erreur).

Semaine 4 :

27/01 :

Fin des TU (back et front).
Ajout de commentaire sur tout le code réalisé depuis le debut du stage.
L'accès a l'API RefId marche enfin mais le token d'Authentication envoyé n'est pas le bon.

28/01 :

Problème du token réglé, l'url d'appel de RefId dans le back n'est pas le bon ce que j'ai donc corrigé. 
Je récupère bien les données voulu en appelant l'API RefId et j'ai commencé à traiter le JSON retourné pour récupérer uniquement l'info nécéssaire.
Gestion des différents cas spéciaux que pourrait renvoyer RefId car elle devrait renvoyer l'historique des postes occupés mais n'en renvoie qu'un seul actuellement. Je gère donc desormais le cas ou il y a la liste des postes au lieu d'un seul et le cas ou il y aurait une erreur avec deux postes occupés simultanément. Dans le cas ou il n'y a pas de poste et donc pas de branche le type d'accès est Restricted-Access.
J'ai créé une class Identite et PosteOccupe dans laquelle je vais charger les données renvoyées par l'API RefId afin qu'elles soient réutilisables si besoin.

29/01 :

Recherches sur comment se fait le mapping de données au format JSON en objets Java pour charger le JSON renvoyé par l'API RefId dans des objets afin que les données soient plus facilement réutilisables et accessibles. En me basant sur des usages similaires et de la doc, j'ai bien reussi a mapper le JSON renvoyé par RefId. L'US 2621 est presque terminée, il reste une relecture que l'un des membres de l'équipe de dev va faire.

30/01 :

Correction de bug sur le mapping. 
Pour eviter de faire trop d'appels a l'API RefId je vais charger les données que l'API renvoie dans le cache avec un durée d'expiration de 24h, les branches et postes occupés n'était que rarement modifiés. Plusieurs problèmes rencontré pour faire cela notament le fait qu'appeler la fonction qui charge les données dans le cache dans une autre fonction de la même classe ne va pas aller chercher les données dans le cache mais faire les appels à refId. J'ai donc du créer un autre service qui se charge du traitement des données chargée par les appels à refId, c'est aussi plus propre car je separe les appels et le traitement.

31/01 :

Clean du code pour un dernier commit et la creation de la merge request, je l'ai assigné a Xavier (tech lead) qui va faire une review et me dire les quelques modifications a faire. 
Je fais aussi un peu de Sonar aujourd'hui car c'est la fin du sprint commencer une US maintenant ne serait pas utile. 
Reunion avec Xavier pour voir les modifs de sa review et pourquoi il aurait fait les choses d'une autre façon avec quelques explications. 

Semaine 5 :

03/02 :

Retro du sprint + daily le matin, affinage backlog l'après midi. 
J'ai commencé en parallèle des reunions à regarder comment faire les corrections suite à la review. J'ai fait des recherches sur Jackson (mapper) pour voir si il y avait un moyen plus simple de mapper une JSONArray contenant des JSONObject que en parcourant l'array pour mapper un par un les JSONObject. Sur toutes les solutions proposées sur internet il faut toujours d'une façon ou d'une autre parcourir l'array.

04/02 :

Merge de la branche de corrections du code que j'ai faite la semaine dernière (passage des ngIf d'angular 14 au @if de angular 18 pour le front) sur ma branche. 
Modifications suite à la review terminées. Correction du front suite au modifs du back.
Mise à jour des TU suite à la modifications de certaines méthodes du back, tous les TU back et front passent. 
J'ai commit toutes les modifications pour que Xavier puisse tester et valider le merge de ma branche.

05/02 :

Merge request acceptée en front et back pour la 2621
PMEP + recherches sur comment implémenter la prochaine US. Clarification de certains points sur la 2675.

06/02 :

Correction d'un problème rencontré après le deploiement en local de la branche develop par plusieurs dev, un des cas n'était pas pris en compte pour la connexion et empêchait de determiner la branche de l'utilisateur.
Commencement de la 2675, quelques test pour essayer de récuperer la liste des utilisateurs d'APIM Gestion. Plusieurs problèmes d'habilitations sur le compte qui fait l'appel a l'api I2aService réglés en fin de journée. 
Quelques tests réalisés sur Bruno pour les appels à I2aService et implémentation d'un controller dans le back.

07/02 :

Après un deploiement sur les environnement de recette et dev par les testeuse, une problème empêchait l'accès au fonctionnalités admin d'APIM Gestion car les appels à RefId semblait être KO.
Après quelques recherches pour résoudre le problème, il s'avère que c'était le Swagger qui n'était pas à jour et empêchait l'appel à l'API du back sur la nouvelle route que j'avait définie pour les appels a RefId. Le Swagger a donc été mis à jour pour les deux environnements mais le problème persistait sur recette. C'était ici un problème d'Egress, sur l'environnement dev les routes autorisées par l'egress comprenaient la version intranet et extranet pour l'url d'obtention d'un token (utilisé dans l'appel à RefId) alors que sur recette il n'y avait que la version intranet et l'appel était fait pour l'extranet.

J'ai continué d'avancer sur la 2675 en parallèle et l'ai presque finie, il reste des TU à faire dans le back et le front. J'arrive bien à récupérer la liste des administrateurs I-team dans le front et il s'ajoutent bien en tant qu'observateur à la création d'une demande support.


Semaine 6 :

10/02 :

Daily et affinage ce matin, RefId pose plusieurs problèmes car certains comptes n'ont pas d'identité de définie dans RefId ce qui fait qu'à la connexion ils se retrouvent en Restricted-Access, il faudrait donc pouvoir utiliser l'ancien fonctionnement dans le cas ou RefId ne retourne rien pour être sur d'au moins pouvoir tester l'appli et ne pas avoir un aussi gros point bloquant.
J'ai reussi à trouver un moyen de mêler les deux fonctionnement (avec et sans refId) mais l'erreur retournée par refId dans le back suite a son appel quand même necessaire 1 fois à l'air de bloquer le reste des appels fait au back.

11/02 :

J'ai finalement résolu le problème lié à refId, quand une personne n'as pas d'identité retournée par refId on applique l'ancien fonctionnement qui se base uniquement sur les roles de l'utilisateur sans se soucier de la branche. Cette alternative sera utilisée jusqu'à ce qu'une identité refId soit créé pour les comptes actifs et de tests de l'ensemble de l'application. Il faut encore que je fasse passer tout les TU du back pour valider les modifications.
J'ai donc modifié les TU que j'avais fait et les ai complétés pour qu'ils puissent tester la connexion avec et sans RefId. 
Reunion de département sur toute la fin d'après midi.

12/02 :

Mise à jour de mes branches front et back pour les dernières modifs push sur develop (correction refId et autres merges). Daily -> problème remonté sur RefId, accès restreint sur l'env de build SREC. 
Recherches sur ce problème remonté par les testeuses, le problème viens du publisher qui n'as pas l'air de bien prendre en compte l'update du swagger de apimAccostages qui est l'API sur laquelle les appels sont fait pour RefId. Le back renvoie un 404 not found car la ressource est inaccessible.

13/01 :

Reunion fin de stage et potentielle possibilité de continuer à la poste
Démo à l'équipe des deux US réalisées : démo plutôt claire avec quelques questions, l'ensembles des points à traiter on été reussi dans les temps et le resultat est satisfaisant.
Un dernier petit blocage, un TU que j'ai rajouté dans le back ne passe pas et je n'arrive pas à trouver de solution donc je vais voir ça avec Ilane.

14/02 :

Rétro du sprint aujourd'hui car il y a des départ en vacances, j'ai pu faire un peu de Sonar sur le front pour corriger quelques erreurs ou retirer des choses inutiles dans le code. Reunion avec la scrum master pour que je lui donne un retour sur mon experience pendant ce stage. Dernières modifications liées à mes US et merge sur develop.



