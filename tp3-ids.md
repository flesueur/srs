# TP 3 : IDS

_François Lesueur ([francois.lesueur@insa-lyon.fr](mailto:francois.lesueur@insa-lyon.fr))_

Ce TP sera réalisé dans l'infrastructure MI-LXC, disponible [ici](https://github.com/flesueur/mi-lxc) (nécessite un Linux en root) ou dans la VM "tp-sec-debian" disponible en salle de TP (`/machines_virtuelles/secu_vms/master/tp-sec-debian.sh`, debian/debian et root/root). L'infrastructure déployée simule plusieurs postes dont un SI d'entreprise (firewall, DMZ, intranet, authentification centralisée, serveur de fichiers, quelques postes de travail interne), une machine d'attaquant (hacker) et quelques autres servant à l'intégration de l'ensemble.

* Pour une utilisation sur un poste personnel depuis le dépôt github, la procédure est expliquée dans le README.md.
* Pour une utilisation dans la VM "tp-sec-debian", MI-LXC est déjà installé et l'infrastructure déployée. Il faut passer root puis aller dans le dossier `/root/mi-lxc`. Ensuite, `git pull` (pour récupérer les dernières modifications...) puis `./mi-lxc.py start`.

Durant ce TP, vous allez analyser un scénario d'attaque et travailler à sa détection par NIDS (Network IDS), HIDS (Host IDS) puis corrélation d'alertes. Les outils utilisés seront Suricata (NIDS), OSSEC (HIDS), Prelude (SIEM et corrélation).

L'architecture réseau et les petits guides de démarrage des outils à manipuler sont à retrouver dans les (nombreuses) annexes (à diagonaliser avant de démarrer, bien sûr) !

Analyse de l'attaque
====================

À partir du scénario d'attaque développé dans le TP1, proposez une stratégie de détection mêlant NIDS, HIDS et corrélation. **Faîtes valider cette stratégie par un enseignant !**

Yakafokon
=========

Déployez cette stratégie sur le SI. Les logiciels sont pré-installés mais il faut :

* créer les règles adaptées (règles vides au départ) ;
* connecter les sondes à prelude-manager ;
* créer le script de corrélation adapté.

Une suggestion (forte) est d'écrire et tester les règles de chaque sonde indépendamment (suricata, OSSEC), sans passage par prelude-manager, via leurs fichiers de logs respectifs. Une fois que la sonde fonctionne comme souhaité, elle peut être reliée au manager. Il faut enfin vérifier que les alertes remontent bien.

Annexes
=======

Architecture réseau
-------------------

L'infrastructure réseau du SI à surveiller est la suivante :

* firewall en entrée de réseau
* nis, dmz, intranet et filer sont des serveurs internes
* commercial, dev et admin sont postes clients internes


Les logiciels suivants sont pré-installés :

* Suricata (firewall)
* Prelude-manager (firewall)
* Prelude-correlator (firewall)
* Prewikka (firewall)
* OSSEC (dmz)


Suricata (NIDS)
---------------

Suricata est un IDPS réseau qui utilise le même format de règles que Snort. Il est installé sur le Firewall. Sa configuration est dans `/etc/suricata/suricata-debian.yaml` et nous allons utiliser le fichier de règles `/etc/suricata/rules/local.rules`. Vous pourrez visualiser le fichier de log `/var/log/suricata/fast.log`.

La règle Snort
```
 alert tcp 10.0.0.1 80 -> 192.168.1.0/24 111 (content:"Waldo"; msg:"Waldo's here";sid:1001)
```

signifie par exemple que :

