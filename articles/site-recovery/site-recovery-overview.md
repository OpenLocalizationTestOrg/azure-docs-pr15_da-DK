<properties
    pageTitle="Hvad er gendannelse af websteder? | Microsoft Azure"
    description="Indeholder en oversigt over tjenesten Azure gendannelse af websteder, og indeholder en oversigt over installationsscenarier."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/13/2016"
    ms.author="raynew"/>

#  <a name="what-is-site-recovery"></a>Hvad er gendannelse af websteder?

Velkommen til Azure websted gendannelse! I denne artikel giver et hurtigt overblik over tjenesten gendannelse af websteder, og hvordan det bidrager til din virksomhed.

Din organisation kræver en Forretningskontinuitet og strategi for genoprettelse efter genoprettelse (BCDR), der holder apps, arbejdsmængder og data sikre og tilgængelige under planlagte og ikke-planlagt nedetid og genopretter til normal arbejdsbetingelser så tidligt som muligt. Gendannelse af websteder er en Azure-tjeneste, der bidrager til denne strategi.

Gendannelse af websteder orchestrates replikering af arbejdsbelastninger, som kører lokalt fysiske servere og virtuelle computere. Du kan gentage servere og FOS fra en primær datacenter til skyen (Azure) eller til en sekundær datacenter. Hvis der opstår afbrydelser i det primære websted, du ikke sekundær webstedet til at holde apps og arbejdsbelastninger tilgængelige og tilgængelig. Du ikke tilbage til din primære placering, når det vender tilbage til normal handlinger.

## <a name="site-recovery-in-the-azure-portal"></a>Gendannelse af websteder i portalen Azure

