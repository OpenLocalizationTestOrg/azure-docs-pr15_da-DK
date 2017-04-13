<properties
   pageTitle="Kører Linux FOS til en N-delt arkitektur på Azure | Microsoft Azure"
   description="Sådan køres Linux FOS for en N-delt arkitektur i Microsoft Azure."
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
   ms.date="10/20/2016"
   ms.author="mwasson"/>

# <a name="running-linux-vms-for-an-n-tier-architecture-on-azure"></a>Kører Linux FOS til en N-delt arkitektur på Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]


> [AZURE.SELECTOR]
- [Kører Linux FOS til en N-delt arkitektur på Azure](guidance-compute-n-tier-vm-linux.md)
- [Kører Windows FOS til en N-delt arkitektur på Azure](guidance-compute-n-tier-vm.md)

I denne artikel beskrives nogle af de metoder til at køre Linux virtuelle maskiner (VM'er) for et program med en N-delt arkitektur. Det er baseret på artiklen [kører flere FOS på Azure][multi-vm].

> [AZURE.NOTE] Azure har to forskellige installation modeller: [Ressourcestyring] [ resource-manager-overview] og klassisk. I denne artikel bruges Ressourcestyring, som Microsoft anbefaler til nye installationer.

## <a name="architecture-diagram"></a>Arkitektur diagram

Der findes variationer af N-delt arkitekturer. Forskelle er der ikke bør for det meste noget i forbindelse med disse anbefalinger. I denne artikel antages en typisk 3 lag web-app:

- **Web niveau.** Håndterer indgående HTTP-anmodninger. Svar returneres gennem dette niveau.

- **Business niveau.** Implementerer forretningsprocesser og andre funktionelle logik til systemet.

- **Databaseniveau.** Indeholder fast datalager, ved hjælp af Apache Cassandra til høj tilgængelighed.

> Et Visio-dokument, der indeholder dette arkitektur diagram kan hentes på [Microsoft download center][visio-download]. I dette diagram er på "Beregn - flere lag (Linux) side.

![[0]][0]

- **Tilgængelighed sæt.** Oprette en [Tilgængelighed angive] [ azure-availability-sets] for hver klasse og klargøre mindst to FOS i hvert lag. Denne metode er nødvendige for at nå tilgængeligheden [SLA] [ vm-sla] for FOS.

- **Undernet.** Oprette et separat undernet for hvert niveau. Angiv adressen område og undernet masken ved hjælp af [CIDR] notation. 

- **Indlæse balancere.** Bruge en [forbindelse til internettet justering af belastning] [ load-balancer-external] til at distribuere indgående internettrafik web niveau, og en [intern justering af belastning] [ load-balancer-internal] til at distribuere netværkstrafik fra web niveau til business-niveau.

- **Jumpbox**. En _jumpbox_, også kaldet en [bastion host]er en VM på det netværk, som administratorer bruger til at oprette forbindelse til de andre FOS. Jumpbox har en NSG, der tillader ekstern trafik kun fra whitelisted offentlige IP-adresser. NSG skal tillade sikker shell (SSH) trafik.

- **Overvågning**. Overvågning af software som [Nagios], [Zabbix]eller [Icinga] kan give dig indsigt i svartid, VM oppetid og den overordnede status for dit system. Installere overvågning softwaren på en VM, der er placeret i et separat management undernet.

- **NSGs**. Bruge [netværk sikkerhedsgrupper] [ nsg] (NSGs) til at begrænse netværkstrafik inden for VNet. For eksempel i trin 3-arkitekturen vist her accepterer databaseniveau ikke trafik fra web front end, kun fra business niveau og management undernet.

- **Apache Cassandra database**. Giver høj tilgængelighed lag data ved at aktivere gentagelse og belastningsjustering.

## <a name="recommendations"></a>Anbefalinger

Azure byder på mange forskellige ressourcer og ressourcetyper, så denne referencearkitektur kan være klargjort mange forskellige måder. Vi har angivet en Azure ressourcestyring skabelon for at installere den referencearkitektur, der følger disse anbefalinger. Hvis du vælger at oprette følger disse anbefalinger i din egen referencearkitektur, medmindre du har særlige krav, en anbefaling der ikke passer.

### <a name="vnet--subnets"></a>VNet / undernet

Når du opretter VNet, kan du tildele nok adresseområde for de undernet, skal du. Angiv VNet adresse område og undernet masken ved hjælp af [CIDR] notation. Bruge en adresseområde, der falder inden for den standard [privat IP-adresse dokumentkomponenter][private-ip-space], som er 10.0.0.0/8, 172.16.0.0/12 og 192.168.0.0/16.

Vælg et adresseområde, der ikke overlapper med netværket lokalt, i tilfælde af, at du har brug at konfigurere en gateway mellem VNet og netværket lokalt senere. Når du opretter VNet, kan du ikke ændre adresseområdet.

Designe undernet med funktionalitet og sikkerhedskrav huske. Alle FOS i det samme niveau eller rolle skal flyttes til det samme undernet, som kan være sikkerhed rammen. Finde flere oplysninger om at designe VNets og undernet, [planlægge og designe Azure virtuelle netværk][plan-network].

Angive adresseområde for undernettet i CIDR Notationen for hvert undernet. For eksempel opretter '10.0.0.0/24' en række 256 IP-adresser. (FOS kan bruge disse 251; fem reserveret. Se [virtuelt netværk ofte stillede spørgsmål om][vnet faq].) Kontrollér, at adresseområder ikke overlapper på tværs af undernet.

### <a name="load-balancers"></a>Indlæse balancere

Den eksterne justering af belastning distribuerer internettrafik til web niveau. Oprette en offentlig IP-adresse for denne justering af belastning. Se [oprette en forbindelse til internettet justering af belastning][lb-external-create].

Den interne justering af belastning distribuerer netværkstrafik fra web niveau til business-niveau. Oprette en front end IP-konfiguration for at give denne justering af belastning en privat IP-adresse, og knytte den til undernettet for business-niveau. Se [få en introduktion til en intern justering af belastning][lb-internal-create].

### <a name="cassandra"></a>Cassandra

Vi anbefaler [DataStax Enterprise] [ datastax] til fremstilling brug, men disse anbefalinger, gælder for en hvilken som helst Cassandra edition. Du kan finde flere oplysninger om at køre DataStax i Azure, se [DataStax Enterprise installationsvejledning til Azure][cassandra-in-azure]. 

Du kan placere FOS for en Cassandra klynge i en tilgængelighed er angivet, til at sikre, at Cassandra replikaerne fordeles på tværs af flere fejl domæner og opgradere domæner. Du kan finde flere oplysninger om fejl domæner og opgradering domæner, skal du se [administrere tilgængeligheden af virtuelle maskiner][availability-sets-manage]. 

Konfigurere 3 fejl domæner (maksimum) per tilgængelighed sæt. 

Konfigurere 18 opgradering domæner per tilgængelighed sæt. Resultatet er det maksimale antal opgradering domæner end stadig kan fordeles jævnt over et domæner.   

Konfigurere noder i rack-aware tilstand. Knytte et domæner til rack i den `cassandra-rackdc.properties` fil.

Du behøver ikke belastningsjustering foran klyngen. Klienten opretter forbindelse direkte til en node i klyngen.

### <a name="jumpbox"></a>Jumpbox

Placer jumpbox, i den samme VNet som de andre FOS, men i et separat management undernet.

Opret en [offentlig IP-adresse] til jumpbox.

Du kan bruge en lille VM størrelse for jumpbox som Standard A1.

Konfigurere NSGs for web niveau, business niveau og database niveau undernet så administrative (SSH) trafik til passerer gennem fra management undernet.

Oprette en NSG for at sikre jumpbox, og anvende den på jumpbox undernettet. Tilføje en NSG regel, der tillader SSH forbindelser kun fra et whitelisted sæt offentlige IP-adresser.

NSG kan knyttes til undernettet eller til jumpbox NIC. Vi anbefaler i dette tilfælde vedhæfte den til NIC, så SSH trafik er kun tilladt at jumpbox, selvom du tilføje andre FOS til det samme undernet.

Tillad ikke SSH adgang fra offentlige internettet til VM'er, der kører programmet arbejdsbelastningen. I stedet skal alle SSH adgang til disse FOS kommer gennem jumpbox. En administrator logger ind i jumpbox, og derefter logfører til andre VM fra jumpbox. Jumpbox tillader SSH trafik fra internettet, men kun fra kendte, whitelisted IP-adresser.

## <a name="availability-considerations"></a>Overvejelser i forbindelse med tilgængelighed

Sætte hver niveau eller VM rolle i et separat tilgængelighed sæt. Ikke sætte FOS fra forskellige niveauer i det samme sæt tilgængelighed. 

Lag databasen oversætter har flere FOS automatisk ikke til en meget tilgængelige database. For en relationel database skal du typisk brug gentagelse og belastningsjustering til at opnå høj tilgængelighed.  

Hvis du har brug for højere tilgængelighed end [Azure SERVICENIVEAUAFTALE for FOS] [ vm-sla] leverer, gentage programmet på tværs af to områder og bruge Azure trafik Manager til failover. Yderligere oplysninger, du [Kører Linux FOS i flere områder for høj tilgængelighed][multi-dc].  

## <a name="security-considerations"></a>Overvejelser om sikkerheden

Brug NSG regler til at begrænse trafik mellem niveauer. For eksempel i trin 3-arkitekturen ovenstående kommunikerer web niveau ikke direkte med database niveauet. Du kan gennemtvinge, skal database niveauet blokere indgående trafik fra web niveau undernet.  

  1. Oprette en NSG og knytte den til databasen niveau undernet.

  2. Tilføj en regel, der afviser al indgående trafik fra VNet. (Brug den `VIRTUAL_NETWORK` mærke i reglen.) 

  3. Tilføje en regel med en højere prioritet, der tillader indgående trafik fra business niveau undernet. Denne regel tilsidesætter forrige reglen, og gør det muligt for business-niveau til at tale med databaseniveau.

  4. Tilføj en regel, der tillader indgående trafik fra inden for database niveau undernettet sig selv. Denne regel tillader kommunikation mellem FOS i databasen lag, der er nødvendige for databasereplikering og belastningsjustering.

  5. Tilføj en regel, der tillader SSH trafik fra jumpbox undernettet. Denne regel kan oprette forbindelse til databaseniveau fra jumpbox-administratorer.

  > [AZURE.NOTE] En NSG har [standardregler] [ nsg-rules] , der tillader al indgående trafik fra inden for VNet. Disse regler kan ikke slettes, men du kan tilsidesætte dem ved at oprette regler for højere prioritet.

Overvej at tilføje et virtuelt netværk-maskinen (for) til at oprette en DMZ mellem offentlige internettet og Azure virtuelt netværk. FOR er en generisk ord for et virtuelt enhed, der kan udføre opgaver i forbindelse med netværk som firewall, pakke inspektion, overvågning, brugerdefinerede routing eller en række andre operationer. Du kan finde flere oplysninger, se [implementere en DMZ mellem Azure og internettet][dmz].

## <a name="scalability-considerations"></a>Overvejelser i forbindelse med skalerbarhed

Indlæse balancere distribuere netværkstrafik til internettet og business niveauer. Skalere vandret ved at tilføje nye VM forekomster. Bemærk, at du kan skalere web og business niveauer uafhængigt af hinanden, baseret på Indlæs. Du kan reducere mulige komplikationer, skyldes er nødvendigheden at vedligeholde klienten forbindelse ved skal FOS i web-lag være i en tilstand. FOS vært forretningsfunktionerne bør også være i en tilstand.

## <a name="manageability-considerations"></a>Overvejelser i forbindelse med administration

Forenkle administration af hele systemet ved hjælp af centralt administrationsværktøjer som [Azure automatisering][azure-administration], [Microsoft Operations Management Suite][operations-management-suite], [kok][chef], eller [Puppet][puppet]. Disse værktøjer kan konsolidere diagnostic og tilstand oplysninger, der er hentet fra flere FOS til at levere en overordnet oversigt over systemet.

## <a name="solution-deployment"></a>Løsningsinstallation

En installation for en referencearkitektur, der implementerer disse anbefalinger er tilgængelig på [Github][github-folder]. Denne referencearkitektur omfatter et web trin, business niveau og et data trin.

1. Klik på knappen nedenfor.  
[! ["Installeres til Azure"] [1]][2]

2. Når linket er åbnet i portalen Azure, Angiv følgende værdier: 
  - **Ressourcegruppe** navnet allerede er defineret i parameterfilen, så vælge **Opret ny** og angive `ra-ntier-sql-network-rg` i tekstfeltet.
  - Vælg området på rullelisten **placering** .
  - Ikke redigere **Skabelon roden Uri** eller tekstfelter **Parameter roden Uri** .
  - Gennemse vilkår og betingelser og derefter klikke på afkrydsningsfeltet **jeg accepterer du vilkår og betingelser, der er anført ovenfor** .
  - Klik på knappen **Køb** .

3. Kontrollér Azure-portalen meddelelse om en meddelelse installationen er fuldført.

4. Parameter filerne indeholder et faste administrator brugernavne og adgangskoder, og det anbefales, at du straks ændrer begge på alle FOS. Klik på hver VM Azure-portalen, og klik derefter på **Nulstil adgangskode** i bladet **Support + fejlfinding** . Vælg **Nulstil adgangskode** i feltet **tilstand** rullemenuen og derefter vælge et nyt **brugernavn** og en **adgangskode**. Klik på knappen **Opdater** kan du bevare den nye brugernavn og adgangskode.

## <a name="next-steps"></a>Næste trin

For at opnå høj tilgængelighed til denne referencearkitektur, anbefaler vi [anvender for flere områder][multi-dc].

<!-- links -->

[azure-administration]: ../automation/automation-intro.md
[azure-availability-sets]: ../virtual-machines/virtual-machines-windows-manage-availability.md#configure-each-application-tier-into-separate-availability-sets
[availability-sets-manage]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[bastion host]: https://en.wikipedia.org/wiki/Bastion_host
[cassandra-consistency]: http://docs.datastax.com/en/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html
[cassandra-consistency-usage]: http://medium.com/@foundev/cassandra-how-many-nodes-are-talked-to-with-quorum-also-should-i-use-it-98074e75d7d5#.b4pb4alb2
[cassandra-in-azure]: https://docs.datastax.com/en/datastax_enterprise/4.5/datastax_enterprise/install/installAzure.html
[cassandra-replication]: http://www.planetcassandra.org/data-replication-in-nosql-databases-explained/
[CIDR]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[chef]: https://www.chef.io/solutions/azure/
[datastax]: http://www.datastax.com/products/datastax-enterprise
[dmz]: guidance-iaas-ra-secure-vnet-dmz.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier
[lb-external-create]: ../load-balancer/load-balancer-get-started-internet-portal.md
[lb-internal-create]: ../load-balancer/load-balancer-get-started-ilb-arm-portal.md
[load-balancer-external]: ../load-balancer/load-balancer-internet-overview.md
[load-balancer-internal]: ../load-balancer/load-balancer-internal-overview.md
[multi-dc]: guidance-compute-multiple-datacenters-linux.md
[multi-vm]: guidance-compute-multi-vm.md
[naming conventions]: guidance-naming-conventions.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[nsg-rules]: ../best-practices-resource-manager-security.md#network-security-groups
[operations-management-suite]: https://www.microsoft.com/en-us/server-cloud/operations-management-suite/overview.aspx
[plan-network]: ../virtual-network/virtual-network-vnet-plan-design-arm.md
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[offentlige IP-adresse]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md
[puppet]: https://puppetlabs.com/blog/managing-azure-virtual-machines-puppet
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines
[vnet faq]: ../virtual-network/virtual-networks-faq.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[Nagios]: https://www.nagios.org/
[Zabbix]: http://www.zabbix.com/
[Icinga]: http://www.icinga.org/
[0]: ./media/blueprints/compute-n-tier-linux.png "N-delt arkitektur ved hjælp af Microsoft Azure"
[1]: ./media/blueprints/deploybutton.png 
[2]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier%2Fazuredeploy.json


