<properties
    pageTitle="Beskytte Active Directory og DNS med gendannelse af Azure websteder | Microsoft Azure"
    description="I denne artikel beskrives, hvordan du kan implementere en løsning til genoprettelse efter genoprettelse til Active Directory ved hjælp af Azure gendannelse af websteder."
    services="site-recovery"
    documentationCenter=""
    authors="prateek9us"
    manager="abhiag"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="08/31/2016"
    ms.author="pratshar"/>

# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>Beskytte Active Directory og DNS med gendannelse af Azure websteder

Virksomhedens programmer som SharePoint, Dynamics AX og SAP er afhængige af Active Directory og en DNS-infrastrukturen kan fungere korrekt. Når du opretter en løsning til genoprettelse efter genoprettelse for programmer, er det vigtigt at huske, at du har brug at beskytte og gendanne Active Directory og DNS-før anden programkomponenterne, at ting fungere korrekt, når der udsendes nedbrud.

Gendannelse af websteder er en Azure tjeneste, som indeholder nedbrud ved orchestrating gentagelse, failover og gendannelse af virtuelle maskiner. Gendannelse af websteder understøtter et antal replikering scenarier konsekvent beskytte, og problemfrit failover virtuelle computere og programmer til privat, offentligt eller værten skyer.

Brug af gendannelse af websteder, kan du oprette en komplet automatiseret nedbrud gendannelse plan til Active Directory. Når der opstår forstyrrelser, kan du starte en failover i sekunder fra et vilkårligt sted og får Active Directory oppe at køre i et par minutter. Hvis du har installeret Active Directory for flere programmer som SharePoint og SAP i din primære websted, og du vil skifte over komplet websted, du kan ikke over Active Directory ved hjælp af gendannelse af websteder, og derefter skifte over andre programmer ved hjælp af programmet-specifikke gendannelse planer.

I denne artikel beskrives det, hvordan du opretter en løsning til genoprettelse efter genoprettelse til Active Directory, hvordan du udfører planlagte, ikke-planlagt og test failover ved hjælp af en plan for et enkelt klik, understøttede konfigurationer og forudsætninger.  Du bør have kendskab til Active Directory og Azure gendannelse af websteder, før du går i gang.

Der findes to anbefalede indstillinger, der er baseret på kompleksiteten af dit miljø.

### <a name="option-1"></a>Valgmulighed 1

Hvis du har et lille antal programmer og en enkelt domænenavn fra domænecontrolleren, og du vil skifte over hele webstedet, derefter anbefaler vi bruge gendannelse af websteder kan replikeres domænecontrolleren til webstedet sekundær (angiver, om du ikke til Azure eller til et sekundært websted). Den samme replikerede virtuelle maskine kan bruges til test failover for.

### <a name="option-2"></a>Mulighed 2

Hvis du har et stort antal programmer og der er mere end én domænenavn fra domænecontrolleren i miljøet, eller hvis du planlægger at mislykkes over nogle programmer ad gangen, anbefaler vi, at ud over replikering domænecontroller virtuel maskine med gendannelse af websteder skal du også konfigurere en ekstra domænenavn fra domænecontrolleren på destinationswebstedet (Azure eller en sekundær lokalt datacenter).

