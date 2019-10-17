# TP 2 : Firewall

_François Lesueur ([francois.lesueur@insa-lyon.fr](mailto:francois.lesueur@insa-lyon.fr))_

Ce TP sera réalisé dans l'infrastructure MI-LXC, disponible [ici](https://github.com/flesueur/mi-lxc) ou dans la VM "tp-sec-debian" disponible en salle de TP (`/machines_virtuelles/secu_vms/master/tp-sec-debian.sh`, root/root). L'infrastructure déployée simule plusieurs postes dont un SI d'entreprise (firewall, DMZ, intranet, authentification centralisée, serveur de fichiers, quelques postes de travail interne), une machine d'attaquant (hacker) et quelques autres servant à l'intégration de l'ensemble.

Pour une utilisation sur un poste personnel depuis le dépôt github, la procédure est expliquée dans le README.md.

Pour une utilisation dans la VM "tp-sec-debian", MI-LXC est déjà installé et l'infrastructure déployée. Il faut passer root puis aller dans le dossier `/root/mi-lxc`. Ensuite, `./mi-lxc.py start`.

Une fois l'environnement démarré, le firewall est à configurer sur la machine target-router (`./mi-lxc.py attach target-router`). Vous devrez travailler en root (mot de passe : root) pendant tout le TP (seul root est habilité à manipuler le pare-feu).

