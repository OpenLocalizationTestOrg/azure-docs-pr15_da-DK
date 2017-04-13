<properties
    pageTitle="Bedste fremgangsmåder for Azure RemoteApp | Microsoft Azure"
    description="Bedste fremgangsmåder for at konfigurere og bruge Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="best-practices-for-configuring-and-using-azure-remoteapp"></a>Bedste fremgangsmåder for at konfigurere og bruge Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Følgende oplysninger kan hjælpe dig med at konfigurere og bruge Azure RemoteApp produktivt.

## <a name="connectivity"></a>Forbindelse


- Brug altid den seneste klientversion. Ved hjælp af ældre klienter kan medføre problemer med serverforbindelsen og andre nedgraderede oplevelser. Aktivering af programmet på computeren automatiske opdateringer til din enhed sikrer, at den seneste klient altid er installeret.
- Brug altid den mest stabil og pålidelig internetforbindelse tilgængelige for dig.  
- Brug understøttes kun proxy-forbindelser til optimal connectivity ydeevne.  SOCKS proxy understøttes ikke.

## <a name="applications"></a>Programmer


- Gem og Luk RemoteApp-programmer, når du er færdig med programmet. Ikke lukke programmet, kan det medføre tab af data.
- Valider brugerdefinerede programmer, før du bruger dem i Azure RemoteApp. Dette omfatter at sikre, at de arbejder på en flere sessioner platform og ikke forbruge unødvendige ressourcer som hukommelse og CPU, der kan starve en anden bruger i den samme websteder. For at vide, skal du hente og gennemse [Programmet kompatibilitet bedste praksis for Remote Desktop Services](http://www.dabcc.com/resources/Application%20Compatibility%20Best%20Practices%20for%20Remote%20Desktop%20Services.pdf).

## <a name="configuration-and-management"></a>Konfiguration og administration


- Hold dine billeder af webstedsskabeloner opdateret, installation af softwareopdateringer og andre vigtige rettelser efter behov. Dette sikrer, at som Azure RemoteApp automatisk-ubalance at opfylde dine kapacitet, hver forekomst er installeret for at udbedre.  
- Kontrollér, at installationen af Active Directory Federation Services (AD FS) er sikker og pålidelig. Ellers klient-godkendelse mislykkes muligvis, at forhindre brugere i at få adgang til Azure RemoteApp.
- Konfigurere billeder af webstedsskabeloner med installerede programmer, roller eller funktioner, så de er uden tilstand. De må ikke stole på alle forekomster af virtuelle maskiner i et RemoteApp-tjeneste, der i en fast tilstand.
    - Gemme alle brugerdata i brugerprofiler eller andre lagerplads placeringer, der er ekstern i forhold til tjenesten, som lokale filer aktier eller OneDrive.
    - Gemme delte data i lagerplads placeringer uden for tjenesten, som lokale filer aktier eller OneDrive.
    - Konfigurere indstillinger for hele systemet, i skabelonen billedet i stedet for på individuelle virtuelle maskiner i en tjeneste.
    - Deaktivere Automatiske opdateringer til publicerede programmer – i stedet anvende dem manuelt på skabelon billedet og teste dem, før du installerer fra skabelonen.
