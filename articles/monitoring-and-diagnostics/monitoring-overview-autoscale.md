<properties
    pageTitle="Oversigt over Autoskalering i virtuelle Microsoft Azure-computere, Skytjenester og online | Microsoft Azure"
    description="Oversigt over Autoskalering i Microsoft Azure. Gælder for virtuelle maskiner, Skytjenester og Webapps."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="robb"/>

# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Oversigt over Autoskalering i virtuelle Microsoft Azure-computere, Skytjenester og Web Apps

I denne artikel beskriver, hvilke Microsoft Azure Autoskalering er, dets fordele, og hvordan du kan komme i gang med at bruge det.  

Azure skærm Autoskalering gælder kun for [Virtuelt skala sæt](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Skytjenester](https://azure.microsoft.com/services/cloud-services/)og [App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/).

>[AZURE.NOTE] Azure har to Autoskalering metoder. En ældre version af Autoskalering gælder for virtuelle maskiner (tilgængelighed sæt). Denne funktion har begrænset understøttelse, og vi anbefaler, at overføre til VM skala sæt efter hurtigere og mere pålidelige Autoskalering support. Et link til, hvordan du bruger den ældre teknologi er inkluderet i denne artikel.  


## <a name="what-is-autoscale"></a>Hvad er Autoskalering?

Autoskalering kan du har den korrekte mængde ressourcer, der kører for at håndtere belastning på dit program. Det kan du tilføje ressourcer for at håndtere øges i belastning og også spare penge ved at fjerne ressourcer, som sidder inaktiv. Du angiver et mindste og største antal forekomster, der skal køre og tilføje eller fjerne FOS automatisk baseret på et sæt regler. Har du en mindste gør sikker på, at kører dit program altid selv under uden belastning. Har du højst begrænser din mulige samlede omkostninger hver time. Du kan automatisk skalere mellem disse to ekstreme ved hjælp af regler, du opretter.

 ![Autoskalering forklaring. Tilføje og fjerne FOS](./media/monitoring-autoscale-overview/AutoscaleConcept.png)

Når reglens betingelser er opfyldt, udløses en eller flere Autoskalering handlinger. Du kan tilføje og fjerne FOS eller udføre andre handlinger. I følgende grundlæggende diagram viser denne proces.  

 ![Grundlæggende Autoskalering rutediagram](./media/monitoring-autoscale-overview/AutoscaleOverview3.png)


## <a name="autoscale-process-explained"></a>Autoskalering proces forklaring
Følgende forklaring anvendes på dele af det forrige diagram.   

### <a name="resource-metrics"></a>Ressource målepunkter
Ressourcer udsende statistik, som er senere behandles af regler. Målepunkter kommer via forskellige metoder.
VM skala sæt bruger telemetridata fra Azure diagnosticering supportmedarbejdere, mens telemetri til webapps og Cloud services kommer direkte fra Azure-infrastruktur. Nogle ofte anvendte statistik omfatter CPU-brug, hukommelsesforbrug, tråd tæller, længde kø og på harddisken. Du kan finde en liste over hvad du kan bruge telemetridata, [Autoskalering almindelige målepunkter](insights-autoscale-common-metrics.md).

### <a name="time"></a>Tid
Tidsplan-baserede regler er baseret på UTC. Du skal angive din tidszone korrekt, når du opretter regler.  

### <a name="rules"></a>Regler
Diagrammet viser kun én Autoskalering regel, men du kan have mange af dem. Du kan oprette komplekse overlappende regler, efter behov for din situation.  Medtage regeltyper  

 - **Metrisk-baserede** - eksempelvis at udføre denne handling, når CPU-brug er større end 50%.
 - **Tidsbaserede** – For eksempel udløse en webhook hver 8 am lørdag i en given tidszone.

Metrisk-baserede regler måle programmet Indlæs og tilføje eller fjerne FOS baseret på Indlæs. Tidsplan-baserede regler kan bruges til at skalere, når du ser klokkeslætsformat i din belastning og vil skalere før en mulige Indlæs Forøg eller Formindsk opstår.  


### <a name="actions-and-automation"></a>Handlinger og automatisering

Regler kan udløse en eller flere typer handlinger.

- **Skala** - skala FOS ind eller ud
- **Mail** - sende mail til abonnement administratorer, flere administratorer og/eller ekstra e-mail-adresse, du angiver
- **Automatisere via webhooks** - opkald webhooks, som kan udløse flere komplekse handlinger i eller uden for Azure. I Azure, kan du starte en Azure automatisering runbook, Azure funktionen eller Azure logik App. Eksempel 3 part URL-adressen uden for Azure omfatter tjenester som slæk og Twilio.


## <a name="autoscale-settings"></a>Autoskalering indstillinger
Autoskalering Brug følgende terminologi og struktur.

- En **Autoskalering indstilling** læses af Autoskalering programmet til at afgøre, om du vil skalere op eller ned. Den indeholder en eller flere profiler, oplysninger om target ressource og indstillinger for meddelelser.
    - En **Autoskalering profil** er en kombination af en kapacitet, angive et sæt af regler for udløsere og skala handlinger for profilen, og en gentagelse. Du kan have flere profiler, som gør det muligt at tager sig af forskellige overlappende krav.
        - En **Kapacitetsindstilling** angiver minimum, maksimum og standardværdier for antallet af forekomster. [relevante sted til at bruge fig 1]
        - En **regel** omfatter en udløser – enten en metriske udløser eller et klokkeslæt udløser – og en skala handling, der angiver, om Autoskalering skal skalere op eller ned, når denne regel er opfyldt.
        - En **Gentagelse** angiver når Autoskalering skal placere denne profil i kraft. Du kan have forskellige Autoskalering profiler på forskellige tider af eller de dage i ugen, f.eks.
- En **indstilling for mailbeskeder** definerer, hvilke meddelelser der skal udføres, når en Autoskalering indtræffer baseret på opfylder kriterierne i en af indstillingen Autoskalering profiler. Autoskalering kan give besked en eller flere mailadresser eller foretage opkald til en eller flere webhooks.

![Azure Autoskalering indstilling, profil og regelstruktur](./media/monitoring-autoscale-overview/AzureResourceManagerRuleStructure3.png)

Den komplette liste over konfigurerbar felter og -beskrivelser er tilgængelig i [Autoskalering REST-API](https://msdn.microsoft.com/library/dn931928.aspx).

Finde kodeeksempler,

* [Avanceret Autoskalering konfiguration ved hjælp af Ressourcestyring skabeloner til VM skala sæt](insights-advanced-autoscale-virtual-machine-scale-sets.md)  
* [Autoskalering REST-API](https://msdn.microsoft.com/library/dn931953.aspx)



## <a name="horizontal-vs-vertical-scaling"></a>Vandret vs lodret skalering

Autoskalering øges ressourcer i kun ubalance vandret, som er et større ("ud") eller mindske ("i") i feltet Antal VM forekomster.  Vandret skalering, som er mere fleksibel i skyen situation, som du kan køre potentielt tusindvis af FOS til at håndtere Indlæs. Lodret skalering er anderledes. Den bevarer det samme antal FOS, men gør VM mere ("op") eller mindre ("ned") effektive. Power måles i hukommelse, CPU-hastighed, diskplads osv.  Lodret skalering har flere begrænsninger. Det er afhængig af tilgængeligheden af større hardware, som kan variere efter område og hurtigt rammer og øvre grænse. Lodret skalering kræver også normalt en VM stop og start. Se [lodret skalerer Azure virtuelt med Automation Azure](../virtual-machines/virtual-machines-linux-vertical-scaling-automation.md)kan finde flere oplysninger.


## <a name="methods-of-access"></a>Metoder til access
Du kan konfigurere Autoskalering via

- [Azure-portalen](insights-how-to-scale.md)
- [PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)
- [På tværs af platforme Command Line Interface (CLI)](insights-cli-samples.md#autoscale )
- [Azure skærm REST-API](https://msdn.microsoft.com/library/azure/dn931953.aspx )

## <a name="supported-services-for-autoscale"></a>Understøttede tjenester til Autoskalering


| Tjenesten                              | Skemaet og dokumenter                                       |
|--------------------------------------|-----------------------------------------------------|
| Webapps                             | [Skalering Webapps](insights-how-to-scale.md)              |
| Cloud Services                       | [Autoskalering en skybaseret tjeneste](../cloud-services/cloud-services-how-to-scale.md) |
| Virtuelle maskiner: klassisk           | [Skalering klassisk virtuelt tilgængelighed sæt](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Virtuelle maskiner: Windows skala sæt| [Skalering VM skala angiver i Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)  |
| Virtuelle maskiner: Linux skala angiver  | [Skalering VM skala angiver i Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) |
| Virtuelle maskiner: Windows eksempel   | [Avanceret Autoskalering konfiguration ved hjælp af Ressourcestyring skabeloner til VM skala sæt](insights-advanced-autoscale-virtual-machine-scale-sets.md) |

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om Autoskalering, Brug Autoscale Walkthroughs herover eller referere til følgende ressourcer:

- [Azure skærm Autoskalering almindelige målepunkter](insights-autoscale-common-metrics.md)
- [Bedste fremgangsmåder til Azure skærm Autoskalering](insights-autoscale-best-practices.md)
- [Brug Autoskalering handlinger til at sende mail og webhook påmindelser](insights-autoscale-to-webhook-email.md)
- [Autoskalering REST-API](https://msdn.microsoft.com/library/dn931953.aspx)
- [Fejlfinding i forbindelse med virtuelt skala sæt Autoskalering](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)