Avant de commencer le TP, vous devez lire le chapitre [Netfilter](https://fr.wikibooks.org/wiki/Administration_r%C3%A9seau_sous_Linux/Netfilter) du Wikibook "Administration Réseau sous Linux".

_Note : Pour les plus aventuriers, il est possible d'utiliser nftables au lieu d'iptables_

Protection de la machine firewall
=================================

Nous allons maintenant mettre en place un firewall sur la machine target-router. En utilisant la page de manuel d'iptables, affichez l'ensemble des règles actives. Vous devriez voir quelque chose qui ressemble à :

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

Pour le moment, peu de règles sont définies ; par la suite, cette commande vous permettra de lister l'ensemble des règles actives dans la table filter.

Première règle iptables
-----------------------

Vérifiez que vous pouvez vous connecter en SSH sur la machine target-router depuis la machine isp-a-hacker (`ssh root@10.180.0.10`).

Nous allons maintenant interdire toutes les connexions sur le port 22 (SSH). Pour cela, il faut interdire dans la chaîne INPUT les paquets TCP sur le port 22 avec la cible DROP.

Essayez maintenant de vous connecter en SSH sur votre machine Firewall depuis la machine Hacker.

Nous avons ici utilisé l'action DROP. Vous pouvez constater que la connexion est bien refusée mais que le client SSH met un certain temps à s'en apercevoir. Pourquoi ? Comment changer ce comportement ?


Priorité des règles
-------------------

Un même paquet peut correspondre à plusieurs règles de filtrage, éventuellement contradictoires : Netfilter applique les règles dans l'ordre et choisit systématiquement la première règle correspondant au paquet (attention, certains firewalls procèdent dans le sens contraire...). On parle alors de masquage de règles.

Afin de tester ce comportement, nous allons utiliser les paramètres de filtrage de la section "Critères" du Wikibooks.

* Montrez sur un exemple que l'ordre des règles compte. Pour modifier le filtrage, vous aurez besoin de supprimer des règles et d'en ajouter à des endroits spécifique : référez-vous au manuel d'iptables.
* Mettez en place un jeu de règles autorisant le SSH sur le routeur uniquement depuis le LAN de l'entreprise (machine Commercial ou Admin).

Dans la pratique, le masquage est souvent utilisé volontairement pour spécifier un cas général peu prioritaire et des cas particuliers plus prioritaires. Évidemment, c'est également source d'erreurs dans ces cas complexes.

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

L'objectif d'une politique de sécurité réseau est de limiter les services accessibles depuis l'extérieur (approche historique) ainsi que de segmenter le réseau interne en zones distinctes (avec autorisations limitées entre ces zones, afin de limiter les risques de propagation automatique/pivot). Décrivez sur papier une politique de sécurité réseau raisonnable pour le SI complet. À vous d'explorer le SI à partir des éléments suivants :

* DMZ fournit un ensemble de services à l'interface entre le SI et le reste du monde
* LDAP fournit une authentification centralisée, nécessaire à tous les postes du SI (dont la DMZ)
* Intranet fournit des applications internes, non accessibles au reste du monde
* Filer héberge un partage de fichier qui doit être accessible à tous les postes clients
* Commercial, Dev et Admin sont des postes client. Le commercial doit pouvoir accéder au site web intranet, le développeur doit pouvoir mettre à jour cet intranet et l'admin doit pouvoir administrer l'ensemble du parc
* Les noms des conteneurs peuvent être affichés avec `./mi-lxc.py` (sans paramètres), les machines ne commençant pas par target représentent le "reste du monde" (WAN)

La commande `netstat -lnp` permet d'afficher les ports en écoute sur une machine donnée.

Votre description (matrice de flux sous forme tabulaire ou graphique) doit être claire et suffisamment précise pour être non ambiguë : un autre étudiant, avec cette description uniquement, devrait pouvoir refaire _exactement_ la même implémentation avec IPTables.

__Faites valider votre matrice de flux papier par l'enseignant.__


Implémentation
--------------

Implémentez votre matrice de flux sur la machine firewall. Vous aurez besoin de procéder en deux étapes :

* Segmenter le réseau target :
	* Éditer `global.json` pour spécifier les interfaces sur le routeur, dans la section "target". Il faut ajouter des bridges (dont le nom doit commencer par "target-") et découper l'espace 10.100.0.0/16. Enfin, il faut ajouter les interfaces eth2, eth3, ... ainsi créées à la liste des `asdev` definie juste au-dessus
	* Éditer `groups/target/local.json` pour éditer les adresses des interfaces et les bridges des machines internes (attention, pour un bridge nommé précédemment "target-dmz", il faut simplement écrire "dmz" ici, la partie "target-" est ajoutée automatiquement). Vous devrez mettre à jour les serveurs mentionnées dans les templates ldapclient et sshfs ou mettre à jour le DNS (fichier `/etc/nsd/target.milxc.zone` sur target-dmz)
	* Exécuter `./mi-lxc.py print` pour visualiser la topologie redéfinie
	* Exécuter `./mi-lxc.py stop && ./mi-lxc.py renet && ./mi-lxc.py start` pour mettre à jour l'infrastructure déployée
* Implémenter de manière adaptée les commandes iptables sur la machine target-router (dans la chaîne FORWARD). Si possible dans un script (qui nettoie les règles au début), en cas d'erreur.

> L'arborescence de MI-LXC et les fichiers json manipulés ici sont décrits [ici](https://github.com/flesueur/mi-lxc#how-to-extend).

Évaluation
----------

Évaluez le déroulé du scénario d'intrusion réalisé au TP1 une fois les règles de firewall déployées.


Contournement de la politique
-----------------------------

Vous souhaitez fournir un accès vers le serveur interne de prototypage (intranet) à un client externe. Vous allez créer un tunnel pour contourner la politique de sécurité. Vous disposez pour cela des machines "dev" (votre poste de travail interne) et "isp-a-ome" (une machine extérieure, à votre domicile).

Côté home :
```	
service apache2 stop   # Libération du port 80
while true; do nc -v -l -p 80 -c "nc -l -p 8080"; done
```

Côté dev :

`while true; do nc 10.150.0.3 80 -c "nc 10.100.0.5 80"; sleep 2; done`

Testez (avec la machine du hacker) que vous pouvez bien accéder au serveur intranet sans aucun contrôle via l'URL `http://10.150.0.3:8080`

Il est très difficile de bloquer ou même détecter les tunnels (imaginez un tunnel chiffré par SSH, ou qui mime une apparence de HTTP, etc.)


Bonus
=====

Shorewall
---------
Vous avez pu vous rendre compte de la complexité du réglage manuel du pare-feu. En particulier, la lecture des règles existantes ou la vérification de leur cohérence présente de nombreuses difficultés. La maintenance d'une telle solution est donc complexe dans un environnement de production : les règles changent souvent et demandent une inspection régulière.

De nombreuses solutions ont été développées pour faciliter la gestion des règles iptables. Nous allons ici utiliser Shorewall. Shorewall n'est pas un démon et repose entièrement sur iptables. Il consiste en une série de scripts permettant de simplifier la configuration, notamment en définissant la notion de zones. Installez shorewall (`apt-get install shorewall`) puis réimplémentez votre politique de sécurité avec cet outil.

_Note : Pour les plus aventuriers, il est possible d'expérimenter firewalld, outil plus moderne_

FTP
---

FTP, comme quelques autre protocoles, présente des difficultés particulières pour les firewall. En effet, la partie contrôle de FTP se passe sur une connexion (et un port) distinct de la partie données. Les firewalls modernes savent créer le lien entre ces deux connexions pour y appliquer un contrôle adapté.

Un serveur FTP est installé dans la DMZ, configurez le firewall pour permettre son usage (transfert de fichiers) depuis une machine externe au SI. Attention, le FTP demande une connexion avec un utilisateur existant, par exemple debian/debian.
