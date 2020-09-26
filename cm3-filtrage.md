# CM3 - Filtrage et surveillance réseau

_François Lesueur ([francois.lesueur@insa-lyon.fr](mailto:francois.lesueur@insa-lyon.fr), [@FLesueur](https://twitter.com/FLesueur))_

ATTENTION, NON FINALISÉ !
=========================

Buts
====

* Filtrer : Protéger le SI
	* Diminuer la surface d'attaque
	* Limiter les attaques sur les services ouverts
	* Sur les 2 dimensions extérieur->intérieur (intrusion) et intérieur->intérieur (pivot)
* Surveiller : Détecter pour analyse ou réaction
	* Protéger une prochaine fois, analyser le chemin
	* Découvrir les ressources qui ont été impactées
	* Problème de confidentialité ou d'intégrité ?
	* Ex Yahoo, 3 milliards de comptes, détecté 3 ans plus tard ; Facebook 2018 ; [discours G. Poupard DG ANSSI aux Assises 2019](https://twitter.com/lsamain/status/1181872739495370753)


La terminologie des classes d'outils est floue et les limites entre les cases sont poreuses ! Évidemment, ce flou et la grande variété de mécanisme et d'actions entretiennent un marché des _appliances_ pas toujours sain, régulièrement conçus à base de [poudre verte](https://poudreverte.org/). L'objectif de ce cours est de découvrir les mécanismes fondamentaux pour pouvoir appréhender l'analyse critique des outils disponibles.


Axes d'étude
======

Inspecter des paquets selon les axes d'étude :

* Filtre/Alerte
* Niveau OSI
* Contexte pré/post/pas
* Ingress/Egress
* Politique locale/globale/mixte
* Périmétrique ou "discret"/Diffus


| Classe | Niveau | Contexte | Filtrant | In/Out | Local/Global | Périmétrique |
|--------|--------|----------|----------|--------|--------------|--------------|
| Firewall | *4* | Pré | Oui | In+Out | Local | Oui |
| IDS | *7* | Oui | *Non* | In | *Global* | Non |
| IPS | *7* | Oui | *Réaction* | In | *Global* | Non |
| RProxy | *7* | Oui | *Oui* | In+Out | Global | *Non |
| DPI | *7* | Pré | *Oui* | In+Out | *Local* | Oui |
| DCI | *7* | *Oui* | Oui | *In+Out* | Local+Global | Oui |


Firewall
======

* Segmentation, délimitation de zones (WAN/LAN/DMZ, WAN/DMZ/LAN1/LAN2/LAN3/ADMIN/...)
* Règles locales
* Ouverture d'une liste de services contrôlés, diminution de la surface d'attaque, matrice de flux
* Layer 4
* 1 service = 1 port
* Ex iptables/nftables
* TP2 !

Modèles :

* Historique : WAN/LAN/DMZ, DMZ tampon, LAN à plat (à proscrire)
* Moderne : Segmentation plus fine du LAN, éviter propagation (vers, pivot, ransomware aujourd'hui)


IDS
===

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
===

Un IDS avec la possibilité de bloquer. Un firewall de niveau 7 ? Challenges :

* Éviter les faux-positifs
* Performance "wirespeed"
* Devient une cible ? Single Point of Failure ?


Reverse proxy
=======

Interfaçage de couche 7 dédié à un protocole :

* Proxy côté serveur (load-balancer, filtre sécu)
* Conformance par réécriture
* Contrôle fin car interprète la requête
* Ex en HTTP : les WAF (Web Application Firewall) tels que mod_security (détectent les injections SQL/XSS simples, patchent à la volée les vulns connues)


DPI
===

* Classification de protocoles
* QoS ou blocage
* Questions de neutralité du net, d'observation (ex Amesys Eagle)


DCI
===

* Reverse proxy sur tous les protocoles
* En entrée de réseau
* Analyse objets de haut niveau (JS, exe, bytecode java, ...)
* Voit tout, critique, rapide. Risque ? cf [Fireeye 12/2015](https://googleprojectzero.blogspot.com/2015/12/fireeye-exploitation-project-zeros.html), vuln dans la "VM" Java (box d'analyse bytecode, java) ou [F5 BIG-IP en 2020](https://www.cert.ssi.gouv.fr/alerte/CERTFR-2020-ALE-015/).





<a rel="license" href="https://creativecommons.org/licenses/by-nc-sa/2.0/fr/"><img alt="Licence Creative Commons" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/2.0/fr/88x31.png" /></a><br />Ce(tte) œuvre est mise à disposition selon les termes de la <a rel="license" href="https://creativecommons.org/licenses/by-nc-sa/2.0/fr/">Licence Creative Commons Attribution - Pas d’Utilisation Commerciale - Partage dans les Mêmes Conditions 2.0 France</a>.
