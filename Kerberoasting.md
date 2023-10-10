---
title: Kerberoasting
permalink: /Kerberoasting/
---

# Kerberoasting

L'attaque Kerberoasting consiste à récupérer des TGS associés à un utilisateur du domaine et à essayer des les cracker.

## Méthodologie

Les étapes de cette attaque sont:

1.  Lister les **Service Principal Names (SPNs)**. Un SPN est de la forme suivante TERMSRV/DC1 (où TERMSRV est le type de service et DC1 est le serveur où le service est actif).
2.  Faire des requêtes pour récupérer les tickets Kerberos.
3.  Exporter les tickets pour les manipuler
4.  Convertir les tickets en un format manipulable par JtR ou Hashcat.
5.  Casser les "hashs" avec hashcat/JtR

**Attention**: dans la plupart des cas les "hashs" récupérés sont basés sur RC4 et donc cassable dans un temps raisonnable. Dans certains cas l'algorithme utilisé sera du AES (avec un grand nombre de répétition) ce qui réduit considérablement l'efficacité du cassage (facteur 20 000). Plus de détail dans l'article sur [Kerberos](/Kerberos/)


## Pratique

La suite Powersploit permet de récupérer la liste des utilisateurs qui on un SPN associé:

``` powershell
Get-NetUser -Domain blabla.local -DomainController XX.XX.XX.XX -Verbose -SPN
```

Il est possible de récupérer les parties à cracker avec [Impacket](/Impacket "wikilink") et l'outil **GetUsersSPN.py**
``` python
GetUserSPNs.py -dc-ip 192.168.1.1 -request domain.local/validaccount
```

## Correction

Il est difficile de bloquer Kerberoasting qui utilise le fonctionnement normal de Kerberos. On peut cependant essayer de détecter une attaque en cours:
- [Detecting Kerberoasting Activity](https://adsecurity.org/?p=3458): 
    - **Activité Kerberos**: En surveillant les logs AD on peut voir quand des tickets sont délivrés. Cependant une grande partie de cette activité sera légitime. Cependant un utilisateur demandant un grand nombre de ticket est louche.
    - **AES vs RC4**: Les systèmes modernes utilisent AES avec Kerberos. Si du RC4 est utilisé c'est potentiellement du Kerberoasting (Kerberoasting peut utiliser AES mais ce sera plus complexe à cracker).
- [Detecting Kerberoasting Activity Part 2 – Creating a Kerberoast Service Account Honeypot](https://adsecurity.org/?p=3513): on crée un faux compte qui n'a aucune raison de délivrer des tickets. S'il délivre un ticket c'est que quelqu'un fait du Kerberoasting.

Il est également possible de forcer l'utilisation d'AES sur les comptes utilisateurs ayant un SPN. Plus d'informations sur [site de Microsoft](https://blogs.msdn.microsoft.com/openspecification/2011/05/30/windows-configurations-for-kerberos-supported-encryption-type/). Cependant cette méthode est incompatible avec des environnements XP / 2003.


## Ressources

-   Kerberoasting - Room326:
    -   [Partie 1](https://room362.com/post/2016/kerberoast-pt1/)
    -   [Partie 2](https://room362.com/post/2016/kerberoast-pt2/)
    -   [Partie 3](https://room362.com/post/2016/kerberoast-pt3/)
-   [t120 Attacking Microsoft Kerberos Kicking the Guard Dog of Hades Tim Medin](https://www.youtube.com/watch?v=PUyhlN-E5MU)
-   [Cracking Kerberos TGS Tickets Using Kerberoast – Exploiting Kerberos to Compromise the Active Directory Domain](https://adsecurity.org/?p=2293)
-   [Kerberoasting Without Mimikatz - Harmj0y](http://www.harmj0y.net/blog/powershell/kerberoasting-without-mimikatz/)
-   [Return From The Underworld The Future Of Red Team Kerberos](https://www.youtube.com/watch?v=E_BNhuGmJwM)

