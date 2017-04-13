<properties
   pageTitle="Indsamle logge ved hjælp af Linux Azure diagnosticering | Microsoft Azure"
   description="I denne artikel beskrives, hvordan du konfigurerer Azure diagnosticering til at indsamle logge fra en tjeneste-strukturen Linux klynge kører i Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="subramar"/>


# <a name="collect-logs-by-using-azure-diagnostics"></a>Indsamle logge ved hjælp af Azure diagnosticering

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

Når du kører en Azure Service strukturen klynge, er det en god ide at indsamle logge fra alle knuderne i et centralt sted. Har du logge på en central placering gør det nemt at analysere og foretage fejlfinding af problemer, uanset om de er i dine tjenester, dit program eller klyngen sig selv. En metode til at overføre og indsamle logge er at bruge filtypenavnet Azure diagnosticering, som overfører logfiler til Azure-lager. Du kan læse begivenheder fra lager og sætte dem i et produkt som [Elastiske Søg](service-fabric-diagnostic-how-to-use-elasticsearch.md) eller en anden parsing af log løsning.

## <a name="log-sources-that-you-might-want-to-collect"></a>Log kilder, du vil indsamle
- **Tjenesten strukturen logfiler**: udledes fra platform via [LTTng](http://lttng.org) og overføres til kontoen lagerplads. Logfiler kan være funktionsdygtige begivenheder eller runtime hændelser, der platformen udsender. Disse logfiler gemmes på den placering, der angiver, klynge manifestet. (Søge efter mærket **AzureTableWinFabETWQueryable** for at få lagerplads kontooplysninger, og se efter **StoreConnectionString**.)
- **Programhændelser**: udledes fra din tjeneste kode. Du kan bruge en hvilken som helst logføring løsning, der skriver tekstbaserede logfiler – for eksempel LTTng. Yderligere oplysninger finder du se i dokumentationen til LTTng på sporing for dit program.  


## <a name="deploy-the-diagnostics-extension"></a>Installere filtypenavnet diagnosticering
Det første trin i indsamling af logfiler er at installere filtypenavnet diagnosticering på hver af FOS i tjenesten strukturen klynge. Filtypenavnet diagnosticering indsamler logge på hver VM og overfører dem til kontoen lagerplads, du angiver. Trinnene varierer afhængigt af om du bruger Azure portal eller Azure ressourcestyring.

Angiv **Diagnostics** til **på**for at installere filtypenavnet Diagnostics på FOS i klyngen som en del af oprettelse af klynge. Når du opretter klyngen, kan du ikke ændre denne indstilling ved hjælp af portalen.

Konfigurer derefter Linux Azure diagnosticering (LAD) for at indsamle filerne, og placer dem til din lagerplads konto. Denne proces forklares som scenario 3 ("overføre din egen logfiler") i artiklen [Brug af LAD til at overvåge og diagnosticere Linux FOS](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md). Følge denne proces får du adgang til at sporing. Du kan overføre sporinger til en visualizer efter eget valg.

Du kan også distribuere filtypenavnet Diagnostics ved hjælp af Azure ressourcestyring. Processen svarer til Windows og Linux og er beskrevet for Windows klynger i [Sådan indsamler logfiler med Azure diagnosticering](service-fabric-diagnostics-how-to-setup-wad.md).

Du kan også bruge handlinger Management-pakken, som beskrevet i [Handlinger Management pakke Log Analytics med Linux](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/).

Når du er færdig med denne konfiguration, overvåger LAD agent de angivne logfiler. Når en ny linje er knyttet til filen, oprettes der en syslog post, der sendes til opbevaring, du har angivet.


## <a name="next-steps"></a>Næste trin
Du kan finde mere detaljerede oplysninger om hvilke hændelser, skal du undersøge under fejlfinding af problemer, se [LTTng dokumentation](http://lttng.org/docs) og [Ved hjælp af LAD](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md).