* On étudie les paquets TCP allant de `10.0.0.1:80` vers le sous-réseau:port `192.168.1.0/24:111`
* Contenant la chaîne "Waldo"
* Le log affichera "Waldo's here" s'il y a une correspondance
* alert peut être remplacé par drop pour jeter le paquet au lieu de le journaliser
* Le sid est un identifiant de règle, _il doit être unique_
* Les règles peuvent être composées de nombreux éléments (contenu, taille, expressions régulières, etc. Tout est ici : [Règles Suricata](https://suricata.readthedocs.io/en/latest/rules/index.html). `http_stat_code` permet par exemple de surveiller le code de retour HTTP. <!-- \url{http://manual.snort.org/node32.html}) -->

Lisez les règles présentes dans le fichier `local.rules`. Déclenchez la règle "COMMUNITY WEB-MISC Test Script Access" en accédant au serveur web de la DMZ. La requête est-elle exécutée avec succès ?

Analysez ensuite la signature de CodeRed (un ver se propageant via une faille des serveurs web Microsoft IIS). Arrivez-vous à déclencher cette alerte ? Peut-on vraiment parler d'intrusion ? Comment qualifier cette alerte ?

Pour avoir un aperçu du type de règles fournies par défaut avec Suricata, vous pouvez exécuter `suricata-oinkmaster-updater` qui téléchargera des listes de règles dans `/etc/suricata/rules/emerging-*.rules`.

Lorsque vous modifiez les règles, il faut recharger le fichier avec  `service suricata restart`.

<!-- 
Dans la configuration préinstallée, Suricata est en écoute seulement (pas IPS). D'autres configurations de Suricata permettent de le mettre en interception, par exemple en activant `nfqueue` dans `/etc/default/suricata`. Pour activer ensuite le passage par Suricata dans la configuration installée, il faut ajouter une décision NFQUEUE au lieu des décision ACCEPT dans les règles IPTables. Par exemple, pour faire passer par Suricata tout le trafic forwardé :
`iptables -A FORWARD -j NFQUEUE` (attention, suricata prend des décisions définitives, le reste des règles n'est pas appelé ensuite ! Une solution plus évoluée utilisant les marques et le mode repeat de Suricata est présentée [ici](https://docs.mirantis.com/mcp/latest/mcp-security-best-practices/use-cases/idps-vnf/ips-mode/nfq.html))
-->

> Dans la configuration préinstallée, Suricata est en écoute seulement (pas IPS). D'autres configurations de Suricata permettent de le mettre en interception. Si vous souhaitez tester Suricata en IPS, voici les étapes :
>
> * `cp /lib/systemd/system/suricata.service  /etc/systemd/system/suricata.service`
> * Remplacer `--af-packet` par `-q0` dans `/etc/systemd/system/suricata.service`
> * Utiliser `drop` au lieu de `alert` dans les règles
> * `service suricata restart`
>
> Pour activer ensuite le passage par Suricata dans la configuration installée, il faut ajouter une décision NFQUEUE au lieu des décision ACCEPT dans les règles IPTables. Par exemple, pour faire passer par Suricata tout le trafic forwardé :
`iptables -A FORWARD -j NFQUEUE` (attention, suricata prend des décisions définitives, le reste des règles n'est pas appelé ensuite ! Une solution plus évoluée utilisant les marques et le mode repeat de Suricata est présentée [ici](https://docs.mirantis.com/mcp/latest/mcp-security-best-practices/use-cases/idps-vnf/ips-mode/nfq.html))



OSSEC (HIDS)
------------

OSSEC est un HIDS installé sur la machine DMZ. Il permet notamment de surveiller les logs (dont accès/refus d'accès du serveur web) et les fichiers présents sur la machine. Sa configuration se trouve dans `/var/ossec/etc/ossec.conf`.

La partie `syscheck` est responsable de surveiller les fichiers présents (doc [ici](https://ossec.github.io/docs/manual/syscheck/index.html), FAQ [ici](https://www.ossec.net/docs/faq/syscheck.html#why-aren-t-new-files-creating-an-alert) avec la règle à ajouter impérativement), il est aussi possible d'analyser les logs (doc [ici](https://ossec.github.io/docs/manual/monitoring/index.html)). Attention pour syscheck, la surveillance d'apparition de nouveaux fichiers (non présente par défaut, il faut ajouter une option et une règle, tout est détaillé sur la page de doc) nécessite de désactiver `realtime`. Pour utiliser syscheck, il faut attendre que la surveillance soit prête (cela prend un certain temps au démarrage), à surveiller dans `/var/ossec/logs/ossec.log`. Les alertes sont ensuite dans `/var/ossec/logs/alerts/alerts.log`. Chaque alerte contient un identifiant de règle, qui permet de retrouver la règle originale dans les fichiers `/var/ossec/rules/*`

<!-- Pour surveiller les fichiers de log, il convient de modifier dans `ossec.conf` le chemin des logs apache (ils sont dans `/var/log/apache2/`).
-->

Prelude-manager (concentrateur)
-------------------------------

Prelude-manager est un concentrateur d'alertes. Il utilise le format d'alerte IDMEF et Suricata et OSSEC savent lui remonter leurs alertes. Le jumelage entre le manager et les sondes est réalisé via un échange [TLS SRP](https://en.wikipedia.org/wiki/TLS-SRP) géré par `prelude-admin`.

Lors du jumelage, chaque sonde conserve un profil local (`/etc/preludes/profiles/<profile>/` contenant sa configuration et son matériel cryptographique (certificat, clés). Le manager, lui, a signé le certificat (avec sa clé privée) mais ne conserve pas de liste des sondes enregistrées. `prelude-admin list -l` permet de lister les profils actuellement configurés sur la machine locale.

Pour jumeler une sonde au manager, il faut :

* côté manager (VM Firewall) : `prelude-admin registration-server prelude-manager`
* côté sonde : `prelude-admin register <profile> "idmef:w" <IP manager> --uid 0 --gid 0`, avec
	* `<profile>` : nom du profil, suricata pour Suricata et OSSEC-DMZ pour OSSEC
	* `<IP manager>` : l'IP du manager

<!-- * `<UID>/<GID>` : l'uid et le gid de la sonde qui va remonter les alertes (ici 0/0 pour suricata, 1005/1005 pour OSSEC), utilisé pour poser les droits sur le matériel cryptographique généré lors du jumelage -->


Prewikka (interface web de visualisation)
-----------------------------------------

Prewikka est une application web permettant la visualisation de l'état des sondes enregistrées ainsi que des alertes. Il est installé sur la machine Firewall. Il doit être démarré avec `prewikka-httpd` et est ensuite accessible depuis le navigateur d'un poste de travail interne à l'URL `http://firewall:8000`. Le compte est admin/admin (bien sûr, la première étape dans un vrai déploiement est de changer cela...).

Si prelude-manager apparaît offline, il faut le relancer (sur la VM Firewall) : `service prelude-manager restart`  (il y a parfois un message d'erreur mais en fait, ça marche [au moins en partie]).

Par défaut, la liste des agents (les sondes) est vide. Il faut ajouter Suricata (activer prelude dans la configuration suricata `/etc/suricata/suricata-debian.yaml` puis `service suricata restart`) puis OSSEC (activer prelude dans la configuration OSSEC `/var/ossec/etc/ossec.conf` puis `service ossec restart`).

Vous pouvez ensuite visualiser des événements. Si tout n'apparaît pas, quelques restarts de services peuvent faire tomber en marche !

Prelude-correlator (corrélation)
--------------------------------

Jumelage avec le manager : `prelude-admin register prelude-correlator "idmef:rw" 127.0.0.1 --uid prelude-correlator --gid prelude-correlator` (accès read/write cette fois-ci car la corrélation nécessite la lecture des alertes remontées et l'écriture de nouvelles alertes). Puis relancer le service prelude-correlator.

Une règle de corrélation est en fait un script python. Vous trouverez un exemple dans `/usr/share/doc/prelude-correlator/examples`. Pour l'installer, tapez :

* `python setup.py build`
* ̀`python setup.py install`

Puis dans `/etc/prelude-correlator/prelude-correlator.conf`, ajoutez :
```
[MyPlugin]
disable=false
```

Enfin, relancez prelude-correlator. Une documentation plus complète est disponible [ici](https://www.prelude-siem.org/projects/prelude/wiki/PreludeCorrelator). YOLO !

