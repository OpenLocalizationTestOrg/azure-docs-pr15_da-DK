<properties
    pageTitle="Failover i gendannelse af websteder | Microsoft Azure" 
    description="Azure gendannelse af websteder koordinater gentagelse, failover og gendannelse af virtuelle maskiner og fysiske servere. Få mere at vide om failover til Azure eller en sekundær datacenter." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016" 
    ms.author="raynew"/>

# <a name="failover-in-site-recovery"></a>Failover i gendannelse af websteder

Gendannelse af websteder Azure service bidrager til din business løbende og genoprettelse efter genoprettelse (BCDR) strategi ved orchestrating gentagelse, failover og gendannelse af virtuelle maskiner og fysiske servere. Computere kan replikeres til Azure eller til et sekundært lokalt datacenter. En hurtig oversigt over læse [Hvad er Azure gendannelse af websteder?](site-recovery-overview.md)

## <a name="overview"></a>Oversigt

I denne artikel indeholder oplysninger og instruktionerne for at gendanne (ikke mere end og ellers tilbage) virtuelle maskiner og fysiske servere, der er beskyttet med gendannelse af websteder. 

Sende en hvilken som helst kommentarer eller spørgsmål nederst i denne artikel eller på [Azure gendannelse Services-forummet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="types-of-failover"></a>Typer af ydelse

Når beskyttelse er aktiveret til virtuelle computere og fysiske servere og de replikering kan du køre failover behovet opstår. Gendannelse af websteder understøtter flere typer ydelse.

**Failover** | **Hvornår skal køres** | **Detaljer** | **Proces**
---|---|---|---
**Test failover** | Kør for at validere din strategi for gentagelse eller udføre en nedbrud gendannelse analysér | Ingen tab af data eller nedetid.<br/><br/>Uden at påvirke gentagelse<br/><br/>Uden at påvirke dit produktionsmiljø | Starte sekundære<br/><br/>Angive, hvordan test computere der skal knyttes til netværk efter failover<br/><br/>Registrere status for opgaver på fanen **job** . Testmaskiner oprettes og starte i den sekundære placering<br/><br/>Azure - forbindelse til computeren i portalen Azure<br/><br/>Sekundær websted – få adgang til computeren på den samme vært og skyen<br/><br/>Udføre tests, og automatisk oprydning test failover indstillinger.
**Planlagte failover** | Kør for at overholde krav<br/><br/>Køre til planlagt vedligeholdelse<br/><br/>Kør for at skifte over data for at holde arbejdsbelastninger, som kører kendte afbrydelser – som en forventede strømafbrydelse eller hårdt vejr rapporter<br/><br/>Køre til failback efter failover fra primære til sekundær | Undgå tab af data<br/><br/>Nedetid er påløbet i det tidsrum, det tager at lukke den virtuelle maskine på primært og finde den frem på den sekundære placering.<br/><br/>Virtuelle maskiner er virkning som mål-computere bliver kilde maskiner efter failover. | Starte sekundære<br/><br/>Registrere status for opgaver på fanen **job** . Kilde-computere er lukket<br/><br/>Replika maskiner start på den sekundære placering<br/><br/>Azure - forbindelse til replika computeren i portalen Azure<br/><br/>Sekundær websted – få adgang til computeren, på samme vært og i samme skyen<br/><br/>Udfør sekundære
**Ikke-planlagt failover** | Køre denne type ydelse på et aktiv måde, når et primære websted bliver ikke tilgængelig på grund af en uventet hændelse, som et power afbrydelse eller virus angreb <br/><br/> Du kan køre en ikke-planlagt failover kan udføres, selvom primære websted er ikke tilgængeligt. | Afhænger af frekvens Replikeringsindstillinger tab af data<br/><br/>Data bliver opdateret i overensstemmelse med den blev synkroniserede sidst | Starte sekundære<br/><br/>Registrere status for opgaver på fanen **job** . Du kan også prøve at lukke virtuelle maskiner og synkronisere seneste data<br/><br/>Replika maskiner start på den sekundære placering<br/><br/>Azure - forbindelse til replika computeren i portalen Azure<br/><br/>Sekundær få adgang til computeren, på samme vært og i samme skyen<br/><br/>Udfør sekundære


Typerne failover, der understøttes, afhænger af scenariet installation.

**Failover retning** | **Test failover** | **Planlagte failover** | **Ikke-planlagt failover**
---|---|---|---
Primære VMM websted til sekundær VMM websted | Understøttes | Understøttes | Understøttes 
Sekundær VMM websted til primære VMM websted | Understøttes | Understøttes | Understøttes
Skyen i skyen (enkelt VMM server) |  Understøttes | Understøttes | Understøttes
VMM websted til Azure | Understøttes | Understøttes | Understøttes 
Azure til VMM websted | Ikke-understøttede | Understøttes | Ikke-understøttede 
Hyper-V websted til Azure | Understøttes | Understøttes | Understøttes
Azure til Hyper-V-websted | Ikke-understøttede | Understøttes | Ikke-understøttede
VMware websted til Azure | Understøttede (udvidet scenarie)<br/><br/> Ikke-understøttede (ældre scenarie) |Ikke-understøttede | Understøttes
Fysisk server til Azure | Understøttede (udvidet scenarie)<br/><br/> Ikke-understøttede (ældre scenarie) | Ikke-understøttede | Understøttes

## <a name="failover-and-failback"></a>Failover og failback

Du kan ikke over virtuelle maskiner til en sekundær lokalt websted eller til Azure, afhængigt af din installation. En computer, der skifter til Azure oprettes som en Azure virtuelt. Du kan ikke over en enkelt virtuel maskine eller fysisk server eller en plan for gendannelse. Gendannelse planer består af en eller flere bestilt grupper, der indeholder beskyttede virtuelle maskiner eller fysiske servere. De er vant til at dirigerer failover af flere maskiner, som mislykkes efter gruppen de er i. [Læs mere](site-recovery-create-recovery-plans.md) om gendannelse planer. 

Bemærk, at, når failover afsluttes, og dine maskiner er oppe at køre på en sekundær placering:

- Hvis du ikke Azure, når failover maskiner ikke er beskyttet eller kopierer i den primære eller sekundære placering. I Azure gemmes virtuelle maskiner i geografisk replikerede lagerplads, som indeholder fleksibilitet, men ikke replikering.
- Hvis du har en ikke-planlagt failover til en sekundær websted, når failover maskiner på sekundær placering ikke er beskyttet eller replikering.
- Hvis du har en planlagt failover til en sekundær websted, når failover maskiner på sekundær placering er beskyttet.
 

### <a name="failback-from-secondary-site"></a>Failback fra sekundær websted

Failback fra en sekundær websted til en primær bruger den samme fremgangsmåde som failover fra primært til sekundær. Når sekundære fra primære til sekundær datacenter er anvendt og fuldført, kan du starte modsat gentagelse, når webstedet primære bliver tilgængelig. Omvendt gentagelse starter replikering mellem den sekundære websted og primært ved at synkronisere delta dataene. Omvendt gentagelse bringer virtuelle maskiner i en beskyttet tilstand, men det sekundære datacenter er stadig aktive placeringen. For at gøre det primære websted til den aktive placering, hvor starter du en planlagt failover fra sekundær til primære, efterfulgt af en anden modsat gentagelse.

### <a name="failback-from-azure"></a>Failback fra Azure

Hvis du har ikke Azure er virtuelle maskiner beskyttet af funktionerne Azure fleksibilitet for virtuelle maskiner. For at gøre det oprindelige primære websted til den aktive placering køre du en planlagt failover. Du kan mislykkes tilbage til den oprindelige placering eller et andet sted, hvis det oprindelige websted er ikke tilgængelig. Hvis du vil starte replikering igen efter failback til den primære placering starter du en modsat gentagelse.

### <a name="failover-considerations"></a>Failover overvejelser i forbindelse med

- **IP-adresse efter failover**– som standard en mislykkedes computer har en anden IP-adresse end kilde maskinen. Hvis du vil bevare samme IP-adresse se: 
    - **Sekundær**– Hvis du ned til en sekundær websted, og du vil beholde en IP-adresse [læse](http://blogs.technet.com/b/scvmm/archive/2014/04/04/retaining-ip-address-after-failover-using-hyper-v-recovery-manager.aspx) denne artikel. Bemærk, at du kan bevare en offentlig IP-adresse, hvis din Internetudbyder understøtter det.
    - **Azure**– Hvis du ikke Azure kan du angive den IP-adresse, du vil tildele under fanen **konfiguration** af egenskaberne virtuelt. Du kan ikke bevare en offentlig IP-adresse efter failover til Azure. Du kan bevare RFC 1918 adresse mellemrum, der bruges som interne adresser.
- **Delvis failover**– Hvis du vil skifte over en del af et websted i stedet for et helt websted Bemærk, at: 
    - **Sekundær**– Hvis du ikke over en del af en primær websted til et sekundært websted, og du vil oprette forbindelse tilbage til det primære websted, kan du bruge en-til-websted VPN-forbindelse til at oprette forbindelse mislykkedes over programmer på webstedet sekundær til infrastrukturkomponenter, der kører på den primære websted. Hvis et helt undernet mislykkes kan du bevare virtuelt IP-adresse. Hvis du ikke over en delvis undernet kan du bevare virtuelt IP-adressen, fordi undernet kan opdeles mellem forskellige websteder.
    - **Azure**– Hvis du mislykkes over en delvis websted til Azure og vil oprette forbindelse tilbage til det primære websted, kan du bruge en VPN til-websted til at oprette forbindelse et mislykket over programmet i Azure til infrastrukturkomponenter, der kører på den primære websted. Bemærk, at hvis hele undernet opstår over du kan bevare virtuelt IP-adresse. Hvis du ikke over en delvis undernet kan du bevare virtuelt IP-adressen, fordi undernet kan opdeles mellem forskellige websteder.
 
- **Bogstavet drev**– Hvis du vil bevare bogstavet på virtuelle maskiner efter failover kan du angive SAN-politikken for den virtuelle maskine til **til**. Virtuelt diske følger automatisk online. [Du finder flere oplysninger](https://technet.microsoft.com/library/gg252636.aspx).
- **Distribuere klient-anmodninger**– gendannelse af websteder fungerer med Azure trafik Manager til at distribuere klient-anmodninger i dit program efter failover.




## <a name="run-a-test-failover"></a>Køre en test failover

Når du kører en test failover bliver du bedt om at vælge netværksindstillinger for test replika computere. Du har et antal indstillinger.  

**Teste failover indstilling** | **Beskrivelse** | **Failover afkrydsningsfelt** | **Detaljer**
---|---|---|---
**Skifte til Azure – uden netværk** | Vælg ikke en destination Azure netværk | Failover Kontroller, der tester virtuelt starter som forventet i Azure | Alle test virtuelle maskiner i en plan for Gendan tilføjes i en enkelt skybaseret tjeneste og kan oprette forbindelse til hinanden<br/><br/>Maskiner forbindelse ikke til et netværk med Azure efter failover.<br/><br/>Brugere kan oprette forbindelse til testmaskiner med en offentlig IP-adresse
**Skifte til Azure – med netværk** | Vælg en destination Azure netværk | Failover kontrollerer, at test-computere er forbundet til netværket | Oprette et Azure netværk, der har isolerede fra netværket Azure fremstilling og konfigurere infrastrukturen for den replikerede virtuelle maskine fungerer som forventet.<br/><br/>Undernet på test virtuelle maskine er baseret på undernet som den mislykkedes over virtuelt forventes at oprette forbindelse til, hvis der opstår en planlagt eller uventet failover.
**Skifte til en sekundær VMM websted – uden netværk** | Vælg ikke et VM netværk | Failover kontrollerer, at der oprettes test-computere.<br/><br/>Virtuelt test oprettes på samme vært som vært, som den replika virtuelle maskine findes. Det er ikke føjet til skyen, virtuelt replika er placeret. | <p>Den mislykkedes over maskine ikke være forbundet med en hvilken som helst netværk.<br/><br/>Maskinen kan være forbundet til en VM netværk, når den er blevet oprettet
**Skifte til en sekundær VMM websted – med netværk** | Vælg et eksisterende netværk med VM en | Failover kontrollerer, at der oprettes virtuelle maskiner | Virtuelt test oprettes på samme vært som vært, som den replika virtuelle maskine findes. Det er ikke føjet til skyen, virtuelt replika er placeret.<br/><br/>Oprette et VM netværk, der har identificeret fra netværket fremstilling<br/><br/>Hvis du bruger et VLAN-baseret netværk anbefaler vi, at du opretter et separat logisk netværk (ikke bruges i fremstilling) i VMM hertil. Dette logiske netværk bruges til at oprette VM netværk til test failover.<br/><br/>Det logiske netværk skal være knyttet til mindst én af netværkskortene af alle de Hyper-V-servere, der er vært for virtuelle maskiner.<br/><br/>For VLAN logiske netværk, skal de netværk websteder, du føjer til logiske netværket være isolerede.<br/><br/>Hvis du bruger et Windows netværk Virtualization – baseret logiske netværk, oprettes der automatisk isolerede VM netværk Azure gendannelse af websteder.
**Skifte til en sekundær VMM websted – oprette et netværk** | En midlertidig test netværk oprettes automatisk på baggrund af den indstilling, du angiver i **Logisk netværk** og dens relaterede netværk websteder | Failover kontrollerer, at der oprettes virtuelle maskiner | Brug denne indstilling, hvis den gendannelse plan bruger mere end ét VM netværk. Hvis du bruger Windows netværk Virtualization netværk, kan denne indstilling automatisk oprette VM netværk med de samme indstillinger (undernet og IP-adresse grupper) i netværket på den replika virtuelle maskine. Disse VM netværk ryddes automatisk, når test sekundære er fuldført.</p><p>Virtuelt test oprettes på samme vært som vært, som den replika virtuelle maskine findes. Det er ikke føjet til skyen, virtuelt replika er placeret.

>[AZURE.NOTE] Den IP-adresse, der blev angivet et virtuelt under test failover gør det samme som den IP-adresse, som det skal vises, når en planlagt eller uventet failover (forudsat, at IP-adressen er tilgængelig i test failover netværket. Hvis den samme IP-adresse er ikke tilgængelig i test failover netværket, virtuelt modtager en anden IP-adresse, der er tilgængelige i test failover netværket.



### <a name="run-a-test-failover-from-on-premises-to-azure"></a>Køre en test failover fra lokale til Azure

Denne procedure beskriver, hvordan du kører en test sekundær server til en plan for gendannelse. Du kan også køre sekundære for en enkelt maskine under fanen **virtuelle computere** .

1. Vælg **Gendannelse plan** > *recoveryplan_name*. Klik på **Failover** > **Test Failover**.
2. Angiv, hvordan replika computere der skal knyttes til et netværk med Azure efter failover, på siden **Bekræft Test Failover** .
3. Hvis du ned til Azure og kryptering af data er aktiveret til skyen, i **Krypteringsnøgle** , skal du vælge det certifikat, der er udstedt, når du har aktiveret datakryptering af under udbyderinstallation. 
4. Opgavestatus failover på fanen **job** . Du skal kunne se replika testcomputer i portalen Azure.
5. Du kan åbne replika maskiner i Azure fra dit lokale websted Påbegynd en RDP-forbindelse til den virtuelle maskine. port 3389 skal være åben på slutpunkt for den virtuelle maskine.
5. Når du er færdig, når sekundære Når fasen **færdigt test** , klik på **Fuldført Test** at afslutte.
5. Registrér, i **noter** og gemme de bemærkninger, der er knyttet til sekundære test.
8. Klik på **sekundære test er fuldført** automatisk rydde op i testmiljøet. Når dette er fuldført vises test sekundære C**omplete** status.

> [AZURE.NOTE] Hvis en test failover fortsætter for mere end to uger skal være fuldført af kraft. Alle elementer eller virtuelle maskiner, der oprettes automatisk under test sekundære slettes.
  

### <a name="run-a-test-failover-from-a-primary-on-premises-site-to-a-secondary-on-premises-site"></a>Køre en test failover fra en primær lokalt websted til et sekundært lokalt websted

Du skal foretage en række ting til at køre en test failover, herunder at foretage en kopi af domænecontrolleren og placere test DHCP og DNS-servere i dit testmiljø. Du kan gøre dette på flere måder:

- Hvis du vil køre en test failover ved hjælp af et eksisterende netværk, skal du forberede Active Directory, DHCP og DNS i dette netværk.
- Hvis du vil køre en test failover ved hjælp af indstillingen til at oprette VM netværk automatisk, skal du tilføje manuel trin før gruppe-1 i den gendannelse plan, du skal bruge til sekundære test og derefter tilføje infrastruktur ressourcerne til den automatisk oprettede netværk før du kører sekundære test.

#### <a name="things-to-note"></a>Ting, du skal være opmærksom

- Ved replikering til en sekundær websted, behøver typen netværk, der bruges af replika computeren ikke så det svarer til typen logiske netværk, der bruges til test failover, men nogle kombinationer fungerer muligvis ikke. Hvis replikaen bruger DHCP og VLAN-baserede isolationsniveauet, skal ikke VM netværket for replikaen en statisk IP-adressegruppe. Så ved hjælp af Windows netværk Virtualization til sekundære test duer, fordi ingen adresse grupper er tilgængelige. Desuden fungerer test failover ikke, hvis replika netværket er ingen isolationsniveauet og test netværket er Windows netværk Virtualization. Dette skyldes, at ingen isolationsniveauet netværk ikke har de undernet, der er nødvendige for at oprette et Windows netværk Virtualization netværk.
- Den måde, i hvilken replika virtuelle computere er tilsluttet tilknyttet VM netværk, når failover, afhænger af, hvordan VM netværket er konfigureret i konsollen VMM:
    - **VM netværk konfigureret uden isolationsniveauet eller VLAN isolationsniveauet**– Hvis DHCP er defineret for netværket, VM, virtuelt replika der skal knyttes til den ved hjælp af de indstillinger, der er angivet til webstedet netværk i det tilknyttede logiske netværk VLAN-ID. Den virtuelle maskine får IP-adressen fra den tilgængelige DHCP-server. Du behøver ikke en statisk IP-adressegruppe, der er defineret for target VM netværket. Hvis en statisk IP-adressegruppe bruges til VM netværket forbindes virtuelt replika til det ved hjælp af de indstillinger, der er angivet til webstedet netværk i det tilknyttede logiske netværk VLAN-ID. Den virtuelle maskine modtager IP-adressen fra den gruppe, der er defineret for VM netværket. Hvis en statisk IP-adressegruppe ikke er defineret mål VM netværk, mislykkes IP-adresse allokering. IP-adressegruppe skal oprettes på både kilde- og destinationswebsteder VMM-servere, du vil bruge til beskyttelse og gendannelse.
    - **VM netværk med Windows netværk virtualization**– Hvis et VM netværk er konfigureret med denne indstilling, skal være defineret en statisk gruppe for target VM netværket, uanset om kilde VM netværket er konfigureret til brug af DHCP- eller en statisk IP-adressen puljen. Hvis du definerer DHCP, target VMM server fungere som en DHCP-server og angive en IP-adresse fra puljen, der er defineret for target VM netværk. Hvis Brug af en statisk IP-adressegruppe er defineret for kildeserveren, tildele target VMM serveren en IP-adresse fra puljen. IP-adresse allokering mislykkes i begge tilfælde, hvis en statisk IP-adressegruppe ikke er defineret.

#### <a name="run-test"></a>Køre test

Denne procedure beskriver, hvordan du kører en test sekundær server til en plan for gendannelse. Du kan også køre sekundære for en enkelt virtuelt eller fysisk server under fanen **virtuelle computere** .

1. Vælg **Gendannelse plan** > *recoveryplan_name*. Klik på **Failover** > **Test Failover**.
2. Angiv, hvordan virtuelle maskiner skal være forbundet med netværk efter test sekundære på siden **Bekræft teste Failover** .
3. Opgavestatus failover på fanen **job** . Klik på **Fuldført teste** at afslutte op sekundære test, når sekundære Når fasen** færdigt test** .
4. Klik på **noter** for at registrere og gemme de bemærkninger, der er knyttet til sekundære test.
4. Bekræft virtuelle maskiner starte uden fejl, når den er fuldført.
5. Udføre test sekundære til at rydde op på isolerede miljø efter ved at bekræfte, at virtuelle maskiner starte uden fejl. Hvis du vælger til automatisk at oprette VM netværk, oprydning sletter alle test virtuelle maskiner og teste netværk.

> [AZURE.NOTE] Hvis en test failover fortsætter for mere end to uger skal være fuldført af kraft. Alle elementer eller virtuelle maskiner, der oprettes automatisk under test sekundære slettes.


#### <a name="prepare-dhcp"></a>Forberede DHCP

Hvis de virtuelle maskiner involveret i test failover bruger DHCP, skal der oprettes en test DHCP-server i det isolerede netværk, der er oprettet med henblik på test failover.


### <a name="prepare-active-directory"></a>Forberedelse af Active Directory
Hvis du vil køre en test sekundær server til test af programmet, skal du en kopi af Active Directory produktionsmiljø i dit testmiljø. Gå igennem [teste active directory failover overvejelser i forbindelse med](site-recovery-active-directory.md#considerations-for-test-failover) sektionen få mere at vide. 


### <a name="prepare-dns"></a>Forberede DNS

Forberede en DNS-server til sekundære test på følgende måde:

- **DHCP**– Hvis virtuelle maskiner bruger DHCP, IP-adressen for test DNS skal opdateres på test DHCP-serveren. Hvis du bruger en netværk slags Windows netværk Virtualization, fungerer VMM server som DHCP-serveren. IP-adressen på DNS skal derfor opdateres i test failover netværket. I dette tilfælde vil de virtuelle maskiner registrere sig selv til de relevante DNS-Server.
- **Statisk adresse**– Hvis virtuelle maskiner bruger en statisk IP-adresse, der skal opdateres IP-adressen på test DNS-server i test failover netværk. Du skal muligvis opdatere DNS med test virtuelle maskiner IP-adresse. Du kan bruge følgende eksempelscript hertil: 

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-planned-failover-primary-to-secondary"></a>Køre en planlagt failover (primær til sekundær)

 Denne procedure beskriver, hvordan du kører en planlagt sekundær server til en plan for gendannelse. Du kan også køre sekundære for en enkelt virtuel maskine under fanen **virtuelle computere** .

1. Kontroller, at alle de ønskede mislykkes virtuelle-computere har fuldført første replikering, før du går i gang.
2. Vælg **Gendannelse plan** > *recoveryplan_name*. Klik på **Failover** > **planlagt Failover**. 
3. Vælg de kilde- og destinationswebsteder placeringer på siden **Bekræft planlagt Failover **. Bemærk failover retning.

    - Hvis forrige failover arbejdet som forventet, og alle virtuelt servere er placeret på enten kilde‑ eller målliste placering, bruges failover retning oplysninger kun til orientering. 
    - Hvis virtuelle maskiner er aktive på både kilde- og destinationswebsteder placeringer, vises knappen **Skift retning** . Brug denne knap til at ændre og angive den retning, hvori sekundære skal udføres.

5. Hvis du ned til Azure og kryptering af data er aktiveret til skyen, i **Krypteringsnøgle** , skal du vælge det certifikat, der er udstedt, når du har aktiveret kryptering af data under udbyder installationen på VMM-serveren. 
6. Når en planlagt failover begynder er det første trin at lukke de virtuelle computere at sikre, at undgå tab af data. Du kan følge failover status på fanen **job** . Hvis der opstår fejl i sekundære (enten på en virtuel maskine eller i et script, der er inkluderet i den gendannelse plan), holder op med den planlagte sekundære for en gendannelse plan. Du kan starte sekundære igen.
8. Når der oprettes replika virtuelle maskiner, de er i en bekræftelse afventer tilstand. Klik på **Anvend** for at bekræfte sekundære. 
9. Når replikering er fuldført virtuelle maskiner start op på den sekundære placering. 

## <a name="run-an-unplanned-failover"></a>Køre en ikke-planlagt failover

Denne procedure beskriver, hvordan du kører en ikke-planlagt sekundær server til en plan for gendannelse. Du kan også køre sekundære for en enkelt virtuelt eller fysisk server under fanen **virtuelle computere** .

1. Vælg **Gendannelse plan** > *recoveryplan_name*. Klik på **Failover** > **ikke-planlagt Failover**. 
3. Vælg de kilde- og destinationswebsteder placeringer på siden **Bekræft ikke-planlagt Failover **. Bemærk failover retning.

    - Hvis forrige failover arbejdet som forventet, og alle virtuelt servere er placeret på enten kilde‑ eller målliste placering, bruges failover retning oplysninger kun til orientering. 
    - Hvis virtuelle maskiner er aktive på både kilde- og destinationswebsteder placeringer, vises knappen **Skift retning** . Brug denne knap til at ændre og angive den retning, hvori sekundære skal udføres.

4. Hvis du ned til Azure og kryptering af data er aktiveret til skyen, i **Krypteringsnøgle** , skal du vælge det certifikat, der er udstedt, når du har aktiveret kryptering af data under udbyder installationen på VMM-serveren. 
5. Vælg **Luk virtuelle maskiner og synkronisere de nyeste data** til at angive, at gendannelse af websteder skal prøve at lukke de beskyttede virtuelle maskiner og synkronisere dataene, så den nyeste version af dataene ikke bliver gennemført. Hvis du ikke vælger denne indstilling eller afkrydsningsfeltet forsøg ikke lykkes bliver sekundære fra det seneste tilgængelige gendannelsespunkt til den virtuelle maskine.
6. Du kan følge failover status på fanen **job** . Bemærk, selvom der opstår fejl under failover en ikke-planlagt, gendannelse planen afspilles, indtil den er fuldført.
7. Efter sekundære er virtuelle maskiner i tilstanden **Udfør afventer** . Klik på **Anvend** for at bekræfte sekundære.
8. Hvis du konfigurerer gentagelse til at bruge flere gendannelse punkter, i ændre gendannelse punkt kan du vælge for at bruge et gendannelse, der ikke er senest (nyeste bruges som standard). Når du bekræfter yderligere fjernes gendannelse punkter.
9. Når replikering er fuldført virtuelle maskiner starter og kører på den sekundære placering. Men de ikke er beskyttet eller kopierer. Når det primære websted er tilgængeligt igen med den samme underliggende infrastruktur, kan du klikke på **Modsat gentage** for at starte modsat gentagelse. Dette sikrer, at alle dataene replikeres tilbage til det primære websted, og at den virtuelle maskine er klar til failover igen. Føre gentagelse, når en ikke-planlagt failover medfører en omkostninger i dataoverførsel. Overførslen anvender den samme metode, der er konfigureret til indledende Replikeringsindstillinger til skyen.

## <a name="failback-from-secondary-to-primary"></a>Failback fra sekundær til primære

 Efter failover fra primært til sekundær placering, replikerede virtuelle maskiner ikke er beskyttet af gendannelse af websteder og den sekundære placering nu fungerer som primært. Følg disse procedurer til at mislykkes tilbage til det oprindelige primære websted. Denne procedure beskriver, hvordan du kører en planlagt sekundær server til en plan for gendannelse. Du kan også køre sekundære for en enkelt virtuel maskine under fanen **virtuelle computere** .

1. Vælg **Gendannelse plan** > *recoveryplan_name*. Klik på **Failover** > **planlagt Failover**.
2. Vælg de kilde- og destinationswebsteder placeringer på siden **Bekræft planlagt Failover **. Bemærk failover retning. Hvis sekundære fra primære arbejdet som forventer, og alle virtuelle maskiner er på den sekundære placering dette er kun til information.
3. Vælg indstillinger i **Synkronisering af Data**, hvis du ned tilbage fra Azure:

    - **Synkronisere data, før du failover (kun Synchonize delta ændringer)**– denne indstilling minimeres nedetid for virtuelle maskiner, som den synkroniserer uden at lukke dem. Det gør følgende:
        - Fase 1: Tager øjebliksbillede af den virtuelle maskine i Azure og kopieres til den lokale Hyper-V vært. Maskinen fortsætter med at køre i Azure.
        - Fase 2: Lukker den virtuelle maskine i Azure, så der optræder ingen nye ændringer. Det sidste sæt ændringer overføres til den lokale server, og den lokale virtuelle maskine startes.
    

    - **Synkroniser data under failover kun (fuld overførsel)**– Brug denne indstilling, hvis du har kørt på Azure for lang tid. Denne indstilling er hurtigere, fordi vi forventer, at de fleste af disken er blevet ændret, og vi ikke vil bruge en masse tid i kontrolsum beregning. Udfører en overførsel af disken. Det er også nyttig, når den lokalt virtuelle maskine er blevet slettet.
    
    > [AZURE.NOTE] Vi anbefaler, at du bruger denne indstilling, hvis du har kørt Azure til et stykke tid (en måned eller mere) eller lokalt VM er blevet slettet. Denne indstilling udføre ikke en hvilken som helst kontrolsum beregninger.
    
5. Hvis du ned til Azure og kryptering af data er aktiveret til skyen, i **Krypteringsnøgle** , skal du vælge det certifikat, der er udstedt, når du har aktiveret kryptering af data under udbyder installationen på VMM-serveren. 
5. Det sidste gendannelsespunkt bruges som standard, men i **Ændre gendannelse punkt** kan du angive et andet gendannelsespunkt. 
6. Klik på markering for at starte failback.  Du kan følge failover status på fanen **job** . 
7. f, du har valgt indstillingen til at synkronisere data, før sekundære, når den indledende datasynkronisering er fuldført, og du er klar til at lukke de virtuelle maskiner i Azure, skal du klikke på **sager**  >  <planned failover job name> **Fuldført Failover**. Dette lukker den Azure maskine, overfører de seneste ændringer til den lokale virtuelle maskine og starter den.
8. Du kan nu logger på den virtuelle maskine til at validere det fås som forventet. 
9. Den virtuelle maskine er i en disposition afventer tilstand. Klik på **Anvend** for at bekræfte sekundære.
10. Klik nu for at fuldføre failback på **Vend gentage** at begynde at beskytte den virtuelle maskine i det primære websted.



## <a name="failback-to-an-alternate-location"></a>Failback til en anden placering

Hvis du har installeret beskyttelse mellem en [Hyper-V websted og Azure](site-recovery-hyper-v-site-to-azure.md) du mulighed for at failback fra Azure til en alternativ lokale placering. Dette er nyttigt, hvis du vil konfigurere nye lokale hardware. Her er, hvordan du gør.

1. Hvis du konfigurerer ny hardware kan du installere Windows Server 2012 R2 og Hyper-V-rollen på serveren.
2. Oprette et virtuelt netværk Skift med det samme navn, du har haft i den oprindelige server.
3. Vælg **Elementer, der er beskyttet** -> **Gruppen beskyttelse**  ->  <ProtectionGroupName>  ->  <VirtualMachineName> du vil mislykkes igen, og vælg **Planlagt Failover**.
4. Vælg **Opret lokale virtuelt, hvis den ikke findes**i **Bekræfte planlagt Failover** . 
5. Vælg den nye Hyper-V host server, hvor du vil placere den virtuelle maskine i **Host Name** .
6. Vi anbefaler du vælger indstillingen **Synkroniser data, før sekundære**i synkronisering af Data. Derved minimeres nedetid for virtuelle maskiner, som den synkroniserer uden at lukke dem. Det gør følgende:

    - Fase 1: Tager snapshot af den virtuelle maskine i Azure og kopieres til den lokale Hyper-V vært. Maskinen fortsætter med at køre i Azure.
    - Fase 2: Lukker den virtuelle maskine i Azure, så der optræder ingen nye ændringer. Det sidste sæt ændringer overføres til den lokale server, og den lokale virtuelle maskine startes.
    
7. Klik på markering for at starte sekundære (failback).
8. Efter den indledende synkronisering er færdig, og du er klar til at lukke den virtuelle maskine i Azure, skal du klikke på **sager** > <planned failover job> > **Fuldført Failover**. Dette lukker den Azure maskine, overfører de seneste ændringer til den lokale virtuelle maskine og starter den.
9. Du kan logge på den lokale virtuelle maskine at bekræfte, at alt fungerer som forventet. Klik derefter på **Udfør** for at afslutte sekundære.
10. Klik på **Vend gentage** for at begynde at beskytte den lokale virtuelle maskine.

    >[AZURE.NOTE] Hvis du annullerer failback jobbet, mens det er i synkronisering af Data trin, bliver den lokale VM i tilstanden beskadiget. Dette skyldes, at dataene synkronisering Kopier de nyeste data fra Azure VM destinationsdisketterne til datadisce lokalt og funktion synkroniseringen er fuldført, disken data ikke muligvis i en ensartet tilstand. Hvis på prem VM startes, når Data synkronisering er annulleret, kan den ikke starte. Udløse failover for at fuldføre Data synkronisering igen.
 
