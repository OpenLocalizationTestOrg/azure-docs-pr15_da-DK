
<properties
    pageTitle="Fejlfinding i forbindelse med RemoteApp skyen samlinger - oprettelse af | Microsoft Azure"
    description="Lær, hvordan du udfører fejlfinding af fejl under oprettelse RemoteApp skyen af websteder"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="troubleshoot-creating-remoteapp-cloud-collections"></a>Fejlfinding i forbindelse med oprettelse af websteder RemoteApp-skyen

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Hvis du har problemer med at oprette en samling i skyen, kan du se følgende oplysninger.

## <a name="your-image-is-invalid"></a>Billedet er ugyldige ##
Hvis du ser en meddelelse som "GoldImageInvalid", når du venter Azure til at klargøre din samling, betyder det, at dit billede af designskabelon ikke opfylder [defineret billede krav](remoteapp-imagereqs.md). Gå så læse disse [krav](remoteapp-imagereqs.md), løse dit billede og forsøger at oprette din samling igen.

## <a name="common-errors-seen-in-the-azure-management-portal"></a>Almindelige fejl fremgår af portalen Azure Management

    DNS server could not be reached
    ProvisioningTimeout

Skyen samlinger ofte fejl under oprettelse af på grund af du bruger brugerdefinerede billeder.  Hvis du ser en af ovenstående fejl, og du bruger et brugerdefineret billede til at oprette gruppen af websteder, skal du kontrollere følgende ting:

- Sikre, at det brugerdefinerede billede, du har overført opfylder billede.
- Mest er almindelige problemet, at billedet ikke var korrekt blevet behandlet med Sysprep.  
- Bekræfte billedet kan starte i Hyper-V og prøv at oprette en IAAS VM direkte i abonnementet Azure ved hjælp af billedet. Hvis VM ikke kan starte og starter ikke, angiver som regel, at det brugerdefinerede billede ikke er blevet forberedt korrekt.  Bekræfte det brugerdefinerede billede blev oprettet efter hvordan du opretter en brugerdefineret skabelon afbildning for RemoteApp

Hvis du bruger et af billederne Microsoft inkluderet med dit abonnement, du forsøge at oprette gruppen af websteder igen. Hvis problemet fortsætter, skal du kontakte Microsoft support.

    PlatformImageTrialModeOnly

Hvis du får denne fejlmeddelelse, som regel betyder det, du er opgraderet til et betalt konto, men du forsøger at bruge et Microsoft leverer billede, der er gyldig kun under tilstanden prøveversionen af tjenesten. I dette tilfælde skal du prøve at oprette din skyen samling igen, men Sørg for at angive den korrekte afbildning.
