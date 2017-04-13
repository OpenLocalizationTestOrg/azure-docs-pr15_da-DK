<properties
   pageTitle="Sundhedsovervågning i tjenesten strukturen | Microsoft Azure"
   description="En introduktion til Azure Service strukturen tilstanden overvågning modellen, som indeholder overvågning af klyngen og dens programmer og -tjenester."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="oanapl"/>

# <a name="introduction-to-service-fabric-health-monitoring"></a>Introduktion til tjenesten strukturen sundhedsovervågning
Azure Service-strukturen introducerer en tilstand-model, som indeholder et omfattende, fleksibel og udvidelig sundhed beregning og rapportering. Modellen kan nær-realtid overvågning af tilstanden for klyngen og de tjenester, der kører i den. Du kan nemt få information om tilstand og rette potentielle problemer, før de foretager kaskadevise og medføre massive afbrydelser. I typisk modellen, send services-rapporter baseret på deres lokale visninger og, sammenlægges oplysninger for at give en samlet klynge niveau visning.

Tjenesten strukturen komponenter Brug denne omfattende sundhedstilstandsmodel til at rapportere deres aktuelle tilstand. Du kan bruge den samme ordning til rapport tilstand fra dine programmer. Hvis du investerer i høj kvalitet sundhedsrapportering, der registrerer din brugerdefinerede betingelser, kan du registrere og løse problemer med for dit program, der kører meget nemmere.

> [AZURE.NOTE] Vi er startet sundhed undersystem for at løse behov for overvåget opgraderinger. Tjenesten strukturen tilbyder overvåget programmet og klynge opgraderinger, der sikrer fuld tilgængelighed, ingen nedetid og minimale til ingen brugerhandlinger. For at opnå disse mål, kontrollerer opgraderingen tilstand, der er baseret på konfigureret opgradering politikker, og gør det muligt for en opgradering fortsætte kun, når sundhed respekterer ønskede tærskler. Ellers skal er opgraderingen enten automatisk annulleret eller er afbrudt midlertidigt for at give administratorer mulighed for at løse problemerne. Hvis du vil vide mere om programmet opgraderinger skal du se [denne artikel](service-fabric-application-upgrade.md).

## <a name="health-store"></a>Sundhed store
Sundhed store bevarer sundhed-relaterede oplysninger om enheder i klynge for faktureringsoplysninger og evaluering. Det er implementeret som en tjeneste-strukturen bevaret med høj sikkerhed service for at sikre høj tilgængelighed og skalerbarhed. Sundhed store er en del af den **strukturen: / System** programmet, og det er tilgængeligt, når klyngen er op og kører.

## <a name="health-entities-and-hierarchy"></a>Sundhed objekter og hierarki
Sundhed objekter er organiseret i et logisk hierarki, der registrerer interaktioner og afhængigheder mellem forskellige enheder. Objekter og hierarki er automatisk udviklet af sundhed butikken baseret på rapporter, der er modtaget fra Service-strukturen komponenter.

Sundhed enheder spejling af tjenesten strukturen enheder. (For eksempel **sundhed programmet enhed** svarer til en programforekomst, der er implementeret i klynge, mens **sundhed node enhed** svarer til en tjeneste-strukturen klyngenode.) Sundhed hierarkiet indsamler interaktionerne af firma, og det er grundlaget for beregning i avanceret tilstand. Du kan få mere at vide om vigtige Service-strukturen begreber i [tjenesten strukturen teknisk oversigt](service-fabric-technical-overview.md). Se [Service-strukturen programmet model](service-fabric-application-model.md)for flere oplysninger om programmet.

Sundhed objekterne og hierarki Tillad klynge og programmer til at være effektivt rapporteret, foretages fejlfinding og overvåges. Sundhed modellen indeholder en nøjagtig, *findelt* gengivelse af tilstanden af de mange bevægelige dele i klyngen.

