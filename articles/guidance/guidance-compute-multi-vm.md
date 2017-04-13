<properties
   pageTitle="Kører flere FOS | Henviser til arkitektur | Microsoft Azure"
   description="Sådan køres flere forekomster af VM på Azure for skalerbarhed, fleksibilitet, administration og sikkerhed."
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
   ms.date="10/19/2016"
   ms.author="mwasson"/>

# <a name="running-multiple-vms-on-azure-for-scalability-and-availability"></a>Køre flere FOS på Azure til skalerbarhed og tilgængelighed 

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

I denne artikel beskrives nogle af de metoder til at køre flere forekomster af virtuelle maskiner (VM) for at forbedre skalerbarhed, tilgængelighed, administration og sikkerhed.   

I denne arkitektur distribueres arbejdsbelastningen på tværs af VM forekomster. Der er en enkelt offentlige IP-adresse, og internettrafik distribueres til FOS ved hjælp af en justering af belastning. Denne arkitektur kan bruges til en enkelt lag-app, som en uden status web app eller lagerplads klynge. Det er også en dokumentkomponent til N-delt programmer. 

I denne artikel bygger på [kører en enkelt VM på Azure][single vm]. Anbefalinger i denne artikel gælder også for denne arkitektur.

## <a name="architecture-diagram"></a>Arkitektur diagram

FOS i Azure kræver understøttende Netværks- og ressourcer.

> Et Visio-dokument, der indeholder dette arkitektur diagram kan hentes på [Microsoft download center][visio-download]. I dette diagram er på "Beregn - flere VM fanen." 

![[0]][0]

Arkitekturen har følgende komponenter: 

- **Tilgængelighed sæt.** [Tilgængelighed angive] [ availability set] indeholder FOS og kræves for at understøtte [tilgængelighed SERVICENIVEAUAFTALE til Azure FOS][vm-sla].

- **VNet**. Hver VM i Azure skal være installeret i et virtuelt netværk (VNet), der er yderligere inddelt i **undernet**.

- **Azure justering af belastning.** [Justering af belastning] distribuerer indgående internetanmodninger til VM forekomsterne i et sæt tilgængelighed. Justering af belastning indeholder nogle relaterede ressourcer:

  - **Offentlige IP-adresse.** En offentlig IP-adresse er nødvendig for justering af belastning modtage internettrafik.

  - **Konfiguration af front end.** Knytter den offentlige IP-adresse til justering af belastning.

  - **Adresse back end-puljen.** Indeholder netværksgrænseflader (NIC) for de VM'er, der skal modtage indgående trafik.

- **Indlæse belastningsjusteringstjenesten regler.** Bruges til at distribuere netværkstrafik mellem alle FOS i adressegruppen back end. 

- **NAT regler.** Bruges til at dirigere trafik til en bestemt VM. For eksempel for at aktivere remote desktop protocol (RDP) til FOS skal du oprette en separat netværk adresse oversættelse (NAT) regel for hver VM. 

- **Netværksgrænseflader (NIC)**. Hver VM har en NIC for at oprette forbindelse til netværket.

- **Lagerplads.** Lagerplads konti hold VM billeder og andre filer-relaterede ressourcer, som VM diagnosticering data fået, ved Azure.

## <a name="recommendations"></a>Anbefalinger

Azure byder på mange forskellige ressourcer og ressourcetyper, så denne referencearkitektur kan være klargjort mange forskellige måder. Vi har angivet en Azure ressourcestyring skabelon for at installere den referencearkitektur, der følger efter de anbefalinger, der er beskrevet nedenfor. Hvis du vælger at oprette din egen referencearkitektur skal du følge disse anbefalinger, medmindre du har særlige krav, der ikke understøtter en anbefaling. 

### <a name="availability-set-recommendations"></a>Tilgængelighed angive anbefalinger

Du skal oprette mindst to FOS i tilgængeligheden indstillet til at understøtte [tilgængelighed SERVICENIVEAUAFTALE til Azure FOS][vm-sla]. Bemærk, at Azure justering af belastning også kræver, at indlæse afstemmes FOS hører til det samme sæt tilgængelighed.

### <a name="network-recommendations"></a>Netværk anbefalinger

FOS bag justering af belastning skal alle være placeret i det samme undernet. Viser ikke FOS direkte til internettet, men i stedet for at give hver VM en privat IP-adresse. Klienter opretter forbindelse ved hjælp af den offentlige IP-adressen på justering af belastning.

### <a name="load-balancer-recommendations"></a>Indlæse belastningsjusteringstjenesten anbefalinger

Tilføj alle FOS i tilgængeligheden indstillet til back end-adresse puljen af belastning.

