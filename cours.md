# Notes de cours de 5TC-SRS : Sécurité des Réseaux et des Systèmes

_François Lesueur ([francois.lesueur@insa-lyon.fr](mailto:francois.lesueur@insa-lyon.fr), [@FLesueur](https://twitter.com/FLesueur))_

<!-- [Intro](#introduction-à-la-crypto) |
[Bases](#bases-de-la-crypto) |
[Maths](#maths-pour-la-crypto) |
[PKI](#infrastructures-à-clés-publiques-pki) |
[Enjeux](#enjeux-éthiques) |
[Biblio](#bibliographie)
-->

<!-- structure, plus carrée, plus détaillée + exemples. Des sections claires, des objectifs, un fil rouge 
Qu'est-ce que la sécu ? CIA. Qu'est-ce que "faire de la sécu" ? Opérer une infra (?). Qu'est-ce qu'un outil de sécu ? truc à tout faire bof vs outil d'aide à l'opérateur. Et le soft dans tout ça ? -->

Introduction
============

Quelques actualités de la sécurité 2019
------------------------------

* Retadup, un botnet demantelé par la gendarmerie (et désactivé à distance) [details](https://www.zdnet.fr/actualites/retadup-le-coup-de-main-d-avast-pour-demanteler-le-botnet-39889659.htm)
* Jailbreak IPhone très bas niveau chekm8 [details](https://blog.trailofbits.com/2019/09/27/tethered-jailbreaks-are-back/)
* Attaque ciblée des smartphones de communautés (Uighurs, Tibet)  [details](https://www.technologyreview.com/f/614277/apple-says-chinas-uighur-muslims-were-targeted-in-iphone-hacking-campaign/), [details](https://citizenlab.ca/2019/09/poison-carp-tibetan-groups-targeted-with-1-click-mobile-exploits/)
* Intrusion avec persistence chez Citrix (supply-chain attack ?) [details](https://www.zdnet.com/article/hackers-lurked-in-citrix-systems-for-six-months/?ftag=CAD2e14604)
* Des ransomwares sur des institutions publiques [details](https://www.wired.com/story/ransomware-hits-georgia-courts-municipal-attacks-spread/)
* Asus Live Update (supply-chain) [details](https://www.wired.com/story/asus-software-update-hack/)
* RCE sur RDP [details](https://wazehell.io/2019/05/22/cve-2019-0708-technical-analysis-rdp-rce/)
* Affaire Huawei/Kaspersky [details](https://www.schneier.com/blog/archives/2019/09/supply-chain_se_1.html)
* Et bien sûr, des tensions géopolitiques
* Non-facts : IA, blockchain

Quelques actualités de la sécurité 2018
------------------------------

* Facebook, 50mio de comptes compromis, utilisé en login tiers. Surveillance home-made, logs [details](https://newsroom.fb.com/news/2018/09/security-update/#details)
* Intel management engine password bypass, AMT, null digest password (pas null pass, null digest), accès caché de l'OS ! [details](https://www.tenable.com/plugins/nessus/97999)
* Meltdown/Spectre, accès caché de l'OS [details](https://www.ovh.com/fr/blog/failles-de-securite-spectre-meltdown-explication-3-failles-mesures-correctives-public-averti/)
* Cryptominer JS, coinhive, TC ;-) (_sécu = ? Utilisation de ressources non prévues/souhaitées, zone grise_) [details](https://blog.malwarebytes.com/cybercrime/2018/02/state-malicious-cryptomining/)
* Nations-Unies, google dorking, (mdp accessibles sur github/trello puis accès aux ressources) [details](https://theintercept.com/2018/09/24/united-nations-trello-jira-google-docs-passwords/)
* DDoS toujours : concurrence minecraft, racket e-commerce [details](https://www.ovh.com/fr/blog/rapport-attaques-ddos-observees-par-ovh-en-2017/)
* VPNFilter, Mirai++ [details](https://blog.talosintelligence.com/2018/05/VPNFilter.html)


Quelques actualités de la sécurité 2017
------------------------------

* Hack (?) Ethereum
* WannaCry ( motivations mystérieuses, répétition ? Arrêt Renault, St Gobain, Merck)
* NotPetya ( Ukraine, Maersk, Tchernobyl. Répétition ? Supply chain attack bien ancrée. Sacrifié ? étonnant, autre accès encore ?)
* Mirai
* Equifax (143 mio noms/prénoms/SSN/num carte/date naissance pour phishing/usurpation/récup authentif)
* Pacemaker
* DNC/Macron Leaks
* Machines à voter
* MalwareTech (killswitch wannacry, arrêté aux US, passé clairement gris mais le cas d'un peu tous...)
* Deloitte (données privées, logiciel pas à jour)
* CCleaner (supply chain)

Quelques réflexions plus ou moins personnelles
-----------------------------------------

* Outillage/vulns de très simple (script kiddie) à élitiste (nation-sponsored, 0days)
* Attribution difficile et peu vérifiable pour le monde civil (mélange de magie noire, bluff, BS et [lancer de dés](https://i.etsystatic.com/11450141/r/il/0ab70a/1132361728/il_570xN.1132361728_4jgx.jpg)), les étatiques ne communiquent pas dessus.
* Bataille entre grandes puissances, impacts sur tous

Enjeux et acteurs
-----------------

* Les organisations (se protéger)
* Les états (protéger leurs orgas, attaquer les autres, mais tous partagent le même soft/hardware. Ils sont très bons, cyberguerre)
* Les hackers (s'amuser, se challenger. Attaque nécessaire pour comprendre, créer cette compétence avec éthique. Audit, bug bounties, on apprend comme ça. Les méchants ne nous ont pas attendus, la sécu a augmenté depuis qu'elle est étudiée/challengée. Pas encore compris dans les nouveaux domaines (FUD pacemaker, attaque judiciaire puis finalement patch)
* L'environnement : des attaquants qui s'adaptent et mènent la danse

Classes d'attaques
------------------

Sécurité = Confidentialité, Intégrité, Disponibilité

* Réseau
	* Passif (écoute)
	* Actif : MitM, être sur le chemin (physique, ethernet ARP spoof, IP rogue DHCP) (NSA, FAI, INSA, attaque équipement sur la route, attaque sur l'hôte)
* Logiciel : RCE, élévation de privilèges, WEB, natif, fautes logiques : vivre dans l'insécurité
* Social engineering (pas la faute de l'utilisateur !) : voler des accès, phishing de qualité, passwords, carte de crédit. De grossier à ciblé, utilisation de facebook pour connaître les amis, ceux qui sont en vacance, où, etc. De beaux site en https avec une petite typo. Mail spoofing. Attraper 1 parmi 1000. Trop facile de taper sur l'utilisateur, les systèmes ne sont pas adaptés, les informaticiens sont nul en compta. Accompagner, prévoir, surveiller.

Pourquoi ces problèmes de sécurité ?
--------------------------------------

* Vulnérabilités : partout, toujours, dans plusieurs états (connues et patchées, connues et non patchées, non connues du grand public et non patchées, non connues et non patchées)
* No disclosure, Full disclosure, Responsible disclosure ([resp fail](https://gadgets.ndtv.com/laptops/news/google-project-zero-microsoft-windows-10-zero-day-exploit-vulnerability-symcrypt-2053185))
* Cycle de vie d'une vulnérabilité (non publique mais utilisée nation-sponsored/elite, perspective avec les outils qui disent les détecter mais ne les ont pas vues depuis x années)

![Cycle de vie (Graphisme de Gabriel Augendre [gabriel@augendre.info] CC-BY 4.0](cycle-de-vie.png)
_(Graphisme de Gabriel Augendre [gabriel@augendre.info], 5TC 2018, CC-BY 4.0_)

* La sécurité est un process et non un état
* Barrière sécurité/développement (dévs non sécu, sécu non dévs, communication pas simple)
* Infrastructure : endroit de mitigation possible. Rustiner ! Défense en profondeur.

Que faire ?
-----------

Systèmes fondamentalement non sûrs donc côté infra :

* défense en profondeur
* cloisonnement
* surveillance
* partage
* patch management
* La sécurité n'est pas une liste d'outils ! éviter la collection ([HalvarFlake blackhat ASIA 2017](https://www.youtube.com/watch?v=PLJJY5UFtqY)), les outils complexes et privilégiés.

Petit panorama des malwares
---------------------------

* Virus
* Vers
* Chevaux de Troie
* Backdoors
* Rootkits
* Botnet
* Keylogger
* Ransomware
* (liste non exhaustive)



Filtrage et surveillance réseau
===============================

Buts
----

* Protéger le SI
	* Diminuer la surface d'attaque
	* Limiter les attaques sur les services ouverts
* Limiter les actions internes
	* Contrôler les activités (infection, relais attaque, baisse productivité)
	* Limiter les fuites de données (?)

La terminologie est floue !


Axes d'étude
------------

Inspecter des paquets selon les axes d'étude :

* Filtre/Alerte :
	* Le filtre protège
	* L'alerte ne protège pas, mais reste très pertinente : risque de se reproduire, on ne sait pas ce qui est impacté, réagir, conf ou int ? Obligation légale/commerciale (RGPD). Savoir qu'une intrusion a eu lieu, quel service, quelles données. Ex Yahoo, 3 milliards de comptes, 3 ans plus tard ; Facebook 2018
* Niveau OSI
* Contexte pré/post/pas
* Ingress/Egress
* Politique locale/globale
* Périmétrique/Interne

Firewall
--------

* Segmentation, délimitation de zones WAN/LAN/DMZ
* Règles locales
* Ouverture d'une liste de services contrôlés, diminution de la surface d'attaque, matrice de flux
* Layer 4
* 1 service = 1 port
* Stateless ou stateful
* Ex iptables/nftables
* TP2 !

Modèles :

* Historique : WAN/LAN/DMZ, DMZ tampon, LAN à plat
* Moderne : Segmentation plus fine du LAN, éviter propagation (vers, pivot)


IDS
---

* Alertes, détecter pour réagir !
* Layer 7, dissecteurs de haut niveau
* Regexps, performances relâchées car offline
* Sondes internes, remontée, corrélation (diminuer le bruit, améliorer la pertinence)
* Apparition du concept de faux positifs et faux négatifs, schéma
* Deux approches :
	* Scénario : motifs, modéliser le mal, le plus courant, bien évidemment facile à contourner
	* Comportement : modéliser le bien par spécification ou apprentissage (sur quelle base ? puis évolution, risque de déviation)
* Ex snort, suricata, prelude, ELK
* TP3 !

Modèle de déploiement :

* Sondes interne réseau/hôte
* Gestionnaire/corrélation


IPS
---

Un IDS avec la possibilité de bloquer. Un firewall de niveau 7 ? Challenges :

* Éviter les faux-positifs
* Performance "wirespeed"
* Devient une cible ? Single Point of Failure ?


Reverse proxy
-------------

Interfaçage de couche 7 dédié à un protocole :

* Proxy côté serveur (load-balancer, filtre sécu)
* Conformance par réécriture
* Contrôle fin car interprète la requête
* Ex en HTTP : les WAF (Web Application Firewall) tels que mod_security (détectent les injections SQL/XSS simples, patchent à la volée les vulns connues)


DPI
---

* Classification de protocoles
* QoS ou blocage
* Questions de neutralité du net, d'observation (ex Amesys Eagle)


DCI
---

* Reverse proxy sur tous les protocoles
* En entrée de réseau
* Analyse objets de haut niveau (JS, exe, bytecode java, ...)
* Voit tout, critique, rapide. Risque ? cf Fireeye 12/2015, vuln dans la "VM" Java (box d'analyse bytecode java)





<a rel="license" href="https://creativecommons.org/licenses/by-nc-sa/2.0/fr/"><img alt="Licence Creative Commons" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/2.0/fr/88x31.png" /></a><br />Ce(tte) œuvre est mise à disposition selon les termes de la <a rel="license" href="https://creativecommons.org/licenses/by-nc-sa/2.0/fr/">Licence Creative Commons Attribution - Pas d’Utilisation Commerciale - Partage dans les Mêmes Conditions 2.0 France</a>.