![Sundhed enheder.][1]
Enheder sundhed organiseret i et hierarki, der er baseret på overordnede / underordnede relationer.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Sundhed enheder er:

- **Klynge**. Repræsenterer tilstanden for en tjeneste-strukturen klynge. Klynge tilstandsrapporter beskrives de betingelser, der påvirker hele klynge og kan ikke være smallere til en eller flere beskadiget børn. Som eksempler kan nævnes hjerneforskningen af klynge opdele på grund af netværksproblemer partitionering eller kommunikation.

- **Node**. Repræsenterer tilstanden for en tjeneste-strukturen node. Node tilstandsrapporter beskrives de betingelser, der påvirker funktionen node. De typisk påvirker alle de installerede enheder, der kører på den. Som eksempler kan nævnes når en node er uden for disk mellemrum (eller en anden maskine hele egenskab, som hukommelse, forbindelser), og når en node er nede. Objektet node er identificeret med nodenavn (streng).

- **Program**på computeren. Repræsenterer tilstanden for et programforekomst, der kører i klyngen. Programmet tilstandsrapporter beskrives de betingelser, der påvirker den overordnede sundhed af programmet. De kan ikke være smallere til individuelle børn (tjenester eller installerede programmer). Som eksempler kan nævnes til slut interaktioner mellem forskellige tjenester i programmet på computeren. Objektet program er identificeret med navnet på programmet (URI).

- **Tjenesten**. Repræsenterer tilstanden for en tjeneste, der kører i klyngen. Tjenesten tilstandsrapporter Beskriv betingelser, der påvirker den overordnede status for tjenesten, og de kan ikke være smallere til en partition eller en replika. Som eksempler kan nævnes en tjenestekonfiguration (såsom port eller eksterne filshare), der forårsager problemer med alle partitioner. Objektet tjenesten er identificeret med navnet på tjenesten (URI).

- **Partition**. Repræsenterer tilstanden for en tjeneste partition. Partition tilstandsrapporter beskrives de betingelser, der påvirker hele replikasættet. Som eksempler kan nævnes når antallet af replikaer er under target Tæl, og når en partition er i quorum tab. Objektet partition er identificeret med partition ID (GUID).

- **Replika**. Repræsenterer tilstanden for en med høj sikkerhed service replika eller en forekomst af uden status tjenesten. De mindste enhed, der watchdogs og systemkomponenter kan rapportere om for et program. For med høj sikkerhed tjenester, som eksempler kan nævnes en primær replika rapportering, når den ikke kan replikeres handlinger til secondaries og når replikering er der ikke fortsætter på den forventede tempo. En forekomst af uden status kan også rapportere, når den er løbe tør for ressourcer eller har problemer med serverforbindelsen. Objektet replika er identificeret med partition ID (GUID) og replika eller forekomst-ID (langt heltal).

- **DeployedApplication**. Repræsenterer tilstanden for et *program, der kører på en node*. Installerede program tilstandsrapporter beskriver betingelser, der er specifikke for programmet på den node, der ikke kan være smallere til servicepakker, der er installeret på den samme node. Som eksempler kan nævnes når pakken programmet ikke kan hentes, på noden, og når der er et problem konfigurationen af programmet principper for sikkerhed på noden. Det installerede program er identificeret med programnavnet (URI) og nodenavn (streng).

- **DeployedServicePackage**. Repræsenterer tilstanden for en servicepakke, der kører på en node i klyngen. Den beskriver betingelser, der er specifikke for en servicepakke, der ikke påvirker andre servicepakker på den samme node for det samme program. Som eksempler kan nævnes en kode pakke i pakken tjeneste, der ikke kan startes, og en konfigurationspakke, der ikke kan læses. Den installerede servicepakke er identificeret med programnavn (URI), nodenavn (streng), og tjenesten manifest navn (streng).

