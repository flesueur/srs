# CM3 - Filtrage et surveillance réseau

_François Lesueur ([francois.lesueur@insa-lyon.fr](mailto:francois.lesueur@insa-lyon.fr), [@FLesueur](https://twitter.com/FLesueur))_

ATTENTION, NON FINALISÉ !
=========================

Buts
====

* Protéger le SI
	* Diminuer la surface d'attaque
	* Limiter les attaques sur les services ouverts
	* Sur les 2 dimensions ext->int (intrusion) et int->int (rebond)
* Limiter les actions internes
	* Contrôler les activités (infection, relais attaque, baisse productivité)
	* Limiter les fuites de données (?)
* Implémenter une politique de flux haut-niveau ("pas de contrôle depuis l'extérieur") sur des éléments de protocole ("pas de connexion TCP entrante", "pas de paquet contenant une signature connue d'attaque", etc.) (implique évidemment une simplification/incomplétude)

La terminologie est floue !


Axes d'étude
======

Inspecter des paquets selon les axes d'étude :

* Filtre/Alerte :
	* Le filtre protège
	* L'alerte ne protège pas, mais reste très pertinente : risque de se reproduire, on ne sait pas ce qui est impacté, réagir, conf ou int ? Obligation légale/commerciale (RGPD). Savoir qu'une intrusion a eu lieu, quel service, quelles données. Ex Yahoo, 3 milliards de comptes, 3 ans plus tard ; Facebook 2018 ; [discours G. Poupard DG ANSSI aux Assises 2019](https://twitter.com/lsamain/status/1181872739495370753)
* Niveau OSI
* Contexte pré/post/pas
* Ingress/Egress
* Politique locale/globale/mixte
* Périmétrique/Interne

Firewall
======

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
* Voit tout, critique, rapide. Risque ? cf Fireeye 12/2015, vuln dans la "VM" Java (box d'analyse bytecode java)





<a rel="license" href="https://creativecommons.org/licenses/by-nc-sa/2.0/fr/"><img alt="Licence Creative Commons" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/2.0/fr/88x31.png" /></a><br />Ce(tte) œuvre est mise à disposition selon les termes de la <a rel="license" href="https://creativecommons.org/licenses/by-nc-sa/2.0/fr/">Licence Creative Commons Attribution - Pas d’Utilisation Commerciale - Partage dans les Mêmes Conditions 2.0 France</a>.
