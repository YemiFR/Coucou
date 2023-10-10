---
title: Contournement d'antivirus
permalink: /Contournement_d'antivirus/
---

# Contournement d'antivirus

Cet article décrit des méthodes pour contourner des antivirus. Ceci est en aucun exhaustif:

-   **Encodage**: Il est possible d'encoder la charge utile et de rajouter du code décodant la charge utile. Ceci est très basique et ne permet pas de contourner la plupart des antivirus.
-   **Recompilation**: On peut recompiler le malware en changeant des strings et en rajoutant du code.
-   **Template**: changer le template utilisé (-k)

## Hyperion

Hyperion se trouve dans [Kali](/Kali "wikilink") (**/usr/share/windows-binaries/Hyperion-1.0.zip**). Il est possible de le compiler et de l'utiliser pour chiffrer un executable. Il est nécessaire d'avoir certains .dll dans le même répertoire.

``` bash
i686-mingw32msvc-g++ Src/Crypter/*.cpp -o hyperion.exe
wine hyperion.exe meterpreter.exe encrypted_meterpreter.exe
```

Une autre alternative à la compilation dans Kali est de télécharger l'executable depuis le répertoire Git de [Veil-Evasion](https://github.com/Veil-Framework/Veil-Evasion/).

Il est également possible de trouver des informations supplémentaires sur Hyperion dans [exploit-db](https://www.exploit-db.com/docs/18849.pdf)

## Powershell
Il est possible de contourner les antivirus qui détectent les outils powershells (voir [Powershell](/Powershell/))

## Hooking

Certains antivirus mettent en place des "hook" sur certaines fonctions sensibles. Cela peut notamment être utilisé pour empêcher un dump de lsass. Les articles suivants proposent des méthodes de contournement de ces hooks:

- [Bypassing Cylance and other AVs/EDRs by Unhooking Windows APIs](https://ired.team/offensive-security/defense-evasion/bypassing-cylance-and-other-avs-edrs-by-unhooking-windows-apis)
- [Defeating Userland Hooks (ft. Bitdefender)](https://0x00sec.org/t/defeating-userland-hooks-ft-bitdefender/12496)
- [Bypass EDR’s memory protection, introduction to hooking](https://medium.com/@fsx30/bypass-edrs-memory-protection-introduction-to-hooking-2efb21acffd6)