Granulariteten af modellen sundhed gør det nemt at registrere og rette problemer. Eksempelvis hvis en tjeneste ikke svarer, er det muligt at rapportere, forekomsten af programmet er beskadiget. Rapportering på, ikke er ideel, men fordi problemet ikke muligvis påvirker alle tjenester i det pågældende program. Rapporten skal anvendes til tjenesten beskadiget eller til en bestemt underordnede partition, hvis du kan finde flere oplysninger, der peger på denne partition. Dataene automatisk overflader igennem hierarkiet, og en beskadiget partition er synligt på service og-program. Denne sammenlægning hjælper med at finde og løse den egentlige årsag problemet hurtigere.

Sundhed hierarkiet består af overordnede / underordnede relationer. En klynge består af knuder og programmer. Programmer har tjenester og installeret programmer. Installerede programmer har implementeret servicepakker. Tjenester har partitioner, og hver partition har en eller flere replikaer. Der er et særligt forhold mellem noder og udløst objekter. Hvis en node er beskadiget, som har rapporteret dens nøglecenter systemkomponent (tjenesten Failover Manager), påvirker det de installerede programmer, servicepakker og replikaer, der er installeret på den.

Sundhed hierarkiet repræsenterer den seneste tilstand baseret på de seneste tilstandsrapporter, hvilket er næsten i realtid oplysninger.
Interne og eksterne watchdogs kan rapportere på de samme objekter, der er baseret på program-specifikke logik eller brugerdefinerede overvåget betingelser. Brugerrapporter fungere sammen med system-rapporterne.

Planlægge at investere i Sådan rapportere og besvare sundhed under designet af en stor skybaseret tjeneste, at gøre det nemmere at foretage fejlfinding, overvåge og betjene tjenesten.

## <a name="health-states"></a>Sundhed tilstande
Tjenesten strukturen bruges tre sundhed tilstande til at beskrive om et objekt er sund: OK, advarsel og fejl. Enhver rapport, der er sendt til sundhed store skal angive en af disse tilstande. Sundhed evaluering resultatet er en af disse tilstande.