Azure har to forskellige [installation modeller](../resource-manager-deployment-model.md) til oprettelse og arbejde med ressourcer. Azure ressourcestyring modellen og klassisk services management modellen. Azure har også to portaler – [Azure klassisk portal](https://manage.windowsazure.com/) , der understøtter den klassiske implementeringsmodel og [Azure-portalen](https://portal.azure.com) med understøttelse af både klassiske og ressourcestyring modeller.

- Gendannelse af websteder er tilgængelig i både klassisk portalen og Azure portalen.
- Du kan understøtte gendannelse af websteder med klassisk services management modellen i Azure klassisk portalen.
- I portalen Azure, du yder support klassisk modellen eller Ressourcestyring installationer. 

Oplysninger i denne artikel gælder for både klassisk og Azure portalen installationer. Forskelle er angivet, hvis det er relevant.


## <a name="why-deploy-site-recovery"></a>Hvorfor installere gendannelse af websteder?

Her er, hvad gendannelse af websteder kan gøre for din virksomhed:

- **Forenkle BCDR**– du kan håndtere gentagelse, failover og gendannelse af flere arbejdsbelastninger på en enkelt placering i portalen Azure. Gendannelse af websteder orchestrates gentagelse og belastningsjustering, men ikke SKÆRING programmets data eller få oplysninger om den.
- **Angiv fleksible gentagelse**– ved hjælp af gendannelse af websteder, du kan gentage arbejdsbelastninger, som kører på understøttede Hyper-V FOS, VMware FOS og Windows/Linux fysiske servere.
- **Udfør nemt gentagelse test**– gendannelse af websteder indeholder test failover for at understøtte nedbrud gendannelse øvelser, uden at påvirke fremstilling miljøer.
- **Skifte over og gendanne**– du kan køre planlagte failover forventede afbrydelser med nul datatab eller ikke-planlagt failover med minimale datatab (afhængigt af replikering frekvens) til uventede nedbrud. Når du failover kan du failback til din primære websteder. Gendannelse af websteder indeholder gendannelse planer, som kan indeholde scripts og Azure automatiske projektmapper, så du kan tilpasse failover og gendannelse af med flere lag programmer.
- **Fjerne en sekundær datacenter**– du kan gentage arbejdsbelastninger til Azure i stedet for til en sekundær websted. Dette fjerner omkostningerne og kompleksiteten vedligeholde en sekundær datacenter. Replikerede data er gemt i Azure-lager, med alle spændstighed, der indeholder. FOS er oprettet med replikerede data, når der opstår failover.
- **Integrere med eksisterende BCDR teknologier**– gendannelse af websteder kan integreres med andre BCDR funktioner. Du kan for eksempel bruge gendannelse af websteder til at beskytte SQL Server backend-version af virksomhedens arbejdsmængder, herunder indbygget understøttelse af SQL Server AlwaysOn, til at administrere sekundære af tilgængelighed grupper.

## <a name="what-can-i-replicate"></a>Hvad kan jeg gentage?

Her er en oversigt over, hvad du kan gentage ved hjælp af gendannelse af websteder.

![Lokalt til en lokal installation](./media/site-recovery-overview/asr-overview-graphic.png)

**GENTAGE** | **REPLIKERES TIL** 
---|---
Arbejdsbelastninger, som kører på lokale VMware FOS | [Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Sekundær websted](site-recovery-vmware-to-vmware.md)
Arbejdsbelastninger, som kører på lokale Hyper-V FOS administreres i VMM skyer  | [Azure](site-recovery-vmm-to-azure.md)<br/><br/> [Sekundær websted](site-recovery-vmm-to-vmm.md) 
Arbejdsbyrde, der kører på lokale Hyper-V FOS administreres i VMM skyer, med SAN lagerplads|  [Sekundær websted](site-recovery-vmm-san.md)
Arbejdsbelastninger, som kører på lokale Hyper-V FOS uden VMM | [Azure](site-recovery-hyper-v-site-to-azure.md)
Arbejdsbelastninger, som kører på servere med lokale fysisk Windows/Linux | [Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Sekundær websted](site-recovery-vmware-to-vmware.md)


## <a name="what-workloads-can-i-protect"></a>Hvilke arbejdsbelastninger, som kan beskytte?

Gendannelse af websteder kan program-aware BCDR, så arbejdsmængder og apps fortsætte med at køre på en ensartet måde, når der optræder udfald. Gendannelse af websteder indeholder:

- **Program-ensartet snapshots**– maskiner gentage ved hjælp af programmet ensartet snapshots til enkelte eller flere lag apps. Ud over at indsamle diskdata, registrere programmet ensartet snapshots capture alle data i hukommelsen og alle transaktioner i processen.
- **I nærheden af synkron gentagelse**– gendannelse af websteder indeholder gentagelse frekvens er så lave som 30 sekunder, før Hyper-V og fortløbende gentagelse til VMware.
- **Fleksible gendannelse planer**– du kan oprette og tilpasse gendannelse planer med eksterne scripts og manuel handlinger. Integration med Azure automatiske runbooks gør det muligt at gendanne en hele programmet stak med et enkelt klik.
- **Integration med SQL Server AlwaysOn**– du kan administrere sekundære af tilgængelighed grupper i gendannelse af websteder gendannelse planer.
- **Automatisering bibliotek**– et omfattende Azure automatisering bibliotek indeholder fremstilling klar, programmet-specifikke scripts, som kan hentes og integreret med gendannelse af websteder.
- **Simpelt netværksadministration**– netværksadministration af Avanceret i gendannelse af websteder og Azure forenkler programmet netværkskrav, herunder at reservere IP-adresser, konfigurere Indlæs balancere og integrere Azure trafik Manager for effektiv netværks switchovers.


## <a name="next-steps"></a>Næste trin

- Få mere at vide i [hvilke arbejdsbelastninger, som kan gendannelse af websteder beskytte?](site-recovery-workload.md)
- Få flere oplysninger om gendannelse af websteder arkitektur i [hvordan fungerer gendannelse af websteder?](site-recovery-components.md)
 
