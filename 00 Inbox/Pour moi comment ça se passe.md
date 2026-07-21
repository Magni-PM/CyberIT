# Pre-Engagement
regles, scope, etc.
# Information gathering
## OSINT 
(Utile en réel, pas forcement pour CPTS/CTF)
## Infrastructure Enumeration
On essaye de comprendre ce qu'on a en face de nous, c'est a dire tout ce que peut offrir notre scope initial.
## Hosts Enumeration
Pour chaque hosts on va approfondir (Services, OS, etc.)
## Service Enumeration
On liste pour chaque hosts comment on peut communiquer avec, les protocoles, les versions, etc.
# Vulnerability Assessment
On recoupe toutes les données et on réfléchis a comment attaquer. On liste les vuln potentielles.
# Exploitation
On met en aapplication les elements théorisé pour obtenir un foothold.
# Post-Exploitation
L'ordre suivant est a prendre avec des pincettes car selon la ou on tombe on priorisera differents element pa exemple si on arrive sur un serveur de fichier on fera peut etre passer le pillaging plus haut dans les priorité.
## Stabilisation accès / Persistence
On stabilise son accès (full tty si shell bancal, etc.) 
Et si possible/authorisé on fait de la prsistence (clé ssh, etc.) sinn on fera ça plus tard.
## Information gathering / Enumeration
On prends des infos sur qui on est, nos droit, on regarde a quoi on a accès, etc.
La globalement on reprends le hosts enumeration de la premiere partie 

## Privesc enumeration
Le nom se suffit a lui meme

## Pillaging 
On va plus loin que le information gathering la on va approfondir, recherche de cred, mais globalement ca pourrait se regrouper.

# Lateral Movement
En se basant sur les infos deja obtenue on va tenter de bouger et si on y arrive retour au debut on est reparti pour un tour.

## Pivoting
Pas une étape en soit mais plus une technique souvent necessaire pour arriver a ce but.

# Post-engagement
rapport etc