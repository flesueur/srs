# TP 2 : Firewall

_François Lesueur ([francois.lesueur@insa-lyon.fr](mailto:francois.lesueur@insa-lyon.fr))_

Ce TP sera réalisé dans l'infrastructure MI-LXC, disponible [ici](https://github.com/flesueur/mi-lxc) (nécessite un Linux en root) ou dans la VM "tp-sec-debian" disponible en salle de TP (`/machines_virtuelles/secu_vms/master/tp-sec-debian.sh`, debian/debian et root/root). L'infrastructure déployée simule plusieurs postes dont un SI d'entreprise (firewall, DMZ, intranet, authentification centralisée, serveur de fichiers, quelques postes de travail interne), une machine d'attaquant (hacker) et quelques autres servant à l'intégration de l'ensemble.

Pour une utilisation sur un poste personnel depuis le dépôt github, la procédure est expliquée dans le README.md.

Pour une utilisation dans la VM "tp-sec-debian", MI-LXC est déjà installé et l'infrastructure déployée. Il faut passer root puis aller dans le dossier `/root/mi-lxc`. Ensuite, `./mi-lxc.py start`.

Une fois l'environnement démarré, le firewall est à configurer sur la machine Firewall. Vous devrez travailler en root (mot de passe : root) pendant tout le TP (seul root est habilité à manipuler le pare-feu).

