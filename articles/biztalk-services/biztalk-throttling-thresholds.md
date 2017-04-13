<properties 
    pageTitle="Få mere at vide om (throttling) i BizTalk Services | Microsoft Azure" 
    description="Få mere at vide om (throttling) tærskler og resultatet runtime funktionsmåder for BizTalk-tjenester. (Throttling) er baseret på hukommelsesforbrug og antallet af beskeder. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="mandia"/>





# <a name="biztalk-services-throttling"></a>BizTalk-tjenester: (throttling)

Azure BizTalk tjenester implementerer service (throttling) baseret på to betingelser: hukommelsesforbrug og antallet af samtidige meddelelser behandling. Dette emne viser en liste over de variere den benyttede tærskler og beskrives funktionen Runtime, når der udsendes en variere den benyttede betingelse.

## <a name="throttling-thresholds"></a>(Throttling) tærskler

I følgende tabel vises variere den benyttede kilde- og tærskler:

||Beskrivelse|Grænseværdi for lav|Grænseværdi for høj|
|---|---|---|---|
|Hukommelse|% af total system hukommelse tilgængelige/PageFileBytes. <p><p>Samlede tilgængelige PageFileBytes er cirka 2 gange RAM-systemet.|60%|70%|
|Behandling af meddelelse|Antallet af beskeder behandling samtidigt|40 * antal kerner|100 * antal kerner|

Når en høj grænse er nået, starter Azure BizTalk Services Sådan begrænses. Begrænsning holder, når lav grænsen nås. For eksempel bruger din tjeneste 65% hukommelse. I så fald begrænses tjenesten ikke. Din tjeneste starter med 70% hukommelse. I så fald tjenesten throttles og fortsætter Sådan begrænses indtil tjenesten bruger hukommelse 60% (lav grænseværdi).

Azure BizTalk Services sporer variere den benyttede status (normal tilstand kontra begrænset tilstand) og variere den benyttede varigheden.


## <a name="runtime-behavior"></a>Runtime funktionsmåde

Når Azure BizTalk Services placeres i tilstanden variere den benyttede, sker der følgende:

- (Throttling) er per rolle forekomst. Eksempel:<br/>
RoleInstanceA begrænsning. RoleInstanceB ikke begrænsning. I så fald behandles meddelelser i RoleInstanceB som forventet. Meddelelser i RoleInstanceA kasseres og mislykkes med følgende fejl:<br/><br/>
**Serveren er optaget. Prøv igen.**<br/><br/>
- En hvilken som helst hente kilder ikke afstemning eller hente en meddelelse. Eksempel:<br/>
En rørledning trækker meddelelser fra en ekstern FTP-kilde. Den rolle forekomst at gøre hente bliver til en variere den benyttede fase. I så fald pipeline holder op med at hente flere meddelelser, indtil den rolle forekomst afbrydes (throttling).
- Et svar sendes til klienten, så klienten kan sende meddelelsen.
- Du skal vente, indtil den (throttling) er blevet løst. Du skal vente, indtil lav grænsen nås.

## <a name="important-notes"></a>Vigtige noter
- (Throttling) kan ikke deaktiveres.
- (Throttling) tærskler kan ikke ændres.
- (Throttling) er implementeret hele systemet.
- Azure SQL-databaseserveren har også indbyggede (throttling).

## <a name="additional-azure-biztalk-services-topics"></a>Yderligere Azure BizTalk Services emner

-  [Installation af Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Selvstudier: Azure BizTalk-tjenester](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Hvordan får jeg Start med at bruge Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Azure BizTalk-tjenester](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Se også
- [BizTalk-tjenester: Udvikler, Basic, Standard og Premium udgaver diagram](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk-tjenester: Ved hjælp af Azure klassisk portal klargøring](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk-tjenester: Klargøring Status diagram](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [BizTalk-tjenester: Dashboard, overvåge og skala faner](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk-tjenester: Sikkerhedskopiering og gendannelse](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk-tjenester: Udstedernavn og udsteder nøgle](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
 
