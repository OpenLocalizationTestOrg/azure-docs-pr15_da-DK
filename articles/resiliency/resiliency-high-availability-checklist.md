<properties
   pageTitle="Høj tilgængelighed tjekliste | Microsoft Azure"
   description="En hurtig tjekliste over indstillinger og handlinger, du kan udføre for at sikre, at du forbedre din programmer tilgængelighed med Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="high-availability-checklist"></a>Høj tilgængelighed tjekliste
En af gode fordelene ved at bruge Azure er muligheden for at øge tilgængelighed (og skalerbarhed) over dine programmer ved hjælp af skyen. Den nedenstående Tjekliste for at sikre, at du foretager de fleste af indstillingerne, er meningen, at hjælpe dig med nogle af de vigtigste infrastruktur grundlæggende funktioner til at sikre, at dine programmer er tolerant. 

>[AZURE.NOTE] De fleste af forslagene nedenfor er ting, der kan implementeres når som helst i dit program og dermed er gode til at "Hurtige løsninger". Den bedste sigt omfatter ofte et programdesign, der er bygget til skyen.  For en tjekliste på disse (flere design et overblik over områder, skal du læse vores [tilgængelighed tjekliste](../best-practices-availability-checklist.md).

###<a name="are-you-using-traffic-manager-in-front-of-your-resources"></a>Bruger du trafik Manager foran dine ressourcer?
Ved hjælp af trafik Manager hjælper med at omdirigere du internettrafik på tværs af Azure områder eller Azure og lokale placeringer. Du kan gøre dette for et sæt af årsager, herunder ventetid og tilgængelighed. Hvis du vil gerne have yderligere oplysninger om, hvordan du bruger trafik Manager til at øge din fleksibilitet og sprede dine trafik til flere områder, kan du læse [Køre FOS i flere datacentre på Azure for høj tilgængelighed](../guidance/guidance-compute-multiple-datacenters.md).

__Hvad sker der, hvis du ikke bruger trafik Manager?__ Hvis du ikke bruger trafik Manager foran dit program, er du begrænset til et enkelt område for dine ressourcer. Begrænser dit målestoksforhold, øges ventetid til brugere, der ikke er tæt på dit valgte område, og sænker din beskyttelse i forbindelse med et område hele tjenesteforstyrrelse.

###<a name="have-you-avoided-using-a-single-virtual-machine-for-any-role"></a>Har du undgås ved hjælp af en enkelt virtuel maskine til en rolle?
Godt design undgår fejl ét afsnit. Dette er vigtigt i alle service-design (lokale eller i skyen), men er især nyttig i skyen, som du kan øge skalerbarhed og fleksibilitet via skalering af (tilføje virtuelle maskiner) i stedet for skalering op (ved hjælp af en mere effektiv virtuel maskine). Hvis du vil gerne have yderligere oplysninger om SVG programmet design, kan du læse [høj tilgængelighed for bygget på Microsoft Azure-programmer](resiliency-high-availability-azure-applications.md).

__Hvad sker der, hvis du har en enkelt virtuel maskine til en rolle?__ En enkelt maskine er et enkelt punkt af manglende og er ikke tilgængelig for [Azure virtuelt serviceaftale](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/). I de bedste tilfælde programmet skal køre lige fint, men dette ikke er et robust design, er ikke omfattet af Azure virtuelt SERVICENIVEAUAFTALE og manglende øger risikoen for nedetid Hvis noget mislykkes ét afsnit.

###<a name="are-you-using-a-load-balancer-in-front-of-your-applications-internet-facing-vms"></a>Bruger du en belastningsjustering foran dit program internettet FOS?
Indlæse balancere gør det muligt at udbrede et vilkårligt antal maskiner den indgående trafik til dit program. Du kan tilføje/fjerne computere fra din justering af belastning til enhver tid, der fungerer fint med virtuelle maskiner (og også med automatisk skalering med virtuelt skala sæt), så du kan nemt håndtere øges trafik eller VM mislykkede. Hvis du vil vide mere om Indlæs balancere, skal du læse [Azure belastning oversigt](../load-balancer/load-balancer-overview.md) og [kører flere FOS på Azure for skalerbarhed og tilgængelighed](../guidance/guidance-compute-multi-vm.md).

__Hvad sker der, hvis du ikke bruger en belastningsjustering foran FOS din forbindelse til internettet?__ Uden belastningsjustering du vil ikke kunne skalere ud (Tilføj flere virtuelle maskiner) og kun kan skalere (øge størrelsen af din web mod virtuelle maskine). Du kan også står over for fejl med denne virtuelle maskine fra ét sted. Du skal også skrive DNS-kode for at se, hvis du har mistet en forbindelse til internettet maskine og tilknytte igen din DNS-post til den nye computer, du begynde at tage placering.

###<a name="are-you-using-availability-sets-for-your-stateless-application-and-web-servers"></a>Er du ved hjælp af tilgængelighed indstiller for serverne over programmet på computeren og på internettet uden status?
Hvordan man anbringer dine maskiner i programmet på samme niveau i et sæt tilgængelighed gør din FOS berettiget til Azure VM SERVICENIVEAUAFTALE. Der er en del af et angivet også tilgængelighed sikrer, at dine maskiner er placeret i forskellige opdatering domæner (det vil sige anden vært maskiner, der er installeret på forskellige tidspunkter) og fejl domæner (det vil sige host-computere, der deler en fælles power kilde- og netværk skifte). Din FOS kan være placeret på samme værtsmaskinen uden at være et tilgængelighed sæt, og dermed kan der være et enkelt punkt af fejl, der ikke er synlige for dig. Hvis du gerne vil vide mere om Øget tilgængelighed af din FOS ved hjælp af tilgængelighed sæt, ved at læse [administrere tilgængeligheden af virtuelle maskiner](../virtual-machines/virtual-machines-windows-manage-availability.md).

__Hvad sker der, hvis du ikke bruger en tilgængelighed, der er konfigureret med dine uden status programmer og -webserverne?__ Ikke bruger en tilgængelighed angive betyder, at du ikke kan udnytte Azure VM SERVICENIVEAUAFTALE. Det betyder også, at computere i laget af dit program kunne alle gå offline, hvis der findes en opdatering på værtsmaskinen (den computer, der er vært FOS du bruger) eller en fælles hardwarefejl.

###<a name="are-you-using-virtual-machine-scale-sets-vmss-for-your-stateless-application-or-web-servers"></a>Bruger du virtuelt skala sæt (VMSS) for din uden status program eller web-servere?
Et godt SVG og tolerant design bruger VMSS til at kontrollere, at du kan Forøg/Formindsk antallet af computere i et lag i dit program (såsom din web lag). VMSS gør det muligt at definere, hvordan dit program niveau skaleres (at tilføje eller fjerne servere, der er baseret på kriterier, du vælger). Hvis du vil gerne have yderligere oplysninger om, hvordan du bruger Azure virtuelt skala sæt til at øge din fleksibilitet til trafik spidser, kan du læse [Virtuelt skala sæt oversigt](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

__Hvad sker der, hvis du ikke os et virtuelt skala konfigureret med min uden status anvendelse af webserver?__ Uden et VMSS, kan du begrænse din mulighed for at skalere uden begrænsninger og for at optimere din brug af ressourcer. Et design, der mangler VMSS har en skalering øvre grænse, der skal løses med yderligere kode (eller manuelt). Denne funktion ikke en VMSS betyder også, at dit program ikke nemt kan tilføje og fjerne computere (uanset skala) for at hjælpe dig med at håndtere store spidser trafik (sådanne som under forfremmet, eller hvis dit websted/app/produkt går virus).

###<a name="are-you-using-premium-storage-and-separate-storage-accounts-for-each-of-your-virtual-machines"></a>Bruger du premium lager og særskilt opbevaring konti for hver af dine virtuelle computere?
Det er en god fremgangsmåde at bruge premium lagerplads for dit fremstilling virtuelle computere. Du skal også sørge for, at du bruger en separat lagerplads konto til hver virtual machine (dette er sandt for mindre installationer. For større installationer, kan du genbruge er lagerplads konti til flere computere, men der en afstemning, der skal udføres for at sikre, at du afstemmes på tværs af opdatering domæner og niveauer af dit program). Hvis du vil gerne have yderligere oplysninger på Azure-lager ydeevne og skalerbarhed, kan du læse [Microsoft Azure lagerplads ydeevne og skalerbarhed tjekliste](../storage/storage-performance-checklist.md).

__Hvad sker der, hvis du ikke bruger separat lagerplads konti for hver virtuelt?__ En lagerplads-konto, som mange andre ressourcer er en enkelt udgangspunktet for fejl. Selvom der er mange beskyttelse og fleksibilitet funktioner i Azure-lager, er et enkelt punkt af fejl ved aldrig et godt design. Eksempelvis hvis adgangsrettigheder beskadiget til, at konto, en lagergrænse ramme eller en [IOP'ER begrænse](../azure-subscription-service-limits.md#virtual-machine-disk-limits) er nået, påvirkes alle virtuelle maskiner ved hjælp af lagerplads konto. Hvis der er en tjenesteforstyrrelse, der påvirker et lager stempel, der indeholder bestemt lagerplads konto kan du også har flere virtuelle maskiner påvirkes.

###<a name="are-you-using-a-load-balancer-or-a-queue-between-each-tier-of-your-application"></a>Bruger du en belastningsjustering eller en kø mellem hvert niveau i dit program?
Ved hjælp af belastning balancere eller køer mellem hvert niveau i dit program, kan du nemt at skalere hvert niveau i dit program, nemt og uafhængigt. Du skal vælge mellem disse teknologier, der er baseret på din ventetid, kompleksitet, og fordeling (det vil sige hvor meget du distribuerer din app) skal. Generelt køer har tendens til at have højere ventetid og føje kompleksitet, men du bliver mere robust og gør det muligt at distribuere dit program over større områder drage fordel (såsom på tværs af områder). Hvis du gerne vil have yderligere oplysninger om, hvordan du kan bruge interne Indlæs balancere eller køer, skal du læse [interne belastning oversigt](../load-balancer/load-balancer-internal-overview.md) og [Azure køer og Service Bus køer - sammenlignet og i farvekategorier](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md).

__Hvad sker der, hvis du ikke bruger en justering af belastning eller kø mellem hvert niveau i dit program?__ Uden en justering af belastning, eller kø, mellem hvert niveau i dit program er det svært at skalere dit program, op eller ned og distribuere dens belastning på tværs af flere computere. Hvis du ikke gør dette kan medføre over eller under klargøring af ressourcerne og risiko for nedetid eller dårlig brugeroplevelsen, hvis du har uventede ændringer i mislykkede forsøg på trafik eller system.
 
###<a name="are-your-sql-databases-using-active-geo-replication"></a>Bruger SQL-databaser aktive geografisk replikering? 
Aktive geografisk replikering gør det muligt at konfigurere op til 4 læsbare sekundær databaser i de samme eller forskellige, områder. Sekundær databaser findes tale om en tjenesteforstyrrelse eller med at oprette forbindelse til den primære database. Hvis du vil vide mere om SQL-Database aktive geografisk-gentagelse, skal du læse [Oversigt: SQL aktive geografisk-databasereplikering](../sql-database/sql-database-geo-replication-overview.md).
 
 __Hvad sker der, hvis du ikke bruger active geografisk replikering med SQL-databaser?__ Uden aktive geografisk-gentagelse, hvis din primære database nogensinde går offline (planlagt vedligeholdelse, tjenesteforstyrrelse, hardwarefejl osv.) databasen programmet er offline, indtil du kan overføre din primære database online igen i en sund tilstand. 
 
###<a name="are-you-using-a-cache-azure-redis-cache-in-front-of-your-databases"></a>Bruger du en cache (Azure Redis Cache) foran databaser?
Hvis dit program har en høj database belastning, hvor de fleste af database-opkald er læser, kan du øge hastigheden i dit program og formindske belastningen på databasen ved at implementere et cachelagring lag foran databasen til offload handlingerne Læs. Du kan øge hastigheden i dit program og formindske indlæsning af din database (dermed stigende skalaen den kan håndtere) ved at placere et cachelagring lag foran din database. Hvis du vil have mere at vide om Azure Redis cachen, ved at læse [cachelagring vejledning](../best-practices-caching.md).
 
 __Hvad sker der, hvis du ikke bruger en cache foran databasen?__ Hvis computeren database er effektive nok til at håndtere trafik Indlæs placerer du på den og derefter dit program reagerer som normalt, men det kan betyde, at ved nederste belastning du skal sætte til en database-computer, der er mere omfattende end det er nødvendigt. Hvis computeren database ikke er effektiv opleve nok til at håndtere dine indlæst, og derefter du begynder at føle dårlig bruger med dit program (ventetid, timeout og eventuelt nedetid).
 
###<a name="have-you-contacted-microsoft-azure-support-if-you-are-expecting-a-high-scale-event"></a>Har du kontaktet Microsoft Azure Support, hvis du forventer en høj skala begivenhed?
Azure support kan hjælpe dig med at øge din tjeneste grænser for håndtere planlagte høj trafik begivenheder (som nyt produkt startes eller speciel helligdage). Understøttelse af Azure muligvis også kan hjælpe dig med at oprette forbindelse til eksperter, der kan hjælpe dig med gennemse dit design med kundeservice og hjælpe dig med at finde den bedste løsning til dine behov høj skala begivenhed. Hvis du vil gerne have yderligere oplysninger om, hvordan du kontakte support til Azure, skal du læse [Azure understøtter ofte stillede spørgsmål om](https://azure.microsoft.com/support/faq/).

__Hvad sker der, hvis du ikke kan kontakte Azure Support til en høj skalerbarhed begivenhed?__ Hvis du ikke kan kommunikere eller planlægge, en stor trafik hændelse, risikerer du nå visse [Azure services begrænser](../azure-subscription-service-limits.md) og dermed oprettelse af en dårlig brugeroplevelse (eller værre, nedetid) under begivenheden. Arkitektonisk anmeldelser og kommunikere forud for skader kan hjælpe med at reducere disse risici.

###<a name="are-you-using-a-content-delivery-network-azure-cdn-in-front-of-your-web-facing-storage-blobs-and-static-assets"></a>Bruger du en indhold netværk til levering af (Azure CDN) foran dit web mod lagerplads BLOB og statisk Aktiver?
Ved hjælp af et CDN hjælper dig med at tage Indlæs fra serverne ved at cachelagre dit indhold på CDN POP/kant placeringer, der er placeret over hele verden. Du kan gøre dette for at mindske ventetid, øge skalerbarhed, Formindsk belastning på serveren, og som en del af en strategi for beskyttelse mod nægtelse af service(DOS) angreb. Hvis du vil gerne have yderligere oplysninger om, hvordan du bruger Azure CDN at øge din fleksibilitet og formindske din kunde ventetid, kan du læse [Oversigt over Azure indhold levering netværk (CDN)](../cdn/cdn-overview.md).

__Hvad sker der, hvis du ikke bruger et CDN?__ Hvis du ikke bruger et CDN, derefter alle din kunde trafik leveres direkte til dine ressourcer. Det betyder, du vil se højere belastning på serverne, reduceres deres skalerbarhed. Desuden kan kunderne opleve højere latenstider CDNs tilbyder placeringer over hele verden, som har sandsynligvis tættere til dine kunder.

##<a name="next-steps"></a>Næste trin:
Hvis du gerne vil læse mere om, hvordan til at designe dine programmer til høj tilgængelighed, skal du læse [høj tilgængelighed for bygget på Microsoft Azure-programmer](resiliency-high-availability-azure-applications.md).
