<properties
    pageTitle="Oversigt over Azure diagnosticering | Microsoft Azure"
    description="Bruge Azure diagnosticering til fejlfinding, måle ydelse, overvågning, trafik analyse i skytjenester, virtuelle maskiner og service-strukturen"
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/02/2016"
    ms.author="robb"/>


# <a name="what-is-microsoft-azure-diagnostics"></a>Hvad er Microsoft Azure diagnosticering


Azure diagnosticering er muligheden for i Azure, der gør det muligt for indsamling af diagnosticering data på en udløst programmet. Du kan bruge filtypenavnet diagnosticering fra en række forskellige kilder. Understøttes i øjeblikket er Azure skyen Service internettet og arbejder roller, Azure virtuelle maskiner, der kører Microsoft Windows og Service-strukturen. Andre Azure tjenester har deres egen separate diagnosticering.

## <a name="data-you-can-collect"></a>Du kan indsamle data

Azure diagnosticering kan indsamle de følgende typer data:

Datakilde|Beskrivelse
---|---
Tællere i ydeevne | Operativsystem og tællere i brugerdefineret ydeevne
Programmet logfiler     | Spore meddelelser, der er skrevet af dit program
Windows-hændelseslogge   | Oplysninger, der sendes til Windows begivenhed logføring system
.NET begivenhed kilde    | Kode ved at skrive hændelser ved hjælp af klassen .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx)
IIS-logfilerne             | Oplysninger om IIS-websteder
Manifestet baseret ETW   | Begivenhed sporing til Windows hændelser, der genereres af en proces
Crashdumps          | Oplysninger om tilstanden for processen i tilfælde af nedbrud et program
Brugerdefineret fejllogge    | Logfiler, der er oprettet af programmet eller tjeneste
Azure diagnosticering infrastruktur logfiler|Oplysninger om diagnosticering selve

Filtypenavnet Azure diagnosticering kan overføre disse data til en Azure-lager-konto eller sende det til tjenester som [Programmet indsigt](./application-insights/app-insights-cloudservices.md). Du kan bruge dataene til fejlfinding og fejlfinding, måle ydelse, overvågning Ressourceforbrug, analyse og kapacitet, planlægning og revision.


## <a name="versioning"></a>Versionsstyring
Se [Oversigt over dokumentversioner Azure diagnosticering](azure-diagnostics-versioning-history.md).

## <a name="next-steps"></a>Næste trin
Vælg hvilken tjeneste, du forsøger at indsamle diagnosticering på og bruge følgende artikler til at komme i gang. Bruge linkene generelle Azure diagnosticering til reference til bestemte opgaver.

## <a name="web-apps"></a>Webapps
Bemærk, at Web Apps ikke bruger Azure diagnosticering. Finde de tilsvarende oplysninger på [Web Apps](./app-service-web/web-sites-enable-diagnostic-log.md)

## <a name="cloud-services-using-azure-diagnostics"></a>Brug af Azure diagnosticering-Skytjenester
- Hvis bruger Visual Studio, skal du se [Brug Visual Studio til at spore et Cloud Services-program](./vs-azure-tools-debug-cloud-services-virtual-machines.md) til at komme i gang. Ellers kan se
- [Hvordan du kan overvåge Cloud services ved hjælp af Azure diagnosticering](./cloud-services/cloud-services-how-to-monitor.md)
- [Konfigurere Azure diagnosticering i et Cloud Services-program](./cloud-services/cloud-services-dotnet-diagnostics.md)

Finde mere avancerede emner

- [Brug af Azure diagnosticering med programmet indsigt til Cloud Services](./application-insights/app-insights-cloudservices.md)
- [Spore strømmen af et Cloud Services-program med Azure diagnosticering](./cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
- [Bruge PowerShell til at konfigurere diagnosticering på Cloud Services](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)


## <a name="virtual-machines-using-azure-diagnostics"></a>Virtuelle maskiner ved hjælp af Azure diagnosticering
- Hvis bruger Visual Studio, skal du se [Brug Visual Studio til at spore virtuelle Azure-computere](./vs-azure-tools-debug-cloud-services-virtual-machines.md) at komme i gang. Ellers kan se
- [Konfigurere Azure diagnosticering på en Azure Virtual Machine](./virtual-machines-dotnet-diagnostics.md)

Finde mere avancerede emner

- [Bruge PowerShell til at konfigurere diagnosticering på virtuelle Azure-computere](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)
- [Oprette en Windows virtuel maskine overvågning og diagnosticering ved hjælp af Azure ressourcestyring skabelon](./virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)

## <a name="service-fabric-using-azure-diagnostics"></a>Tjenesten strukturen ved hjælp af Azure diagnosticering
Kom i gang med [skærm et Service-strukturen til computeren](./service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Mange andre Service-strukturen diagnosticering artikler er tilgængelige i navigationstræet i venstre side, når du få adgang til denne artikel.

## <a name="general-azure-diagnostics-articles"></a>Generelt Azure diagnosticering artikler
- [Azure diagnosticering skema konfiguration](https://msdn.microsoft.com/library/azure/mt634524.aspx) – Lær at ændre skemafilen for at indsamle og distribuere diagnosticering data. Bemærk, at du kan også bruge Visual Studio til at ændre skemafilen.
- [Hvordan Azure diagnosticering data er gemt i Azure-lager](./cloud-services/cloud-services-dotnet-diagnostics-storage.md) - vide navnene på de tabeller og BLOB hvor diagnosticering dataene skrives.
- Lær at [bruge tællere i ydeevne i Azure diagnosticering](./cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md).
- Lær at [distribuere Azure diagnosticeringsoplysninger til programmet indsigt](./azure-diagnostics-configure-applicationinsights.md)
- Hvis du har problemer med diagnosticering starter eller søge efter dine data i tabeller Azure-lager, se [Fejlfinding i forbindelse med Azure diagnosticering](./azure-diagnostics-troubleshooting.md)