Avant de commencer le TP, vous devez lire le chapitre [Netfilter](https://fr.wikibooks.org/wiki/Administration_r%C3%A9seau_sous_Linux/Netfilter) du Wikibook "Administration Réseau sous Linux".


Protection de la machine firewall
=================================

Nous allons maintenant mettre en place un firewall sur la machine Firewall. En utilisant la page de manuel d'iptables, affichez l'ensemble des règles actives. Vous devriez voir quelque chose qui ressemble à :

```
Chain INPUT (policy ACCEPT 819K packets, 170M bytes)
pkts bytes target prot opt in out source destination
Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
pkts bytes target prot opt in out source destination
Chain OUTPUT (policy ACCEPT 728K packets, 188M bytes)
pkts bytes target prot opt in out source destination
```

Vous voyez donc pour chaque chaîne :

* la "policy" (comportement par défaut) ;
* le nombre de paquets qui a traversé les règles de la chaîne ;
* le nombre d'octets correspondants.

Pour le moment, aucune règle supplémentaire n'est définie ; par la suite, cette commande vous permettra de lister l'ensemble des règles actives dans la table filter. Ici, Netfilter accepte donc tous les paquets.

Première règle iptables
-----------------------

Vérifiez que vous pouvez vous connecter en SSH sur la machine Firewall depuis la machine Hacker (`ssh root@10.0.0.2`).

Nous allons maintenant interdire toutes les connexions sur le port 22 (SSH). Pour cela, il faut interdire dans la chaîne INPUT les paquets TCP sur le port 22.

Essayez maintenant de vous connecter en SSH sur votre machine Firewall depuis la machine Hacker.

Nous avons ici utilisé l'action DROP. Vous pouvez constater que la connexion est bien refusée mais que le client SSH met un certain temps à s'en apercevoir. Pourquoi ? Comment changer ce comportement ?


Priorité des règles
-------------------

Un même paquet peut correspondre à plusieurs règles de filtrage, éventuellement contradictoires : Netfilter applique les règles dans l'ordre et choisit systématiquement la première règle correspondant au paquet (attention, certains firewalls procèdent dans le sens contraire...).

Afin de tester ce comportement, nous allons utiliser les paramètres de filtrage de la section "Critères" du Wikibooks.

* Montrez sur un exemple que l'ordre des règles compte. Pour modifier le filtrage, vous aurez besoin de supprimer des règles et d'en ajouter à des endroits spécifique : référez-vous au manuel d'iptables.
* Mettez en place un jeu de règles autorisant le SSH sur Firewall uniquement depuis le LAN de l'entreprise (machine Commercial ou Admin).


Modules IPTables
================

Iptables est extensible par un système de modules. Vous trouverez une description des modules existant dans le manuel d'iptables, dans la section MATCH EXTENSIONS.

Multiport
---------

Le module multiport permet de créer des règles correspondant à plusieurs ports : `iptables -A INPUT -m multiport -p tcp --dports port1,port2,port3 -j...`

Créez une règle avec multiport autorisant les ports 22 et 53.


Suivi de connexion (conntrack)
------------------------------

Netfilter permet le suivi des connexions via le module state (firewall _stateful_). Ce module permet d'identifier les nouveaux flux, les flux établis et les flux liés à un autre flux. Ce suivi de connexion permet d'affiner le filtrage de certains protocoles.

Le module state définit plusieurs états possibles pour les flux réseaux, dont :

* NEW : c'est une nouvelle connexion
* ESTABLISHED : cette connexion est déjà connue (elle est passée par l'état NEW il y a peu de temps)
* RELATED : cette connexion est liée ou dépendante d'une connexion déjà ESTABLISHED. Attention, seul le premier paquet d'une connexion peut être RELATED, les suivants sont ESTABLISHED. Essentiellement utilisé pour le protocole FTP.

Créez une règle pour autoriser, en sortie du Firewall, uniquement les réponses à des connexions SSH entrantes.


Mise en place d'une politique de sécurité réseau
================================================

Spécification
-------------

Décrivez sur papier une politique de sécurité réseau raisonnable pour le SI complet (segmentation en zones et autorisations spécifiques entre ces zones). À vous d'explorer le SI à partir des éléments suivants :

* DMZ fournit un ensemble de services à l'interface entre le SI et le reste du monde
* NIS fournit une authentification centralisée, nécessaire à tous les postes du SI (dont la DMZ)
* Intranet fournit des applications internes, non accessibles au reste du monde
* Filer héberge un partage de fichier qui doit être accessible à tous les postes clients
* Commercial, Dev et Admin sont des postes client. Le commercial doit pouvoir accéder au site web intranet, le développeur doit pouvoir mettre à jour cet intranet et l'admin doit pouvoir administrer l'ensemble du parc

La commande `netstat -lnp` permet d'afficher les ports en écoute sur une machine donnée. Le serveur NIS a besoin de rendre accessibles ypserv et rpcbind.

Votre description (matrice de flux sous forme tabulaire ou graphique) doit être claire et suffisamment précise pour être non ambiguë : un autre étudiant, avec cette description uniquement, devrait pouvoir refaire _exactement_ la même implémentation avec IPTables.

__Faites valider votre matrice de flux papier par l'enseignant.__


Implémentation
--------------

Implémentez votre matrice de flux sur la machine firewall. Vous aurez besoin des quelques manipulations suivantes :

* Pour créer une zone, vous devez y associer un bridge (switch virtuel). Sur la machine hôte, `brctl addbr <nom_du_bridge>`
* Pour rebrancher une carte réseau d'un conteneur sur un autre bridge, il faut sur l'hôte :
	* Découvrir quel est le nom de cette carte réseau côté hôte : `./mi-lxc.py shownics <nom_du_conteneur>` (exemple de nom : "hacker")
	* La débrancher du bridge actuel : `brctl delif <ancien_bridge> <nom_carte_reseau>`
	* La rebrancher sur le nouveau bridge : `brctl addif <nouveau_bridge> <nom_carte_reseau>`
* Pour créer un nouvelle carte réseau dans un conteneur (typiquement sur Firewall), il faut sur l'hôte :
	* Créer une carte virtuelle : `ip link add name <nom_cote_hote> type veth peer name <nom_temporaire>`
	* L'activer : `ip link set dev <nom_cote_hote> up`
	* L'ajouter au bridge : `brctl addif <bridge> <nom_cote_hote>`
	* L'associer au conteneur : `lxc-device -n <nom_du_conteneur> add <nom_temporaire> <nom_dans_le_conteneur>`
	* Puis la configurer dans le conteneur (up, adresse IP)

Bonus
=====

Shorewall
---------
Vous avez pu vous rendre compte de la complexité du réglage manuel du pare-feu. En particulier, la lecture des règles existantes ou la vérification de leur cohérence présente de nombreuses difficultés. La maintenance d'une telle solution est donc complexe dans un environnement de production : les règles changent souvent et demandent une inspection régulière.

De nombreuses solutions ont été développées pour faciliter la gestion des règles iptables. Nous allons ici utiliser Shorewall. Shorewall n'est pas un démon et repose entièrement sur iptables. Il consiste en une série de scripts permettant de simplifier la configuration, notamment en définissant la notion de zones. Installez shorewall (`apt-get install shorewall`) puis réimplémentez votre politique de sécurité avec cet outil.


FTP
---

FTP, comme quelques autre protocoles, présente des difficultés particulières pour les firewall. En effet, la partie contrôle de FTP se passe sur une connexion (et un port) distinct de la partie données. Les firewalls modernes savent créer le lien entre ces deux connexions pour y appliquer un contrôle adapté.

Un serveur FTP est installé dans la DMZ, configurez le firewall pour permettre son usage depuis une machine externe au SI. Attention, le FTP demande une connexion avec un utilisateur existant, par exemple debian/debian.

