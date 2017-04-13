<properties
    pageTitle="Oversigt over overvågning i Microsoft Azure | Microsoft Azure"
    description="Øverste niveau oversigt over overvågning og diagnosticering i Microsoft Azure, herunder beskeder, webhooks, autoskalering og meget mere."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"l
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="robb"/>

# <a name="overview-of-monitoring-in-microsoft-azure"></a>Oversigt over overvågning i Microsoft Azure

Denne artikel indeholder en oversigt over overvågning Azure ressourcer. Den indeholder markører til oplysninger om bestemte typer af ressourcer.  Du kan finde oplysninger på højt niveau på overvågning dit program fra ikke-Azure synspunkt, [overvågnings- og diagnosticering vejledning](../best-practices-monitoring.md).

Skyen programmer er komplekse med mange bevægelige dele. Overvågning indeholder data for at sikre, at dit program forbliver op at køre i en sund tilstand. Det også hjælper dig med at stave fra potentielle problemer eller foretage fejlfinding af tidligere dem. Desuden kan du bruge overvågningsdata til at få deep viden om programmet. Denne viden kan hjælpe dig med at forbedre ydeevnen i programmet på computeren eller vedligeholde eller automatisere handlinger, der ellers ville kræve manuel handling.

I det følgende diagram viser en oversigt over Azure overvågning, herunder typen logge, kan du indsamle og hvad du kan gøre med de pågældende data.   

![Logiske Model til overvågning og diagnosticering for ikke-Beregn ressourcer](./media/monitoring-overview/MonitoringAzureResources-non-compute_v3.png)

Figur 1: Grundlæggende Model til overvågning og diagnosticering for ikke-Beregn ressourcer

<br/>

![Logiske Model til overvågning og diagnosticering for Beregn ressourcer](./media/monitoring-overview/MonitoringAzureResources-compute_v3.png)

Figur 2: Grundlæggende Model til overvågning og diagnosticering for Beregn ressourcer


## <a name="monitoring-sources"></a>Overvågning kilder
### <a name="activity-logs"></a>Logfiler over aktivitet
Du kan søge aktivitet loggen (tidligere kaldet Operational eller overvågningslog) for at få oplysninger om din ressource, som det ses af Azure infrastrukturen. Loggen indeholder oplysninger som gange når ressourcer oprettes eller slettes.  

### <a name="host-vm"></a>Host VM
**Beregne kun**


Nogle beregne ressourcer som Skytjenester, virtuelle maskiner, og Service-strukturen har en dedikeret Host VM de interagere med. Host VM er det tilsvarende beløb i roden VM i Hyper-V hypervisor modellen. I dette tilfælde kan du indsamle målepunkter på lige Host VM ud over gæst OS.  

For andre Azure tjenester er der ikke nødvendigvis en 1:1 tilknytning mellem din ressource og en bestemt vært VM så host VM målepunkter ikke er tilgængelige.


### <a name="resource---metrics-and-diagnostics-logs"></a>Ressource - metrik og diagnosticering logfiler
Collectable målepunkter varierer afhængigt af ressourcetypen. For eksempel indeholder virtuelle maskiner statistik på Disk-IO og procent CPU. Men disse statistik findes ikke til en tjeneste Bus kø, som indeholder målepunkter som kø størrelse og meddelelse overførselshastighed i stedet.

Du kan få målepunkter på gæst OS og diagnosticering moduler som Azure diagnosticering for Beregn ressourcer. Azure diagnosticering hjælper med at indsamle og distribuere indsamle diagnosticering data på andre placeringer, herunder Azure-lager.

En liste over aktuelt collectable målepunkter er tilgængelig på [understøttet måleredskaber](monitoring-supported-metrics.md).

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Program - Diagnostics logfiler, programmet logfiler og målepunkter
**Beregne kun**

Programmer kan køre oven på gæst OS i Beregn modellen. De udsende deres egne sæt af logfiler og målepunkter.

Typer af målepunkter medtage

- Tællere i ydeevne
- Programmet logfiler
- Windows-hændelseslogge
- .NET begivenhed kilde
- IIS-logfilerne
- Manifestet baseret ETW
- Crashdumps
- Kunde Fejllogge


## <a name="uses-for-monitoring-data"></a>Brug af overvågningsdata

### <a name="visualize"></a>Visualisere
Visualisere overvågningsdata i grafik og diagrammer hjælper dig med at finde tendenser meget hurtigere end kigge selve dataene.  

Et par visualisering metoder omfatter følgende:

- Bruge Azure-portalen
- Distribuere data til Azure programmet indsigt
- Distribuere data til Microsoft PowerBI
- Distribuere dataene til et tredjeparts visualisering værktøj ved hjælp af enten direkte streaming eller ved at få værktøjet som læst fra et arkiv i Azure-lager