Definer regler for indlæsning af belastningsjusteringstjenesten direkte netværkstrafik til FOS. For eksempel for at aktivere HTTP-trafik skal oprette en regel, der knytter port 80 fra front end-konfigurationen til port 80 på adressen back end-puljen. Når justering af belastning modtager en anmodning på port 80 af den offentlige IP-adresse, distribuerer anmodningen til port 80 på en af netværkskortene i adressegruppen back end.

Angive NAT regler til at dirigere trafik til en bestemt VM. For at aktivere oprette RDP til FOS for eksempel en separat NAT regel for hver VM. Hver regel skal knytte et entydigt portnummer til port 3389, standardporten for RDP. (For eksempel bruger port 50001 til "VM1," port 50002 til "VM2", og så videre). Tildele NAT-regler til netværkskort på FOS. 

### <a name="storage-account-recommendations"></a>Lagerplads konto anbefalinger

Oprette separate Azure lagerplads konti til hver VM til at indeholde de virtuelle harddiske (virtuelle harddiske) for at undgå nå input/output-handlinger i anden [(IOP'ER) begrænsninger] [ vm-disk-limits] for lagerplads konti. 

Oprette et lager konto for at logge til diagnosticering. Denne konto lagerplads kan deles af alle FOS.

## <a name="scalability-considerations"></a>Overvejelser i forbindelse med skalerbarhed

Der er to muligheder for skalering af FOS i Azure: 

- Bruge en belastningsjustering til at distribuere netværkstrafik på tværs af et sæt af FOS. Hvis du vil skalere ud, klargøre yderligere FOS og lægge dem bag justering af belastning. 

- Bruge [virtuelt skala sæt][vmss]. Et skala sæt indeholder et speficied antal identiske FOS bag en justering af belastning. VM skala angiver support Autoskalering baseret på ydeevne målepunkter. Yderligere FOS føjes automatisk til justering af belastning, som belastningen på FOS øges. 

I næste afsnit sammenligne disse to indstillinger.

### <a name="load-balancer-without-vm-scale-sets"></a>Justering af belastning uden VM skala sæt

Belastningsjustering tager indgående netværksanmodninger og distribuerer dem på tværs af netværkskortene i adressegruppen back end. Skalere vandret, føje flere forekomster af VM til sættet tilgængelighed (eller deallokere FOS at indskrænke). 

Lad os antage, at du kører en webserver. Du skal tilføje en Indlæs belastningsjusteringstjenesten regel for port 80 og/eller port 443 (for SSL). Når en klient sender en HTTP-anmodning, justering af belastning vælger en back end-IP-adresse, ved hjælp af en [krypteres algoritme] [ load balancer hashing] , der indeholder den kilde-IP-adresse. På denne måde skal distribueres forespørgsler fra klienter på tværs af alle FOS. 

> [AZURE.TIP] Når du tilføjer en ny VM til en tilgængelig Angiv, Sørg for at oprette en NIC for VM og tilføje NIC til puljen back end-adressen på justering af belastning. Ellers sendes internettrafik ikke til den nye VM.

Hver Azure abonnement har standard begrænsninger på plads, herunder et maksimalt antal FOS per område. Du kan øge grænsen ved at arkivere en supportanmodning. Du kan finde flere oplysninger, se [Azure-abonnement og grænser, kvoter, og begrænsninger][subscription-limits].  

### <a name="vm-scale-sets"></a>VM skala sæt 

VM skala sæt hjælpe dig med at installere og administrere et sæt af identiske FOS. Med alle VM skala sæt understøtter SAND Autoskalering uden allerede klargøring FOS, gør det nemmere at oprette store services målretning af stor Beregn, big data og bulkvareenhed arbejdsbelastninger, FOS konfigureret på samme måde. 

Du kan finde flere oplysninger om VM skala sæt se [Virtuelt skala angiver oversigt over][vmss].

Overvejelser ved brug af VM skalere sæt:

- Overvej skala sæt, hvis du vil hurtigt FOS kan skalere, eller skal Autoskalering. 

- Skala sæt understøtter i øjeblikket ikke datadisce. Indstillingerne for lagring af data er Azure fillagring, OS drev, Temp-drev eller en ekstern store, såsom Azure-lager. 

- Alle forekomster af VM inden for en skala, som angives automatisk hører til det samme tilgængelighed sæt med 5 fejl domæner og 5 opdatering domæner.

- Skala sæt bruges der som standard "overprovisioning", hvilket betyder, at sættet skala indledningsvis danner flere FOS, end du beder om og derefter sletter de ekstra FOS. Dette forbedrer den overordnede succes rente, når FOS. 

- Vi anbefaler, at du ikke længere derefter end 20 FOS per lagerplads konto med overprovisioning aktiveret eller ikke mere end 40 FOS med overprovisioning deaktiveret.  

- Du kan finde ressourcestyring skabeloner til implementering skala angiver i [Azure Hurtig start skabeloner][vmss-quickstart].

- Der er to grundlæggende måder at konfigurere FOS implementeret i et sæt skala: oprette et brugerdefineret billede, eller brug udvidelser til at konfigurere VM, når den er klargjort.

    - Et skala sæt baseret på et brugerdefineret billede skal oprette alle OS disken virtuelle harddiske i én lagerplads konto. 

    - Med brugerdefinerede billeder skal du holde billedet opdateret.

    - Med filtypenavne, kan det tage længere tid til en nyligt klargjort VM at dreje op.

Se [Designe VM skala sæt For skala]til yderligere overvejelser[vmss-design].

> [AZURE.TIP]  Når du bruger en hvilken som helst automatisk skalering løsning, test den med fremstilling niveau arbejde indlæse godt på forhånd. 

## <a name="availability-considerations"></a>Overvejelser i forbindelse med tilgængelighed

Tilgængeligheden sæt gør din app mere robust både planlagte og ikke-planlagt vedligeholdelse begivenheder.

- _Planlagt vedligeholdelse_ opstår, når Microsoft opdaterer den underliggende platform nogle gange forårsager FOS skal genstartes. Azure gør sikker FOS i et sæt tilgængelighed er ikke alle genstartes på samme tid, mindst én der bevares kører, mens andre genstarter.

- _Ikke-planlagt vedligeholdelse_ sker der, hvis der er en hardwarefejl. Azure sikrer, at, der er klargjort FOS i et sæt tilgængelighed på tværs af mere end én server rack. Dette hjælper med at reducere virkningerne af mislykkede forsøg på hardware, netværk udfald, power afbrydelser og så videre.

Få mere at vide under [administrere tilgængeligheden af virtuelle maskiner][availability set]. Den følgende video har også en god oversigt over tilgængeligheden af sæt: [Hvordan kan jeg konfigurere en tilgængelighed, der er angivet til skala FOS][availability set ch9]. 

> [AZURE.WARNING]  Sørg for at konfigurere tilgængeligheden angive, når du klargør VM. Der findes i øjeblikket ingen måde at føje en ressourcestyring VM til en tilgængelighed, angives, efter VM er klargjort.

Justering af belastning bruger [sundhed sonder] til at overvåge tilgængeligheden af VM forekomster. Hvis en efterprøvning af af ikke kan få adgang til en forekomst senest timeout, holder justering af belastning sende trafik til VM. Dog belastning fortsætter med at sende forespørgsler, og hvis VM bliver tilgængelig igen, belastning genoptages sende trafik til VM.

Her er nogle anbefalinger på Indlæs belastningsjusteringstjenesten sundhed sonder:

- Sonder kan du teste HTTP eller TCP. Hvis FOS kører en HTTP-server (IIS, nginx, Node.js app og osv.) til at oprette en HTTP efterprøvning af af. Ellers skal du oprette en TCP efterprøvning af af.

- Angiv stien til HTTP slutpunktet for en HTTP efterprøvning af af. Efterprøvning af af kontrollerer, om en HTTP 200 svar fra denne sti. Det kan være rodsti ("/") eller en tilstand-overvågning slutpunkt, der implementerer nogle brugerdefineret logik for at kontrollere tilstanden af programmet. Slutpunktet skal tillade anonyme HTTP-anmodninger.

- Efterprøvning af af der sendes fra en [kendt] [ health-probe-ip] IP-adresse, 168.63.129.16. Kontrollér, at du ikke blokerer trafik til eller fra denne IP-adresse i en hvilken som helst firewall politikker eller netværk sikkerhed gruppe (NSG) regler.

- Bruge [sundhed efterprøvning af af logfiler] [ health probe log] til at få vist status for systemtilstand sonder. Aktivere logføring i portalen Azure for hver justering af belastning. Logfiler skrives til Azure Blob-lager. Loggene viser hvor mange FOS på back end ikke modtager netværkstrafik på grund af mislykkede efterprøvning af af svar.

## <a name="manageability-considerations"></a>Overvejelser i forbindelse med administration

Med flere FOS bliver det vigtigt at automatisere processer, så de er pålidelig og kan gentages. Du kan bruge [Azure automatisering] [ azure-automation] til at automatisere installation, OS rettelse og andre opgaver. Azure automatisering er en automation-tjeneste, der kører på Azure og er baseret på Windows PowerShell. Eksempel på automatiseringsscripts er tilgængelige i [Galleriet Runbook] på TechNet.

## <a name="security-considerations"></a>Overvejelser om sikkerheden

Virtuelt Network er trafik isolationsniveauet rammen i Azure. FOS i en VNet ikke kan kommunikere direkte til FOS i en anden VNet. FOS i samme VNet kan kommunikere, medmindre du opretter [netværk sikkerhedsgrupper] [ nsg] (NSGs) til at begrænse trafik. Få mere at vide under [Microsofts skytjenester og netværkssikkerhed][network-security].

For indgående internettrafik definere regler for indlæsning af belastningsjusteringstjenesten hvilken trafik kan få fat i back-end. Regler for indlæsning af belastningsjusteringstjenesten understøtter dog ikke IP-whitelisting, så hvis du vil whitelist visse offentlige IP-adresser, tilføje en NSG til undernettet.

## <a name="solution-deployment"></a>Løsningsinstallation

En installation for en referencearkitektur, der implementerer disse anbefalinger er tilgængelig på GitHub. Denne referencearkitektur omfatter et virtuelt netværk (VNet), netværk sikkerhedsgruppe (NSG), justering af belastning og to virtuelle maskiner (VM'er).

Arkitekturen reference kan installeres enten med Windows eller Linux FOS ved at følge vejledningen herunder: 

1. Højreklik på knappen nedenfor, og vælg enten "Åbn link på en ny fane" eller "Åbn hyperlink i nyt vindue":  
[![Installere på Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-multi-vm%2Fazuredeploy.json)

2. Når linket er åbnet i portalen Azure, skal du angive værdier for nogle af indstillingerne: 
    - **Ressourcegruppe** navnet allerede er defineret i parameterfilen, så vælge **Brug eksisterende** og angive `ra-multi-vm-rg` i tekstfeltet.
    - Vælg området på rullelisten **placering** .
    - Ikke redigere **Skabelon rod Uri** eller tekstfelterne **Parameter rod Uri** .
    - Vælg den **Os Type** på rullelisten boks, **windows** eller **linux**. 
    - Gennemse vilkår og betingelser og derefter klikke på afkrydsningsfeltet **jeg accepterer du vilkår og betingelser, der er anført ovenfor** .
    - Klik på knappen **Køb** .

3. Vente på at fuldføre installationen.

4. Parameter filerne indeholde en faste administratorens brugernavn og adgangskode, og det anbefales, at du straks ændrer begge. Klik på den VM med navnet `ra-multi-vm1` i portalen Azure. Klik derefter på **Nulstil adgangskode** i bladet **Support + fejlfinding** . Vælg **Nulstil adgangskode** i feltet **tilstand** rullemenuen og derefter vælge et nyt **brugernavn** og en **adgangskode**. Klik på knappen **Opdater** for at gemme den nye brugernavn og adgangskode. Gentag dette for VM med navnet `ra-multi-vm2`.

Finde oplysninger om andre måder at installere denne referencearkitektur, se filen med vigtige oplysninger i [vejledning-single-vm] [ github-folder] GitHub mappe. 

## <a name="next-steps"></a>Næste trin

Placere flere FOS bag en belastningsjustering er en dokumentkomponent til oprettelse af med flere lag arkitekturer. Yderligere oplysninger, du [Kører Windows FOS for en N-delt arkitektur på Azure] [ n-tier-windows] og [Kører Linux FOS for en N-delt arkitektur på Azure][n-tier-linux]

<!-- Links -->
[availability set]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[availability set ch9]: https://channel9.msdn.com/Series/Microsoft-Azure-Fundamentals-Virtual-Machines/08
[azure-automation]: https://azure.microsoft.com/en-us/documentation/services/automation/
[azure-cli]: ../virtual-machines-command-line-tools.md
[bastion host]: https://en.wikipedia.org/wiki/Bastion_host
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-multi-vm
[health probe log]: ../load-balancer/load-balancer-monitor-log.md
[sundhed sonder]: ../load-balancer/load-balancer-overview.md#service-monitoring
[health-probe-ip]: ../virtual-network/virtual-networks-nsg.md#special-rules
[justering af belastning]: ../load-balancer/load-balancer-get-started-internet-arm-cli.md
[load balancer hashing]: ../load-balancer/load-balancer-overview.md#hash-based-distribution
[n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[n-tier-windows]: guidance-compute-n-tier-vm.md
[naming conventions]: guidance-naming-conventions.md
[network-security]: ../best-practices-network-security.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md 
[Runbook galleri]: ../automation/automation-runbook-gallery.md#runbooks-in-runbook-gallery
[single vm]: guidance-compute-single-vm.md
[subscription-limits]: ../azure-subscription-service-limits.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_2/
[vmss]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md
[vmss-design]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md
[vmss-quickstart]: https://azure.microsoft.com/documentation/templates/?term=scale+set
[VM-sizes]: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
[0]: ./media/blueprints/compute-multi-vm.png "Arkitekturen i en multi-VM løsning på Azure omfatter en tilgængelighed, der er konfigureret med to FOS og belastningsjustering"