>[AZURE.NOTE] Selvom du implementering indstilling-2 for at gøre en test failover, skal du stadig gentage domænecontrolleren med gendannelse af websteder. Læs [teste failover overvejelser](#considerations-for-test-failover) kan finde flere oplysninger.


I følgende afsnit beskrives, hvordan du aktiverer beskyttelse for et domænenavn fra domænecontrolleren i gendannelse af websteder, og hvordan du konfigurerer et domænenavn fra domænecontrolleren i Azure.


## <a name="prerequisites"></a>Forudsætninger

- En lokal installation af Active Directory og DNS-server.
- En Azure websted gendannelse Services samling af legitimationsoplysninger i et Microsoft Azure-abonnement.
- Hvis du replikeres til Azure Kør værktøjet vurdering af Azure virtuelt parathed på FOS at sikre, at er de kompatible med Azure FOS og Azure websted gendannelsestjenester.


## <a name="enable-protection-using-site-recovery"></a>Aktivér beskyttelse ved hjælp af gendannelse af websteder


### <a name="protect-the-virtual-machine"></a>Beskytte den virtuelle maskine

Aktivér beskyttelse af domænecontroller/DNS virtuel maskine i gendannelse af websteder. Konfigurere indstillinger for gendannelse af websteder baseret på den virtuelle maskine type (Hyper-V eller VMware). Vi anbefaler, at en nedbrud ensartet replikering frekvens på 15 minutter.

###<a name="configure-virtual-machine-network-settings"></a>Konfigurere indstillinger for virtuelt netværk

Konfigurere netværksindstillinger i gendannelse af websteder til domænecontroller/DNS virtual machine, så VM knyttes til det rigtige netværk efter failover. Eksempelvis hvis du replikering Hyper-V FOS til Azure kan du vælge VM i skyen VMM eller i gruppen beskyttelse for at konfigurere netværksindstillinger, som vist nedenfor

![VM netværksindstillinger](./media/site-recovery-active-directory/VM-Network-Settings.png)

## <a name="protect-active-directory-with-active-directory-replication"></a>Beskytte Active Directory med Active Directory-replikering

### <a name="site-to-site-protection"></a>Websted til beskyttelse

Oprette et domænenavn fra domænecontrolleren på sekundær webstedet, og Angiv navnet på det samme domæne, der anvendes på det primære websted, når du opgraderer serveren til en domain controller rolle. Du kan bruge snap-in **Active Directory-områder og -tjenester** til at konfigurere indstillinger for objektet websted link, der tilføjes på websteder. Ved at konfigurere indstillinger på hyperlink til et websted, du kan styre, hvornår replikering forekommer mellem to eller flere websteder, og hvor ofte. Du kan finde flere oplysninger i [Planlægge replikering mellem websteder](https://technet.microsoft.com/library/cc731862.aspx) .

###<a name="site-to-azure-protection"></a>Websted til Azure beskyttelse

Følg vejledningen for at [oprette et domænenavn fra domænecontrolleren i en Azure virtuelt netværk](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Angiv det samme domænenavn, der bruges på webstedet primære, når du opgraderer serveren til en domain controller rolle.

Derefter [omkonfigurere DNS-server til det virtuelle netværk](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network), du bruger DNS-serveren i Azure.

![Azure-netværk](./media/site-recovery-active-directory/azure-network.png)

## <a name="test-failover-considerations"></a>Overvejelser i forbindelse med test failover

Test serverskift i et netværk, der har identificeret fra fremstilling netværk, så der ikke er nogen indflydelse på fremstilling arbejdsmængder.

De fleste programmer kræver også tilstedeværelsen af et domænenavn fra domænecontrolleren og en DNS-server, der skal fungere, så før programmets mislykkedes, et domænenavn fra domænecontrolleren skal oprettes i den isolerede netværk, der skal bruges til test failover. Den nemmeste måde at gøre dette er at aktivere beskyttelse på domænecontroller/DNS virtuel maskine med gendannelse af websteder og køre en test failover på den virtuelle maskine, før du kører en test failover af gendannelse planen for programmet. Her er, hvordan du gør dette:

1. Aktivér beskyttelse i gendannelse af websteder til domænecontroller/DNS virtual machine.
2. Oprette et isolerede netværk. Et virtuelt netværk, der er oprettet i Azure som standard er adskilt fra andre netværk. Vi anbefaler, at IP-adresseområder for dette netværk er den samme som netværket fremstilling. Aktiver ikke forbindelse til websted på dette netværk.
3. Angive en DNS IP-adresse i det netværk, der er oprettet, som den IP-adresse, som du mod forventning den virtuelle maskine DNS for at få. Hvis du replikeres til Azure, derefter Giv IP-adressen for den VM, der skal bruges på failover **Target IP** -indstillingen i VM egenskaber. Hvis du replikeres til en anden lokale websted og du bruger DHCP Følg vejledningen for at [konfigurere DNS og DHCP til test failover](site-recovery-failover.md#prepare-dhcp)

>[AZURE.NOTE] Den IP-adresse, der er allokeret til en virtuel maskine under en test failover er lig med det ville få på under en planlagt eller uventet failover IP-adressen, hvis IP-adressen er tilgængelig i test failover netværk. Hvis det ikke, modtager den virtuelle maskine en anden IP-adresse, der er tilgængelige i test failover netværket.

4. Køre en test failover af filen i det isolerede netværk på domænecontroller virtuel maskine. Bruge seneste tilgængelige programmet ensartet gendannelsespunkt på domænecontroller virtuelle maskine til at gøre sekundære test. 
5. Køre en test sekundær server til programmet gendannelse plan.
6. Når test er fuldført, skal du markere test failover sagen på domænecontroller virtuelle maskine og af gendannelse planen 'Færdigt' på fanen **job** i portalen gendannelse af websteder.

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS- og domæne controller på forskellige computere

Hvis DNS ikke er på den samme virtuelle maskine som domænenavn fra domænecontrolleren skal du oprette en DNS VM for sekundære test. Hvis de er på den samme VM, kan du springe dette afsnit.

Du kan bruge et nyt DNS-server og oprette de nødvendige zoner. Active Directory-domænet er contoso.com, kan du oprette en DNS-zone med navnet contoso.com. De poster, der svarer til Active Directory skal opdateres i DNS, på følgende måde:

1. Kontrollere, at disse indstillinger er på plads, før andre virtuel maskine i gendannelse planen dukker op:

    - Zonen skal navngives efter navnet på skov rod.
    - Zonen skal være fil sikkerhedskopier.
    - Zonen skal være aktiveret for sikker og ikke-sikker opdateringer.
    - Resolver på domænecontroller virtuelle maskine skal pege på IP-adressen på DNS-virtuelle maskine.

2. Kør følgende kommando på domænecontroller virtuel maskine:

    `nltest /dsregdns`

3. Tilføje en zone på DNS-server, Tillad ikke-sikker opdateringer og tilføje en post til den til DNS:

        dnscmd /zoneadd contoso.com  /Primary
        dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
        dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
        dnscmd /config contoso.com /allowupdate 1


## <a name="next-steps"></a>Næste trin

Læs [hvilke arbejdsbelastninger, som kan jeg beskytte?](../site-recovery/site-recovery-workload.md) mere at vide om beskyttelse af enterprise arbejdsmængder med Azure gendannelse af websteder.
