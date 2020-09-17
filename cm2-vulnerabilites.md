# CM2 - Panorama des vulnérabilités

_François Lesueur ([francois.lesueur@insa-lyon.fr](mailto:francois.lesueur@insa-lyon.fr), [@FLesueur](https://twitter.com/FLesueur))_

ATTENTION, NON FINALISÉ !
=========================

Les pans de la sécurité
==================

Sécurité = Confidentialité, Intégrité, Disponibilité : <audio controls src="media/cia.mp3"></audio>

Les spécificités de la sécurité : <audio controls src="media/specsecu.mp3"></audio>

Sécu défensive et sécu offensive (dans le respect de la loi, bien évidemment) : <audio controls src="media/defoff.mp3"></audio>


Notion de vulnérabilité
================

Qu'est-ce qu'une vulnérabilité ?

* Vulnérabilités : partout, toujours, dans plusieurs états (connues et patchées, connues et non patchées, non connues du grand public et non patchées, non connues et non patchées)
* No disclosure, Full disclosure, Responsible disclosure ([Politique Google P0](https://googleprojectzero.blogspot.com/2020/01/policy-and-disclosure-2020-edition.html), [resp fail](https://gadgets.ndtv.com/laptops/news/google-project-zero-microsoft-windows-10-zero-day-exploit-vulnerability-symcrypt-2053185)) <audio controls src="media/disclosure.mp3"></audio>
* Cycle de vie d'une vulnérabilité : <video controls src="media/cycledevie.mkv"></video>

![Cycle de vie (Graphisme de Gabriel Augendre [gabriel@augendre.info] CC-BY 4.0](cycle-de-vie.png)
_(Graphisme de Gabriel Augendre [gabriel@augendre.info], 5TC 2018, CC-BY 4.0)_

Quand on sait que certaines vulnérabilités sont non publiques mais utilisée par des états/des groupes avancés, que penser des outils qui disent détecter les menaces inconnues (grâce à l'IA, évidemment) mais ne les ont pas détecté depuis les années qu'elles sont utilisées ?

Classes d'attaques
=============

* Réseau
	* Passif (écoute)
	* Actif : MitM, être sur le chemin (physique, ethernet ARP spoof, IP rogue DHCP) (NSA, FAI, INSA, attaque équipement sur la route, attaque sur l'hôte)
* Logiciel : RCE, élévation de privilèges, WEB, natif, fautes logiques : vivre dans l'insécurité
* Social engineering (pas la faute de l'utilisateur !) : voler des accès, phishing de qualité, passwords, carte de crédit. De grossier à ciblé, utilisation de facebook pour connaître les amis, ceux qui sont en vacance, où, etc. De beaux site en https avec une petite typo. Mail spoofing. Attraper 1 parmi 1000. Trop facile de taper sur l'utilisateur, les systèmes ne sont pas adaptés, les informaticiens sont nul en compta. Accompagner, prévoir, surveiller. [Schneier : "Stop trying to fix the user"](https://www.schneier.com/blog/archives/2016/10/security_design.html)


Que faire ?
========

Systèmes fondamentalement non sûrs. L'infrastructure est un endroit de mitigation possible :

* défense en profondeur
* cloisonnement
* surveillance
* partage
* patch management
* La sécurité n'est pas une liste d'outils ! éviter la collection ([HalvarFlake blackhat ASIA 2017](https://www.youtube.com/watch?v=PLJJY5UFtqY)), les outils complexes et privilégiés.

Le déroulé du cours
===================

* Ce qu'on va voir, ce qu'on ne va pas voir
* Séances (cours, TP, article, discord)

Petit panorama des malwares
==================

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
