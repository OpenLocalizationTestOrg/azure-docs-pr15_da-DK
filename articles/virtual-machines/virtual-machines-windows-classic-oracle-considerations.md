<properties
pageTitle="Overvejelser ved brug af Oracle VM billeder | Microsoft Azure"
description="Få mere at vide om understøttede konfigurationer og begrænsninger til en Oracle VM på Windows Server i Azure, før du installerer."
services="virtual-machines-windows"
documentationCenter=""
manager="timlt"
authors="rickstercdn"
tags="azure-service-management"/>

<tags
ms.service="virtual-machines-windows"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-windows"
ms.workload="infrastructure-services"
ms.date="09/06/2016"
ms.author="rclaus" />

#<a name="miscellaneous-considerations-for-oracle-virtual-machine-images"></a>Diverse overvejelser i forbindelse med Oracle virtuelt billeder



Denne artikel omhandler overvejelser i forbindelse med Oracle virtuelle maskiner i Azure, som er baseret på Oracle software billeder, der leveres af Oracle.  

-  Oracle-Database virtuelt billeder
-  Oracle WebLogic Server virtuelt billeder
-  Oracle JDK virtuelt billeder

##<a name="oracle-database-virtual-machine-images"></a>Oracle-Database virtuelt billeder
### <a name="clustering-rac-is-not-supported"></a>Understøttelse af klynger (RAC) understøttes ikke

Azure understøtter i øjeblikket ikke Oracle reelle programmet klynger (RAC) af Oracle-Database. Kun enkeltstående forekomster af Oracle-Database er muligt. Dette skyldes, at Azure ikke understøtter i øjeblikket virtuelle disk-deling i en læse-og skriveadgang måde mellem flere virtuelt forekomster. Multicast-UDP også understøttes ikke.

### <a name="no-static-internal-ip"></a>Ingen statiske interne IP-Adresser

