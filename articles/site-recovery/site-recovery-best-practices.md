<properties
    pageTitle="Forberede gendannelse af websteder installation | Microsoft Azure"
    description="I denne artikel beskrives, hvordan du gør klar til at udrulle replikering med Azure gendannelse af websteder."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="prepare-for-azure-site-recovery-deployment"></a>Forberede installationen af Azure gendannelse af websteder

Læs denne artikel for at få et højt niveau oversigt over installation kravene til hver gentagelse scenarier, der understøttes af tjenesten Azure gendannelse af websteder. Når du har læst de generelle krav for hvert scenario, kan du sammenkæde med specifikke installationsoplysninger til hver installation.

Når du har læst indlæg i denne artikel, ingen kommentarer eller spørgsmål nederst i denne artikel eller på [Azure gendannelse Services-forummet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Oversigt

Virksomheder har brug for en strategi for BCDR, der bestemmer, hvordan apps, arbejdsmængder og data holde køre og være tilgængelige under planlagte og ikke-planlagt nedetid, og gendanne til normal arbejdsbetingelser så tidligt som muligt. Din strategi for BCDR skal beholde forretningsdata sikre og kan gendannes, og sikre, at arbejdsbelastninger forbliver konstant er tilgængelig, når der udsendes nedbrud. 

Gendannelse af websteder er en Azure-tjeneste, der bidrager til din strategi for BCDR ved orchestrating replikering af lokale fysiske servere og virtuelle maskiner til skyen (Azure) eller til en sekundær datacenter. Hvis der opstår afbrydelser i din primære placering, du ikke sekundær placeringen for at holde apps og arbejdsbelastninger, som er tilgængelige. Du ikke tilbage til din primære placering, når det vender tilbage til normal handlinger. Se mere i [Hvad er gendannelse af websteder?](site-recovery-overview.md)

## <a name="select-your-deployment-model"></a>Vælg din implementeringsmodel

Azure har to forskellige [installation modeller](../resource-manager-deployment-model.md) til oprettelse og arbejde med ressourcer: Azure ressourcestyring modellen og klassisk services management modellen. Azure har også to portaler – [Azure klassisk portal](https://manage.windowsazure.com/) , der understøtter den klassiske implementeringsmodel og [Azure-portalen](https://ms.portal.azure.com/) med understøttelse af begge installation-modeller.

Gendannelse af websteder er tilgængelig i både klassisk portalen og Azure portalen. Du kan understøtte gendannelse af websteder med klassisk services management modellen i portalen Azure klassisk. Du kan understøtte klassisk modellen eller Ressourcestyring installationer på portalen Azure. [Du finder flere oplysninger](site-recovery-overview.md#site-recovery-in-the-azure-portal) om installation af ved hjælp af Azure portal.

Når du vælger en installation model note, som:

- Vi anbefaler, at du bruger [Azure portal](https://portal.azure.com) og ressourcestyring model, hvor det er muligt.
- Gendannelse af websteder giver en nemmere og mere intuitiv Introduktion oplevelse i portalen Azure.
- Ved hjælp af portalen Azure, kan du gentage maskiner både klassisk og ressourcestyring lageret i Azure. Desuden kan du bruge LRS eller GRS lagerplads konti i portalen Azure.
- Portalen Azure kombinerer tjenesterne sikkerhedskopiering og gendannelse af websteder på en enkelt gendannelse Services samling, så du kan konfigurere og administrere BCDR tjenester fra ét sted.
- Brugere med Azure abonnementer klargjort med programmet skyen løsning CSP'EN (Provider) kan nu administrere gendannelse af websteder handlinger i portalen Azure.
- Replikering VMware FOS eller fysisk maskiner til Azure i portalen Azure indeholder en række nye funktioner, herunder understøttelse af premium lager og muligheden for at udelukke bestemte diske fra gentagelse.


## <a name="select-your-deployment-scenario"></a>Vælg scenariet installation

**Installation** | **Detaljer** | **Azure-portalen** | **Klassisk portal** | **PowerShell**
---|---|---|---|---
**VMware FOS til Azure** | Gentage VMware FOS til Azure-lager | I Azure kan portal FOS ikke classic eller Ressourcestyring lagerplads<br/><br/> Installation i [Azure-portalen](site-recovery-vmware-to-azure.md) indeholder en strømlinet installation oplevelse og et antal funktion fordele. | Du kan installere med [udvidet model](site-recovery-vmware-to-azure-classic.md) og skifte til klassisk Azure lagerplads på portalen Azure klassisk.<br/><br/> Der er også en ældre model, der ikke bør bruges til nye installationer. |  PowerShell understøttes ikke i øjeblikket.
**Hyper-V FOS til Azure** | Hyper-V FOS til Azure-lager. FOS kan være på Hyper-V hosts administreres i System Center VMM skyer eller uden VMM. | I Azure kan portal FOS ikke classic eller Ressourcestyring lagerplads.<br/><br/> Azure-portalen indeholder en strømlinet installation oplevelse. Du kan [Få mere at vide](site-recovery-vmm-to-azure.md) om replikering Hyper-V FOS i VMM skyer. Du kan [Få mere at vide](site-recovery-hyper-v-site-to-azure.md) om replikering Hyper-V FOS (uden VMM).| På portalen klassisk Azure kan du ikke FOS til klassisk Azure-lager | PowerShell installation understøttes.
**Fysiske servere til Azure** | Gentage fysiske Windows/Linux-servere til Azure-lager | I Azure kan portalen servere ikke classic eller Ressourcestyring lagerplads.<br/><br/> Installation i [Azure-portalen](site-recovery-vmware-to-azure.md) indeholder en strømlinet installation oplevelse og et antal funktion fordele. | Du kan installere med [udvidet model](site-recovery-vmware-to-azure-classic.md) og skifte til klassisk Azure lagerplads på portalen Azure klassisk.<br/><br/> Der er også en ældre model, der ikke bør bruges til nye installationer. | PowerShell installation understøttes ikke i øjeblikket.
**VMware FOS/fysiske servere til sekundær websted* | Gentage VMware FOS eller fysiske Windows/Linux-servere til en sekundær websted. [Få mere at vide og hente](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) brugervejledningen InMage spejder. | Ikke tilgængelig i portalen Azure | På portalen klassisk skal du hente InMage spejder fra en samling af legitimationsoplysninger gendannelse af websteder. | PowerShell installation understøttes ikke i øjeblikket
**Hyper-V FOS til en sekundær websted** | Gentage Hyper-V FOS i VMM skyer en sekundær skyen | Du kan gentage Hyper-V FOS i VMM skyer en sekundær websted ved hjælp af Hyper-V replika kun i [Azure-portalen](site-recovery-vmm-to-vmm.md) . SAN understøttes ikke i øjeblikket. | Du kan gentage Hyper-V FOS i VMM skyer i portalen Azure klassisk en sekundær websted ved hjælp af [Hyper-V replika](site-recovery-vmm-to-vmm-classic.md) eller [SAN gentagelse](site-recovery-vmm-san.md) | PowerShell installation understøttes



## <a name="check-what-you-need-for-deployment"></a>Se, hvad du har brug for til installation

### <a name="replicate-to-azure"></a>Replikeres til Azure

**Krav** | **Detaljer** 
---|---
**Azure-konto** | Du skal bruge en [Microsoft Azure](http://azure.microsoft.com/) -konto.<br/><br/> Du kan starte med en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/). Du kan [Få mere at vide](https://azure.microsoft.com/pricing/details/site-recovery/) om gendannelse af websteder priser.
**Azure-lager** | Replikerede data er gemt i Azure-lager og Azure FOS oprettes, når der opstår failover. Hvis du vil gentage til Azure, skal du en [Azure-lager-konto](../storage/storage-introduction.md).<br/><br/>Hvis du implementerer gendannelse af websteder i portalen klassisk skal du en eller flere [almindelige GRS lagerplads konti](..storage/storage-redundancy.md#geo-redundant-storage).<br/><br/> Hvis du implementerer i portalen Azure kan du bruge GRS eller LRS lagerplads.<br/><br/> Hvis du replikering VMware FOS eller fysisk servere i Azure portalen premium opbevaring understøttes. Bemærk, at hvis du bruger en premium lagerplads konto skal du også kontakte en standard-lager-konto for at gemme logfiler over gentagelse, hente igangværende ændringer til lokale data. [Premium lagerplads](../storage/storage-premium-storage.md) bruges typisk til virtuelle maskiner, der har brug for en ensartet måde høj EY ydeevne og lav ventetid til host EY intensivt arbejdsbelastninger.<br/><br/> Hvis du vil bruge en premium-konto til at gemme replikerede data, skal du også kontakte en standard-lager-konto for at gemme logfiler over gentagelse, hente igangværende ændringer til lokale data.
**Azure-netværk** | Hvis du vil gentage til Azure, skal du bruge et Azure netværk, der Azure FOS opretter forbindelse til, når de er oprettet efter failover.<br/><br/> Hvis du implementerer i portalen klassisk skal du bruge et klassisk netværk. Hvis du anvender i portalen Azure, kan du bruge en classic eller Ressourcestyring netværk.<br/><br/> Netværket skal være i samme område som samling af legitimationsoplysninger.
**Kort over netværk (VMM til Azure)** | Hvis du replikering fra VMM til Azure, sikrer [kort over netværk](site-recovery-network-mapping.md) , Azure FOS har forbindelse til korrekte netværk efter failover.<br/><br/> Hvis du vil konfigurere netværk tilknytning skal du konfigurere VM netværk i portalen VMM.
**Lokalt** | **VMware FOS**: Du skal bruge en lokal maskine kører gendannelse af websteder komponenter, VMware vSphere hosts/vCenter server og FOS, du vil gentage. [Du finder flere oplysninger](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).<br/><br/> **Fysiske servere**: Hvis du replikering fysiske servere skal du bruge en lokal maskiner, der kører gendannelse af websteder komponenter og fysiske servere, du vil gentage. [Du finder flere oplysninger](site-recovery-vmware-to-azure.md#configuration-server-prerequisites). Hvis du vil [mislykkes tilbage](site-recovery-failback-azure-to-vmware.md) efter failover til Azure skal du en VMware infrastructure gøre det.<br/><br/> **Hyper-V FOS**: Hvis du vil gentage Hyper-V FOS i VMM skyer skal du bruge en VMM server og Hyper-V hosts hvilke FOS du vil beskytte er placeret. [Du finder flere oplysninger](site-recovery-vmm-to-azure.md#on-premises-prerequisites).<br/><br/> Hvis du vil gentage Hyper-V FOS uden VMM skal du Hyper-V hosts, hvor FOS er placeret. [Du finder flere oplysninger](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites).
**Beskyttet computere** | Beskyttet maskiner, der replikeres til Azure skal overholde [Azure forudsætninger](#azure-virtual-machine-requirements) , der er beskrevet nedenfor.


### <a name="replicate-to-a-secondary-site"></a>Gentage til en sekundær websted

**Krav** | **Detaljer** 
---|---
**Azure-konto** | Du skal bruge en [Microsoft Azure](http://azure.microsoft.com/) -konto.<br/><br/> Du kan starte med en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/). Du kan [Få mere at vide](https://azure.microsoft.com/pricing/details/site-recovery/) om gendannelse af websteder priser.
**Lokalt** | **VMware FOS**: I det primære websted skal du en process-server til cachelagring, komprimering og kryptering af replikeringsdata før du sender den til det websted, sekundær. I det sekundære websted skal du installere en server configuration for at administrere og overvåge installationen og en master destinationsadresse-server. Vi anbefaler også en vContinuum server for lettere at administrere. Desuden skal du en eller flere VMware vSphere værter og eventuelt en vCenter server. [Få mere at vide](site-recovery-vmware-to-vmware.md)<br/><br/> **Hyper-V FOS i VMM skyer**: skal du konfigurere VMM servere, og Hyper-V hosts, der indeholder FOS, du vil gentage. [Du finder flere oplysninger](site-recovery-vmm-to-vmm.md#on-premises-prerequisites).
**Kort over netværk (VMM til VMM)** | Hvis du replikering fra VMM VMM, sikrer [kort over netværk](site-recovery-network-mapping.md) , replika FOS er forbundet til relevante netværk efter failover og placeres optimalt på Hyper-V værter. Hvis du vil konfigurere kort over netværk skal du konfigurere VM netværk på VMM serverne.
**Lagerplads tilknytning** | Hvis du replikering fra VMM til VMM kan du eventuelt konfigurere [lagerplads tilknytning](site-recovery-storage-mapping.md) til at angive lagerplads destinationen for replikerede FOS. Lagerplads tilknytning kan konfigureres til både Hyper-V replika og SAN gentagelse.<br/><br/> Lagerplads tilknytning understøttes ikke i øjeblikket i portalen Azure.


## <a name="verify-url-access"></a>Bekræfte URL-adresse til access

Kontrollér, at disse URL-adresser er tilgængelige fra servere.

**URL-ADRESSE** | **VMM til VMM** | **VMM til Azure** | **Hyper-V til Azure** | **VMware til Azure**
---|---|---|---|---
*. accesscontrol.windows.net | Tillad | Tillad | Tillad | Tillad
*. backup.windowsazure.com | Ikke påkrævet | Tillad | Tillad | Tillad
*. hypervrecoverymanager.windowsazure.com | Tillad | Tillad | Tillad | Tillad
*. store.core.windows.net | Tillad | Tillad | Tillad | Tillad
*. blob.core.windows.net | Ikke påkrævet | Tillad | Tillad | Tillad
https://www.msftncsi.com/ncsi.txt | Tillad | Tillad | Tillad | Tillad
https://dev.MySQL.com/Get/Archives/MySQL-5.5/MySQL-5.5.37-Win32.msi | Ikke påkrævet | Ikke påkrævet | Ikke påkrævet | Tillad

## <a name="azure-virtual-machine-requirements"></a>Azure virtuelt krav

Du kan installere gendannelse af websteder for at gengive virtuelle maskiner og fysiske servere, der kører operativsystemer understøttes af Azure. Dette omfatter de fleste versioner af Windows og Linux. Skal du kontrollere, der er i overensstemmelse med Azure krav virtuelle maskiner, som du vil beskytte lokalt.


**Funktion** | **Krav** | **Detaljer**
---|---|---
Hyper-V host | Skal køre Windows Server 2012 R2 | Forudsætninger kontrol mislykkes, hvis operativsystem ikke understøttes
VMware hypervisor  | Understøttet operativsystem | [Kontrollere systemkrav](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
Gæst operativsystem | Hyper-V på Azure replikering: gendannelse af websteder understøtter alle operativsystemer, der [understøttes af Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Til VMware og fysisk server-replikering: kontrollere Windows og Linux [forudsætninger](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | Forudsætninger Kontrollér mislykkes, hvis der ikke understøttes.
Gæst operativsystem arkitektur | 64-bit | Forudsætninger kontrol mislykkes, hvis der ikke understøttes
Operativsystem diskstørrelse |  Op til 1023 GB | Forudsætninger kontrol mislykkes, hvis der ikke understøttes
Operativsystem disk Tæl | 1 | Forudsætninger Kontrollér mislykkes, hvis der ikke understøttes.
Data disk Tæl | 16 eller mindre (maksimale værdi er en funktion af størrelsen på den virtuelle maskine, der oprettes. 16 = XL) | Forudsætninger kontrol mislykkes, hvis der ikke understøttes
Virtuelle datastørrelse disk | Op til 1023 GB | Forudsætninger kontrol mislykkes, hvis der ikke understøttes
Netværkskort | Flere kort understøttes |
Statiske IP-adresse | Understøttes | Du kan angive den statiske IP-adresse til den virtuelle maskine, der oprettes i Azure, hvis den primære virtuelle maskine bruger en statisk IP-adresse. Bemærk, at statiske IP-adresse til en linux virtuel maskine, der kører på Hyper-v ikke understøttes.
iSCSI-disk | Ikke understøttet | Forudsætninger kontrol mislykkes, hvis der ikke understøttes
Delte Virtuelle | Ikke understøttet | Forudsætninger kontrol mislykkes, hvis der ikke understøttes
FC disk | Ikke understøttet | Forudsætninger kontrol mislykkes, hvis der ikke understøttes
Harddisk format| VIRTUEL HARDDISK <br/><br/> VHDX | Selvom VHDX ikke understøttes i øjeblikket i Azure, konverterer gendannelse af websteder automatisk VHDX til Virtuelle, når du ikke Azure. Når du ikke tilbage til lokale virtuelle maskiner fortsætte med at bruge formatet VHDX.
BitLocker | Ikke understøttet | BitLocker skal være deaktiveret inden du beskytter en virtuel maskine.
Virtuelt navn| Mellem 1 og 63 tegn. Begrænset til bogstaver, tal og bindestreger. Skal starte og afslutte med et bogstav eller et tal | Opdatere værdien i egenskaberne virtuelt i gendannelse af websteder
Virtuelt type | <p>Generering af 1</p> <p>Generering af 2 – Windows</p> |  Generering af 2 virtuelt med OS disktype grundlæggende Disk, som omfatter 1 eller 2 datamængder med en disk formateres som VHDX, som er mindre end 300GB understøttes. Linux generering af 2 virtuelle maskiner understøttes ikke. [Læs mere](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## <a name="optimizing-your-deployment"></a>Optimere din installation

Brug følgende tip til at hjælpe dig med at optimere og skalere installationen.

- **Operativsystem lydstyrken størrelse**: Når du gentage en virtuel maskine til Azure operativsystem lydstyrken skal være mindre end 1 TB. Hvis du har flere enheder end dette kan du manuelt flytte dem til en anden disk før du starter installation.
- **Data disk størrelse**: Hvis du replikeres til Azure du kan have op til 32 datadisce på en virtuel maskine, hver med maksimalt 1 TB. Du kan effektivt gentage og mislykkes over en ~ 32 TB virtuel maskine.
- **Gendannelse plan begrænsninger**: gendannelse af websteder kan skalere til tusindvis af virtuelle maskiner. Gendannelse planer er designet som en model til programmer, der skal mislykkes over sammen så vi kan begrænse antallet computere i en gendannelse plan til 50.
- **Begrænsninger for Azure service**: alle Azure abonnement leveres med et sæt af standard begrænsninger på kerner, cloud services osv. Vi anbefaler, at du kører en test failover for at validere tilgængeligheden af ressourcer i dit abonnement. Du kan ændre disse begrænsninger via Azure support.
- **Planlægning af kapacitet**: læse mere om [kapacitetsplanlægning af](site-recovery-capacity-planner.md) for gendannelse af websteder.
- **Replikering båndbredde**: Hvis du er korte på gentagelse båndbredde Bemærk, at:
    - **ExpressRoute**: gendannelse af websteder, der fungerer sammen med Azure ExpressRoute og WAN optimizers som Riverbed. [Læs mere](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) om ExpressRoute.
    - **Replikeringstrafik**: gendannelse af websteder bruger udfører en smart indledende replikering ved hjælp af kun datablokke og ikke hele Virtuelle. Kun ændringer replikeres under igangværende replikering.
    - **Netværkstrafik**: Du kan styre netværkstrafik, der bruges til replikering ved at konfigurere [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx) med en politik, der er baseret på den destination IP-adresse og port.  Desuden, hvis du replikeres til Azure gendannelse af websteder ved hjælp af Azure Backup agent kan du konfigurere (throttling) for denne agent. [Du finder flere oplysninger](https://support.microsoft.com/kb/3056159).
- **RTO**: måle gendannelse tid formålet (RTO) du kan forvente med gendannelse af websteder vi anbefaler, at du kører en test failover og Vis gendannelse af websteder job til at analysere hvor meget tid det tager for at færdiggøre handlingerne. Hvis du ikke Azure, til den bedste RTO anbefaler vi, at du automatisere alle manuelle handlinger ved at integrere med Azure automatiske og gendannelsesfiler planer.
- **Frigivne Produktionsordre**: gendannelse af websteder understøtter en nær synkron gendannelse punkt målsætning (frigivne Produktionsordre), når du replikeres til Azure. Dette forudsætter tilstrækkelig båndbredde mellem din datacenter og Azure.


##<a name="service-urls"></a>Tjenesten URL-adresser
Sørg for, at disse URL-adresser er tilgængelige fra serveren


**URL-adresser** | **VMM til VMM** | **VMM til Azure** | **Hyper-V websted til Azure** | **VMware til Azure**
---|---|---|---|---
 \*. accesscontrol.windows.net | Kræves adgang  | Kræves adgang  | Kræves adgang  | Kræves adgang
 \*. backup.windowsazure.com |  | Kræves adgang  | Kræves adgang  | Kræves adgang
 \*. hypervrecoverymanager.windowsazure.com | Kræves adgang  | Kræves adgang  | Kræves adgang  | Kræves adgang
 \*. store.core.windows.net | Kræves adgang  | Kræves adgang  | Kræves adgang  | Kræves adgang
 \*. blob.core.windows.net |  | Kræves adgang  | Kræves adgang  | Kræves adgang
 https://www.msftncsi.com/ncsi.txt | Kræves adgang  | Kræves adgang  | Kræves adgang  | Kræves adgang
 https://dev.MySQL.com/Get/Archives/MySQL-5.5/MySQL-5.5.37-Win32.msi | | | | Kræves adgang


## <a name="next-steps"></a>Næste trin

Du kan læse de detaljerede forudsætninger og begynde at implementere hvert scenario efter undervisning og sammenligne generelle installation krav.

- [Gentage VMware virtuelle maskiner til Azure](site-recovery-vmware-to-azure-classic.md)
- [Gentage fysiske servere til Azure](site-recovery-vmware-to-azure-classic.md)
- [Gentage Hyper-V server i VMM skyer til Azure](site-recovery-vmm-to-azure.md)
- [Gentage Hyper-V virtuelle maskiner (uden VMM) til Azure](site-recovery-hyper-v-site-to-azure.md)
- [Gentage Hyper-V FOS til en sekundær websted](site-recovery-vmm-to-vmm.md)
- [Gentage Hyper-V FOS til en sekundær websted med SAN](site-recovery-vmm-san.md)
- [Gentage Hyper-V FOS med en enkelt VMM-server](site-recovery-single-vmm.md)
