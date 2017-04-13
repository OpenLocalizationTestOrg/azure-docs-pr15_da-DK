<properties 
    pageTitle="Azure køer og Service Bus kø - sammenlignet og i farvekategorier | Microsoft Azure"
    description="Analyserer forskellene og lighederne mellem to typer køer, som Azure."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="tbd"
    ms.date="09/23/2016"
    ms.author="sethm" />

# <a name="azure-queues-and-service-bus-queues---compared-and-contrasted"></a>Azure køer og Service Bus kø - sammenlignet og i farvekategorier

I denne artikel analyserer forskellene og lighederne mellem de to typer køer, som Microsoft Azure i dag: Azure køer og Service Bus køer. Ved hjælp af disse oplysninger, kan du sammenligner og kontrast de respektive teknologier og foretage en bedre beslutning om, hvilken løsning der bedst opfylder dine behov.

## <a name="introduction"></a>Introduktion

Microsoft Azure understøtter to typer kø mekanismer: **Azure køer** og **Bus servicekøer**.

**Azure køer**, som er en del af infrastrukturen [Azure-lager](https://azure.microsoft.com/services/storage/) , funktioner en enkel RESTEN-baserede få/læg/oversigten grænseflade, give pålidelige, fast messaging i og mellem tjenester.

**Service Bus køer** er en del af en bredere [Azure messaging](https://azure.microsoft.com/services/service-bus/) infrastruktur, der understøtter queuing samt udgivelse/abonnement, Web service remoting og integration mønstre. Se [Oversigt over Service Bus messaging](service-bus-messaging-overview.md)kan finde flere oplysninger om Service Bus køer, emner/abonnementer og relæer.

Mens begge kø teknologier findes samtidig, indført Azure køer først som en dedikeret kø lagerplads metode til indbygget oven på Azure lagerplads tjenesterne. Service Bus køer er opbygget oven på den bredere "formidlet messaging" infrastruktur, der er udviklet til at integrere programmer eller programkomponenter, der kan spænder over flere kommunikationsprotokoller, aftaler, data, har du tillid til domæner og/eller miljøer.

I denne artikel sammenlignes de to kø teknologier, ved at Azure som omhandler forskellene i opførsel og implementering af de funktioner, som hver. I artiklen indeholder også vejledning til valg af, hvilke funktioner kan bedst passer til dine behov til udvikling af programmet.

## <a name="technology-selection-considerations"></a>Overvejelser i forbindelse med teknologi markering

Både Azure køer og Service Bus køer er MSMQ service tilbydes aktuelt på Microsoft Azure-installationer. Hver har en smule funktionssæt, hvilket betyder, at du kan vælge en eller en anden, eller brug begge, afhængigt af din bestemte løsning eller business/tekniske problemer, du løsningsmetoder behov.

Når afgøre, hvilken kø teknologi til formålet med en given løsning, skal løsningsarkitekter og udviklere overveje nedenstående anbefalinger. Få mere at vide ved at se næste afsnit.

Som en løsning architect/udvikler, **du bør overveje at bruge Azure køer** når:

- Dit program skal mere end 80 GB meddelelser i en kø, hvor meddelelserne, der har en mindre end 7 dage levetid gemmes.

- Dit program ønsker at registrere status for opgaver til at behandle en meddelelse i køen. Dette er nyttigt, hvis den arbejder behandling af en meddelelse går ned. En efterfølgende arbejder kan derefter bruge disse oplysninger til at fortsætte fra hvor den foregående arbejder slap.

- Du har brug for server side logge af alle de transaktioner, køres i forhold til Køstyring.

Som en løsning architect/udvikler, **du bør overveje at bruge Service Bus køer** når:

- Din løsning skal kunne modtage meddelelser uden at skulle afstemning køen. Med tjenesten Bus dette kan opnås ved hjælp af lang-afstemning modtager ved hjælp af de TCP-baserede protokoller, der understøtter Service Bus.

- Din løsning kræver køen til at levere en garanterede first-i-first-out (FIFO) bestilt levering.

- Du vil have en symmetriske oplevelse i Azure og på Windows Server (privat skyen). Du kan finde yderligere oplysninger finder [Service Bus til Windows Server](https://msdn.microsoft.com/library/dn282144.aspx).

- Din løsning skal kunne understøtte automatisk dubletter.

- Du vil dit program tilladelse til at behandle meddelelser som parallelle længerevarende streams (meddelelser er knyttet til en stream ved hjælp af [Session-id-](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) egenskaben på meddelelsen). I denne model konkurrerer hver node i programmet på arket til streams, i modsætning til meddelelser. Når en stream er angivet til en lang node, kan noden undersøge tilstanden for tilstanden programmet værdistrøm ved hjælp af transaktioner.

- Din løsning kræver transaktions funktionsmåde og atomicitet ved afsendelse og modtagelse flere meddelelser fra en kø.

- Time to live (TTL) egenskab ved programmet-specifikke arbejdsbelastningen kan overskride 7-dages perioden.

- Programmet håndterer meddelelser, som kan være højere end 64 KB, men ikke sandsynligvis tilgang 256 KB begrænser.

- Du tage dig af et krav til at levere en rollebaseret access model til køer og andre rights/tilladelser til afsendere og modtagere.

- Størrelsen af din kø vokser ikke større end 80 GB.

- Du vil bruge den AMQP 1.0 baseret på standarder SMS-protokollen. Du kan finde flere oplysninger om AMQP, [Service Bus AMQP oversigt](./service-bus-amqp-overview.md).

- Du kan få oplysninger om en eventuel overførsel fra kø-baserede punkt til punkt kommunikation til en meddelelse exchange mønster, der gør det muligt for nem integration af flere modtagere (abonnenter), hver især modtager uafhængige kopier af nogle eller alle meddelelser, som sendes til køen. Disse refererer til Udgiv/Abonner muligheden for indbygget leveres af Service Bus.

- Din SMS løsning skal kunne understøtte "På de fleste-én gang" levering GARANTIBRUD uden brug af for dig at opbygge ekstra infrastruktur til komponenter.

- Du vil kunne publicere og forbruge batches med meddelelser.

- Du har brug for fuld integration med Windows Communication Foundation (WCF) kommunikation stablen i .NET Framework.

## <a name="comparing-azure-queues-and-service-bus-queues"></a>Sammenligne Azure køer og Service Bus køer

Tabellerne i de følgende afsnit giver en logisk gruppering af kø funktioner og gør det muligt at sammenligne hurtigt, de funktioner, der er tilgængelige i både Azure køer og Service Bus køer.

## <a name="foundational-capabilities"></a>Finde grundlæggende funktioner

Dette afsnit sammenlignes nogle af de grundlæggende kø funktioner, der leveres af Azure køer og Service Bus køer.

|Sammenligningskriterier|Azure køer|Service Bus køer|
|---|---|---|
|Rækkefølge GARANTIBRUD|**Nej** <br/><br>Du kan finde yderligere oplysninger finder den første note i afsnittet "Yderligere oplysninger".</br>|**Ja - første-i-første-Out (FIFO)**<br/><br>(ved hjælp af messaging sessioner)|
|Levering GARANTIBRUD|**Med mindst-det samme**|**Med mindst-det samme**<br/><br/>**I de fleste-én gang**|
|Understøttelse af atomisk handling|**Nej**|**Ja**<br/><br/>|
|Modtage funktionsmåde|**Ikke-blokering**<br/><br/>(afsluttes med det samme Hvis der findes ingen ny meddelelse)|**Blokering af med/uden timeout**<br/><br/>(tilbud lange afstemning eller ["Komet metode"](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Ikke-blokering**<br/><br/>(ved hjælp af .NET managed API kun)|
|Push-typografi API|**Nej**|**Ja**<br/><br/>[OnMessage](https://msdn.microsoft.com/library/azure/jj908682.aspx) og **OnMessage** sessioner .NET API.|
|Modtage tilstand|**Kig & Lease**|**Kig og låse**<br/><br/>**Modtage og Slet**|
|Tilstand med udelt adgang|**Bortlease-baseret**|**Lås-baseret**|
|Bortlease/Lås varighed|**30 sekunder (standard)**<br/><br/>**7 dage (maksimum)** (Du kan forny eller frigive en meddelelse rettighed ved hjælp af [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx) API).|**60 sekunder (standard)**<br/><br/>Du kan forny en meddelelse Lås ved hjælp af [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) API.|
|Bortlease/Lås præcision|**Meddelelse niveau**<br/><br/>(alle meddelelser, kan have en anden timeoutværdien, som du kan derefter opdatere efter behov under behandling af meddelelsen ved hjælp af [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx) API)|**Kø niveau**<br/><br/>(hver kø har en lås præcision, der er anvendt på alle dens meddelelser, men du kan forny låsen ved hjælp af [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) API).|
|Batches modtager|**Ja**<br/><br/>(eksplicit angive antal meddelelser under hentning af meddelelser, op til maksimalt 32 meddelelser)|**Ja**<br/><br/>(Hvis du aktiverer en foreløbig Hent egenskab implicit eller eksplicit ved hjælp af transaktioner)|
|Batchopdelte send|**Nej**|**Ja**<br/><br/>(ved hjælp af transaktioner eller klientsiden samling)|

### <a name="additional-information"></a>Yderligere oplysninger

- Meddelelser i Azure køer er typisk first-i-first-out, men de kan nogle gange kan defekt; for eksempel, hvor varigheden af en meddelelse synlighed timeout udløber (for eksempel som et resultat af en ned under behandling-klientprogrammet). Når synlighed timeout udløber, vises meddelelsen igen i køen for en anden medarbejder til annullering i kø den. På dette tidspunkt kan nyligt synlige meddelelsen være placeret i køen (til dequeued igen) efter en meddelelse, som oprindeligt blev sat i kø efter den.

- Hvis du allerede bruger Azure lagerplads BLOB eller tabeller, og du begynde at bruge køer, er du garanti 99,9% tilgængelighed. Hvis du bruger BLOB eller tabeller med Service Bus køer, har du nederste tilgængelighed.

- Garanterede FIFO mønstret i Service Bus køer kræver brug af SMS sessioner. I tilfælde af, at programmet går ned under behandling af en meddelelse, der er modtaget i tilstanden **oversigten og Lås** , starter næste gang en kø modtager accepterer en SMS-session, den med mislykkedes meddelelsen, når dens time to live (TTL) er udløbet.

- Azure køer er designet til at understøtte standard kø scenarier, som afkobling programkomponenter for at øge skalerbarhed og tolerance for fejl, indlæse udjævning, og udvikling af arbejdsprocesser.

- Service Bus køer understøtter *Med mindste-det samme* levering garantien. *Med de fleste-det samme* semantisk kan desuden være understøttes, ved hjælp af sessionstilstand til at gemme application-tilstand og ved hjælp af transaktioner skal modtage meddelelser og opdatere sessionstilstanden atomically.

- Azure køer indeholder en ensartet og ensartede programming model på tværs af køer, tabeller og BLOB – både for udviklere og for handlinger teams.

- Service Bus køer yde support til lokale transaktioner i konteksten af en enkelt kø.

- **Modtage og slette** tilstanden understøttes af Service Bus giver mulighed for at reducere SMS handlingen TÆL (og tilknyttede omkostninger) mod sænket levering assurance.

- Azure køer giver rettigheder muligheden for at udvide rettigheder til meddelelser. Dette giver mulighed for medarbejdere til at vedligeholde korte rettigheder på meddelelser. Dermed, hvis en bruger, der går ned, meddelelsen kan hurtigt behandles igen af en anden medarbejder. Desuden kan en arbejder udvide bortlease på en meddelelse, hvis den skal behandle den længere end den aktuelle bortlease tid.

- Azure køer tilbyder en synlighed timeout, kan du angive efter enqueueing eller Sådan flytning af en meddelelse. Desuden kan du opdatere en meddelelse med forskellige bortlease værdier på kørselstidspunktet, og du kan opdatere forskellige værdier på tværs af meddelelser i den samme kø. Service Bus Lås timeout er defineret i kø metadataene. dog kan du forny låsen ved at ringe til metoden [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) .

- Den maksimale timeout er for blokering af en modtager handling i tjenesten Bus køer 24 dage. Men RESTEN-baserede timeout har den største værdi af 55 sekunder.

- Klientsiden samling leveres af Service Bus gør det muligt for en kø klient til batchen flere meddelelser til en enkelt send handling. Samling er kun tilgængelig for asynkron send handlinger.

- Funktioner som 200 TB loftet over Azure køer (mere, når du virtualiserer konti) og ubegrænset køer gør det til en ideel platform for SaaS udbydere.

- Azure køer giver en fleksibel og performant delegerede access kontrolelement ordning.

## <a name="advanced-capabilities"></a>Avancerede funktioner

Dette afsnit sammenligner avancerede funktioner i Azure køer og Service Bus køer.

|Sammenligningskriterier|Azure køer|Service Bus køer|
|---|---|---|
|Planlagt levering|**Ja**|**Ja**|
|Automatisk inaktive bogstaver|**Nej**|**Ja**|
|Øge kø time to live værdi|**Ja**<br/><br/>(via direkte opdatering af synlighed timeout)|**Ja**<br/><br/>(leveres via en dedikeret API-funktion)|
|Meddelelse understøttelse af uautoriserede|**Ja**|**Ja**|
|Lokal opdatering|**Ja**|**Ja**|
|Serversiden transaktionslog|**Ja**|**Nej**|
|Lagringsmål|**Ja**<br/><br/>**Minut målepunkter**: giver realtid måleredskaber for tilgængelighed, Kreative, API ringe tæller, fejl tæller og mere alle i realtid (aggregeret i minuttet og rapporteret efter et par minutter fra hvad lige er der sket fremstilling. Du kan finde yderligere oplysninger [Om Analytics Lagringsmål](https://msdn.microsoft.com/library/azure/hh343258.aspx).|**Ja**<br/><br/>(flere forespørgsler ved at ringe til [GetQueues](https://msdn.microsoft.com/library/azure/hh293128.aspx))|
|Administration af stat|**Nej**|**Ja**<br/><br/>For [Microsoft.ServiceBus.Messaging.EntityStatus.Active](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx)|
|Videresendelse automatisk-|**Nej**|**Ja**|
|Fjern kø funktionen|**Ja**|**Nej**|
|Meddelelse grupper|**Nej**|**Ja**<br/><br/>(ved hjælp af messaging sessioner)|
|Programtilstanden hver meddelelse gruppe|**Nej**|**Ja**|
|Registrering af dubletter|**Nej**|**Ja**<br/><br/>(kan konfigureres i afsender side)|
|WCF-integration|**Nej**|**Ja**<br/><br/>(tilbyder out box WCF bindinger)|
|Windows Firewall-integration|**Brugerdefineret**<br/><br/>(kræver opbygning af en brugerdefineret Windows Firewall-aktivitet)|**Oprindelig**<br/><br/>(tilbyder out box Windows Firewall aktiviteter)|
|Gennemse meddelelse grupper|**Nej**|**Ja**|
|Henter meddelelse sessioner efter ID|**Nej**|**Ja**|

### <a name="additional-information"></a>Yderligere oplysninger

- Begge kø teknologier aktivere meddelelsen planlægges til levering på et senere tidspunkt.

- Kø automatisk viderestilling aktiverer tusindvis af køer til automatisk-fremad deres meddelelser til en enkelt kø, hvorfra programmet modtagelse forbruger meddelelsen. Du kan bruge denne funktion til at opnå sikkerhed, Kontrolflow og isolere lagerplads mellem hver meddelelse publisher.

- Azure køer yde support til opdatering af meddelelsesindhold. Du kan bruge denne funktion for faste tilstandsoplysninger og stigende statusopdateringer i meddelelsen, så den kan behandles fra det sidste kendte kontrolpunkt i stedet for at starte fra bunden. Med Service Bus køer, kan du aktivere det samme scenario ved hjælp af meddelelse sessioner. Sessioner gør det muligt at gemme og hente application behandling-tilstand (ved hjælp af [SetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx) og [GetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx)).

- [Inaktive bogstaver](service-bus-dead-letter-queues.md), som kun understøttes af Service Bus køer, kan være nyttige til at isolere meddelelser, der ikke kan behandles korrekt ved modtagelse programmet, eller når meddelelser kan nå deres destination på grund af en udløbet time to live (TTL) egenskab. TTL-værdien angiver, hvor lang tid en meddelelse forbliver i køen. Med tjenesten Bus flyttes meddelelsen til en speciel kø kaldet $DeadLetterQueue, når TTL udløber.

- Hvis du vil finde "uautoriserede" meddelelser i Azure køer, når Sådan flytning meddelelsen programmet undersøger, om egenskaben **[DequeueCount](https://msdn.microsoft.com/library/azure/dd179474.aspx)** for meddelelsen. Hvis **DequeueCount** er over en given tærskelværdi, flytter meddelelsen til en brugerdefineret program "blind bogstavet" kø i programmet.

- Azure muliggør dig at få en detaljeret logfil over alle de transaktioner, køres i forhold til køen, som også som aggregeret målepunkter. Begge disse indstillinger er nyttige til fejlfinding og forstå, hvordan dit program bruger Azure køer. De kan også bruges til-justering af ydeevnen dit program og reducere omkostningerne ved brug af køer.

- Begrebet "meddelelse sessioner" understøttes af Service Bus gør det muligt for meddelelser, der hører til en bestemt logiske gruppe der skal knyttes til en given modtager, der også opretter en session-lignende forbindelse mellem meddelelser og deres respektive modtagere. Du kan aktivere denne avancerede funktioner i Service Bus ved at angive egenskaben [Session-id](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) på en meddelelse. Modtagere kan derefter lytter på et bestemt session-ID og modtage meddelelser, der har det angivne session-id.

- Funktionen kopiering registrering, der understøttes af Service Bus køer automatisk fjerner dublerede meddelelser, som sendes til en kø eller emne, baseret på værdien af egenskaben [MessageID](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) .

## <a name="capacity-and-quotas"></a>Kapacitet og kvoter

Dette afsnit sammenligner Azure køer og Service Bus køer fra perspektivet [kapacitet og kvoter](service-bus-quotas.md) , der gælder muligvis.

|Sammenligningskriterier|Azure køer|Service Bus køer|
|---|---|---|
|Maksimale kø størrelse|**200 TB**<br/><br/>(begrænset til en enkelt konto lagerkapacitet)|**1 GB til 80 GB**<br/><br/>(defineret efter oprettelse af en kø og [aktivere partitionering](service-bus-partitioning.md) – se afsnittet "Yderligere oplysninger")|
|Maksimumstørrelse meddelelser|**64 KB**<br/><br/>(48 KB når **Base64** tegnsæt)<br/><br/>Azure understøtter lange meddelelser ved at kombinere køer og BLOB – på dette tidspunkt kan du enqueue op til 200GB for et enkelt element.|**256 KB** eller **1 MB**<br/><br/>(herunder både brevhovedet og meddelelsesteksten, maksimale sidehoved størrelse: 64 KB).<br/><br/>Afhænger af [webtjenesten](service-bus-premium-messaging.md).|
|Maksimale meddelelse TTL|**7 dage**|**`TimeSpan.Max`**|
|Maksimale antal køer|**Ubegrænset**|**10.000**<br/><br/>(hver tjeneste navneområde, øges)|
|Maksimale antal samtidige klienter|**Ubegrænset**|**Ubegrænset**<br/><br/>(grænsen for 100 samtidige forbindelser gælder kun for TCP-protokollen-baseret kommunikation)|

### <a name="additional-information"></a>Yderligere oplysninger

- Service Bus håndhæves kø begrænsninger. Den maksimale kø størrelse er angivet efter oprettelse af køen og kan have en værdi mellem 1 og 80 GB. Hvis værdien kø størrelse er angivet på oprettelse af køen er nået, afvises yderligere indgående meddelelser, og en undtagelse kan modtages af koden opkald. Se [Service Bus kvoter](service-bus-quotas.md)kan finde flere oplysninger om kvoter i Service Bus.

- Du kan oprette Service Bus køer i 1, 2, 3, 4 eller 5 GB størrelser (standard er 1 GB). Med partitionering (som er standard), Service Bus opretter 16 partitioner for hver GB, du angiver. Således, hvis du opretter en kø, der er 5 GB i størrelse, med 16 partitioner den maksimale kø størrelse bliver (5 * 16) = 80 GB. Du kan se den maksimale størrelse på din partitioneret kø eller emne ved at kigge på tilbagekaldelsen på [Azure-portalen][].

- Med Azure køer, hvis indholdet af meddelelsen ikke er XML-sikker, være skal den **Base64** -kodet. Hvis du **Base64**-kode meddelelsen, bruger data kan være op til 48 KB, i stedet for 64 KB.

- Med Service Bus køer, alle de meddelelser, der er gemt i en kø består af to dele: et sidehoved og en brødtekst. Den samlede størrelse af meddelelsen kan ikke overstiger den maksimale meddelelsesstørrelse, der understøttes af webtjenesten.

- Når klienter kommunikerer med Service Bus køer via TCP-protokollen, er det maksimale antal samtidige forbindelser til en enkelt Service Bus kø er begrænset til 100. Dette nummer deles mellem afsendere og modtagere. Hvis denne kvote er nået, afvises efterfølgende anmodninger om flere forbindelser, og en undtagelse kan modtages af koden opkald. Dette er ikke grænse i at oprette forbindelse til køerne ved hjælp af REST-baseret API-klienter.

- Hvis du kræver mere end 10.000 køer i et enkelt Service Bus navneområde, kan du kontakte Azure supportteamet og anmode om en forøgelse. Hvis du vil skalere ud over 10.000 køer med Service Bus, kan du også oprette flere navneområder ved hjælp af [Azure-portalen][].

## <a name="management-and-operations"></a>Administration og handlinger

Dette afsnit sammenligner funktioner for administration af fra Azure køer og Service Bus køer.

|Sammenligningskriterier|Azure køer|Service Bus køer|
|---|---|---|
|Management-protokollen|**HEN til HTTP/HTTPS**|**HEN til HTTPS**|
|Runtime protokol|**HEN til HTTP/HTTPS**|**HEN til HTTPS**<br/><br/>**AMQP 1.0 Standard (TCP med TLS)**|
|.NET administrerede API|**Ja**<br/><br/>(.NET administrerede Storage klient API)|**Ja**<br/><br/>(.NET administrerede formidlet SMS API)|
|Lokal C++|**Ja**|**Nej**|
|Java API|**Ja**|**Ja**|
|PHP API|**Ja**|**Ja**|
|Node.js API|**Ja**|**Ja**|
|Understøttelse af vilkårlig metadata|**Ja**|**Nej**|
|Reglerne for navngivning af kø|**Op til 63 tegn**<br/><br/>(Bogstaver i et kø navn skal være små bogstaver).|**Op til 260 tegn**<br/><br/>(Kø kurver og navnene er case-insensitive.)|
|Få kø længde funktionen|**Ja**<br/><br/>(Omtrentlig værdi, hvis meddelelser udløber ud over TTL uden at blive slettet.)|**Ja**<br/><br/>(Nøjagtige, punkt-in-time værdi.)|
|Funktionen oversigten|**Ja**|**Ja**|

### <a name="additional-information"></a>Yderligere oplysninger

- Azure køer yde support til vilkårlig attributter, der kan anvendes på beskrivelsen kø i form af navn/værdi-par.

- Begge kø teknologier gør muligheden at Vis en meddelelse uden at skulle låse den, hvilket kan være nyttigt, når du implementerer et kø explorer/browser værktøj.

- Tjenesten Bus .NET formidlet SMS API'er udnyttelse fuld dupleks TCP-forbindelser til forbedret ydeevne, når der sammenlignes med RESTEN via HTTP, og de understøtter den standard AMQP 1.0-protokollen.

- Navnene på Azure køer kan være 3-63 tegn lang tid, kan indeholde små bogstaver, tal og bindestreger. Få mere at vide under [navngivning af køer og Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).

- Service Bus kø navne kan være op til 260 tegn og har mindre restriktiv naming regler. Service Bus kø navne kan indeholde bogstaver, tal, perioder, bindestreger og understregningstegn.

## <a name="authentication-and-authorization"></a>Godkendelse og autorisation

Dette afsnit beskrives de godkendelse og autorisation funktioner, der understøttes af Azure køer og Service Bus køer.

|Sammenligningskriterier|Azure køer|Service Bus køer|
|---|---|---|
|Godkendelse|**Symmetriske nøgle**|**Symmetriske nøgle**|
|Sikkerhedsmodel|Delegeret adgang via SAS tokens.|SAS|
|Identitetssammenslutninger udbyder|**Nej**|**Ja**|

### <a name="additional-information"></a>Yderligere oplysninger

- Alle anmodninger til en af de kø teknologier skal godkendes. Offentlige køer med anonym adgang understøttes ikke. Brug af [SAS](service-bus-sas-overview.md), kan du løse dette scenario ved at publicere en kun skrive SAS, skrivebeskyttet SAS eller endda SAS en fuld adgang.

- Godkendelsesskemaet fra Azure køer involverer brug af en symmetriske nøgle, som er en hash-baseret meddelelse godkendelse kode (HMAC), beregnes med algoritmen SHA 256 og kodet som en **Base64** -streng. Du kan finde flere oplysninger om den pågældende protokol, [godkendelse til Azure lagerplads Services](https://msdn.microsoft.com/library/azure/dd179428.aspx). Service Bus køer understøtter en lignende model ved hjælp af symmetriske nøgler. Se [Delt Access signatur godkendelse med Service Bus](service-bus-shared-access-signature-authentication.md)kan finde flere oplysninger.

## <a name="cost"></a>Omkostninger

Dette afsnit sammenligner Azure køer og Service Bus køer fra et økonomisk synspunkt.

|Sammenligningskriterier|Azure køer|Service Bus køer|
|---|---|---|
|Kø transaktion omkostninger|**$0.0036**<br/><br/>(per 100.000 transaktioner)|**Grundlæggende trin**: **$0,05**<br/><br/>(per millioner handlinger)|
|Fakturerbar handlinger|**Alle**|**Send/modtag kun**<br/><br/>(ingen gebyr for andre handlinger)|
|Inaktiv transaktioner|**Fakturerbar**<br/><br/>(Forespørgsler en tom kø tælles som en fakturerbar transaktion.)|**Fakturerbar**<br/><br/>(En Modtag mod en tom kø betragtes meddelelsen fakturerbar).|
|Lagerplads omkostninger|**$0.07**<br/><br/>(månedsbasis GB /)|**kr 0,00**|
|Udgående dataoverførsel omkostninger|**$0.12 - $0.19**<br/><br/>(Afhængigt af Geografi.)|**$0.12 - $0.19**<br/><br/>(Afhængigt af Geografi.)|

### <a name="additional-information"></a>Yderligere oplysninger

- Data med pengeoverførsler betaler baseret på den samlede mængde af data, der forlader Azure datacentre via internettet i en given faktureringsperiode.

- Data med pengeoverførsler mellem Azure tjenester, der er placeret i det samme område er ikke underlagt gebyr.

- Og denne du skriver er alle indgående dataoverførsler ikke underlagt gebyr.

- Givet fra support for lange afstemning, kan ved hjælp af Service Bus køer være rentable i situationer, hvor lav ventetid levering er påkrævet.

>[AZURE.NOTE] Alle omkostninger kan blive ændret. Denne tabel afspejler aktuelle priser og omfatter ikke alle tilbud, der aktuelt er tilgængelige. Finde opdaterede oplysninger om Azure priser, på siden [Azure priser](https://azure.microsoft.com/pricing/) . Du kan finde flere oplysninger om Service Bus priser, se [Service Bus priser](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="conclusion"></a>Konklusion

Ved at få en bedre forståelse af de to teknologier, du vil kunne gøre en bedre beslutning om, hvilke kø-teknologi, der skal bruges, og hvornår. Beslutning til at bruge Azure køer eller Service Bus køer klart afhænger af en række faktorer. Disse faktorer kan afhænge af stærkt individuelle behovet i dit program og dets arkitektur. Hvis dit program allerede bruger de grundlæggende funktioner i Microsoft Azure, kan du foretrækker at vælge Azure køer, især hvis du har brug for grundlæggende kommunikation og messaging mellem tjenester eller har du brug for køer, som kan være større end 80 GB.

Da Service Bus køer giver et antal avancerede funktioner, som sessioner, transaktioner, registrering af, automatisk dubletter inaktive bogstaver og robust Udgiv/Abonner-funktioner, de kan være foretrukne valg, hvis du opbygger en hybrid-programmet, eller hvis programmet ellers kræver disse funktioner.

## <a name="next-steps"></a>Næste trin

I følgende artikler indeholder flere vejledning og oplysninger om brug af Azure køer eller Service Bus køer.

- [Sådan bruger du Bus servicekøer](service-bus-dotnet-get-started-with-queues.md)
- [Hvordan du bruger tjenesten kø Storage](../storage/storage-dotnet-how-to-use-queues.md)
- [Bedste fremgangsmåder til forbedring af ydeevnen ved hjælp af Service Bus formidlet messaging](service-bus-performance-improvements.md)
- [Introduktion til køer og emner i Azure Service Bus](http://www.code-magazine.com/article.aspx?quickid=1112041)
- [Den udvikler vejledning til Service Bus](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
- [Ved hjælp af tjenesten kø i Azure](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)
- [Forstå Azure lagerplads fakturering – båndbredde, transaktioner og kapacitet](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

[Azure-portalen]: https://portal.azure.com
 
