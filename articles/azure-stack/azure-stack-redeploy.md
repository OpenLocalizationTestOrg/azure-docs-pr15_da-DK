<properties
    pageTitle="Geninstaller Azure stak | Microsoft Azure"
    description="Geninstaller Azure stablen."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="erikje"/>

# <a name="redeploy-azure-stack"></a>Geninstaller Azure stak

Hvis du vil geninstallere Azure stak, skal du starte forfra fra bunden, som beskrevet nedenfor.

## <a name="steps-to-redeploy-azure-stack"></a>Trin til at geninstallere Azure stak

1. Genstart værten i det oprindelige operativsystem (installeret til helt). Dette er ikke standardindstillingen i startmenuen, så du skal bruge KVM eller lokale konsol for at markere den under genstarten (under installationen, du med navnet på "Start fra Virtuelle" OS til "AzureStack TP2", dette hjælper med at identificere som OS er som).

    Du behøver ikke at fjerne den eksisterende start-post (det nye understøttelse af script "PrepareBootFromVHD.ps1" tager sig af, for dig.)

2. Hvis du ikke har KVM eller kunne tænke dig at vælge Start OS før at du har genstartet:
    
    1. Find script.\BootMenuNoKVM.ps1. Denne fil er tilgængelige med de andre support scripts leveres sammen med dette build.
    
    2. Køre scriptet med administratorrettigheder. Vælg navnet på oprindelige Host OS. Dette starter værten i den oprindelige vært OS uden at have KVM-adgang.
    
    3. Når scriptet er fuldført kan du blive bedt om at bekræfte genstarten.

    - Hvis der er andre brugere, der er logget på, mislykkes denne kommando.

    - Skal du køre følgende kommando: Genstart computeren-gennemtvinge 
 
3. Slet filen CloudBuilder.vhdx, der blev brugt som en del af den forrige installation.

    Du behøver ikke at slette den eksisterende Lagerpulje fra den forrige TP2 installation. Scriptet installation registrerer og rydder op i den nuværende og derefter opretter nye.

5. Geninstaller kan kopiere en ny kopi af CloudBuilder.vhdx, starte det osv.

## <a name="next-steps"></a>Næste trin

[Oprette forbindelse til Azure stak](azure-stack-connect-azure-stack.md)
