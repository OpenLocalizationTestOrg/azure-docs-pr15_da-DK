<properties
   pageTitle="Køre Linux FOS i flere områder til høj tilgængelighed | Henviser til arkitektur | Microsoft Azure"
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

# <a name="running-linux-vms-in-multiple-regions-for-high-availability"></a>Køre Linux FOS i flere områder til høj tilgængelighed

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

> [AZURE.SELECTOR]
- [Køre Linux FOS i flere områder til høj tilgængelighed](guidance-compute-multiple-datacenters-linux.md)
- [Køre Windows FOS i flere områder til høj tilgængelighed](guidance-compute-multiple-datacenters.md)

Vi anbefaler et sæt af fremgangsmåder til at køre Linux virtuelle maskiner (VM'er) i flere Azure områder, at opnå tilgængelighed og en robust nedbrud gendannelse infrastruktur i denne artikel.

> [AZURE.NOTE] Azure har to forskellige installation modeller: [Ressourcestyring] [ resource groups] og klassisk. I denne artikel bruges Ressourcestyring, som Microsoft anbefaler til nye installationer.

En flere områder arkitektur kan give højere tilgængelighed end anvender for et enkelt område. Hvis en internationale afbrydelse påvirker det primære område, kan du bruge [Trafik Manager] [ traffic-manager] overføres til den sekundære område. Denne arkitektur kan også hjælpe, hvis et enkelt undersystem af programmet mislykkes.

Der er flere generelle fremgangsmåder til at opnå høj tilgængelighed på tværs af datacentre:   
  
- Aktiv/passiv med varmt standby. Trafikken går til et område, mens de andre venter på standby. FOS i det sekundære område er allokeret og kører til enhver tid.

- Aktiv/passiv med kolde standby. Det samme, men FOS i det sekundære område er ikke tildelt indtil, der kræves for failover. Denne metode koster mindre til at køre, men Generelt er længere nedetid under en fejl.

- Aktive. Begge områder er aktiv, og anmodninger er Indlæs ligeligt mellem dem. Hvis et datacenter er tilgængelig, hentes den af rotation.

Denne arkitektur fokuserer på aktiv/passiv med varmt standby, ved hjælp af trafik Manager for failover. Bemærk, at du kan installere et lille antal FOS for varme standby og derefter skalere ud efter behov.

## <a name="architecture-diagram"></a>Arkitektur diagram

I det følgende diagram bygger på arkitekturen vises i [tilføje pålidelighed til en N-delt arkitektur på Azure](guidance-compute-n-tier-vm-linux.md). 

> Et Visio-dokument, der indeholder dette arkitektur diagram kan hentes på [Microsoft download center][visio-download]. I dette diagram er på "Beregn - flere område (Linux) side.

![[0]][0]

- **Primære og sekundære områder**. Denne arkitektur bruger to områder til at opnå større tilgængelighed. En er det primære område. Under normale handlinger sendes netværkstrafik til det primære område. Men hvis, som er tilgængelig, dirigeres trafik til den sekundære område.

- ** [Azure trafik Manager] [ traffic-manager] ** dirigerer indgående anmodninger til det primære område. Hvis denne region bliver utilgængeligt, mislykkes trafik Manager over til den sekundære område. Yderligere oplysninger finder du i afsnittet [Konfiguration af trafik Manager](#configuring-traffic-manager).

- **Ressourcegrupper**. Oprette separate [ressourcegrupper] [ resource groups] for den primære region, det sekundære region, og til trafik Manager. Det giver dig mulighed for at administrere hvert område som en enkelt samling af ressourcer. Du kan for eksempel Geninstaller ét område, uden at foretage dig ned et andre. [Sammenkæde ressourcegruppe][resource-group-links], så du kan køre en forespørgsel for at få vist alle ressourcerne for programmet.

- **VNets**. Oprette en separat VNet for hvert område. Kontrollér, at felterne adresse ikke overlapper.

- **Apache Cassandra** implementeret i data Center på tværs af Azure områder. Cassandra datacentre er implementeret i forskellige Azure områder for høj tilgængelighed. Noder er konfigureret i rack-aware tilstand med fejl og opgradere domæner til fleksibilitet i området i hvert område.

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

### <a name="cassandra-deployment-across-multiple-regions"></a>Cassandra installation på tværs af flere områder

Cassandra datacentre: inddelinger af arbejdsmængder: en gruppe af relaterede noder, der er konfigureret sammen i en klynge for gentagelse og arbejdsbelastningen opdeling.

Vi anbefaler [DataStax Enterprise] [ datastax] fremstilling brug. Du kan finde flere oplysninger om at køre DataStax i Azure, se [DataStax Enterprise installationsvejledning til Azure][cassandra-in-azure]. Følgende generelle anbefalinger gælder for en hvilken som helst Cassandra edition.

- Tildele en offentlig IP-adresse til hver node. Dette gør det muligt for klynger til at kommunikere på tværs af områder ved hjælp af Azure grundlæggende element infrastrukturen, give høj overførselshastighed ved lav omkostninger.

- Sikker noder ved hjælp af de relevante firewall og NSG konfigurationer, at tillade trafik kun til og fra kendte hosts, herunder kunder og andre klyngenoder. Bemærk, at Cassandra bruger forskellige porte for kommunikation, OpsCenter, gnister og så videre. Port brugen i Cassandra, under [konfiguration af firewall port adgang][cassandra-ports].

- Brug SSL-kryptering for alle valgte [klient-til-node] [ ssl-client-node] og [node-til-node] [ ssl-node-node] kommunikation.

- I et område, skal du følge vejledningen i [Cassandra anbefalinger](guidance-compute-n-tier-vm-linux.md#cassandra-recommendations).

## <a name="availability-considerations"></a>Overvejelser i forbindelse med tilgængelighed

Med en kompleks N-delt app, kan du ikke nødvendigt at gentage hele programmet i det sekundære område. I stedet kan du bare gentage et kritiske undersystem, der er nødvendige for at understøtte Forretningskontinuitet.

Trafik Manager er et punkt mulige fejl i systemet. Hvis den tjeneste, kan klienter kan ikke få adgang til dit program under nedetiden. Gennemse [Trafik Manager SLA][tm-sla], og afgøre, om ved hjælp af trafik Manager alene opfylder virksomhedens behov for høj tilgængelighed. Hvis ikke, kan du overveje at tilføje en anden trafik management-løsning som en failback. Hvis tjenesten Azure trafik Manager mislykkes, skal du ændre din CNAME-poster i DNS til at pege på anden trafik management-tjenesten. (Dette trin skal udføres manuelt, og dit program vil være tilgængelige, indtil DNS-ændringer er blevet overført).

For klynge Cassandra failover scenarier skal du tænke på, der er afhængige af konsistens niveauer, der bruges af programmet, samt antallet af replikaer bruges. Se [konfiguration af dataenes konsistens] til konsistens niveauer og forbrug i Cassandra[ cassandra-consistency] og [Cassandra: hvor mange noder er talt til med Quorum?][cassandra-consistency-usage] Datatilgængelighed i Cassandra bestemmes af niveauet konsistens bruges af programmet og replikering fungerer. Se [Replikering af Data i NoSQL databaser forklaring]af replikering i Cassandra[cassandra-replication].

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
[cassandra-in-azure]: https://academy.datastax.com/resources/deployment-guide-azure
[cassandra-consistency]: http://docs.datastax.com/en/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html
[cassandra-replication]: http://www.planetcassandra.org/data-replication-in-nosql-databases-explained/
[cassandra-consistency-usage]: https://medium.com/@foundev/cassandra-how-many-nodes-are-talked-to-with-quorum-also-should-i-use-it-98074e75d7d5#.b4pb4alb2
[cassandra-ports]: http://docs.datastax.com/en/latest-dse/datastax_enterprise/sec/secConfFirePort.html
[datastax]: https://www.datastax.com/products/datastax-enterprise
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[hybrid-vpn]: guidance-hybrid-network-vpn.md
[regional-pairs]: ../best-practices-availability-paired-regions.md
[resource groups]: ../azure-resource-manager/resource-group-overview.md
[resource-group-links]: ../resource-group-link-resources.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssl-client-node]: http://docs.datastax.com/en/cassandra/2.0/cassandra/security/secureSSLClientToNode_t.html
[ssl-node-node]: http://docs.datastax.com/en/cassandra/2.0/cassandra/security/secureSSLNodeToNode_t.html
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
[0]: ./media/blueprints/compute-multi-dc-linux.png "Meget tilgængeligt netværksarkitektur til Azure N-delt programmer"