De mulige [sundhed stater](https://msdn.microsoft.com/library/azure/system.fabric.health.healthstate) er:

- **OK**. Objektet er i orden. Der er ingen kendte problemer, der rapporteres for den eller dets underordnede websteder (når det er relevant).

- **Advarsel om**. Objektet har nogle problemer, men det er endnu ikke er beskadiget (for eksempel, der er forsinkelser, men ikke forårsager problemer med at funktionelle endnu). Betingelsen advarsel fastsætte selve uden en speciel brugerinput i nogle tilfælde, og det er praktisk til at levere indsigt i hvad der foregår. I andre tilfælde kan betingelsen advarsel forringe til alvorlige problemer uden brugerinput.

- **Fejl**. Objektet er beskadiget. Indsats at løse tilstanden for enheden, fordi den ikke fungerer korrekt.

- **Ukendt**. Enheden, findes ikke i tilstanden store. Dette resultat, kan fås fra de distribuerede forespørgsler, der flette resultater fra flere komponenter. For eksempel få node listen forespørgsel skifter til **FailoverManager** og **HealthManager**, få programmet listen forespørgsel går til **ClusterManager** og **HealthManager**. Disse forespørgsler flette resultater fra flere systemkomponenter. Hvis en anden systemkomponent returnerer et objekt, der ikke har nået eller har ryddet op fra sundhed store, flettede resultatet er i en ukendt tilstand.

## <a name="health-policies"></a>Sundhed politikker
Sundhed store gælder sundhed politikker for at afgøre, om et objekt er sund baseret på dens rapporter og dets underordnede websteder.

> [AZURE.NOTE] Sundhed politikker kan angives i klynge manifestet (for klynge og node sundhed evaluering) eller i programmanifestet (for programmet evaluering og en af dets underordnede websteder). Sundhed evaluering anmodninger kan også sende i brugerdefineret sundhed evaluering politikker, som bruges kun til denne evaluering.

Som standard gælder Service-strukturen strenge regler (alt skal være sund) for den hierarkiske overordnet / underordnet-relation. Hvis endnu en af underordnede har en beskadiget begivenhed, betragtes beskadiget overordnet som.

### <a name="cluster-health-policy"></a>Klynge sundhed politik
[Klynge sundhed politik](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.aspx) bruges til at evaluere den klynge tilstanden og node sundhed tilstand. Politikken kan defineres i klynge manifestet. Hvis den ikke findes, bruges standardpolitikken (nul tolererede fejl).
Politikken klynge sundhed indeholder:

- [ConsiderWarningAsError](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.considerwarningaserror.aspx). Angiver, om at behandle advarsel tilstand rapporterer som fejl under sundhed evaluering. Som standard: falsk.

- [MaxPercentUnhealthyApplications](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications.aspx). Angiver den maksimale tolererede procentdel af programmer, der kan være beskadiget, før klyngen betragtes som ved en fejl.

- [MaxPercentUnhealthyNodes](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes.aspx). Angiver den maksimale tolererede procentdel af knuder, der kan være beskadiget, før klyngen betragtes som ved en fejl. I store klynger er nogle noder altid ned eller ud for reparationer, så denne procentdel skal være konfigureret til at acceptere.

- [ApplicationTypeHealthPolicyMap](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap.aspx). Programmet type sundhed politik kortet kan bruges til at beskrive specielle formål typer under klynge sundhed evaluering. Som standard er alle programmer sætte i en gruppe og evalueres med MaxPercentUnhealthyApplications. Hvis visse typer af programmet skal behandles anderledes, kan de hentes af globale puljen. I stedet evalueres de mod de procentdele, der er knyttet til deres type programnavn i kortet. For eksempel i en klynge er der tusindvis af programmer forskellige typer, og nogle control programmer forekomster af en speciel programtype. Control programmer bør aldrig være ved en fejl. Du kan angive globale MaxPercentUnhealthyApplications til 20% for at acceptere nogle fejl, men programmet type "ControlApplicationType" Angiver MaxPercentUnhealthyApplications til 0. Denne metode, hvis nogle af de mange programmer er beskadiget, men under globale beskadiget procentdelen, vil klyngen evalueres til advarsel. En advarsel tilstanden påvirker ikke klynge opgraderingen eller andre overvågning som fejltilstand. Men blot én kontrolelementet programmet i fejl ville få klynge beskadiget, som udløser filmrulle tilbage eller afbrydes klynge opgraderingen, afhængigt af konfigurationen af opgradering.
For de programmet typer, der er defineret i kortet, hentes alle forekomster af tjenesteprogrammer af globale puljen af programmer. De evalueres baseret på det samlede antal programmer af typen programmet, ved hjælp af den bestemte MaxPercentUnhealthyApplications fra kortet. Resten af programmerne forbliver i den globale gruppe og evalueres med MaxPercentUnhealthyApplications.

I følgende eksempel er et uddrag af et klynge manifest. Du kan definere poster i programmet type kortet, der indsættes tegn foran parameternavn med "ApplicationTypeMaxPercentUnhealthyApplications-", efterfulgt af navnet på programmet på computeren.

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
    <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
  </Section>
</FabricSettings>
```

### <a name="application-health-policy"></a>Programmets tilstandspolitik
[Programmets tilstandspolitik](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.aspx) beskrives, hvordan evalueringen af begivenheder og underordnede stater sammenlægning er gjort for programmer og deres underordnede websteder. Det kan defineres i programmanifestet, **ApplicationManifest.xml**i pakken programmet på computeren. Hvis nogen politikker ikke er angivet, antages Service-strukturen, at enheden er beskadiget, hvis den har en tilstandsrapport eller et underordnet på tilstanden advarsel eller fejlmeddelelse.
De politikker, der kan konfigureres er:

- [ConsiderWarningAsError](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.considerwarningaserror.aspx). Angiver, om at behandle advarsel tilstand rapporterer som fejl under sundhed evaluering. Som standard: falsk.

- [MaxPercentUnhealthyDeployedApplications](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications.aspx). Angiver den maksimale tolererede procentdel af installerede programmer, der kan være beskadiget, før programmet betragtes som ved en fejl. Denne procent beregnes ved at dividere antallet af beskadiget installerede programmer over antallet af knuder, programmerne, der aktuelt er implementeret på i klyngen. Beregningen runder op til at acceptere en fejl på small antal noder. Standard procentdel: nul.

- [DefaultServiceTypeHealthPolicy](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy.aspx). Angiver service type sundhed standardpolitikken, der erstatter sundhed standardpolitikken for alle tjenestetyper af i programmet på computeren.

- [ServiceTypeHealthPolicyMap](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap.aspx). Indeholder en oversigt over service sundhed politikker per tjenestetype. Disse politikker Erstat standardpolitikker service type tilstand for hver angivne tjenestetype. Eksempelvis hvis et program har en uden status gateway tjenestetype og en med høj sikkerhed program tjenestetype, kan du konfigurere sundhed politikkerne for deres evaluering anderledes. Når du angiver politik for hver tjenestetype, kan du få mere detaljerede kontrol af tilstanden for tjenesten.

### <a name="service-type-health-policy"></a>Tjenesten type sundhed politik
[Tjenesten type sundhed politik](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.aspx) angiver, hvordan evaluere og samle tjenesterne, og underordnede af tjenester. Politikken indeholder:

- [MaxPercentUnhealthyPartitionsPerService](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice.aspx). Angiver den maksimale tolererede procentdel af partitioner, beskadiget, før en tjeneste betragtes som beskadiget. Standard procentdel: nul.

- [MaxPercentUnhealthyReplicasPerPartition](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition.aspx). Angiver den maksimale tolererede procentdel af beskadiget replikaer, før en partition betragtes som beskadiget. Standard procentdel: nul.

- [MaxPercentUnhealthyServices](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices.aspx). Angiver den maksimale tolererede procentdel af beskadiget tjenester, før programmet, der betragtes som beskadiget. Standard procentdel: nul.

I følgende eksempel er et uddrag af et progammanifest:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>Sundhed beregning
Brugere og automatiseret tjenester kan evaluere tilstand for en hvilken som helst enhed når som helst. Hvis du vil evaluere en enheds tilstand, sundhed store samlinger alle sundhed-rapporter baseret på enheden og evaluerer alle dets underordnede websteder (når det er relevant). Sundhed sammenlægning algoritme bruger sundhed politikker, som angiver, hvordan du evaluere tilstandsrapporter og hvordan skal aggregeres underordnede sundhed stater (når det er relevant).

### <a name="health-report-aggregation"></a>Sundhed rapport sammenlægning
Ét objekt kan have flere tilstandsrapporter sendes af forskellige reporters (systemkomponenter eller watchdogs) på forskellige egenskaber. Sammenlægningen bruger politikker tilknyttede sundhed, især medlemmet ConsiderWarningAsError for programmet eller klynge sundhed politik. ConsiderWarningAsError angiver, hvordan evaluering advarsler.

Sammenlagt tilstanden udløses af de *værste* tilstandsrapporter på enheden. Hvis der er mindst én fejlrapport tilstand, er aggregeret tilstanden en fejl.

![Sundhed rapport sammenlægning med fejlrapport.][2]

En tilstand-enhed, der indeholder en eller flere fejl tilstandsrapporter evalueres som fejl. På samme måde gælder for et udløbet sundhed rapporten, uanset dens tilstand.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Hvis der er ingen fejlrapporter og en eller flere advarsler, er aggregeret tilstanden advarsel eller fejlmeddelelse, afhængigt af flaget ConsiderWarningAsError politik.

![Sundhed rapport sammenlægning med advarsel rapport og ConsiderWarningAsError falsk.][3]

Sundhed rapport sammenlægning med advarsel rapport og ConsiderWarningAsError indstillet til falsk (standard).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Underordnet sundhed sammenlægning
Sammenlagt tilstanden for et objekt afspejler de underordnede sundhed stater (når det er relevant). Algoritmen til sammenlægning underordnede sundhed stater bruger sundhed politikkerne gældende baseret på enhedstypen.

![Underordnede enheder sundhed sammenlægning.][4]

Underordnet sammenlægning baseret på politikker for systemtilstand.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Når sundhed store har evalueret alle underordnede, samler deres sundhed tilstande, afhængigt af den konfigurerede maksimale procentdel af børn, beskadiget. Denne procentdel hentes fra politikken baseret på enhed og underordnet typen.

- Hvis alle børn har OK stater, er tilstanden underordnede sammenlagt OK.

- Hvis børn har både OK og advarsel stater, advarsel tilstanden underordnede sammenlagt.

- Hvis der er børn med fejl stater, ikke respekterer den maksimalt tilladte procentdel af børn, beskadiget, er aggregeret tilstanden en fejl.

- Hvis overholde underordnede med fejl stater den maksimale tilladte procentdel af børn, beskadiget, aggregeret tilstanden advarsel.

## <a name="health-reporting"></a>Sundhedsrapportering
Systemkomponenter, System-strukturen programmer og intern/ekstern watchdogs kan rapportere mod Service-strukturen enheder. Reporters skal *lokale* bestemmelser af tilstanden for de overvågede enheder, der er baseret på betingelser, som de overvågning. De behøver at se på en hvilken som helst globale tilstand eller aggregere data. Ønskede funktionsmåden er at have simpel reporters eller ikke komplekse, der kommer, der har brug for at se på mange ting at udlede hvilke oplysninger der skal sendes.

Hvis du vil sende sundhed data til sundhed store, skal en reporter identificere den pågældende enhed og oprette en tilstandsrapport. Rapporten kan derefter sendes via API ved hjælp af [FabricClient.HealthClient.ReportHealth](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient_members.aspx), via PowerShell eller via RESTEN.

### <a name="health-reports"></a>Tilstandsrapporter
[Tilstandsrapporter](https://msdn.microsoft.com/library/azure/system.fabric.health.healthreport.aspx) for hver af enheder i klyngen indeholder følgende oplysninger:

- **SourceId**. En streng, der entydigt identificerer reporter af hændelsen tilstand.

- **Enheds-id**. Identificerer den enhed, hvor rapporten er anvendt. Det er forskellig alt efter den [enhed, Skriv](service-fabric-health-introduction.md#health-entities-and-hierarchy):

  - Klynge. Ingen.

  - Node. Nodenavn (streng).

  - Programmet på computeren. Programnavn (URI). Repræsenterer navnet på forekomsten af programmet installeret i klyngen.

  - Tjenesten. Service navn (URI). Repræsenterer navnet på forekomsten af implementeret i klyngen.

  - Partition. Partition-ID (GUID). Repræsenterer partition bruger-id.

  - Replika. Med høj sikkerhed tjeneste replika-ID'ET eller uden status tjeneste forekomst-ID'ET (INT64).

  - DeployedApplication. Programnavnet (URI) og nodenavn (streng).

  - DeployedServicePackage. Programnavn (URI), nodenavn (streng) og service manifestet navn (streng).

- **Egenskaben**. En *streng* (ikke en fast optælling), der gør det muligt for reporter kategorisere hændelsen tilstand for en bestemt egenskab for objektet. Reporter A kan for eksempel rapportere tilstanden for Node01 "lagerplads" egenskab og reporter B kan rapportere tilstanden for egenskaben Node01 "connectivity". Disse rapporter i store sundhed, behandles det som separat tilstand begivenheder for objektet Node01.

- **Beskrivelse**. En streng, der gør det muligt for en rapportøren indeholder detaljerede oplysninger om hændelsen tilstand. **SourceId**, **Egenskaber**og **HealthState** bør fuldt beskrive rapporten. Beskrivelsen af tilføjer læsbar oplysninger om rapporten. Teksten, der gør det nemmere for administratorer og brugere at forstå rapporten tilstand.

- **HealthState**. En [optælling](service-fabric-health-introduction.md#health-states) , der beskriver tilstanden for rapporten. Accepterede værdier er OK, advarsler og fejl.

- **TimeToLive**. Et timespan, der angiver, hvor lang tid rapporten tilstand er gyldig. Kombineret med **RemoveWhenExpired**, kan det sundhed store vide, hvordan du evaluere udløbet begivenheder. Værdien er uendelig som standard, og rapporten er gyldig uendelig.

- **RemoveWhenExpired**. En boolesk værdi. Hvis indstilles til sand, udløbne sundhed rapporten automatisk fjernet fra sundhed store og rapporten påvirker ikke enhed sundhed evaluering. Bruges, når rapporten er gyldige for et angivet tidsrum kun og indberetterens behøver ikke at fjerne det udtrykkeligt. Det bruges også til at slette rapporter fra sundhed store (for eksempel en watchdog ændres og holder op med at sende rapporter med forrige kilde- og egenskaben). Det kan sende en rapport med en kort TimeToLive sammen med RemoveWhenExpired at fjerne eventuelle tidligere tilstand fra sundhed store. Hvis værdien er indstillet til falsk, fortolkes udløbet rapporten som en fejl på tjenestetilstand evalueringen. Værdien false signaler til sundhed store, kilden skal rapportere med jævne mellemrum for denne egenskab. Hvis det ikke er tilfældet, skal være der noget galt med watchdog. Den watchdog sundhed, registreres ved at overveje hændelsen som en fejl.

- **SequenceNumber**. Et positivt heltal, der skal være stigende, repræsenterer rækkefølgen af rapporterne. Det bruges af sundhed store til at registrere forældede rapporter, der er modtaget sent på grund af netværksforsinkelser eller andre problemer. En rapport er afvist, hvis sekvens tallet er mindre end eller lig med mest senest anvendt tallet for den samme enhed, kilde og egenskab. Hvis det ikke er angivet, genereres sekvensnummeret automatisk. Det er nødvendigt at placere nummeret sequence kun, når du rapporterer om tilstandsovergange. I dette tilfælde skal kilden at huske, hvilke de sendes rapporter og holde oplysningerne for gendannelse på failover.

Disse fire dele af oplysninger – SourceId, enheds-id, egenskaber og HealthState – der kræves for hver tilstandsrapport. SourceId streng ikke er tilladt skal starte med præfikset "**systemet.**", som er reserveret til system-rapporter. Det samme objekt er der kun en rapport til den samme kilde og egenskab. Flere rapporter for den samme kilde og egenskaben tilsidesætte hinanden, enten på klientsiden sundhed (hvis de er batches) eller tilstanden gemme side. Udskiftning er baseret på sekvens tal. nyere rapporter (med højere sekvens tal) erstatter ældre rapporter.

### <a name="health-events"></a>Sundhed begivenheder
Internt, bevarer sundhed store [sundhed hændelser](https://msdn.microsoft.com/library/azure/system.fabric.health.healthevent.aspx), der indeholder alle oplysninger fra de rapporter og ekstra metadata. Metadata, der indeholder den tid, sundhed klienten har fået rapporten og den tid, det blev ændret på serversiden. Sundhed hændelser, der returneres af [sundhed forespørgsler](service-fabric-view-entities-aggregated-health.md#health-queries).

De tilføjede metadata indeholder:

- **SourceUtcTimestamp**. Tid, der fik rapporten til sundhed klienten (Coordinated Universal Time).

- **LastModifiedUtcTimestamp**. Det tidspunkt, rapporten sidst blev ændret på serversiden (Coordinated Universal Time).

- **IsExpired**. Et flag for at angive, om rapporten er udløbet, når forespørgslen blev udført af sundhed butikken. En begivenhed kan være udløbet, kun, hvis RemoveWhenExpired er falsk. Ellers skal hændelsen returneret ikke af forespørgslen og fjernes fra store.

- **LastOkTransitionAt**, **LastWarningTransitionAt** **LastErrorTransitionAt**. Det sidste tidspunkt for advarsel-OK/fejl overgange. Disse felter giver oversigten over tilstanden tilstandsovergange for begivenheden.

Felterne tilstand overgang kan bruges til smartere påmindelser eller "historiske" sundhed hændelsesoplysninger. De muliggør scenarier f.eks.:

- Besked, når en egenskab er blevet på advarsel/fejl for mere end X minutter. Kontrollere betingelsen for et bestemt tidsrum undgår beskeder på midlertidige betingelser. For eksempel en besked, hvis der advarsel om tilstanden for mere end fem minutter kan oversættes til (HealthState == advarsel og nu - LastWarningTransitionTime > 5 minutter).

- Beskeder om kun på betingelser, der er blevet ændret i sidst X minutter. Hvis en rapport var allerede ved fejl, før det angivne tidspunkt, kan det ignoreres, da den var allerede angivelse af tidligere.

- Hvis en egenskab aktivering/deaktivering af mellem advarsel og fejl, finde ud af, hvor længe den er blevet beskadiget (det vil sige, ikke OK). For eksempel en besked, hvis egenskaben ikke har været sund i mere end fem minutter kan oversættes til (HealthState! = Ok og nu - LastOkTransitionTime > 5 minutter).

## <a name="example-report-and-evaluate-application-health"></a>Eksempel: Rapportere og evaluerer programmet tilstand
Følgende eksempel sender en tilstandsrapport via PowerShell på programmet **strukturen: / WordCount** fra kilden **MyWatchdog**. Sundhed rapporten indeholder oplysninger om egenskaben sundhed "tilgængelighed" i en fejltilstand tilstand med uendelig TimeToLive. Det forespørger derefter programmet tilstand, hvilket returnerer sammenlagt sundhed tilstand fejl og de hændelser, rapporteret tilstand på listen over sundhed begivenheder.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>Brug af sundhed modellen
Sundhed modellen tillader skytjenester og den underliggende Service-strukturen platform skalere, fordi overvågning og tilstand bestemmelser er fordelt på forskellige skærme i klyngen.
Andre systemer har en enkelt, centralt tjeneste på niveauet for klynge, fortolker alle *potentielt* nyttige oplysninger fra tjenester. Denne metode hindrer deres skalerbarhed. Det også muligt ikke at indsamle meget bestemte oplysninger for at hjælpe med at identificere problemer og potentielle problemer som tæt på den egentlige årsag som muligt.

Sundhed modellen bruges stærkt til overvågning og diagnosticering til at vurdere klynge og programmet tilstand og til overvåget opgraderinger. Andre tjenester Brug sundhed data til at udføre automatiske reparationer, opbygge klynge sundhed historik og udsteder beskeder på bestemte betingelser.

## <a name="next-steps"></a>Næste trin
[Vis Service-strukturen tilstandsrapporter](service-fabric-view-entities-aggregated-health.md)

[Bruge system tilstandsrapporter til fejlfinding](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Sådan rapport, og Kontrollér tjenestetilstand](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Tilføje brugerdefinerede Service-strukturen tilstandsrapporter](service-fabric-report-health.md)

[Overvåge og diagnosticere services lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Tjenesten strukturen programmet opgradering](service-fabric-application-upgrade.md)
