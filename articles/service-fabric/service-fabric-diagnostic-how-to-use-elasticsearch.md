<properties
   pageTitle="Brug af Elasticsearch som en tjeneste-strukturen programlageret sporing | Microsoft Azure"
   description="Beskriver, hvordan Service-strukturen programmer kan bruge Elasticsearch og Kibana til at gemme, indeks og søge i programmet sporinger (logfiler)"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/09/2016"
   ms.author="karolz@microsoft.com"/>

# <a name="use-elasticsearch-as-a-service-fabric-application-trace-store"></a>Brug Elasticsearch som en tjeneste-strukturen programmet sporing gemme
## <a name="introduction"></a>Introduktion
I denne artikel beskrives, hvordan [Azure Service strukturen](https://azure.microsoft.com/documentation/services/service-fabric/) programmer kan bruge **Elasticsearch** og **Kibana** til sporing af programlageret, indeksering og søgning. [Elasticsearch](https://www.elastic.co/guide/index.html) er et open source-, fordelt og SVG realtid søgning og analytics-program, der er velegnet til denne opgave. Det kan være installeret på Windows og Linux virtuelle maskiner, der kører på Microsoft Azure. Elasticsearch kan effektivt behandle *strukturerede* sporinger, der er oprettet ved hjælp af teknologier som **Begivenhed sporing for Windows (ETW)**.

ETW bruges af tjenesten strukturen runtime til kilde diagnostiske oplysninger (sporinger). Det er den anbefalede metode til tjenesten strukturen programmer til deres diagnostiske oplysninger fra kilden for. Ved hjælp af den samme ordning giver mulighed for korrelation mellem runtime-leveret og fra programmet sporinger og foretage fejlfinding nemmere. Tjenesten strukturen projektskabeloner i Visual Studio omfatter et logføring-API (baseret på klassen .NET **EventSource** ), der udsender ETW sporinger som standard. Se [overvågnings- og diagnosticering tjenester i en lokal computer udvikling opsætning](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)for en generel oversigt over Service-strukturen programmet sporing ved hjælp af ETW.

Sporinger i Elasticsearch vises, skal de registreres på noderne Service-strukturen klynge i realtid (mens programmet kører) og sendes til et Elasticsearch slutpunkt. Der er to overordnede indstillinger for registrering af sporing:

+ **Processen sporing indsamle**  
Programmet, eller mere præcist serviceprocessen, er ansvarlig for afsendelse af diagnosticering dataene til sporing store (Elasticsearch).

+ **Indsamle uden for proces sporing**  
En separat agent indsamle sporinger fra den tjeneste eller processer og sende dem til sporing store.

Nedenfor vi beskriver, hvordan du konfigurerer Elasticsearch på Azure, diskutere professionelle og ulemper for både registrere indstillinger, og beskriver, hvordan du kan konfigurere en tjeneste-strukturen tjeneste for at sende data til Elasticsearch.


## <a name="set-up-elasticsearch-on-azure"></a>Konfigurere Elasticsearch på Azure
Den mest enkle måde at konfigurere tjenesten Elasticsearch på Azure er [**Azure ressourcestyring skabeloner**](../azure-resource-manager/resource-group-overview.md)igennem. En omfattende [Hurtig start Azure ressourcestyring skabelon til Elasticsearch](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) er tilgængelig fra Hurtig start Azure skabeloner lager. Denne skabelon bruger separat lagerplads konti for skalaenheder (grupper af knuder). Det kan også Klargør separat klienten og serveren noder med forskellige konfigurationer og forskellige antal datadisce vedhæftet.

Her kan bruge vi en anden skabelon, kaldet **ES MultiNode** fra [Azure diagnosticeringsværktøjer lager](https://github.com/Azure/azure-diagnostics-tools). Denne skabelon er nemmere at bruge, og der oprettes en Elasticsearch klynge, der er beskyttet af HTTP basisgodkendelse. Før du fortsætter, skal du hente lageret fra GitHub til din computer og (ved enten at klone lageret eller hente en zip-fil). Skabelonen ES MultiNode er placeret i mappen med samme navn.

### <a name="prepare-a-machine-to-run-elasticsearch-installation-scripts"></a>Forberede en computer til at køre Elasticsearch installationsscripts
Den nemmeste måde at bruge skabelonen ES MultiNode er gennem et medfølgende Azure PowerShell-script kaldet `CreateElasticSearchCluster`. Hvis du vil bruge dette script, skal du installere PowerShell moduler og et værktøj, der hedder **openssl**. Disse er nødvendig for at oprette en SSH nøgle, der kan bruges til at administrere din Elasticsearch klynge fra en fjernplacering.

`CreateElasticSearchCluster`script henvender sig til nemmere at bruge med skabelonen ES MultiNode fra en Windows-computer. Det er muligt at bruge skabelonen på en Windows-computer, men dette scenario er ikke medtaget i denne artikel.

1. Hvis du ikke har installeret dem allerede, skal du installere [**Azure PowerShell moduler**](http://aka.ms/webpi-azps). Når du bliver bedt om det, skal du klikke på **Kør**, derefter **installere**. Azure PowerShell 1,3 eller nyere er påkrævet.

2. Værktøjet **openssl** er inkluderet i fordelingen af [**Ciffer til Windows**](http://www.git-scm.com/downloads). Hvis du allerede ikke har gjort det, kan du installere [Ciffer til Windows](http://www.git-scm.com/downloads) nu. (Installation standardindstillingerne er OK).

3. Forudsætter, at der ciffer er blevet installeret, men ikke inkluderet i systemstien, Åbn en Microsoft Azure PowerShell-vinduet, og Kør følgende kommandoer:

    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```

    Erstatte den `<Git installation folder>` med ciffer placeringen på din computer standard er **"C:\Program Files\Git"**. Bemærk tegnet semikolon i starten af den første sti.

4. Sikre dig, at du er logget på Azure (via [`Add-AzureRmAccount`](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet) og at du har valgt det abonnement, der skal bruges til at oprette din klynge elastiske søgning. Du kan kontrollere, at korrekte abonnement er valgt ved hjælp af `Get-AzureRmContext` og `Get-AzureRmSubscription` cmdletter.

5. Hvis du allerede har gjort det, kan du ændre den aktuelle mappe til mappen ES MultiNode.

### <a name="run-the-createelasticsearchcluster-script"></a>Kør scriptet CreateElasticSearchCluster
Før du kører scriptet, åbne den `azuredeploy-parameters.json` fil og bekræfte eller angive værdier for parametrene script. Der findes følgende parametre:

|Parameternavn           |Beskrivelse|
|-----------------------  |--------------------------|
|dnsNameForLoadBalancerIP |Det navn, der bruges til at oprette det offentligt synlige DNS-navn for den elastiske Søg klynge (ved at føje Azure område domænet til det angivne navn). Hvis denne parameterværdi er "myBigCluster", og det valgte Azure område er Vest USA, er det resulterende DNS-navn for klyngen for eksempel myBigCluster.westus.cloudapp.azure.com. <br /><br />Dette navn fungerer også som rodnavn for mange elementer, der er knyttet til den elastiske Søg klynge, som data nodenavne.|
|adminUsername           |Navnet på administratorkontoen til administration af elastiske Søg klynge (tilsvarende SSH nøgler oprettes automatisk).|
|dataNodeCount           |Antallet af knuder på elastiske Søg klynge. Den aktuelle version af scriptet skelner ikke mellem data og forespørgsel noder; alle noder Afspil begge roller. Standard er 3 noder.|
|dataDiskSize            |Størrelsen af datadisce (i GB), der er allokeret for hver datanode. Hver node modtager 4 data disk udelukkende dedikeret til elastiske Search-tjenesten.|
|område                  |Navnet på Azure område, hvor elastiske Søg klynge skal være placeret.|
|esUserName              |Brugernavnet for den bruger, der er konfigureret til at have adgang til ES klynge (underlagt HTTP basisgodkendelse). Adgangskoden er ikke en del af parametre fil og skal der gives hvornår `CreateElasticSearchCluster` bruges scriptet.|
|vmSizeDataNodes         |Størrelsen på Azure virtuelt til elastiske Søg klyngenoder. Standard Standard_D2.|

Nu er du klar til at køre scriptet. Problem med følgende kommando:

```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name> -Region <azure-region> -EsPassword <es-password>
```

hvor 

|Script parameternavn    |Beskrivelse|
|-----------------------  |--------------------------|
|`<es-group-name>`        |Navnet på gruppen Azure ressource, der indeholder alle elastiske Søg klyngeressourcer.|
|`<azure-region>`         |Navnet på det Azure område, hvor elastiske Søg klynge skal oprettes.|         
|`<es-password>`          |Adgangskoden til brugerens elastiske søgning.|

>[AZURE.NOTE] Hvis du får en undtagelsesfejl fra Cmdletten Test-AzureResourceGroup, har du glemt til at logge på Azure (`Add-AzureRmAccount`).

Hvis du får en fejl i at køre scriptet, og du bestemme, at fejlen skyldes en forkert skabelon parameterværdi, rette parameterfilen og køre scriptet igen med en anden ressource gruppenavn. Du kan også genbruge den samme ressource gruppenavn og har scriptet oprydning den gamle liste ved at tilføje den `-RemoveExistingResourceGroup` -parameter til script aktiveringen.

### <a name="result-of-running-the-createelasticsearchcluster-script"></a>Resultatet af at køre scriptet CreateElasticSearchCluster
Når du har kørt den `CreateElasticSearchCluster` script, på følgende primære elementer oprettes. I dette eksempel antager vi, at du har brugt "myBigCluster" som værdien af den `dnsNameForLoadBalancerIP` parameter, og at det område, hvor du har oprettet klyngen er Vest USA.

|Genstand|Navn, placering og kommentarer|
|----------------------------------|----------------------------------|
|SSH nøgle til fjernadministration |myBigCluster.key fil (i den mappe, hvorfra CreateElasticSearchCluster blev kørt). <br /><br />Denne vigtige fil kan bruges til at oprette forbindelse til noden admin og (via noden administrator) til data noderne i klyngen.|
|Administrator node                        |myBigCluster admin.westus.cloudapp.azure.com <br /><br />En dedikeret VM til Elasticsearch klynge fjernadministration – den eneste, der tillader ekstern SSH forbindelser. Den kører på det samme virtuelle netværk som alle Elasticsearch klyngenoder, men den kan ikke køre Elasticsearch tjenester.|
|Data noder                        |myBigCluster1... myBigCluster*N* <br /><br />Data noder, der kører Elasticsearch og Kibana tjenester. Du kan oprette forbindelse via SSH til de enkelte noder, men kun via noden administrator.|
|Elasticsearch klynge             |http://myBigCluster.westus.cloudapp.Azure.com/ES/ <br /><br />Det primære slutpunkt for Elasticsearch klynge (Bemærk /es suffiks). Det er beskyttet af HTTP basisgodkendelse (legitimationsoplysningerne, der blev angivet esUserName/esPassword parametrene for skabelonen ES MultiNode). Klyngen har også hovedet plug-in'en installeret (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head) til administration af grundlæggende klynge.|
|Kibana service                    |http://myBigCluster.westus.cloudapp.Azure.com <br /><br />Tjenesten Kibana er konfigureret til at vise data fra den oprettede Elasticsearch klynge. Det er beskyttet af de samme legitimationsoplysninger som klynge selve.|

## <a name="in-process-versus-out-of-process-trace-capturing"></a>Igangværende kontra uden for proces sporing indsamle
I introduktionen, nævnt diagnosticering dataindsamling to grundlæggende måder: i processen og uden for proces. Hver har styrker og svagheder.

Fordelene ved **processen sporing indsamle** er:

1. *Nem konfiguration og installation*

    * Konfiguration af diagnosticering dataindsamling er kun en del af konfigurationen af programmet. Det er nemt at beholde den altid "synkroniseret" med resten af programmet.

    * Per programmet eller per tjenesten konfiguration er nemt kan lade sig gøre.

    * Uden for proces sporing indsamle kræver normalt en separat installation og konfiguration af diagnosticering agent, som er en ekstra administrative opgave og en potentiel kilde til fejl. Bestemt agent teknologi kan ofte kun én forekomst af agent per virtuelt (node). Dette betyder, at konfigurationen til samling af diagnosticering konfigurationen der deles af alle programmer og tjenester, der kører på noden.

2. *Fleksibilitet*

    * Programmet kan sende dataene, uanset hvor de skal gå, så længe der er et dokumentbibliotek med klient, der understøtter lagerplads målrettede datasystem. Ny dræn kan tilføjes som ønskede.

    * Kompleks capture, filtrering og data sammenlægning regler kan implementeres.

    * En uden for proces sporing indsamle er ofte begrænset af vaskene data, der understøtter agenten. Nogle supportmedarbejdere er extensible.

3. *Adgang til interne programdata og kontekst*

    * Det diagnosticering undersystem kører i programtjenester/processen kan nemt øge sporinger med kontekstafhængige oplysninger.

    * I tilgang uden for proces skal dataene sendes til en agent via nogle kommunikation mellem processer ordning, som begivenhed sporing til Windows. Denne funktion kan fastsætte yderligere begrænsninger.

Fordelene ved **hentning af uden for proces sporing** er:

1. *Muligheden for at overvåge programmet og indsamle nedbrud gemmer*

    * Igangværende sporing indsamle kan være mislykkes, hvis programmet ikke kan startes eller går ned. En uafhængig agent er meget bedre mulighed for at indsamle vigtige oplysninger om fejlfinding.<br /><br />

2. *Udløbsdato, stabilitet og dokumenterede ydeevnen*

    * En agent, der er udviklet af en platform leverandør (som en Microsoft Azure diagnosticering agent) er underlagt strenge test og kæmper Stramning.

    * Med igangværende sporing indsamle skal omhu at sikre, at aktiviteten sende diagnosticering data fra en ansøgningsprocedure ikke virker forstyrrende på programmets hovedopgaver eller introducere tidsindstilling eller ydeevne problemer. En uafhængigt af hinanden, der kører agent er mindre giver risiko for disse problemer og er udviklet specielt til at begrænse dens indvirkning på systemet.

Det er muligt at kombinere og få glæde af begge tilgange. Faktisk, kan det være den bedste løsning til mange programmer.

Vi bruger her **Microsoft.Diagnostic.Listeners bibliotek** og igangværende sporet indsamle for at sende data fra en-strukturen tjenesteprogram til en Elasticsearch klynge.

## <a name="use-the-listeners-library-to-send-diagnostic-data-to-elasticsearch"></a>Brug biblioteket lyttere for at sende diagnosticering data til Elasticsearch
Biblioteket Microsoft.Diagnostic.Listeners er en del af PartyCluster Service-strukturen Northwind. Sådan bruger du det:

1. Du kan hente [eksemplet PartyCluster](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster) fra GitHub.

2. Kopiere Microsoft.Diagnostics.Listeners og Microsoft.Diagnostics.Listeners.Fabric projekterne (hele mapper) fra mappen PartyCluster eksempel til mappen løsning for det program, der er meningen til at sende dataene til Elasticsearch.

3. Åbn target løsningen, højreklik på noden løsning i Solution Explorer, og vælg **Tilføje eksisterende projekt**. Føje Microsoft.Diagnostics.Listeners projektet til løsningen. Gentag den samme for Microsoft.Diagnostics.Listeners.Fabric projektet.

4. Føje et projekt fra din tjeneste projekter til de to tilføjede projekter. (Hver tjeneste, som er meningen til at sende data til Elasticsearch skal referere til Microsoft.Diagnostics.EventListeners og Microsoft.Diagnostics.EventListeners.Fabric.)

    ![Projekt-referencer til Microsoft.Diagnostics.EventListeners og Microsoft.Diagnostics.EventListeners.Fabric biblioteker][1]

### <a name="service-fabric-general-availability-release-and-microsoftdiagnosticstracing-nuget-package"></a>Tjenesten strukturen generelt tilgængelighed version og Microsoft.Diagnostics.Tracing Nuget pakke
Programmer, der er oprettet med tjenesten strukturen generelt tilgængelighed release (2.0.135, udgivet marts 31 2016) målrette **.NET Framework 4.5.2**. Denne version er den højeste version af .NET Framework understøttes af Azure på tidspunktet for GA udgave. I denne version af framework mangler desværre visse EventListener APIs, der skal biblioteket Microsoft.Diagnostics.Listeners. Da EventSource (den komponent, der ligger til grund for logføring API'er i strukturen programmer) og EventListener er tæt forbundet, kan alle projekter, der bruger biblioteket Microsoft.Diagnostics.Listeners skal bruge en alternativ implementering af hændelseskilde. Denne implementering leveres af **Microsoft.Diagnostics.Tracing Nuget pakke**, oprettet af Microsoft. Pakken er fuldt bagudkompatible med EventSource inkluderet i en ramme, så ingen kodeændringer skal være nødvendige end der refereres til navneområdet ændringer.

For at begynde at bruge Microsoft.Diagnostics.Tracing implementeringen af klassen hændelseskilde, skal du følge disse trin for hver tjeneste projekt, der skal sende data til Elasticsearch:

1. Højreklik på tjenesten projektet og vælg **Administrer Nuget pakker**.

2. Skifte til nuget.org pakke kilde (hvis det ikke allerede er markeret), og Søg efter "**Microsoft.Diagnostics.Tracing**".

3. Installere på `Microsoft.Diagnostics.Tracing.EventSource` pakke (og dens afhængigheder).

4. Åbn filen **ServiceEventSource.cs** eller **ActorEventSource.cs** i projektet tjenesten og erstatte den `using System.Diagnostics.Tracing` direktiv oven på filen med de `using Microsoft.Diagnostics.Tracing` direktiv.

Disse trin kan ikke det er nødvendigt, når **.NET Framework 4.6** understøttes af Microsoft Azure.

### <a name="elasticsearch-listener-instantiation-and-configuration"></a>Elasticsearch lytteren forskellige og konfiguration
Det sidste trin til at sende diagnosticering data til Elasticsearch er at oprette en forekomst af `ElasticSearchListener` , og Konfigurer den med Elasticsearch forbindelsesdata. Lytteren registrerer automatisk alle hændelser udløses via EventSource klasser defineres i tjenesten projektet. Det skal være levende under levetiden for tjenesten, så er det bedste sted til at oprette den i initialisering tjenestekode. Her er, hvordan initialiseringskoden for en uden status tjeneste kan se efter de nødvendige ændringer (tilføjelser, der er fremhævet i kommentarer, der starter med `****`):

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Runtime;

// **** Add the following directives
using Microsoft.Diagnostics.EventListeners;
using Microsoft.Diagnostics.EventListeners.Fabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>        
        private static void Main()
        {
            try
            {
                // **** Instantiate ElasticSearchListener
                var configProvider = new FabricConfigurationProvider("ElasticSearchEventListener");
                ElasticSearchListener esListener = null;
                if (configProvider.HasConfiguration)
                {
                    esListener = new ElasticSearchListener(configProvider);
                }

                // The ServiceManifest.XML file defines one or more service type names.
                // Registering a service maps a service type name to a .NET type.
                // When Service Fabric creates an instance of this service type,
                // an instance of the class is created in this host process.

                ServiceRuntime.RegisterServiceAsync("Stateless1Type", 
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                // Prevents this host process from terminating so services keep running.
                Thread.Sleep(Timeout.Infinite);

                // **** Ensure that the ElasticSearchListner instance is not garbage-collected prematurely
                GC.KeepAlive(esListener);
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e);
                throw;
            }
        }
    }
}
```

Elasticsearch Forbindelsesdataene skal anbringes i en separat sektion i konfigurationsfil service (**PackageRoot\Config\Settings.xml**). Navnet på sektionen skal svare til den værdi, der overføres til den `FabricConfigurationProvider` parametre, for eksempel:

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
Værdierne i `serviceUri`, `userName` og `password` parametre svarer til den adresse på Elasticsearch klynge slutpunkt, Elasticsearch brugernavn og adgangskode, henholdsvis. `indexNamePrefix`er præfikset for Elasticsearch indeks; biblioteket Microsoft.Diagnostics.Listeners opretter et nyt indeks til dens data dagligt.

### <a name="verification"></a>Bekræftelse
Det var det! Når tjenesten kører, starter det nu sende sporinger til tjenesten Elasticsearch angivet i konfigurationen. Du kan kontrollere dette ved at åbne Kibana UI der er knyttet til den destinationsadresse Elasticsearch forekomst. I eksemplet er sidens adresse http://myBigCluster.westus.cloudapp.azure.com/. Kontrollér, at der indekserer sammen med navnepræfikset, der er valgt til den `ElasticSearchListener` forekomst faktisk er blevet oprettet og udfyldes med data.

![Kibana viser PartyCluster programhændelser][2]

## <a name="next-steps"></a>Næste trin
- [Få flere oplysninger om diagnosticering og overvågning en tjeneste-strukturen tjeneste](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png
