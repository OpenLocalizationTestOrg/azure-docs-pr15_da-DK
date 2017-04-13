<properties
   pageTitle="Køre Windows FOS i flere områder til høj tilgængelighed | Henviser til arkitektur | Microsoft Azure"
   description="Sådan installeres FOS i flere områder på Azure til høj tilgængelighed og fleksibilitet."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/21/2016"
   ms.author="mwasson"/>

# <a name="running-windows-vms-in-multiple-regions-for-high-availability"></a>Køre Windows FOS i flere områder til høj tilgængelighed

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

> [AZURE.SELECTOR]
- [Køre Linux FOS i flere områder til høj tilgængelighed](guidance-compute-multiple-datacenters-linux.md)
- [Køre Windows FOS i flere områder til høj tilgængelighed](guidance-compute-multiple-datacenters.md)

Vi anbefaler et sæt af fremgangsmåder til at køre Windows virtuelle maskiner (VM'er) i flere Azure områder, at opnå tilgængelighed og en robust nedbrud gendannelse infrastruktur i denne artikel.

> [AZURE.NOTE] Azure har to forskellige installation modeller: [Ressourcestyring] [ resource groups] og klassisk. I denne artikel bruges Ressourcestyring, som Microsoft anbefaler til nye installationer.

En flere områder arkitektur kan give højere tilgængelighed end anvender for et enkelt område. Hvis en internationale afbrydelse påvirker det primære område, kan du bruge [Trafik Manager] [ traffic-manager] overføres til den sekundære område. Denne arkitektur kan også hjælpe, hvis et enkelt undersystem af programmet mislykkes. 

Der er flere generelle fremgangsmåder til at opnå høj tilgængelighed på tværs af datacentre:

- Aktiv/passiv med varmt standby. Trafikken går til et område, mens de andre venter på standby. FOS i det sekundære område er allokeret og kører til enhver tid.

- Aktiv/passiv med kolde standby. Det samme, men FOS i det sekundære område er ikke tildelt indtil, der kræves for failover. Denne metode koster mindre til at køre, men Generelt er længere nedetid under en fejl.

- Aktive. Begge områder er aktiv, og anmodninger er Indlæs ligeligt mellem dem. Hvis et datacenter er tilgængelig, hentes den af rotation. 

Denne arkitektur fokuserer på aktiv/passiv med varmt standby, ved hjælp af trafik Manager for failover. Bemærk, at du kan installere et lille antal FOS for varme standby og derefter skalere ud efter behov.

## <a name="architecture-diagram"></a>Arkitektur diagram

I det følgende diagram bygger på arkitekturen vises i [tilføje pålidelighed til en N-delt arkitektur på Azure](guidance-compute-n-tier-vm.md).

> Et Visio-dokument, der indeholder dette arkitektur diagram kan hentes på [Microsoft download center][visio-download]. I dette diagram er på "Beregn - flere område (Windows) side.

[! [0]][0]

- **Primære og sekundære områder**. Denne arkitektur bruger to områder til at opnå større tilgængelighed. En er det primære område. Under normale handlinger sendes netværkstrafik til det primære område. Men hvis, som er tilgængelig, dirigeres trafik til den sekundære område.

- ** [Azure trafik Manager] [ traffic-manager] ** dirigerer indgående anmodninger til det primære område. Hvis denne region bliver utilgængeligt, mislykkes trafik Manager over til den sekundære område. Yderligere oplysninger finder du i afsnittet [Konfiguration af trafik Manager](#configuring-traffic-manager).

- **Ressourcegrupper**. Oprette separate [ressourcegrupper] [ resource groups] for den primære region, det sekundære region, og til trafik Manager. Det giver dig mulighed for at administrere hvert område som en enkelt samling af ressourcer. Du kan for eksempel Geninstaller ét område, uden at foretage dig ned et andre. [Sammenkæde ressourcegruppe][resource-group-links], så du kan køre en forespørgsel for at få vist alle ressourcerne for programmet.

- **VNets**. Oprette en separat VNet for hvert område. Kontrollér, at felterne adresse ikke overlapper. 

- **SQL Server altid på tilgængelighed gruppe**. Hvis du bruger SQL Server, anbefaler vi [SQL altid på Availabilty grupper] [ sql-always-on] for høj tilgængelighed. Opret en enkelt tilgængelighed gruppe, som indeholder forekomsterne af SQL Server i begge områder. Du kan finde flere oplysninger i afsnittet [konfiguration af gruppen SQL Server altid på tilgængelighed](#configuring-the-sql-server-alwayson-availability-group ).

> [AZURE.NOTE] Også overveje [Azure SQL-Database][azure-sql-db], som giver en relationel database som en skybaseret tjeneste. Du behøver ikke at konfigurere en tilgængelighed gruppe eller administrere failover med SQL-Database.  

- **VPN Gateways**: oprette en [VPN-gateway] [ vpn-gateway] i hver VNet og konfigurere en [forbindelse VNet-VNet][vnet-to-vnet], for at aktivere netværkstrafik mellem de to VNets. Dette er påkrævet for gruppen SQL altid på tilgængelighed.

## <a name="recommendations"></a>Anbefalinger

Azure byder på mange forskellige ressourcer og ressourcetyper, så denne referencearkitektur kan være klargjort mange forskellige måder. Vi har angivet en Azure ressourcestyring skabelon for at installere den referencearkitektur, der følger disse anbefalinger. Hvis du vælger at oprette følger disse anbefalinger i din egen referencearkitektur, medmindre du har særlige krav, en anbefaling der ikke passer.

### <a name="regional-pairing"></a>Internationale parring

Hvert område for Azure hører sammen med et andet område i den samme Geografi. Vælg Generelt områder i det samme internationale par (for eksempel af US 2 og OS Central). Fordele ved at gøre dette, omfatter:

- Hvis der er en bred afbrydelse, prioriteres gendannelse af mindst ét område af alle par.

- Planlagte Azure systemopdateringer udbredes til parvis områder fortløbende, for at minimere mulige nedetid.

- Par findes i den samme Geografi skal man mødes data residency krav.

Men Sørg for, at begge områder understøtter alle de Azure tjenester, der er behov for dit program (se [Services efter område][services-by-region]). Finde flere oplysninger om internationale par [Business løbende og genoprettelse efter nedbrud (BCDR): Azure parvis områder][regional-pairs].

### <a name="traffic-manager-configuration"></a>Konfigurationen trafik Manager

Overvej følgende punkter ved konfiguration trafik manager for det pågældende scenarie:

- **Routing.** Trafik Manager understøtter flere [routing algoritmer][tm-routing]. Dette scenario, der er beskrevet i denne artikel, brug af _prioritet_ routing (tidligere kaldet _failover_ routing). Med denne indstilling sender trafik Manager alle anmodninger til det primære region, medmindre det primære område bliver er utilgængelig. På dette tidspunkt skifter den automatisk til den sekundære område. Se [konfigurere Failover routing metode][tm-configure-failover].

- **Sundhed efterprøvning af af.** Trafik Manager bruger HTTP (eller HTTPS) [efterprøvning af af] [ tm-monitoring] til at overvåge tilgængeligheden af hvert område. Efterprøvning af af kontrollerer, om en HTTP 200 svar til en bestemt URL-sti. Oprette et slutpunkt, som oplyser den overordnede status for programmet, og bruge dette slutpunkt for systemtilstand efterprøvning af af som en bedste fremgangsmåde. Ellers kan efterprøvning af af rapportere en "sund" slutpunkt, når vigtige dele af programmet faktisk ned. Yderligere oplysninger finder du ser [Sundhed slutpunkt overvågning mønster][health-endpoint-monitoring-pattern].   

Når trafik Manager ikke, er der et stykke tid, når klienter ikke kan nå det program, som kan være flere minutter. To faktorer påvirker den samlede varighed:

- Sundhed efterprøvning af af skal registrerer, at det primære datacenter er blevet er utilgængelig.

- DNS-servere skal opdatere cachelagrede DNS-posterne for IP-adresse, som afhænger af den DNS-time to live (TTL). Standard TTL er 300 sekunder (5 minutter), men du kan konfigurere denne værdi, når du opretter profilen trafik Manager.

Se Få mere at vide [Om trafik Manager overvåge][tm-monitoring]. 

Hvis trafik Manager skifter, anbefaler vi udføre en manuel failback i stedet for automatisk ned tilbage. Kontrollér, at alle programmet delsystemer er sund først. Ellers kan du oprette en situation, hvor programmet spejlvendes frem og tilbage mellem datacentre.

Som standard mislykkes trafik Manager automatisk tilbage. For at undgå dette, skal du manuelt Skru prioriteten af det primære område efter hændelsen failover. Antag f.eks, at det primære område er prioritet 1, og sekundært er prioritet 2. Angiv det primære område til prioritet 3, for at forhindre automatiske failback efter en failover. Når du er klar til at skifte tilbage, skal du opdatere prioriteten til 1.

Kommandoen følgende Azure CLI opdaterer prioriteten:

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --priority 3
```    

En anden måde at undgå bistabilt kredsløb er midlertidigt deaktivere slutpunktet:

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --status Disabled
```

Afhængigt af årsagen en ydelse, skal du muligvis redploy ressourcerne inden for et område. Udføre en klar test før ned tilbage. Testen skal kontrollere ting som:

- FOS er konfigureret korrekt. (Alle nødvendige software er installeret, IIS er kører, osv.)

- Programmet undersystemer er sund. 

- Funktionelle test. (For eksempel databaseniveau er nås fra web niveau).

### <a name="sql-server-always-on-configuration"></a>Konfiguration af SQL Server altid på

SQL Server altid på tilgængelighed grupper kræver et domænenavn fra domænecontrolleren. Alle noder i gruppen tilgængelighed skal være i samme AD domæne. Følgende punkter giver vejledning om hvordan du konfigurerer en SQL Server altid på tilgængelighed gruppe:

- Som minimum skal du placere to domænecontrollere i hvert område. 

- Give hvert domænenavn fra domænecontrolleren en statisk IP-adresse.

- Oprette en VNet-VNet forbindelse for at aktivere kommunikationen mellem VNets.

- Tilføj domæne enheder (fra begge områder) til listen DNS server IP-adresser for hver VNet. Du kan bruge følgende kommando for CLI. Yderligere oplysninger, se [administrere DNS-servere, der bruges af et virtuelt netværk (VNet)][vnet-dns].

```bat
azure network vnet set --resource-group dc01-rg --name dc01-vnet --dns-servers "10.0.0.4,10.0.0.6,172.16.0.4,172.16.0.6"
```

- Oprette en [Windows Server-Failoverklyngedannelse] [ wsfc] (WSFC) klynge, som indeholder forekomsterne af SQL Server i begge områder. 

- Oprette en SQL Server altid på tilgængelighed gruppe, som indeholder forekomsterne af SQL Server i begge de primære og sekundære områder. Se [Forlænge altid på tilgængelighed gruppe til Remote Azure Datacenter (PowerShell)](https://blogs.msdn.microsoft.com/sqlcat/2014/09/22/extending-alwayson-availability-group-to-remote-azure-datacenter-powershell/) for at se trinnene. 

- Du kan placere den primære replika i det primære område.

- Du kan placere en eller flere sekundære replikaer i det primære område. Konfigurere disse indstillinger for at bruge synkron Anvend med automatisk failover.

- Du kan placere en eller flere sekundære replikaer i det sekundære område. Konfigurere disse indstillinger for at bruge *asynkron* Anvend for at forbedre ydeevnen. (Ellers alle SQL transaktioner nødvendigt at vente på en returkørsel via netværket til den sekundære region.) 

> [AZURE.NOTE] Asynkron Anvend replikaer understøtter ikke automatisk failover. 

Du kan finde flere oplysninger [Kører Windows FOS for en N-delt arkitektur på Azure](guidance-compute-n-tier-vm.md#SQL-AlwaysOn-Availability-Group).

## <a name="availability-considerations"></a>Overvejelser i forbindelse med tilgængelighed

Med en kompleks N-delt app, kan du ikke nødvendigt at gentage hele programmet i det sekundære område. I stedet kan du bare gentage et kritiske undersystem, der er nødvendige for at understøtte Forretningskontinuitet.

Trafik Manager er et punkt mulige fejl i systemet. Hvis den tjeneste, kan klienter kan ikke få adgang til dit program under nedetiden. Gennemse [Trafik Manager SERVICENIVEAUAFTALE][tm-sla], og afgøre, om ved hjælp af trafik Manager alene opfylder virksomhedens behov for høj tilgængelighed. Hvis ikke, kan du overveje at tilføje en anden trafik management-løsning som en failback. Hvis tjenesten Azure trafik Manager mislykkes, skal du ændre din CNAME-poster i DNS til at pege på anden trafik management-tjenesten. (Dette trin skal udføres manuelt, og dit program vil være tilgængelige, indtil DNS-ændringer er blevet overført). 

SQL Server-klynge er der to failover scenarier skal du tænke på:

1. Alle SQL replikaerne i det primære område mislykkedes. Det kan for eksempel ske under en internationale afbrydelse. Det er tilfældet, skal du manuelt mislykkes over gruppen SQL tilgængelighed, selvom trafik Manager mislykkes automatisk på front end. Følg trinnene i [udføre en tvungen manuel Failover af en SQL Server tilgængelighed gruppe](https://msdn.microsoft.com/library/ff877957.aspx), som beskriver, hvordan du udfører en tvunget failover ved hjælp af SQL Server Management Studio, Transact-SQL eller PowerShell i SQL Server 2016. 

    > [AZURE.WARNING] Med tvunget failover er der en risiko for tab af data. Når det primære område er igen er online, tage et øjebliksbillede af databasen, og brug [tablediff] til at finde forskellene.

2. Trafik Manager kan ikke det sekundære område, men den primære SQL-replika er stadig tilgængelig. For eksempel kan kan front end-niveauet mislykkes, uden at påvirke den SQL-FOS. I så fald internettrafik er distribueret til den sekundære region, og denne region kan stadig oprette forbindelse til den primære SQL-replika. Dog vises der øget ventetid, fordi SQL-forbindelserne går på tværs af områder. I dette tilfælde skal du udføre en manuel failover på følgende måde: 

    - Skifte midlertidigt en SQL-replika i det sekundære område til *synkron* bekræftelse. Dette sikrer, at der ikke er tab af data under sekundære.

    - Skifte til SQL-replikaen. 
    
    - Når du ikke tilbage til primære region, kan du gendanne indstillingen asynkron bekræftelse. 

## <a name="manageability-considerations"></a>Overvejelser i forbindelse med administration

Når du opdaterer din installation, du opdatere ét område ad gangen for at reducere risikoen for en global fejl fra en forkert konfiguration eller en fejl i programmet på computeren.

Test fleksibilitet for systemet, der fejl. Her er nogle almindelige situationer teste:

- Luk VM forekomster.

- Tryk ressourcer som CPU og hukommelse.

- Afbryde/forsinkelse netværk.

- Ned processer.

- Udløbe certifikater.

- Simulere hardware-fejl.

- Luk tjenesten DNS på domænecontrollere.

Måle gendannelse klokkeslæt, og Kontrollér de opfylder virksomhedens behov. Test kombinationer af tilstande fejl, samt.

## <a name="next-steps"></a>Næste trin

Denne serie har fokuseret på ren skyen installationer. Enterprise scenarier kræver ofte et hybrid-netværk, oprette forbindelse et lokalt netværk med en Azure virtuelt netværk. Hvis du vil lære at oprette sådan en hybrid et netværk, skal du se [implementere en Hybrid netværksarkitektur med Azure og lokale VPN][hybrid-vpn].

<!-- Links -->

[azure-sla]: https://azure.microsoft.com/support/legal/sla/
[azure-sql-db]: https://azure.microsoft.com/en-us/documentation/services/sql-database/
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[hybrid-vpn]: guidance-hybrid-network-vpn.md
[regional-pairs]: ../best-practices-availability-paired-regions.md
[resource groups]: ../azure-resource-manager/resource-group-overview.md
[resource-group-links]: ../resource-group-link-resources.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[sql-always-on]: https://msdn.microsoft.com/en-us/library/hh510230.aspx
[tablediff]: https://msdn.microsoft.com/en-us/library/ms162843.aspx
[tm-configure-failover]: ../traffic-manager/traffic-manager-configure-failover-routing-method.md
[tm-monitoring]: ../traffic-manager/traffic-manager-monitoring.md
[tm-routing]: ../traffic-manager/traffic-manager-routing-methods.md
[tm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/traffic-manager/v1_0/
[traffic-manager]: https://azure.microsoft.com/en-us/services/traffic-manager/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vnet-dns]: ../virtual-network/virtual-networks-manage-dns-in-vnet.md
[vnet-to-vnet]: ../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[wsfc]: https://msdn.microsoft.com/en-us/library/hh270278.aspx
[0]: ./media/blueprints/compute-multi-dc.png "Meget tilgængeligt netværksarkitektur til Azure N-delt programmer"