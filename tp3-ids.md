# TP 3 : IDS

_François Lesueur ([francois.lesueur@insa-lyon.fr](mailto:francois.lesueur@insa-lyon.fr))_

Ce TP sera réalisé dans la VM MI-LXC disponible [ici](https://filesender.renater.fr/?s=download&token=d6af4d45-5e41-4cde-accd-c377812ba285). L'infrastructure déployée simule plusieurs postes dont un SI d'entreprise (firewall, DMZ, intranet, authentification centralisée, serveur de fichiers, quelques postes de travail internes de l'entreprise _Target_), une machine d'attaquant "isp-a-hacker" et quelques autres servant à l'intégration de l'ensemble.

> Pour les curieux, le code de MI-LXC, qui sert à générer cette VM automatiquement, est disponible avec une procédure d'installation documentée [ici](https://github.com/flesueur/mi-lxc)

<!--  ou dans la VM "tp-sec-debian" disponible en salle de TP (`/machines_virtuelles/secu_vms/master/tp-sec-debian.sh`, debian/debian et root/root). L'infrastructure déployée simule plusieurs postes dont un SI d'entreprise (firewall, DMZ, intranet, authentification centralisée, serveur de fichiers, quelques postes de travail interne), une machine d'attaquant (hacker) et quelques autres servant à l'intégration de l'ensemble. La compréhension plus fine du SI de l'entreprise ciblée fait partie des objectifs du TP. -->


Vous devez vous connecter à la VM en root/root. MI-LXC est déjà installé et l'infrastructure déployée, il faut avec un terminal aller dans le dossier `/root/mi-lxc`. Pour démarrer l'infrastructure, tapez `./mi-lxc.py start`. Durant ce TP, vous allez analyser un scénario d'attaque et travailler à sa détection par NIDS (Network IDS), HIDS (Host IDS) puis corrélation d'alertes. Les outils utilisés seront Suricata (NIDS), OSSEC (HIDS), Prelude (SIEM et corrélation).

L'architecture réseau et les petits guides de démarrage des outils à manipuler sont à retrouver dans les (nombreuses) annexes (à diagonaliser avant de démarrer, bien sûr) !

> Dans la VM et sur les machines MI-LXC, vous pouvez installer des logiciels supplémentaires. Par défaut, vous avez mousepad pour éditer des fichiers de manière graphique. La VM peut être affichée en plein écran. Si cela ne fonctionne pas, il faut parfois changer la taille de fenêtre manuellement, en tirant dans l'angle inférieur droit, pour que VirtualBox détecte que le redimensionnement automatique est disponible. Il y a une case adéquate (taille d'écran automatique) dans le menu écran qui doit être cochée. Si rien ne marche, c'est parfois en redémarrant la VM que cela peut se déclencher. Mais il *faut* la VM en plein écran.

Analyse de l'attaque
====================

À partir du scénario d'attaque développé dans le TP1, proposez une stratégie de détection mêlant NIDS, HIDS et corrélation. Par exemple :

* Un NIDS (Suricata) est un IDS réseau, tel que vu en cours. Il permet de détecter des motifs dans des paquets réseau
* Un HIDS (OSSEC) est un IDS hôte, une sonde installée sur une machine. Il permet de surveiller des logs, des apparitions/modifications de fichiers
* Un corrélateur (Prelude) permet de collecter des alertes variées vers un point unique. Il permet également d'associer des alertes qui correspondent à un même événement vu par plusieurs sondes.

**Faîtes valider cette stratégie par un enseignant !**

Yakafokon
=========

Déployez cette stratégie sur le SI. Les logiciels sont pré-installés mais il faut :

* configurer les outils
* créer les règles adaptées (règles vides au départ) ;
* connecter les sondes à prelude-manager ;
* créer le script de corrélation adapté.

Une suggestion (forte) est d'écrire et tester les règles de chaque sonde indépendamment (suricata, OSSEC), sans passage par prelude-manager, via leurs fichiers de logs respectifs. Une fois que la sonde fonctionne comme souhaité, elle peut être reliée au manager. Il faut enfin vérifier que les alertes remontent bien.

Annexes
=======

Architecture réseau
-------------------

L'infrastructure réseau du SI à surveiller est la suivante :

* router en entrée de réseau
* ldap, dmz, intranet et filer sont des serveurs internes
* commercial, dev et admin sont des postes clients internes


Les logiciels suivants sont pré-installés :

* Suricata (router)
* Prelude-manager (router)
* Prelude-correlator (router)
* Prewikka (router)
* OSSEC (dmz)


Suricata (NIDS)
---------------

