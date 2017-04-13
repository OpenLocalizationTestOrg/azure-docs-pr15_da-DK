<properties
   pageTitle="Køre Windows FOS til en N-delt arkitektur | Henviser til arkitektur | Microsoft Azure"
   description="Sådan implementere en med flere lag arkitektur på Azure, udbetalende især tilgængelighed, sikkerhed, skalerbarhed og administration sikkerhed."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
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

# <a name="running-windows-vms-for-an-n-tier-architecture-on-azure"></a>Kører Windows FOS til en N-delt arkitektur på Azure

> [AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

> [AZURE.SELECTOR]
- [Kører Linux FOS til en N-delt arkitektur på Azure](guidance-compute-n-tier-vm-linux.md)
- [Kører Windows FOS til en N-delt arkitektur på Azure](guidance-compute-n-tier-vm.md)

I denne artikel beskrives nogle af de metoder til at køre Windows virtuelle maskiner (VM'er) for et program med en N-delt arkitektur. Det er baseret på artiklen [kører flere FOS på Azure][multi-vm].

> [AZURE.NOTE] Azure har to forskellige installation modeller: [Ressourcestyring] [ resource-manager-overview] og klassisk. I denne artikel bruges Ressourcestyring, som Microsoft anbefaler til nye installationer.

## <a name="architecture-diagram"></a>Arkitektur diagram

Der findes variationer af N-delt arkitekturer. Forskelle er der ikke bør for det meste noget i forbindelse med disse anbefalinger. I denne artikel antages en typisk 3 lag web-app:

- **Web niveau.** Håndterer indgående HTTP-anmodninger. Svar returneres gennem dette niveau.

- **Business niveau.** Implementerer forretningsprocesser og andre funktionelle logik til systemet.

- **Databaseniveau.** Indeholder fast datalager, ved hjælp af [SQL Server altid på tilgængelighed grupper] [ sql-alwayson] for høj tilgængelighed.

> Et Visio-dokument, der indeholder dette arkitektur diagram kan hentes på [Microsoft download center][visio-download]. I dette diagram er på "Beregn - flere lag (Windows) side.

![[0]][0]

- **Tilgængelighed sæt.** Oprette en [Tilgængelighed angive] [ azure-availability-sets] for hver klasse og klargøre mindst to FOS i hvert lag. Denne metode er nødvendige for at nå tilgængeligheden [SLA] [ vm-sla] for FOS.

- **Undernet.** Oprette et separat undernet for hvert niveau. Angiv adressen område og undernet masken ved hjælp af [CIDR] notation. 

- **Indlæse balancere.** Bruge en [forbindelse til internettet justering af belastning] [ load-balancer-external] til at distribuere indgående internettrafik web niveau, og en [intern justering af belastning] [ load-balancer-internal] til at distribuere netværkstrafik fra web niveau til business-niveau.

- **Jumpbox**. En _jumpbox_, også kaldet en [bastion host]er en VM på det netværk, som administratorer bruger til at oprette forbindelse til de andre FOS. Jumpbox har en NSG, der tillader ekstern trafik kun fra whitelisted offentlige IP-adresser. NSG skal tillade Fjernskrivebord (RDP) trafik.

- **Overvågning**. Overvågning af software som [Nagios], [Zabbix]eller [Icinga] kan give dig indsigt i svartid, VM oppetid og den overordnede status for dit system. Installere overvågning softwaren på en VM, der er placeret i et separat management undernet.

- **NSGs**. Bruge [netværk sikkerhedsgrupper] [ nsg] (NSGs) til at begrænse netværkstrafik inden for VNet. For eksempel i trin 3-arkitekturen vist her accepterer databaseniveau ikke trafik fra web front end, kun fra business niveau og management undernet.

- **SQL Server altid på tilgængelighed gruppe.** Giver høj tilgængelighed lag data ved at aktivere gentagelse og belastningsjustering.

- **Active Directory Domain Services (AD DS)-servere**. Active Directory-domænetjenester (AD DS) gemmer directory-data og administrerer kommunikation mellem brugere og domæner, herunder bruger logon processer, godkendelse og directory søgninger. En Active Directory-domænecontrolleren er en server, der kører Active Directory-Domænetjenester. Før Windows Server 2016 skal altid på tilgængelighed-grupper er tilsluttet et domæne. Dette skyldes, at tilgængelighed grupper, der er afhængige af Windows Server Failover klynge (WSFC) teknologi. Windows Server 2016 introducerer muligheden for at oprette en sekundær klynge uden Active Directory. Du kan finde flere oplysninger, se [Hvad er nyt i Failoverklyngedannelse i Windows Server 2016][wsfc-whats-new]

## <a name="recommendations"></a>Anbefalinger

Azure byder på mange forskellige ressourcer og ressourcetyper, så denne referencearkitektur kan være klargjort mange forskellige måder. Vi har angivet en Azure ressourcestyring skabelon for at installere den referencearkitektur, der følger disse anbefalinger. Hvis du vælger at oprette følger disse anbefalinger i din egen referencearkitektur, medmindre du har særlige krav, en anbefaling der ikke passer.

### <a name="vnet--subnets"></a>VNet / undernet

Når du opretter VNet, kan du tildele nok adresseområde for de undernet, skal du. Angiv VNet adresse område og undernet masken ved hjælp af [CIDR] notation. Bruge en adresseområde, der falder inden for den standard [privat IP-adresse dokumentkomponenter][private-ip-space], som er 10.0.0.0/8, 172.16.0.0/12 og 192.168.0.0/16.

Vælg et adresseområde, der ikke overlapper med netværket lokalt, i tilfælde af, at du har brug at konfigurere en gateway mellem VNet og netværket lokalt senere. Når du opretter VNet, kan du ikke ændre adresseområdet.

Designe undernet med funktionalitet og sikkerhedskrav huske. Alle FOS i det samme niveau eller rolle skal flyttes til det samme undernet, som kan være sikkerhed rammen. Finde flere oplysninger om at designe VNets og undernet, [planlægge og designe Azure virtuelle netværk][plan-network].

Angive adresseområde for undernettet i CIDR Notationen for hvert undernet. For eksempel opretter '10.0.0.0/24' en række 256 IP-adresser. (FOS kan bruge disse 251; fem reserveret. Se [virtuelt netværk ofte stillede spørgsmål om][vnet faq].) Kontrollér, at adresseområder ikke overlapper på tværs af undernet.

### <a name="network-security-groups"></a>Netværk sikkerhedsgrupper

Brug NSG regler til at begrænse trafik mellem niveauer. For eksempel i trin 3-arkitekturen ovenstående kommunikerer web niveau ikke direkte med database niveauet. Du kan gennemtvinge, skal database niveauet blokere indgående trafik fra web niveau undernet.  

  1. Oprette en NSG og knytte den til databasen niveau undernet.

  2. Tilføj en regel, der afviser al indgående trafik fra VNet. (Brug den `VIRTUAL_NETWORK` mærke i reglen.) 

  3. Tilføje en regel med en højere prioritet, der tillader indgående trafik fra business niveau undernet. Denne regel tilsidesætter forrige reglen, og gør det muligt for business-niveau til at tale med databaseniveau.

  4. Tilføj en regel, der tillader indgående trafik fra inden for database niveau undernettet sig selv. Denne regel tillader kommunikation mellem FOS i databasen lag, der er nødvendige for databasereplikering og belastningsjustering.

  5. Tilføj en regel, der tillader RDP trafik fra jumpbox undernettet. Denne regel kan oprette forbindelse til databaseniveau fra jumpbox-administratorer.

  > [AZURE.NOTE] En NSG har [standardregler] [ nsg-rules] , der tillader al indgående trafik fra inden for VNet. Disse regler kan ikke slettes, men du kan tilsidesætte dem ved at oprette regler for højere prioritet.

### <a name="load-balancers"></a>Indlæse balancere

Den eksterne justering af belastning distribuerer internettrafik til web niveau. Oprette en offentlig IP-adresse for denne justering af belastning. Se [oprette en forbindelse til internettet justering af belastning][lb-external-create].

Den interne justering af belastning distribuerer netværkstrafik fra web niveau til business-niveau. Oprette en front end IP-konfiguration for at give denne justering af belastning en privat IP-adresse, og knytte den til undernettet for business-niveau. Se [få en introduktion til en intern justering af belastning][lb-internal-create].

### <a name="sql-server-always-on-availability-groups"></a>SQL Server altid på tilgængelighed grupper

Vi anbefaler, at du [Altid på tilgængelighed-grupper] [ sql-alwayson] til SQL Server høj tilgængelighed. Altid på tilgængelighed-grupper kræver et domænenavn fra domænecontrolleren. Alle noder i gruppen tilgængelighed skal være i samme AD domæne.

Øvrige lag oprette forbindelse til databasen via en [tilgængelighed gruppe lytteren][sql-alwayson-listeners]. Lytteren gør det muligt for en SQL-klienten til at oprette forbindelse uden at kende navnet på den fysiske forekomst af SQL Server. FOS, access databasen skal være medlem af domænet. Klienten (i dette tilfælde et andet niveau) bruger DNS til at løse det lytteren virtuelle netværksnavn til IP-adresser.

Konfigurere SQL Server altid på på følgende måde:

- Oprette en Windows Server Failover klynge (WSFC) klynge og en SQL Server altid på tilgængelighed gruppe. Du kan finde flere oplysninger, se [Introduktion til altid på tilgængelighed-grupper][sql-alwayson-getting-started].

- Opret en intern justering af belastning med en statisk privat IP-adresse.

- Oprette en tilgængelighed gruppe lytteren og knytte den lytteren DNS-navn til IP-adressen på en intern justering af belastning. 

- Oprette en regel for indlæsning belastningsjusteringstjenesten for SQL Server lytte port (TCP-port 1433 som standard). Indlæse belastningsjusteringstjenesten reglen skal aktivere _flydende IP-adresse_, også kaldet direkte serveren returnere. Derved VM svare direkte på klienten, som gør det muligt for en direkte forbindelse til den primære replika.

    > [AZURE.NOTE] Når flydende IP-adresse er aktiveret, vil front end portnummeret skal være den samme som back end-portnummeret i Indlæs belastningsjusteringstjenesten reglen.

Når en SQL-klient forsøger at oprette forbindelse, dirigerer belastning anmodning om forbindelse til den replika, som er den aktuelle primære. Hvis der er en failover til en anden replika, dirigerer belastning automatisk efterfølgende anmodninger om til den nye primære replika. Du kan finde flere oplysninger, se [konfigurere justering af belastning for SQL altid på][sql-alwayson-ilb].

Under en failover er eksisterende klientforbindelser lukket. Når sekundære er fuldført, sendes nye forbindelser til den nye primære replika.

Hvis din app gør betydeligt mere læser end skriver, kan du aflaste nogle af skrivebeskyttede forespørgsler til en sekundær replika. Se [ved hjælp af en lytteren at oprette forbindelse til en skrivebeskyttet sekundær replika (skrivebeskyttet Routing)][sql-alwayson-read-only-routing].

Teste din installation ved [at tvinge en manuel failover][sql-alwayson-force-failover].

### <a name="jumpbox"></a>Jumpbox

Tillad ikke RDP adgang fra offentlige internettet til VM'er, der kører programmet arbejdsbelastningen. I stedet skal alle RDP/SSH adgang til disse FOS kommer gennem jumpbox. En administrator logger ind i jumpbox, og derefter logfører til andre VM fra jumpbox. Jumpbox tillader RDP-trafik fra internettet, men kun fra kendte, whitelisted IP-adresser.

Placer jumpbox, i den samme VNet som de andre FOS, men i et separat management undernet.

Opret en [offentlig IP-adresse] til jumpbox.

Du kan bruge en lille VM størrelse for jumpbox som Standard A1.

Konfigurere NSGs for web niveau, business niveau og database niveau undernet så administrative (RDP) trafik til passerer gennem fra management undernet.

Oprette en NSG for at sikre jumpbox, og anvende den på jumpbox undernettet. Tilføje en NSG regel, der tillader RDP forbindelser kun fra et whitelisted sæt offentlige IP-adresser.

NSG kan knyttes til undernettet eller til jumpbox NIC. Vi anbefaler i dette tilfælde vedhæfte den til NIC, så RDP trafik er kun tilladt at jumpbox, selvom du tilføje andre FOS til det samme undernet.

## <a name="availability-considerations"></a>Overvejelser i forbindelse med tilgængelighed

Sætte hver niveau eller VM rolle i et separat tilgængelighed sæt. Ikke sætte FOS fra forskellige niveauer i det samme sæt tilgængelighed. 

Lag databasen oversætter har flere FOS automatisk ikke til en meget tilgængelige database. For en relationel database skal du typisk brug gentagelse og belastningsjustering til at opnå høj tilgængelighed. For SQL Server, anbefaler vi bruger [Altid på tilgængelighed-grupper][sql-alwayson]. 

Hvis du har brug for højere tilgængelighed end [Azure SERVICENIVEAUAFTALE for FOS] [ vm-sla] leverer, gentage programmet på tværs af to områder og bruge Azure trafik Manager til failover. Yderligere oplysninger, du [Kører Windows FOS i flere områder for høj tilgængelighed][multi-dc].   

## <a name="security-considerations"></a>Overvejelser om sikkerheden

Kryptere data resten. Bruge [Azure nøgle samling] [ azure-key-vault] til at administrere tasterne database kryptering. Tasten samling kan gemme kryptering taster i hardware sikkerhed moduler (HSMs). Kan finde flere oplysninger i [Konfigurere Azure nøgle samling Integration til SQL Server på Azure FOS] [ sql-keyvault] det anbefales også for at gemme programmet hemmeligheder, som database forbindelsesstrenge i nøgle samling.

Overvej at tilføje et virtuelt netværk-maskinen (for) til at oprette en DMZ mellem offentlige internettet og Azure virtuelt netværk. FOR er en generisk ord for et virtuelt enhed, der kan udføre opgaver i forbindelse med netværk som firewall, pakke inspektion, overvågning, brugerdefinerede routing eller en række andre operationer. Du kan finde flere oplysninger, se [implementere en DMZ mellem Azure og internettet][dmz].

## <a name="scalability-considerations"></a>Overvejelser i forbindelse med skalerbarhed

Indlæse balancere distribuere netværkstrafik til internettet og business niveauer. Skalere vandret ved at tilføje nye VM forekomster. Bemærk, at du kan skalere web og business niveauer uafhængigt af hinanden, baseret på Indlæs. Du kan reducere mulige komplikationer, skyldes er nødvendigheden at vedligeholde klienten forbindelse ved skal FOS i web-lag være i en tilstand. FOS vært forretningsfunktionerne bør også være i en tilstand.

## <a name="manageability-considerations"></a>Overvejelser i forbindelse med administration

Forenkle administration af hele systemet ved hjælp af centralt administrationsværktøjer som [Azure automatisering][azure-administration], [Microsoft Operations Management Suite][operations-management-suite], [kok][chef], eller [Puppet][puppet]. Disse værktøjer kan konsolidere diagnostic og tilstand oplysninger, der er hentet fra flere FOS til at levere en overordnet oversigt over systemet.

## <a name="solution-deployment"></a>Løsningsinstallation

En installation for en referencearkitektur, der implementerer disse anbefalinger er tilgængelig på [Github][github-folder]. Denne referencearkitektur omfatter et web trin, business niveau, en data niveau samt en jumpbox VM og Active Directory domain enheder.

Arkitekturen reference kan installeres ved at følge vejledningen herunder: 

1. Klik på knappen nedenfor.  
[! ["Installeres til Azure"] [1]][2]

2. Når linket er åbnet i portalen Azure, Angiv følgende værdier: 
  - **Ressourcegruppe** navnet allerede er defineret i parameterfilen, så vælge **Opret ny** og angive `ra-ntier-sql-network-rg` i tekstfeltet.
  - Vælg området på rullelisten **placering** .
  - Ikke redigere **Skabelon roden Uri** eller tekstfelter **Parameter roden Uri** .
  - Gennemse vilkår og betingelser og derefter klikke på afkrydsningsfeltet **jeg accepterer du vilkår og betingelser, der er anført ovenfor** .
  - Klik på knappen **Køb** .

3. Kontrollér Azure-portalen meddelelse om en meddelelse installationen er fuldført.

4. Klik på knappen nedenfor.  
[! ["Installeres til Azure"] [1]][3]

5. Når linket er åbnet i portalen Azure, Angiv følgende værdier: 
  - **Ressourcegruppe** navnet allerede er defineret i parameterfilen, så vælge **Brug eksisterende** og angive `ra-ntier-sql-workload-rg` i tekstfeltet.
  - Vælg området på rullelisten **placering** .
  - Ikke redigere **Skabelon roden Uri** eller tekstfelter **Parameter roden Uri** .
  - Gennemse vilkår og betingelser og derefter klikke på afkrydsningsfeltet **jeg accepterer du vilkår og betingelser, der er anført ovenfor** .
  - Klik på knappen **Køb** .

6. Kontrollér Azure-portalen meddelelse om en meddelelse installationen er fuldført.

7. Klik på knappen nedenfor.  
[! ["Installeres til Azure"] [1]][4]

8. Når linket er åbnet i portalen Azure, Angiv følgende værdier: 
  - **Ressourcegruppe** navnet allerede er defineret i parameterfilen, så vælge **Opret ny** og angive `ra-ntier-sql-network-rg` i tekstfeltet.
  - Vælg området på rullelisten **placering** .
  - Ikke redigere **Skabelon roden Uri** eller tekstfelter **Parameter roden Uri** .
  - Gennemse vilkår og betingelser og derefter klikke på afkrydsningsfeltet **jeg accepterer du vilkår og betingelser, der er anført ovenfor** .
  - Klik på knappen **Køb** .

9. Kontrollér Azure-portalen meddelelse om en meddelelse installationen er fuldført.

10. Parameter filerne indeholder et faste administrator brugernavne og adgangskoder, og det anbefales, at du straks ændrer begge på alle FOS. Klik på hver VM Azure-portalen, og klik derefter på **Nulstil adgangskode** i bladet **Support + fejlfinding** . Vælg **Nulstil adgangskode** i feltet **tilstand** rullemenuen og derefter vælge et nyt **brugernavn** og en **adgangskode**. Klik på knappen **Opdater** kan du bevare den nye brugernavn og adgangskode. 

Finde oplysninger om andre måder at installere denne referencearkitektur, se filen med vigtige oplysninger i [vejledning-single-vm] [ github-folder] Github mappe. 

## <a name="next-steps"></a>Næste trin

For at opnå høj tilgængelighed til denne referencearkitektur, anbefaler vi [anvender for flere områder][multi-dc].

<!-- links -->

[arm-templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
[azure-administration]: ../automation/automation-intro.md
[azure-audit-logs]: ../resource-group-audit.md
[azure-availability-sets]: ../virtual-machines/virtual-machines-windows-manage-availability.md#configure-each-application-tier-into-separate-availability-sets
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-key-vault]: https://azure.microsoft.com/services/key-vault.md
[azure-load-balancer]: ../load-balancer/load-balancer-overview.md
[bastion host]: https://en.wikipedia.org/wiki/Bastion_host
[CIDR]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[chef]: https://www.chef.io/solutions/azure/
[dmz]: guidance-iaas-ra-secure-vnet-dmz.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier-sql
[lb-external-create]: ../load-balancer/load-balancer-get-started-internet-portal.md
[lb-internal-create]: ../load-balancer/load-balancer-get-started-ilb-arm-portal.md
[load-balancer-external]: ../load-balancer/load-balancer-internet-overview.md
[load-balancer-internal]: ../load-balancer/load-balancer-internal-overview.md
[multi-dc]: guidance-compute-multiple-datacenters.md
[multi-vm]: guidance-compute-multi-vm.md
[n-tier]: guidance-compute-n-tier-vm.md
[naming conventions]: guidance-naming-conventions.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[nsg-rules]: ../best-practices-resource-manager-security.md#network-security-groups
[operations-management-suite]: https://www.microsoft.com/en-us/server-cloud/operations-management-suite/overview.aspx
[plan-network]: ../virtual-network/virtual-network-vnet-plan-design-arm.md
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[offentlige IP-adresse]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md
[puppet]: https://puppetlabs.com/blog/managing-azure-virtual-machines-puppet
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[sql-alwayson]: https://msdn.microsoft.com/en-us/library/hh510230.aspx
[sql-alwayson-force-failover]: https://msdn.microsoft.com/en-us/library/ff877957.aspx
[sql-alwayson-getting-started]: https://msdn.microsoft.com/en-us/library/gg509118.aspx
[sql-alwayson-ilb]: https://blogs.msdn.microsoft.com/igorpag/2016/01/25/configure-an-ilb-listener-for-sql-server-alwayson-availability-groups-in-azure-arm/
[sql-alwayson-listeners]: https://msdn.microsoft.com/en-us/library/hh213417.aspx
[sql-alwayson-read-only-routing]: https://technet.microsoft.com/en-us/library/hh213417.aspx#ConnectToSecondary
[sql-keyvault]: ../virtual-machines/virtual-machines-windows-ps-sql-keyvault.md
[vm-planned-maintenance]: ../virtual-machines/virtual-machines-windows-planned-maintenance.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines
[vnet faq]: ../virtual-network/virtual-networks-faq.md
[wsfc-whats-new]: https://technet.microsoft.com/windows-server-docs/failover-clustering/whats-new-in-failover-clustering
[Nagios]: https://www.nagios.org/
[Zabbix]: http://www.zabbix.com/
[Icinga]: http://www.icinga.org/
[VM-sizes]: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/deploy-reference-architecture.sh
[vnet-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/virtualNetwork.parameters.json
[vnet-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/virtualNetwork.parameters.json
[nsg-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/networkSecurityGroups.parameters.json
[nsg-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/networkSecurityGroups.parameters.json
[webtier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/webTier.parameters.json
[webtier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/webTier.parameters.json
[businesstier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/businessTier.parameters.json
[businesstier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/businessTier.parameters.json
[datatier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/dataTier.parameters.json
[datatier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/dataTier.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[0]: ./media/blueprints/compute-n-tier.png "N-delt arkitektur ved hjælp af Microsoft Azure"
[1]: ./media/blueprints/deploybutton.png 
[2]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2FvirtualNetwork.azuredeploy.json
[3]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2Fworkload.azuredeploy.json
[4]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2Fsecurity.azuredeploy.json