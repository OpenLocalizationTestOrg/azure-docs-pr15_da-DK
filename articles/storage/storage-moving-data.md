<properties
    pageTitle="Flytning af Data til og fra Azure Storage | Microsoft Azure"
    description="I denne artikel indeholder en oversigt over de forskellige metoder til at flytte data til og fra Azure-lager."
    services="storage"
    documentationCenter=""
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="micurd"/>

# <a name="moving-data-to-and-from-azure-storage"></a>Flytning af data til og fra Azure-lager

Hvis du vil flytte lokale data til Azure-lager (eller omvendt), er der flere forskellige måder at gøre dette. Den fremgangsmåde, der passer dig bedst, afhænger af scenariet. I denne artikel giver et hurtigt overblik over forskellige scenarier og relevante tilbud for hver enkelt.

## <a name="building-applications"></a>Opbygning af programmer

Hvis du opretter et program, er udvikling af mod REST-API eller en af vores mange klientbiblioteker en god måde at flytte data til og fra Azure-lager.

Azure-lager indeholder omfattende klientbiblioteker til .NET, iOS, Java, Android, Universal Windows-Platform (UWP), Xamarin, C++, Node.JS, PHP, fonetisk og Python. Klientbiblioteker tilbyder avancerede funktioner som forsøg, logføring og parallelle overførsler. Du kan også udvikle direkte mod REST-API, som kan kaldes af en hvilken som helst sprog, der gør HTTP/HTTPS-anmodninger.

Se [Introduktion til Azure Blob-lager](storage-dotnet-how-to-use-blobs.md) for at få mere at vide.

Vi tilbyder desuden også [Azure lagerplads Data bevægelse bibliotek](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) som er et bibliotek, der er udviklet til høj ydeevne kopiering af data til og fra Azure. Se vores Data bevægelse bibliotek [dokumentation](https://github.com/Azure/azure-storage-net-data-movement) for at få mere at vide. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Hurtigt arbejde visning/med dine data

Hvis du vil en nem måde at få vist dataene Azure-lager, mens du også har du mulighed for at uploade og downloade dine data, derefter overveje at bruge en Azure Storage Explorer.

Se vores liste over [Azure lagerplads Softwareundersøgelse](storage-explorers.md) til at få mere at vide.

## <a name="system-administration"></a>Systemadministration af

Hvis du kræver eller mere føler dig tryg ved en kommandolinjen utility (fx systemadministratorer), er her et par muligheder for du kan overveje:

### <a name="azcopy"></a>AzCopy

AzCopy er et Windows kommandolinjen værktøj til høj ydeevne kopiering af data til og fra Azure-lager. Du kan også kopiere data i en lagerplads konto eller mellem forskellige lagerplads konti.

Se [overføre data med AzCopy kommandolinjeparametre Utility](storage-use-azcopy.md) til at få mere for at vide.

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell er et modul, der giver cmdletter til administration af tjenester på Azure. Det er en opgavebaseret kommandolinje-shell og scriptsproget udviklet specielt til systemadministration af.

Se [Bruge Azure PowerShell med Azure-lager](storage-powershell-guide-full.md) til at få mere at vide.

### <a name="azure-cli"></a>Azure CLI

Azure CLI indeholder et sæt af Åbn kilde på tværs af platforme kommandoer til at arbejde med Azure tjenester. Azure CLI er tilgængelig på Windows, OSX og Linux.

Se [Brug af Azure CLI med Azure-lager](storage-azure-cli.md) til at få mere at vide.

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Flytte store mængder data med en langsom netværk

En af de største udfordringer, der er knyttet til flytning af store datamængder er filoverførsel tid. Hvis du vil hente data til/fra Azure-lager uden at bekymre dig om netværk omkostninger eller skrive kode, er Azure Importér/Eksportér en hensigtsmæssig løsning.

Se [Azure Importér/Eksportér](storage-import-export-service.md) for at få mere at vide.

## <a name="backing-up-your-data"></a>Sikkerhedskopiere dine data

Hvis du skal blot sikkerhedskopiering af dine data til Azure-lager, er Azure sikkerhedskopiering metode til at gå. Dette er en effektiv løsning til sikkerhedskopiering af lokale data og Azure FOS.

Se [Azure sikkerhedskopi](../backup/backup-introduction-to-azure-backup.md) til at få mere at vide.

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Få adgang til dine data i det lokale miljø og fra skyen

Hvis du har brug for en løsning for at få adgang til dine data i det lokale miljø og fra skyen, derefter du bør overveje at bruge Azure's hybrid cloud storageløsning, StorSimple. Denne løsning består af en fysisk StorSimple enhed, intelligent butikker ofte anvendte data på SSDs, gang imellem bruges data på harddiske og inaktiv/sikkerhedskopi/arkivering data på Azure-lager.

Se [StorSimple](../storsimple/storsimple-overview.md) til at få mere at vide.

## <a name="recovering-your-data"></a>Gendanne dine data

Hvis du har lokalt arbejdsmængder og programmer, skal du bruge en løsning, der gør det muligt for din virksomhed til at fortsætte i tilfælde af nedbrud. Azure gendannelse af websteder håndterer gentagelse, failover og gendannelse af virtuelle maskiner og fysiske servere. Replikerede data er gemt i Azure-lager, så du kan fjerne behovet for en sekundær på stedet datacenter.

Se [Azure gendannelse af websteder](../site-recovery/site-recovery-overview.md) for at få mere at vide.