Suricata est un IDPS réseau. Il est installé sur "target-router". Sa configuration est dans `/etc/suricata/suricata.yaml` et nous allons utiliser le fichier de règles `/etc/suricata/rules/local.rules`. Vous pourrez visualiser les alertes dans le fichier de log `/var/log/suricata/fast.log`.

La règle
```
 alert tcp 10.0.0.1 80 -> 192.168.1.0/24 111 (content:"Waldo"; msg:"Waldo's here";sid:1001)
```

signifie par exemple que :

* On étudie les paquets TCP allant de `10.0.0.1:80` vers le sous-réseau:port `192.168.1.0/24:111`
* Contenant la chaîne "Waldo"
* Le log affichera "Waldo's here" s'il y a une correspondance
* `alert` peut être remplacé par `drop` pour jeter le paquet au lieu de le journaliser
* Le `sid` est un identifiant de règle, _il doit être unique_
* Les règles peuvent être composées de nombreux éléments (contenu, taille, expressions régulières, etc.). Tout est ici : [Règles Suricata](https://suricata.readthedocs.io/en/latest/rules/index.html). `http_stat_code` permet par exemple de surveiller le code de retour HTTP. <!-- \url{http://manual.snort.org/node32.html}) -->

Lisez les règles présentes dans le fichier `local.rules`. Déclenchez la règle "COMMUNITY WEB-MISC Test Script Access" en accédant au serveur web de la DMZ (http://www.target.mixlc), par ex. depuis la machine `isp-a-hacker`. La requête est-elle exécutée avec succès ?

Analysez ensuite la signature de CodeRed (un ver se propageant via une faille des serveurs web Microsoft IIS). Arrivez-vous à déclencher cette alerte ? Peut-on vraiment parler d'intrusion ? Comment qualifier cette alerte ?

Pour avoir un aperçu du type de règles fournies par défaut avec Suricata, vous pouvez exécuter `suricata-oinkmaster-updater` qui téléchargera des listes de règles dans `/etc/suricata/rules/*.rules`.

Lorsque vous modifiez les règles, il faut recharger le fichier en relançant le service avec `service suricata restart`.

> Dans la configuration préinstallée, Suricata est en écoute seulement (donc "IDS" mais pas "IPS"). D'autres configurations de Suricata permettent de le mettre en interception. Si vous souhaitez tester Suricata en IPS, voici les étapes :
>
> * `cp /lib/systemd/system/suricata.service  /etc/systemd/system/suricata.service`
> * Remplacer `--af-packet` par `-q0` dans `/etc/systemd/system/suricata.service`
> * Recharger systemd `systemctl daemon-reload`
> * Utiliser `drop` au lieu de `alert` dans les règles
> * `service suricata restart`
>
> Pour ensuite activer le passage des paquets par Suricata, il faut ajouter une décision NFQUEUE au lieu des décision ACCEPT dans les règles IPTables. Par exemple, pour faire passer par Suricata tout le trafic forwardé :
`iptables -I FORWARD -j NFQUEUE` (attention, suricata prend des décisions définitives, le reste des règles n'est pas appelé ensuite ! [Une solution plus évoluée utilisant les marques et le mode repeat de Suricata existe.](https://docs.mirantis.com/mcp/latest/mcp-security-best-practices/use-cases/idps-vnf/ips-mode/nfq.html))



OSSEC (HIDS)
------------
OSSEC est un HIDS installé sur la machine "target-dmz". Il permet notamment de surveiller les logs (dont accès/refus d'accès du serveur web) et les fichiers présents sur la machine. Sa configuration se trouve dans `/var/ossec/etc/ossec.conf`.

Les alertes sont dans `/var/ossec/logs/alerts/alerts.log`. Chaque alerte contient un identifiant de règle, qui permet de retrouver la règle originale dans les fichiers `/var/ossec/rules/*.xml`.

### syscheck
Le module syscheck est responsable de surveiller les fichiers présents pour détecter leurs modifications ou même les nouveaux (pas activé par défaut). Il se configure dans la section `<syscheck>` de `/var/ossec/etc/ossec.conf`. Lire la [doc](https://ossec.github.io/docs/manual/syscheck/index.html) et cette réponse de [FAQ](https://www.ossec.net/docs/faq/syscheck.html#why-aren-t-new-files-creating-an-alert).

Sur la machine "target-dmz" les fichiers uploadés sur dokuwiki sont stockés dans `/var/lib/dokuwiki/data/media`. Configurez ce qu'il faut comme indiqué précédemment (une option et une règle) pour obtenir une alerte à chaque nouveau fichier sur le wiki.

Attention :
* dans la configuration du `<directories>` à surveiller il faut désactiver `realtime` explicitement
* la règle est à ajouter impérativement dans un `<group>`
* syscheck fonctionne grâce à des scans réguliers (très lents pour ne pas impacter le système) et compare les résultats avec la base du précédent scan. Il faut donc attendre que le scan soit passé et cela prend un certain temps... On peut le surveiller dans `/var/ossec/logs/ossec.log`
  * début du scan : "INFO: Starting syscheck database (pre-scan)."
  * fin du scan (peut prendre plusieurs minutes !) : "INFO: Finished creating syscheck database (pre-scan completed)."

Pour tester vous aurez besoin du mot de passe de l'utilisateur `admin` sur le wiki (cf. TP1) et d'utiliser le "Media Manager".

Plutôt qu'attendre on peut déclencher un re-scan du système avec `service ossec restart` ou `/var/ossec/bin/agent_control -r -u 000`.

### logs
Il est aussi possible d'analyser les logs : [doc](https://ossec.github.io/docs/manual/monitoring/index.html)

Relancez depuis la machine "isp-a-hacker" le bruteforce sur le mot de passe de l'admin du wiki avec la commande : `python3 tp/intrusion/dokuwiki.py www.target.milxc` et observez les alertes OSSEC.

Prelude-manager (concentrateur)
-------------------------------

Prelude-manager est un concentrateur d'alertes. Il utilise le format d'alerte IDMEF et Suricata et OSSEC savent lui remonter leurs alertes. Le jumelage entre le manager et les sondes est réalisé via un échange [TLS SRP](https://en.wikipedia.org/wiki/TLS-SRP) géré par `prelude-admin`.

Lors du jumelage, chaque sonde conserve un profil local (`/etc/preludes/profiles/<profile>/` contenant sa configuration et son matériel cryptographique (certificat, clés). Le manager, lui, a signé le certificat (avec sa clé privée) mais ne conserve pas de liste des sondes enregistrées. `prelude-admin list -l` permet de lister les profils actuellement configurés sur la machine locale.

Pour jumeler une sonde au manager, il faut :

* côté manager (router) : `prelude-admin registration-server prelude-manager`
* côté sonde : `prelude-admin register <profile> "idmef:w" <IP manager> --uid 0 --gid 0`, avec
	* `<profile>` : nom du profil, suricata pour Suricata et OSSEC-DMZ pour OSSEC
	* `<IP manager>` : l'IP du manager

<!-- * `<UID>/<GID>` : l'uid et le gid de la sonde qui va remonter les alertes (ici 0/0 pour suricata, 1005/1005 pour OSSEC), utilisé pour poser les droits sur le matériel cryptographique généré lors du jumelage -->


Prewikka (interface web de visualisation)
-----------------------------------------

Prewikka est une application web permettant la visualisation de l'état des sondes enregistrées ainsi que des alertes. Il est installé sur la machine Firewall. Il doit être démarré avec `prewikka-httpd` et est ensuite accessible depuis le navigateur d'un poste de travail interne à l'URL `http://router:8000`. Le compte est admin/admin (bien sûr, la première étape dans un vrai déploiement est de changer cela...).

Il faut ensuite lancer prelude-manager (sur router) : `service prelude-manager start`.

Par défaut, la liste des agents (les sondes) est vide. Il faut ajouter Suricata (activer prelude dans la configuration suricata `/etc/suricata/suricata.yaml` puis `service suricata restart`) puis OSSEC (activer prelude dans la configuration OSSEC `/var/ossec/etc/ossec.conf` puis `service ossec restart`).

Vous pouvez ensuite visualiser des événements. Si tout n'apparaît pas, quelques restarts de services peuvent faire tomber en marche !

Prelude-correlator (corrélation)
--------------------------------

Jumelage avec le manager : `prelude-admin register prelude-correlator "idmef:rw" 127.0.0.1 --uid 0 --gid 0` (accès read/write cette fois-ci car la corrélation nécessite la lecture des alertes remontées et l'écriture de nouvelles alertes). Puis relancer le service prelude-correlator.

Une règle de corrélation est en fait un script python. Vous trouverez un exemple dans `/usr/share/doc/prelude-correlator/examples`. Pour l'installer, tapez :

* `python setup.py build`
* `python setup.py install`

Puis dans `/etc/prelude-correlator/prelude-correlator.conf`, ajoutez :
```
[MyPlugin]
disable=false
```

Enfin, relancez prelude-correlator. Une documentation plus complète est disponible [ici](https://www.prelude-siem.org/projects/prelude/wiki/PreludeCorrelator). YOLO !


_Crédits : certaines parties ont été inspirées de sujets de [Guillaume Hiet](http://guillaume.hiet.fr/)_
