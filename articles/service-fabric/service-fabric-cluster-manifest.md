<properties
   pageTitle="Konfigurere din enkeltstående klynge | Microsoft Azure"
   description="I denne artikel beskrives, hvordan du konfigurerer din enkeltstående eller privat Service-strukturen klynge."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="dkshir"/>


# <a name="configuration-settings-for-standalone-windows-cluster"></a>Konfigurationsindstillinger for enkeltstående Windows klynge

I denne artikel beskrives, hvordan du konfigurerer en enkeltstående Service-strukturen klynge, ved hjælp af _**ClusterConfig.JSON**_ -fil. Du kan bruge denne fil til at angive oplysninger som noderne Service-strukturen og deres IP-adresser, forskellige typer af knuder på klyngen, sikkerhedskonfigurationer samt netværkstopologi med hensyn til fejl/opgraderingen domæner for din enkeltstående klynge.

Når du [Hent pakken enkeltstående Service-strukturen](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), et par eksempler på ClusterConfig.JSON filen bliver overført til din computer og arbejde. Eksemplerne har *DevCluster* i deres navne hjælper dig med at oprette en klynge med alle tre noder på den samme computer, som logiske noder. Af disse, skal være markeret mindst én node som en primær node. Denne klynge er nyttigt til et udvikling eller test miljø og understøttes ikke som en fremstilling klynge. Eksemplerne har *MultiMachine* i deres navne, kan du oprette en fremstilling kvalitet klynge med hver node på en anden computer. Antallet af primære noder for disse klynge baseres på det, [pålideligheden niveau](#reliability).

1. *ClusterConfig.Unsecure.DevCluster.JSON* og *ClusterConfig.Unsecure.MultiMachine.JSON* viser, hvordan du opretter en usikker test- eller klynge henholdsvis. 
    
2. *ClusterConfig.Windows.DevCluster.JSON* og *ClusterConfig.Windows.MultiMachine.JSON* viser, hvordan du opretter test- eller klynge, sikret ved hjælp af [Windows-sikkerhed](service-fabric-windows-cluster-windows-security.md).

3. *ClusterConfig.X509.DevCluster.JSON* og *ClusterConfig.X509.MultiMachine.JSON* viser, hvordan du opretter test- eller klynge, sikret ved hjælp af [X509 sikkerhed baseret på certifikat](service-fabric-windows-cluster-x509-security.md). 


Nu vil vi undersøge de forskellige sektioner af en _**ClusterConfig.JSON**_ -fil som nedenfor.

## <a name="general-cluster-configurations"></a>Generelle klyngekonfigurationer
Dette omfatter generelle klynge specifikke konfigurationer, som vist i JSON kodestykket nedenfor.

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2015-01-01-alpha",

Du kan give et brugervenligt navn til din tjeneste strukturen klynge ved at tildele den til variablen **navn** . **ClusterConfigurationVersion** er versionsnummeret for din klynge Du skal sætte den, hver gang du opgraderer din tjeneste strukturen klynge. Dog skal du lade **apiVersion** til standardværdien.


<a id="clusternodes"></a>
## <a name="nodes-on-the-cluster"></a>Noder på klyngen
Du kan konfigurere noderne på din tjeneste strukturen klynge ved hjælp af afsnittet **noder** som følgende kodestykke viser.

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

En tjeneste-strukturen klynge skal indeholde mindst 3 noder. Du kan tilføje flere noder til denne sektion ud fra din konfiguration. I følgende tabel beskrives konfigurationsindstillinger for hver enkelt node.

|**Node-konfiguration**|**Beskrivelse**|
|-----------------------|--------------------------|
|nodeName|Du kan give et brugervenligt navn til noden.|
|IP-adresse|Find ud af IP-adressen på dit node ved at åbne et kommandovindue og skrive `ipconfig`. Bemærk, at IPv4-adressen og tildele den til variablen **IP-adresse** .|
|nodeTypeRef|Hver node kan tildeles en anden nodetype. [Node-typer](#nodetypes) er defineret i afsnittet nedenfor.|
|faultDomain|Fejl domæner giver klynge administratorer at definere de fysiske noder, der kan mislykkes på samme tid på grund af delte fysisk afhængigheder.|
|upgradeDomain|Opgradering domæner beskriver sæt af knuder, der er lukket til tjenesten strukturen opgraderinger på næsten samme tidspunkt. Du kan vælge hvilke noder til at tildele til hvilke opgradering domæner, som de ikke er begrænset af en hvilken som helst fysiske krav.| 


## <a name="cluster-properties"></a>Klynge egenskaber

Sektionen **Egenskaber** i ClusterConfig.JSON bruges til at konfigurere klyngen på følgende måde.

<a id="reliability"></a>
### <a name="reliability"></a>Pålidelighed 
Sektionen **reliabilityLevel** definerer antallet kopier af systemets tjenester, der kan køre på de primære noder af klyngen. Dette øger pålideligheden af disse tjenester og dermed klyngen. Du kan angive denne variabel til enten *Bronze*, *Silver*, *Gold* eller *Platinum* til 3, 5, 7 eller 9 kopier af disse tjenester henholdsvis. Få vist et eksempel nedenfor.

    "reliabilityLevel": "Bronze",
    
Bemærk, at da en primære node kører en enkelt kopi af systemets tjenester, du har brug for et minimum af 3 primære noder til *Bronze*, 5 for *Silver*, 7 for *Gold* og 9 for *Platinum* pålidelighed niveauer.


### <a name="diagnostics"></a>Diagnosticering
Sektionen **diagnosticsStore** kan du konfigurere parametre for at aktivere til diagnosticering og fejlfinding i forbindelse med node eller klynge fejl, som vist i følgende kodestykket. 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

**Metadata** er en beskrivelse af din klynge diagnosticering og kan angives ud fra din konfiguration. Disse variabler hjælpe med at indsamle ETW spore logfiler, Crashdumps samt tællere i ydeevne. Læs [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) og [ETW sporing](https://msdn.microsoft.com/library/ms751538.aspx) yderligere oplysninger om ETW spore logfiler. Alle logfiler, herunder [går ned, gemmer](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) og [tællere i ydeevne](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) kan omdirigeres til mappen **connectionString** på din computer. Du kan også bruge *AzureStorage* til lagring af diagnosticering. Se et eksempel kodestykke nedenfor.

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>Sikkerhed 
Afsnittet **sikkerhed** er nødvendig for en sikker enkeltstående Service-strukturen klynge. Følgende kodestykke viser en del af dette afsnit.

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

**Metadata** er en beskrivelse af din sikker klynge og kan angives ud fra din konfiguration. Se, hvilken type af sikkerhed, der implementerer klyngen og knuderne **ClusterCredentialType** og **ServerCredentialType** . De kan angives til enten *X509* for et værdipapir med certifikat-baserede eller *Windows* til en Azure Active Directory-baseret sikkerhed. Resten af afsnittet **sikkerhed** afhænger af typen sikkerheden. Læs [sikkerhed baseret på certifikater i en enkeltstående klynge](service-fabric-windows-cluster-x509-security.md) eller [Windows-sikkerhed i en enkeltstående klynge](service-fabric-windows-cluster-windows-security.md) for at få oplysninger om, hvordan du udfylde resten af afsnittet **sikkerhed** .


<a id="nodetypes"></a>
### <a name="node-types"></a>Node-typer
**NodeTypes** afsnit beskrives typer noderne din klynge med. Mindst én nodetype skal være angivet til en klynge, som vist i nedenstående kodestykket. 

    "nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpointPort": "19001",
        "leaseDriverEndpointPort": "19002"
        "serviceConnectionEndpointPort": "19003",
        "httpGatewayEndpointPort": "19080",
        "applicationPorts": {
            "startPort": "20575",
            "endPort": "20605"
        },
        "ephemeralPorts": {
            "startPort": "20606",
            "endPort": "20861"
        },
        "isPrimary": true
    }]

**Navn** er det fulde navn for denne nodetype bestemt. Tildele dets fulde navn til **nodeTypeRef** variablen for denne node som for at oprette en node af denne nodetype, der er nævnt [ovenfor](#clusternodes). Definere Forbindelsesslutpunkterne, der skal bruges for hver nodetype. Du kan vælge en hvilken som helst portnummer for disse slutpunkter for forbindelse, så længe de ikke er i konflikt med andre slutpunkter i denne klynge. I en klynge med flere noder, vises der en eller flere primære noder (det vil sige **isPrimary** indstillet til *Sand*), afhængigt af [**reliabilityLevel**](#reliability). Læs [Service-strukturen klynge kapacitet planlægning overvejelser i forbindelse med](service-fabric-cluster-capacity.md) oplysninger om **nodeTypes** og **reliabilityLevel** værdier, og at vide, hvad er primære og ikke-primær node-typer. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Slutpunkter, der bruges til at konfigurere Nodetyperne

- *clientConnectionEndpointPort* er den port, der bruges af klienten til at oprette forbindelse til klynge, når du bruger klienten API'er. 
- *clusterConnectionEndpointPort* er den port, hvormed noderne kommunikere med hinanden.
- *leaseDriverEndpointPort* er den port, der bruges af klynge bortlease driver til at finde ud af, om noderne er stadig er aktiv. 
- *serviceConnectionEndpointPort* er den port, der bruges af programmer og tjenester, der er installeret på en node til at kommunikere med tjenesten strukturen-klienten på bestemt noden.
- *httpGatewayEndpointPort* er den port, som Service-strukturen Explorer til at oprette forbindelse til klyngen.
- *ephemeralPorts* er de [dynamiske porte, der bruges af Operativsystemet](https://support.microsoft.com/kb/929851). Tjenesten strukturen anvender en del af disse som programmet porte og resterende bliver tilgængelig for Operativsystemet. Også knyttes dette interval til den eksisterende område, der findes i OS, så du kan bruge de områder, der er givet i JSON eksempelfiler for alle formål. Du har brug at sikre, at forskellen mellem starten og slutningen porte er mindst 255. 
- *applicationPorts* er de porte, der anvendes af tjenesten strukturen programmer. Disse skal være et undersæt af *ephemeralPorts*, nok til at dække slutpunkt kravet af dine programmer. Tjenesten strukturen Brug disse, når nye porte der kræves samt tager sig af åbne firewallen for disse porte. 
- *reverseProxyEndpointPort* er en valgfri omvendt proxy slutpunkt. Du kan finde flere oplysninger i [Tjenesten strukturen omvendt Proxy](service-fabric-reverseproxy.md) . 


### <a name="other-settings"></a>Andre indstillinger
Sektionen **fabricSettings** kan du angive mapperne roden for tjenesten strukturen data og logfiler. Du kan tilpasse disse kun under den indledende klynge oprettelsen. Et eksempel kodestykke i dette afsnit finder du nedenfor.

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

Vi anbefaler ved hjælp af et ikke-OS drev som FabricDataRoot og FabricLogRoot, da det giver mere pålidelighed mod OS går ned. Bemærk, at hvis du tilpasser kun data roden, derefter log roden placeres ét niveau under data rod.


## <a name="next-steps"></a>Næste trin

Når du har en hel ClusterConfig.JSON-fil, der er konfigureret i henhold til opsætningen enkeltstående klynge, du kan installere din klynge ved at følge i artiklen [oprette en Azure Service strukturen klynge lokalt eller i skyen](service-fabric-cluster-creation-for-windows-server.md) og derefter fortsætte med at [visualisere din klynge med tjenesten strukturen Stifinder](service-fabric-visualizing-your-cluster.md).


