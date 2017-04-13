<properties
  pageTitle="Indlæse belastningsjusteringstjenesten brugerdefinerede sonder og overvåge systemtilstand status | Microsoft Azure"
  description="Lær at bruge brugerdefinerede sonder til Azure justering af belastning til at overvåge forekomster bag justering af belastning"
  services="load-balancer"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure-resource-manager"
/>
<tags
  ms.service="load-balancer"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="10/24/2016"
  ms.author="sewhee" />

# <a name="understand-load-balancer-probes"></a>Forstå Indlæs belastningsjusteringstjenesten sonder

Azure justering af belastning giver mulighed for at overvåge tilstanden for forekomster af server ved hjælp af sonderne. Når en efterprøvning af af ikke svarer, stopper justering af belastning sende nye forbindelser til den forekomst, beskadiget. De eksisterende forbindelser påvirkes ikke, og der sendes nye forbindelser til sund forekomster.

Skybaseret tjeneste roller (arbejder roller og web roller) Brug en gæst agent for efterprøvning af af overvågning. TCP- eller HTTP brugerdefinerede sonder skal konfigureres, når du bruger virtuelle maskiner bag justering af belastning.

## <a name="understand-probe-count-and-timeout"></a>Forstå efterprøvning af af Tæl og timeout

Efterprøvning af af afhænger:

- Antallet af vellykket sonder, der tillader en forekomst for at være mærket som op.
- Antallet af mislykkede sonder, der kan medføre en forekomst for at være mærket som ned.

Værdien timeout og frekvens er angivet i SuccessFailCount afgør, om en forekomst er bekræftet til at køre eller ikke kører. I portalen Azure, er timeouten angivet til to gange værdien for hyppigheden.

Efterprøvning af af konfigurationen af alle Indlæs afstemmes forekomster til et slutpunkt (det vil sige, et Indlæs afstemmes sæt) skal være den samme. Det betyder, at du ikke har en anden efterprøvning af af konfiguration for hver rolle forekomst eller virtuelt i den samme hostet tjeneste for en bestemt slutpunkt kombination. For eksempel skal hver forekomst have identiske lokale porte og timeout.

>[AZURE.IMPORTANT] En justering af belastning efterprøvning af af bruger IP-adresse 168.63.129.16. Denne offentlige IP-adresse letter kommunikation til interne platform ressourcer for den Flyt-dine-ejer-IP-Azure virtuelle netværk scenarie. Den virtuelle offentlige IP-adresse 168.63.129.16 bruges i alle områder og ændrer ikke. Vi anbefaler, at du tillader denne IP-adresse i en lokal firewall politikker. Det skal ikke betragtes en sikkerhedsrisiko fordi kun den interne Azure platform kan datakilde en meddelelse fra, som omhandler. Hvis du ikke gør dette, vil der være uventet funktionsmåde i en række forskellige scenarier som konfiguration af den samme IP-adresseområder af 168.63.129.16 og har dubleret IP-adresser.

## <a name="learn-about-the-types-of-probes"></a>Få mere at vide om typerne sonder

### <a name="guest-agent-probe"></a>Efterprøvning af agent af gæst

Denne efterprøvning af af er kun tilgængelig for Azure Cloud Services. Justering af belastning anvender gæst agent i den virtuelle maskine, og klik derefter lytter og svarer med et svar med HTTP 200 OK, når forekomsten er i tilstanden klar (det vil sige, ikke i et andet land som optaget, genbrug eller stoppe).