Azure tildeler hver virtuel maskine en intern IP-adresse. Medmindre den virtuelle maskine er en del af et virtuelt netværk, IP-adressen på den virtuelle maskine er dynamisk og kan ændres, når den virtuelle maskine genstarter. Dette kan medføre problemer, fordi Oracle-Database forventer IP-adressen, der skal være statisk. For at undgå problemet skal du overveje at føje den virtuelle maskine til et virtuelt Azure-netværk. Se [Virtuelt netværk](https://azure.microsoft.com/documentation/services/virtual-network/) og [oprette et virtuelt netværk i Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) kan finde flere oplysninger.

### <a name="attached-disk-configuration-options"></a>Tilknyttet disk konfigurationsindstillinger

Du kan placere datafiler på enten operativsystem disken for den virtuelle maskine eller på en vedhæftet disk også kendt som datadisce. Vedhæftede diske tilbyder muligvis bedre ydeevne og størrelse fleksibilitet end operativsystem disken. Operativsystem disken kan være bedre kun for databaser under 10 gigabyte (GB).

Vedhæftede diske er afhængige af tjenesten Azure Blob storage. Hver disk kan teoretiske maksimalt ca. 500 input/output-operationer sekundet (IOP'ER). Ydeevnen for tilsluttede diske muligvis ikke optimal indledningsvis og IOP'ER ydeevnen kan forbedre betydeligt efter et "Brænd i" tidsrum cirka 60-90 minutter af kontinuerlig drift. Hvis en disk efterfølgende forbliver inaktiv, IOP'ER ydeevnen kan blive forringet indtil en anden Brænd i tidsrum fortløbende. Kort sagt giver flere aktiv en disk, jo mere sandsynligt er det er at tilgang optimal IOP'ER ydeevne.

Denne metode er den mest begrænse i ydeevne, selvom den nemmeste metode til at vedhæfte en enkelt disk til den virtuelle maskine og placere databasefiler på disken. I stedet kan du ofte forbedre effektive IOP'ER ydeevnen, hvis du bruger flere vedhæftede diske, fordeler databasedata på tværs af dem og derefter bruge Oracle automatisk lagring Management (ASM). Se [Oversigt over Oracle automatisk lagring](http://www.oracle.com/technetwork/database/index-100339.html) kan finde flere oplysninger. Selvom det er muligt at bruge striping af flere diske på niveauet for operativsystem, som denne metode ikke bør, fordi det ikke er kendt til at forbedre IOP'ER ydeevne.

Overvej to forskellige metoder til at vedhæfte flere diske, der er baseret på, om du vil prioritere ydeevnen for Læs handlinger eller skrive handlinger for databasen:

- **Oracle ASM på sin egen** er sandsynligvis vil medføre bedre ydeevne for skrivning handling, men værre IOP'ER for Læs handlinger i forhold til den fremgangsmåde, ved hjælp af disk matrixer. I nedenstående illustration vises logisk denne aftale.  
    ![](media/virtual-machines-windows-classic-oracle-considerations/image2.png)

>[AZURE.IMPORTANT] Evaluere forholdet mellem skriveadgang og læst ydeevne på grundlag af tilfælde. De faktiske resultater kan variere, når du bruger dette.

### <a name="high-availability-and-disaster-recovery-considerations"></a>Høj tilgængelighed og genoprettelse efter genoprettelse overvejelser

Når du bruger Oracle-Database i Azure virtuelle maskiner, er du ansvarlig for at implementere en høj tilgængelighed og genoprettelse efter løsning til genoprettelse for at undgå eventuelle nedetid. Du har også ansvarlig for at sikkerhedskopiere dine egne data og programmet.

Høj tilgængelighed og genoprettelse efter nedbrud til Oracle-Database Enterprise Edition (uden RAC) på Azure kan der opnås ved hjælp af [Data vagt, aktive Data vagt](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html)eller [Oracle guld port](http://www.oracle.com/technetwork/middleware/goldengate), med to databaser i to separate virtuelle maskiner. Begge virtuelle maskiner skal være i den samme [skybaseret tjeneste](virtual-machines-linux-classic-connect-vms.md) , og det samme [virtuelt netværk](https://azure.microsoft.com/documentation/services/virtual-network/) til at sikre, at de kan få adgang til hinanden via private fast IP-adressen.  Vi anbefaler desuden placere virtuelle maskiner i det samme [tilgængelighed angive](virtual-machines-windows-manage-availability.md) tillade Azure til at placere dem i separate fejl domæner og opgradere domæner. Kun virtuelle maskiner i samme skytjenesten kan deltage i det samme sæt tilgængelighed. Hver virtuelt skal have mindst 2 GB hukommelse og 5 GB ledig diskplads.

Med Oracle Data vagt, kan der opnås med en primær database i et virtuelt, en sekundær (standby) database i en anden virtuelt høj tilgængelighed og envejs gentagelse konfigurere mellem dem. Resultatet er læseadgang til kopien af databasen. Du kan konfigurere tovejs-replikering mellem de to databaser med Oracle GoldenGate. Hvis du vil se, hvordan du konfigurerer en høj tilgængelighed løsning for dine databaser med disse værktøjer, dokumentationen [Aktive Data vagt](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) og [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) på webstedet Oracle. Hvis du har brug for skrivebeskyttet adgang til kopien af databasen, kan du bruge [Oracle aktive Data vagt](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

##<a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server virtuelt billeder

-  **Klynge understøttes på Enterprise Edition kun.** Du har licens for at bruge WebLogic klynge, når du bruger Enterprise-udgave af WebLogic Server. Brug ikke klynge med WebLogic Server Standard Edition.

-  **Forbindelse timeout:** Hvis dit program er afhængig af forbindelser til offentlige slutpunkter af en anden Azure skybaseret tjeneste (for eksempel en database Avanceret service), kan Azure lukke disse åbne forbindelser efter fire minutters inaktivitet. Dette kan påvirke funktioner og programmer, der anvender på forbindelsesgrupper, fordi forbindelser, der er aktiv i mere end denne grænse kan ikke længere forbliver gyldige. Hvis dette påvirker dit program, kan du overveje at aktivere "heller" logik på din forbindelsesgrupper.

    Hvis et slutpunkt er *interne* til implementeringen af denne Azure skyen service (som en enkeltstående database virtuel maskine inden for *samme* skytjenesten som din WebLogic virtuelle maskiner), derefter forbindelsen er direkte og afhænger ikke af Azure justering af belastning og er derfor ikke underlagt forbindelsestimeout for en.

-  **UDP multicast understøttes ikke.** Azure understøtter UDP unicasting, men ikke multicastudsendelse eller udsendelse. WebLogic Server kan stole på Azure UDP unicast funktioner. For bedst resultater stole på UDP unicast, anbefaler vi, at WebLogic klyngestørrelsen opbevares statisk, eller beholdes med højst 10 administrerede servere, der er inkluderet i klyngen.

-  **WebLogic Server forventer offentlige og private porte, der skal være den samme for T3 access (for eksempel, når du bruger Enterprise JavaBeans).** Forestil dig en med flere lag situation, hvor et tjenesteprogram lag (EJB) der kører på en WebLogic Server-klynge bestående af to eller flere administrerede servere, i en skybaseret tjeneste med navnet **SLWLS**. Klient-niveau er i en anden skybaseret tjeneste, der kører en simpel Java-programmet forsøger at ringe EJB i tjenesten lag. Fordi det er nødvendigt at indlæse balance service lag, skal en offentlige belastningsfordeling slutpunkt skal oprettes til de virtuelle maskiner i WebLogic Server-klynge. Hvis den private port, du angiver for det pågældende slutpunkt er forskellig fra den offentlige port (for eksempel 7006:7008), opstår der en fejl som følgende:

        [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

        Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

    Dette skyldes, at for fjernadgang T3, WebLogic Server forventer Indlæs belastningsjusteringstjenesten port og serverport WebLogic administrerede skal være den samme. I ovenstående fald klienten der har adgang til port 7006 (Indlæs belastningsjusteringstjenesten port) og administrerede serveren lytter på 7008 (private porten). Denne begrænsning gælder kun for T3 access ikke HTTP.

    Du kan undgå dette problem ved at benytte en af følgende løsninger:

    -  Brug de samme private og offentlige portnumre for indlæsning afstemmes slutpunkter dedikeret T3 Access.

    -  Omfatte følgende JVM parameter, når du starter WebLogic Server:

            -Dweblogic.rjvm.enableprotocolswitch=true

Du kan finde relaterede oplysninger, KB artikel **860340.1** på <http://support.oracle.com>.

-  **Dynamisk klynge og justering af belastning begrænsninger.** Antag, at du vil bruge en dynamisk klynge i WebLogic Server og vise det via et enkelt, offentlige belastningsfordeling slutpunkt i Azure. Dette kan gøres, så længe du bruger et fast portnummer for hver af de administrerede servere (ikke dynamisk tildelte fra et område) og ikke starter mere administrerede servere, end der er maskiner administratoren registrerer (det vil sige, ikke mere end ét administrerede server per virtuelt). Hvis din konfiguration resulterer i flere WebLogic servere, der startes, end der er virtuelle maskiner (det vil sige, hvor flere forekomster af WebLogic Server dele den samme virtuelle maskine), og klik derefter det er ikke muligt for mere end én af disse forekomster af WebLogic Server-servere bindes til et bestemt portnummer – de andre på den virtuelle maskine mislykkes.

    På den anden side, hvis du konfigurerer en administrator server for at tildele forskellige portnumre automatisk til dens administrerede servere, er derefter belastning ikke muligt for Azure ikke understøtter tilknytning fra en enkelt offentlige port til flere private porte, som kræves til denne konfiguration.

-  **Flere forekomster af Weblogic Server på en virtuel maskine.** Afhængigt af din installation krav, kan du overveje mulighed for at køre flere forekomster af WebLogic Server på den samme virtuelle maskine, hvis den virtuelle maskine er stort nok. For eksempel på en mellemstore størrelse virtuel maskine, som indeholder to kerner, kan du køre to forekomster af WebLogic Server. Bemærk imidlertid, stadig anbefales, at du undgår introducerer enkelt punkter af fejl i din arkitektur, der ville være tilfældet, hvis du har brugt et enkelt virtuel maskine, der kører flere forekomster af WebLogic Server. Brug af mindst to virtuelle maskiner kan være en bedre fremgangsmåde og hver af disse virtuelle maskiner kan derefter køre flere forekomster af WebLogic Server. Hver af disse forekomster af WebLogic Server kan stadig være en del af den samme klynge. Note, men det er ikke muligt at bruge Azure til belastning slutpunkter, der vises ved sådanne WebLogic Server-installationer i den samme virtuelle maskine, fordi Azure justering af belastning kræver belastningsjusterede servere skal fordeles mellem entydige virtuelle computere.

##<a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK virtuelt billeder

-  **JDK 6 og 7 seneste opdateringer.** Mens vi anbefaler, at du bruger den nyeste offentlige, understøttet version af Java (aktuelt Java 8), er Azure også JDK 6 og 7 billeder tilgængelig. Dette er beregnet til ældre programmer, som ikke endnu er klar til at blive opgraderet til JDK 8. Mens opdateringer til forrige JDK billeder kan ikke længere være tilgængelige for offentligheden, givet Microsoft partnerskabet med Oracle, er JDK 6 og 7 billeder fra Azure beregnet til at indeholde en nyere ikke-offentlige opdatering, som tilbydes normalt i Oracle til kun en gruppe af udvalgte Oracle-understøttede kunder. Nye versioner af JDK billederne skal gøres tilgængelige over tid med opdaterede versioner af JDK 6 og 7.

    Den JDK, der er tilgængelige i denne JDK 6 og 7 billeder, og den virtuelle maskiner og billeder, der er afledt af dem, kan kun bruges i Azure.

-  **64-bit JDK.** Oracle WebLogic Server virtuelt billederne og Oracle JDK virtuelt billeder fra Azure indeholder 64-bit versioner af både Windows Server og JDK.

##<a name="additional-resources"></a>Yderligere ressourcer
[Oracle virtuelt billeder til Azure](virtual-machines-linux-classic-oracle-images.md)
