# Notes de cours de 5TC-SRS : Sécurité des Réseaux et des Systèmes

_François Lesueur ([francois.lesueur@insa-lyon.fr](mailto:francois.lesueur@insa-lyon.fr), [@FLesueur](https://twitter.com/FLesueur))_

<!-- [Intro](#introduction-à-la-crypto) |
[Bases](#bases-de-la-crypto) |
[Maths](#maths-pour-la-crypto) |
[PKI](#infrastructures-à-clés-publiques-pki) |
[Enjeux](#enjeux-éthiques) |
[Biblio](#bibliographie)
-->


Introduction
============

Quelques actualités de la sécurité 2018
------------------------------

* Facebook, 50mio de comptes compromis, utilisé en login tiers [details](https://newsroom.fb.com/news/2018/09/security-update/#details)
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
* Attribution difficile et peu vérifiable (mélange de magie noire, bluff, BS et lancer de dés)
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
* Cycle de vie d'une vulnérabilité (non publique mais utilisée nation-sponsored/elite, perspective avec les outils qui disent les détecter mais ne les ont pas vues depuis x années
* La sécurité est un process et non un état
* Barrière sécurité/développement (dévs non sécu, sécu non dévs, communication pas simple)
* Infrastructure : endroit de mitigation possible. Rustiner ! Défense en profondeur.

Que faire ?
-----------

Systèmes fondamentalement non sûrs donc côté infra :

* défense en profondeur
* cloisonnement
* surveillance
* patch management
* La sécurité n'est pas une liste d'outils ! éviter la collection [HalvarFlake blackhat ASIA 2017](https://www.youtube.com/watch?v=PLJJY5UFtqY), les outils complexes et privilégiés.

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



<a rel="license" href="https://creativecommons.org/licenses/by-nc-sa/2.0/fr/"><img alt="Licence Creative Commons" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/2.0/fr/88x31.png" /></a><br />Ce(tte) œuvre est mise à disposition selon les termes de la <a rel="license" href="https://creativecommons.org/licenses/by-nc-sa/2.0/fr/">Licence Creative Commons Attribution - Pas d’Utilisation Commerciale - Partage dans les Mêmes Conditions 2.0 France</a>.
