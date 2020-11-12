# CM GE - Introduction à la sécurité numérique

_François Lesueur ([francois.lesueur@insa-lyon.fr](mailto:francois.lesueur@insa-lyon.fr), [@FLesueur](https://twitter.com/FLesueur))_



Quelques actualités de la sécurité
==============

Passer 30 minutes/1 heure pour faire un petit tour d'actualités de votre choix

Actualité pas que récente mais orientée "sécurité industrielle", à discuter de vive voix sur la visio
-------------------------------

* Stuxnet, 2010, conçu par US+Israël pour s'attaquer aux centrifugeuses iraniennes (programme nucléaire) [détails](https://fr.wikipedia.org/wiki/Stuxnet)
* Nations-Unies, 2018, google dorking, (mdp accessibles sur github/trello puis accès aux ressources, pas spécifique industriel mais similaire en terme de maturité de sécurité) [details](https://theintercept.com/2018/09/24/united-nations-trello-jira-google-docs-passwords/)
* Vague des "ransomwares" [WannaCry](https://fr.wikipedia.org/wiki/WannaCry) et [NotPetya](https://fr.wikipedia.org/wiki/Cyberattaque_NotPetya), 2017 (Arrêt Renault, Merck, etc. pour Wannacry ; Ukraine, Saint-Gobain, etc. pour NotPetya. Motivations mystérieuses/répétition pour WannaCry, motivation géopolitique pour NotPetya avec le conflit Russie-Ukraine)
* Un rapport Kaspersky [ici](https://ics.kaspersky.com/media/Kaspersky_ARC_ICS-2020-Trend-Report.pdf), en ouverture optionnelle, à lire plus tard pour approfondir si souhaité


Actualités de la sécurité numérique 2020
----

<audio controls src="media/attaques2020.mp3">[attaques2020.mp3](media/attaques2020.mp3)</audio>

* Raccoon [détails](https://raccoon-attack.com/)
* Carlson Wagonlit victime d'un ransomware
* Garmin victime d'un ransomware
* CVE-2020-5902 et CVE-2020-5903
* DDoS sur les résolveurs DNS [détails](https://www.afnic.fr/fr/ressources/blog/a-propos-de-l-attaque-sur-les-resolveurs-dns-de-fai-francais-1.html)
* Huawei et la 5G (question non limitée à Huawei, évidemment)
* Octopus Scanner [détails](https://securitylab.github.com/research/octopus-scanner-malware-open-source-supply-chain)
* Wordpress File Manager [détails](https://www.zdnet.com/article/millions-of-wordpress-sites-are-being-probed-attacked-with-recent-plugin-bug/)
* Cyberguerre US-Chine [détails](https://us-cert.cisa.gov/ncas/alerts/aa20-258a)
* Zerologon CVE-2020-1472
* PulseSecure [details](https://www.securityweek.com/pulse-secure-vpn-vulnerability-exploited-deliver-ransomware)
* Emotet (ransomware) [details](https://www.cert.ssi.gouv.fr/alerte/CERTFR-2020-ALE-019/)
* Altran (ransomware) [details](https://www.lemondeinformatique.fr/actualites/lire-cyberextorsion-contre-altran-un-cout-estime-a-20-meteuro-74492.html)





Quelques réflexions plus ou moins personnelles
===============

Contexte général : <audio controls src="media/introsecu2.mp3">[introsecu2.mp3](media/introsecu2.mp3)</audio>

Menaces et risque : <audio controls src="media/risques.mp3">[risques.mp3](media/risques.mp3)</audio>


* Outillage/vulns de très simple (script kiddie) à élitiste (nation-sponsored, 0days)
* Attribution difficile et peu vérifiable pour le monde civil (mélange de magie noire, bluff, BS et [lancer de dés](https://i.etsystatic.com/11450141/r/il/0ab70a/1132361728/il_570xN.1132361728_4jgx.jpg)), les étatiques ne communiquent pas dessus.
* Bataille entre grandes puissances, impacts sur tous
* Au fond, qu'est-ce que c'est que de "faire de la sécurité numérique" ?


Enjeux et acteurs
===============

Les acteurs : <audio controls src="media/acteurs.mp3">[acteurs.mp3](media/acteurs.mp3)</audio>

Exemple d'enjeu : <audio controls src="media/enjeux.mp3">[enjeux.mp3](media/enjeux.mp3)</audio>


* Les organisations (se protéger)
* Les états (protéger leurs orgas, attaquer les autres, mais tous partagent le même soft/hardware. Ils sont très bons, cyberguerre)
* Les hackers (s'amuser, se challenger). Attaque nécessaire pour comprendre, créer cette compétence avec éthique. Audit, bug bounties, on apprend comme ça. Les méchants ne nous ont pas attendus, la sécu a augmenté depuis qu'elle est étudiée/challengée. Pas encore compris dans les nouveaux domaines (FUD pacemaker, attaque judiciaire puis finalement patch)
* L'environnement : des attaquants qui s'adaptent et mènent la danse



Les pans de la sécurité
==================

Sécurité = Confidentialité, Intégrité, Disponibilité : <audio controls src="media/cia.mp3">[cia.mp3](media/cia.mp3)</audio>

Les spécificités de la sécurité : <audio controls src="media/specsecu.mp3">[specsecu.mp3](media/specsecu.mp3)</audio>

<!--
Sécu défensive et sécu offensive (dans le respect de la loi, bien évidemment) : <audio controls src="media/defoff.mp3">[defoff.mp3](media/defoff.mp3)</audio>
-->

Notion de vulnérabilité
================

Qu'est-ce qu'une vulnérabilité ?

* Vulnérabilités : partout, toujours, dans plusieurs états (connues et patchées, connues et non patchées, non connues du grand public et non patchées, non connues et non patchées)
* No disclosure, Full disclosure, Responsible disclosure ([Politique Google P0](https://googleprojectzero.blogspot.com/2020/01/policy-and-disclosure-2020-edition.html), [resp fail](https://gadgets.ndtv.com/laptops/news/google-project-zero-microsoft-windows-10-zero-day-exploit-vulnerability-symcrypt-2053185)) <audio controls src="media/disclosure.mp3">[disclosure.mp3](media/disclosure.mp3)</audio>
* Cycle de vie d'une vulnérabilité : Vidéo (Graphisme de Gabriel Augendre [gabriel@augendre.info], 5TC 2018, CC-BY 4.0) <video controls src="media/cycledevie.mp4" width="1170" height="538">[cycledevie.mp4](media/cycledevie.mp4)</video>

<!-- ![Cycle de vie (Graphisme de Gabriel Augendre [gabriel@augendre.info] CC-BY 4.0](cycle-de-vie.png)
_(Graphisme de Gabriel Augendre [gabriel@augendre.info], 5TC 2018, CC-BY 4.0)_ -->

Quand on sait que certaines vulnérabilités sont non publiques mais utilisée par des états/des groupes avancés, que penser des outils qui disent détecter les menaces inconnues (grâce à l'IA, évidemment) mais ne les ont pas détecté depuis les années qu'elles sont utilisées ?




<a rel="license" href="https://creativecommons.org/licenses/by-nc-sa/2.0/fr/"><img alt="Licence Creative Commons" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/2.0/fr/88x31.png" /></a><br />Ce(tte) œuvre est mise à disposition selon les termes de la <a rel="license" href="https://creativecommons.org/licenses/by-nc-sa/2.0/fr/">Licence Creative Commons Attribution - Pas d’Utilisation Commerciale - Partage dans les Mêmes Conditions 2.0 France</a>.