Se [konfigurere definitionsfil service (csdef) for systemtilstand sonder](https://msdn.microsoft.com/library/azure/ee758710.aspx) eller [få en introduktion til en forbindelse til internettet justering af belastning til skytjenester](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services)kan finde flere oplysninger.

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>Hvad gør en gæst agent rør, markere en forekomst som beskadiget?

Hvis gæst agent ikke svarer med HTTP 200 OK, belastning markerer forekomsten af som ikke svarer og holder op med at sende trafik til den pågældende forekomst. Justering af belastning fortsætter med at pinge forekomsten. Hvis gæst agent reagerer med en HTTP 200, sender justering af belastning trafik til den pågældende forekomst igen.

Når du bruger en web rolle, koden websted typisk kører i w3wp.exe, som ikke overvåges af Azure-strukturen eller gæst agent. Det betyder, at der rapporteres ikke fejl i w3wp.exe (for eksempel HTTP 500 svar) til gæst agent, og justering af belastning træder ikke i forekomsten af rotation.

### <a name="http-custom-probe"></a>HTTP efterprøvning af brugerdefinerede af

Brugerdefineret HTTP justering af belastning efterprøvning af af tilsidesætter den standard gæst agent efterprøvning af af, hvilket betyder, at du kan oprette din egen brugerdefinerede logik til at afgøre tilstanden for forekomsten rolle. Justering af belastning sonder slutpunktet hver 15 sekunder, som standard. Forekomsten betragtes som skal være i Indlæs belastningsjusteringstjenesten rotation, hvis det reagerer med en HTTP 200 i timeout-perioden (31 sekunder som standard).

Det kan være nyttigt, hvis du vil implementere din egen logik for at fjerne forekomster fra Indlæs belastningsjusteringstjenesten rotation. For eksempel kan du vælger at fjerne en forekomst, hvis den er større end 90% CPU, og returnerer en ikke-200 status. Hvis du har web roller, der bruger w3wp.exe, det betyder også får du automatisk overvågning af dit websted, fordi fejl i dit websted kode vender statussen ikke 200 til Indlæs belastningsjusteringstjenesten efterprøvning af af.

>[AZURE.NOTE] Den brugerdefinerede HTTP-test understøtter relative stier og HTTP-protokollen. HTTPS understøttes ikke.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>Hvad gør en HTTP brugerdefinerede efterprøvning af af markere en forekomst som beskadiget?

- HTTP-programmet returnerer en HTTP-svarkode end 200 (for eksempel 403, 404 eller 500). Dette er en positiv kvittering, der forekomsten programmet skal udføres af tjenesten det samme.
- HTTP-serveren svarer ikke på efter timeout-perioden. Afhængigt af værdien for timeout, der er angivet, kan det betyde, flere efterprøvning af af anmoder om gå ubehandlede før efterprøvning af af bliver markeret som ikke kører (det vil sige, før SuccessFailCount sonder sendes).
- Serveren afbryder forbindelsen via en nulstilling af TCP.

### <a name="tcp-custom-probe"></a>TCP efterprøvning af brugerdefinerede af

TCP sonder igangsætte en forbindelse ved at udføre en tre-vejs-handshake med den angivne port.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>Hvad gør en TCP brugerdefinerede efterprøvning af af markere en forekomst som beskadiget?

- TCP-serveren svarer ikke på efter timeout-perioden. Når efterprøvning af af er markeret som ikke kører, afhænger af antallet af mislykkede forespørgsler, der er konfigureret til at gå ubesvarede før markeres efterprøvning af af som ikke kører.
- Efterprøvning af af modtager en TCP nulstille fra den rolle forekomst.

Du kan finde flere oplysninger om konfiguration af efterprøvning af af en HTTP-tilstand eller en TCP efterprøvning af af kan du se [få en introduktion til justering af belastning i ressourcestyring ved hjælp af PowerShell en forbindelse til internettet](load-balancer-get-started-internet-arm-ps.md#create-lb-rules-nat-rules-a-probe-and-a-load-balancer).

## <a name="add-healthy-instances-back-into-load-balancer-rotation"></a>Tilføje sund forekomster i Indlæs belastningsjusteringstjenesten rotation igen

TCP og HTTP sonder betragtes som sund og markere forekomsten af rolle som sund når:

- Justering af belastning får et positivt efterprøvning af af første gang VM starter.
- Det tal SuccessFailCount (beskrevet tidligere) definerer værdien af vellykket sonder, der er nødvendige for at markere forekomsten af rolle som sund. Hvis en forekomst af rolle er blevet fjernet, skal antallet af vellykket og efterfølgende sonder er lig med eller overskrider værdien af SuccessFailCount til at markere forekomsten af rolle som overskrift.

>[AZURE.NOTE] Hvis tilstanden for en forekomst af rolle udsving, venter belastning længere før du sætter den rolle forekomst tilbage i tilstanden sund. Dette sker via politik om at beskytte brugeren og infrastrukturen.

## <a name="use-log-analytics-for-load-balancer"></a>Bruge log analytics til justering af belastning

Du kan bruge [log analytics til justering af belastning](load-balancer-monitor-log.md) til at se, om efterprøvning af af sundhedsstatus og efterprøvning af af antal. Logføring kan bruges med Power BI eller Azure funktionsdygtige indsigt til at levere statistik om justering af belastning sundhedsstatus.