### <a name="archive"></a>Arkiv
Overvågning af data er typisk skrevet til Azure-lager og bevares der, indtil du slette den.

Et par måder at bruge disse data:

- Når skrevet, kan du få andre værktøjer, der er i eller uden for Azure læse den og behandle den.
- Du henter dataene lokalt til det lokale arkiv eller ændre din opbevaringspolitik i skyen for at holde data af længere perioder.  
- Du forlader dataene i Azure-lager på ubestemt tid, selvom du behøver at betale for Azure-lager baseret på mængden data, du beholder.
-

### <a name="query"></a>Forespørgsel
Du kan bruge Azure skærm REST API'ER, kryds platform kommandolinjen (CLI)-kommandoer, PowerShell-cmdlet'er eller .NET SDK til at få adgang til dataene i systemet eller Azure-lager

Som eksempler kan nævnes:

-  Henter data for et brugerdefineret overvågning program, du har skrevet
-  Oprette brugerdefinerede forespørgsler og sende dataene til et tredjepartsprogram.

### <a name="route"></a>Distribuere
Du kan streame overvågningsdata på andre placeringer i realtid.

Som eksempler kan nævnes:

- Sende til programmet viden, så du kan bruge værktøjerne visualisering der.
- Send til begivenhed hubber, så du kan sende til tredjepartsværktøjer til analyse af realtid.

### <a name="automate"></a>Automatisere
Du kan bruge overvågningsdata til udløser begivenheder eller hele processer som eksempler kan nævnes:

- Brug data til Autoskalering beregne forekomster op eller ned baseret på programmet Indlæs.
- Sende mails, når en metrikværdi krydser en foruddefineret grænse.
- Ringe til en URL-adresse (webhook) for at udføre en handling i et system uden for Azure
- Starte en runbook i Azure automatisering til at udføre en række opgaver

## <a name="methods-of-use"></a>Metoder til brug
Generelt, kan du manipulere datasporing, routing og hentning ved hjælp af en af følgende metoder. Ikke alle metoder er tilgængelige for alle handlinger eller datatyper.

- [Azure-portalen](https://portal.azure.com)
- [PowerShell](insights-powershell-samples.md)  
- [På tværs af platforme Command Line Interface (CLI)](insights-cli-samples.md)
- [REST-API](https://msdn.microsoft.com/library/dn931943.aspx)
- [.NET SDK](https://msdn.microsoft.com/library/dn802153.aspx)

## <a name="azures-monitoring-offerings"></a>Azures overvågning tilbud
Azure har tilbud, der er tilgængelige for overvågning af dine tjenester fra helt fra bunden infrastruktur til programmet telemetri. Den bedste overvågning strategi kombinerer brugen af alle tre få omfattende, detaljerede indsigt i tilstanden for dine tjenester.

- [Azure skærm](http://aka.ms/azmondocs) – tilbud visualisering, forespørgsel, routing, advarer, autoskalering og automatisering data både fra Azure infrastrukturen (aktivitetslog) og hver enkelt Azure ressource (diagnosticeringslogfiler). I denne artikel er en del af dokumentationen til Azure skærm. Azure skærm navnet blev udgivet September 25 på Ignite 2016.  Navnet på den forrige er "Azure indsigt."  
- [Programmet indsigt](https://azure.microsoft.com/documentation/services/application-insights/) – indeholder omfattende registrering og diagnosticering til problemer på programlag af din tjeneste, godt integreret oven på data fra Azure overvågning. Det er standard diagnosticering platformen til App-tjenesten Web Apps.  Du kan distribuere data fra andre tjenester til den.  
- [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) del af [Handlinger Management-pakke](https://www.microsoft.com/cloud-platform/operations-management-suite) – indeholder en holistisk IT management-løsning til både lokalt og fra tredjepart skybaseret infrastruktur (såsom AWS) foruden Azure ressourcer.  Data fra Azure skærm kan sendes direkte til Log Analytics, så du kan se målene og logfiler for hele miljøet på ét sted.     


## <a name="next-steps"></a>Næste trin
Få mere at vide om

- [Azure skærm i en video fra Ignite 2016](https://myignite.microsoft.com/videos/4977)
- [Introduktion til Azure skærm](monitoring-get-started.md)
- Hvis du forsøger at diagnosticere problemer i programmet skybaseret tjeneste, virtuelt eller tjeneste strukturen [Azure diagnosticering](../azure-diagnostics.md) .
- Hvis du forsøger at diagnosticering problemer i din App Service online [Program indsigt](https://azure.microsoft.com/documentation/services/application-insights/) .
- [Fejlfinding i forbindelse med Azure-lager](../storage/storage-e2e-troubleshooting.md) , når du bruger BLOB-lager, tabeller eller køer
- [Log analyser](https://azure.microsoft.com/documentation/services/log-analytics/) og [Handlinger Management pakke](https://www.microsoft.com/cloud-platform/operations-management-suite)
