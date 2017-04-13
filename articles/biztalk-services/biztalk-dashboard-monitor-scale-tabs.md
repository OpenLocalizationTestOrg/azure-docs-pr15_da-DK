<properties 
    pageTitle="Dashboard, overvåge, skalering, konfigurerer, og Hybrid forbindelser i BizTalk Services | Microsoft Azure" 
    description="Få mere at vide om kontrolelementerne og overvåge ydeevnen under fanerne klassisk portal for BizTalk-tjenester: Dashboard, skærm, skala, konfigurere og Hybrid forbindelser. MABS, WABS" 
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
    ms.date="08/23/2016" 
    ms.author="mandia"/>




# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>Gennemse fanerne Dashboard, skærm, skala, konfigurere og Hybrid forbindelse

Når du opretter din BizTalk Service og installere programmet, kan du ændre nogle af indstillingerne for BizTalk Service og overvåge ydeevnen programmet på computeren. 

Når du åbner Azure klassisk portalen, placeres du automatisk på fanen **Alle elementer** . Vælg din BizTalk Service under fanen **Alle elementer** for at få vist din BizTalk Service, eller Vælg fanen **BizTalk-tjenester** . og derefter vælge navnet på din BizTalk Service.

Dette åbner et nyt vindue med følgende faner. Dette emne beskrives disse faner.

## <a name="quick-start-quick-startquickstart"></a>Hurtig Start (![Hurtig Start][QuickStart])
Afhængigt af BizTalk Services Edition, alle indstillinger vises muligvis ikke tilgængelig. 
<table border="1">
    <tr>
        <td><strong>Få værktøjerne</strong></td>
        <td>Hent BizTalk Services SDK for at installere Visual Studio project-skabeloner på computeren lokalt udvikling. Skabelonerne oprette <strong>BizTalk tjenester</strong> (bro) og de <strong>BizTalk servicegenstande</strong> (Transformation) Visual Studio projekter, der er installeret på din BizTalk Service.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">Hvordan får jeg Start med at bruge Azure BizTalk Services SDK</a> og <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">installerer Azure BizTalk Services SDK</a> vises trinnene for at komme i gang.
        </td>
    </tr>
    <tr>
        <td><strong>Oprette partner aftaler</strong></td>
        <td>Åbner portalen Azure BizTalk Services hostes på Azure, hvor du kan tilføje partnere og oprette X12, AS2, og EDIFACT EDI aftaler.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Konfiguration af komponenterne til eller-Brugergrupper Messaging på BizTalk Services-portalen</a> indeholder trin at komme i gang.
        </td>
    </tr>

<tr>
        <td><strong>Lær mere om BizTalk-tjenester</strong></td>
        <td>Gå til den <a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">learning center</a> til at få mere at vide om Azure BizTalk-tjenester.</td>
</tr>
</table>


I proceslinjen nederst kan du:

<table border="1">

<tr>
<td><strong>Administrere</strong> installationen af programmet</td>
<td>Åbner portalen Azure BizTalk-tjenester. Portalen BizTalk Services er indgang til eller-Brugergrupper konfiguration, herunder at tilføje partnere og oprette X12, AS2, og EDIFACT aftaler.
<br/><br/>
Dette er den samme som <strong>Opret partner aftaler</strong> under fanen <strong>Hurtig Start</strong> .
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Konfigurerer komponenter til eller-Brugergrupper Messaging på BizTalk Services-portalen</a> indeholder flere oplysninger på portalen BizTalk-tjenester.</td>
</tr>

<tr>
<td><strong>Forbindelsesoplysningerne</strong> af Access kontrolelement Namespace</td>
<td>Når du vælger forbindelsesoplysninger, vises Access kontrolelement Namespace, standard udsteder og standard-tasten. Du kan kopiere disse værdier.
<br/><br/>
Du kan også åbne Access kontrolelement Portal. <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Opret en adgangskontrol Namespace</a> indeholder flere oplysninger om Access kontrolelement Portal.</td>
</tr>

<tr>
<td><strong>Synkronisere taster</strong> i kontoen lagerplads</td>
<td>Når du opretter en konto med lagerplads, oprettes automatisk en primærnøgle og sekundær nøgle. Disse kryptering taster kan kontrollere adgang til kontoen lagerplads. BizTalk Service anvender automatisk den primærnøgle. <strong>Synkroniser nøgler</strong> giver brugerne mulighed at skifte mellem den primærnøgle og den sekundære nøgle uden at forstyrre BizTalk Service.
<br/><br/>
For eksempel ønskede BizTalk Service at bruge en primærnøgle til kontoen lagerplads. Sådan gør du:
<br/><br/>
<ol>
<li>Vælg din BizTalk Service, og vælg <strong>Synkroniser taster</strong>. Vælg den sekundære nøgle. Når du gør dette, starter BizTalk Service ved hjælp af tasten sekundær.</li>
<li>I Azure klassisk portalen, Vælg din konto, lager og for at gendanne den primærnøgle. Husk, at din BizTalk Service bruger en sekundær nøgle.</li>
<li>Vælg din BizTalk Service, og vælg <strong>Synkroniser taster</strong>. Markér nu den primærnøgle. Dette er den nye primærnøgle, du gendannes.</li>
<li>Vælg kontoen lagerplads på Azure-klassisk portalen og genoprette nøglen sekundær.</li>
</ol>
<br/>
Denne proces kaldes "overgang taster". Aktivere brugere til at skifte mellem den primærnøgle og en sekundær nøgle uden at forstyrre BizTalk Service har til formål.</td>
</tr>

<tr>
<td><strong>Slette</strong> dit program</td>
<td>Når du vælger Slet BizTalk Service, og alle elementer, der er installeret på den fjernes.</td>
</tr>
</table>


## <a name="dashboard"></a>Dashboard
Afhængigt af BizTalk Services Edition, alle indstillinger vises muligvis ikke tilgængelig. 

Når du vælger kaldt BizTalk Service, vises fanen Dashboard. I Dashboard kan du:

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>Brugen oversigt: Viser antallet af anvendte Hybrid forbindelser
Viser også dataforbruget i GB. 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>Metriske grafen: Viser en fast liste med ydeevnen målepunkter
Denne statistik indeholder realtid værdier om tilstanden for BizTalk Service. Du kan også vælge **Relative** eller **absolutte** værdier og tidsinterval **Interval** målene, der vises i diagrammet. 

Gå til [Tilgængelige målepunkter](#Metrics) i dette emne for en beskrivelse af disse ydeevne målepunkter.


##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>Oversigt over: Lister BizTalk Service egenskaber

<table border="1">

<tr>
<td><strong>Opdatere sporing Database legitimationsoplysninger</strong></td>
<td>Ændrer det brugernavn og adgangskode, der bruges til at logge på databasen sporing.</td>
</tr>
<tr>
<td><strong>Opdatere SSL-certifikat</strong></td>
<td>Kan opdatere BizTalk Service for at bruge et andet SSL-certifikat. Et selvsigneret SSL-certifikat oprettes automatisk, når du <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">opretter BizTalk-tjenesten</a>.</td>
</tr>
<tr>
<td><strong>Hent certifikat</strong></td>
<td>Du kan hente det SSL-certifikat, der anvendes af din BizTalk Service til en lokal computer.</td>
</tr>
<tr>
<td><strong>Status</strong></td>
<td>Viser den aktuelle status for din BizTalk Service. Se <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">BizTalk-tjenester: Tjenesten tilstand diagram</a>. </td>
</tr>
<tr>
<td><strong>URL-adressen</strong></td>
<td>URL-adressen for din BizTalk-tjeneste. Dette er den samme som <strong>Domæne URL-adresse</strong> indtastet oprettelsen af din BizTalk Service.</td>
</tr>
<tr>
<td><strong>IP-offentlige virtuelle (VIP) adresse</strong></td>
<td>Den IP-adresse, der er tildelt til din BizTalk Service. Der bruges til alle input slutpunkter og er kildeadressen til udgående trafik. Denne IP-adresse tilhører BizTalk Service, så længe den er oprettet. Hvis du sletter BizTalk Service, tildeles IP-adressen til en anden BizTalk Service.</td>
</tr>
<tr>
<td><strong>ACS Namespace</strong></td>
<td>Godkender med BizTalk-tjenesten.</td>
</tr>
<tr>
<td><strong>Edition</strong></td>
<td>Lister på Edition angivet når BizTalk Service oprettes.</td>
</tr>
<tr>
<td><strong>Placering</strong></td>
<td>Viser det geografiske område, der er vært BizTalk Service.</td>
</tr>
<tr>
<td><strong>Oprettet</strong></td>
<td>Viser den dato og klokkeslæt for BizTalk Service.</td>
</tr>
<tr>
<td><strong>Sporing af Database</strong></td>
<td>Navnet Azure SQL-Database, der gemmer registrering tabeller, der bruges af din BizTalk Service. 
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Krav til forklaring</a> indeholder oplysninger om sporing af databasen.</td>
</tr>
<tr>
<td><strong>Overvågning/arkivering lagerplads</strong></td>
<td>Kontonavnet Azure-lager, der gemmer overvågning output fra BizTalk Service.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Krav til forklaring</a> indeholder oplysninger om kontoen lagerplads.</td>
</tr>
<tr>
<td><strong>Navn på abonnement</strong></td>
<td>Viser det abonnement, der er vært BizTalk Service. Abonnementets styrer adgang til portalen Azure klassisk.</td>
</tr>
<tr>
<td><strong>Abonnement-ID</strong></td>
<td>Et abonnement-ID genereres automatisk, når der oprettes et abonnement. Når du bruger REST API'er, du muligvis angive abonnement-ID.</td>
</tr>
</table>

[BizTalk Services: klargøring ved hjælp af Azure klassisk portal](http://go.microsoft.com/fwlink/p/?LinkID=302280) indeholder trinnene, hvis du vil oprette en BizTalk Service.


##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>Administrere forbindelsesoplysningerne, Synkroniser taster, og Slet på proceslinjen:

<table border="1">

<tr>
<td><strong>Administrere</strong> installationen af programmet</td>
<td>Åbner portalen BizTalk Azure-tjenester. Portalen BizTalk Services er indgang til eller-Brugergrupper konfiguration, herunder at tilføje partnere og oprette X12, AS2, og EDIFACT aftaler.
<br/><br/>
Dette er den samme som <strong>Opret partner aftaler</strong> under fanen <strong>Hurtig Start</strong> .
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Konfigurerer komponenter til eller-Brugergrupper Messaging på BizTalk Services-portalen</a> indeholder flere oplysninger på portalen BizTalk-tjenester.</td>
</tr>
<tr>
<td><strong>Forbindelsesoplysningerne</strong> af Access kontrolelement Namespace</td>
<td>Viser Access kontrolelement Namespace, standard udsteder og nøgleværdier, der som standard der kan kopieres.
<br/><br/>
Du kan også åbne Access kontrolelement Portal. Denne Access kontrolelement Portal er den samme som ved hjælp af indstillingen Active Directory i den venstre navigationsrude.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Administrere dine ACS Namespace</a> indeholder flere oplysninger på portalen Access kontrolelement.</td>
</tr>
<tr>
<td><strong>Synkronisere taster</strong> i kontoen lagerplads</td>
<td>Når du opretter en konto med lagerplads, oprettes automatisk en primærnøgle og sekundær nøgle. Disse kryptering taster kan kontrollere adgang til kontoen lagerplads. BizTalk Service anvender automatisk den primærnøgle. <strong>Synkroniser nøgler</strong> giver brugerne mulighed at skifte mellem den primærnøgle og den sekundære nøgle uden at forstyrre BizTalk Service.
<br/><br/>
For eksempel ønskede BizTalk Service at bruge en primærnøgle til kontoen lagerplads. Sådan gør du:
<br/><br/>
<ol>
<li>Vælg din BizTalk Service, og vælg <strong>Synkroniser taster</strong>. Vælg den sekundære nøgle. Når du gør dette, starter BizTalk Service ved hjælp af tasten sekundær.</li>
<li>I Azure klassisk portalen, Vælg din konto, lager og for at gendanne den primærnøgle. Husk, at din BizTalk Service bruger en sekundær nøgle.</li>
<li>Vælg din BizTalk Service, og vælg <strong>Synkroniser taster</strong>. Markér nu den primærnøgle. Dette er den nye primærnøgle, du gendannes.</li>
<li>Vælg kontoen lagerplads på Azure-klassisk portalen og genoprette nøglen sekundær.</li>
</ol>
<br/>
Denne proces kaldes "overgang taster". Aktivere brugere til at skifte mellem den primærnøgle og en sekundær nøgle uden at forstyrre BizTalk Service har til formål.</td>
</tr>

<tr>
<td><strong>Slette</strong> dit program</td>
<td>BizTalk Service og alle elementer, der er installeret på den fjernes.</td>
</tr>
</table>


## <a name="monitor"></a>Skærm
Gælder ikke for den gratis version.

Når du vælger kaldt BizTalk Service, er tilgængelige under fanen skærm, og viser følgende:

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>Metriske grafen: Viser den valgte performance målepunkter
Denne statistik indeholder realtid værdier om tilstanden for BizTalk Service. Du vælger hvilke ydeevne målepunkter vises. Seks ydeevne målepunkter maksimalt kan vises samtidigt. 

Du kan også vælge **Relative** eller **absolutte** værdier og tidsinterval **Interval** målene, der vises. 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>For at fjerne eller få vist målepunkter i graph:
1. Vælg fanen **skærm** .
2. Vælg **Tilføj metrisk** i proceslinjen:  
![Vælg Tilføj målepunkter][AddMetrics]
3. Markér den ydeevne målepunkter, du vil have vist.
4. Vælg markering for at vende tilbage til fanen **skærm** .
5. Vælg cirklen ud for metrikværdien til at vise den metrisk værdi i diagrammet.  

    For eksempel, der er nedtonet **CPU-brug** metrisk Hvis outputtet vises ikke i graph:  
![CPU-brug metrisk er nedtonet][GrayedMetric]  

    Vælg den gråtonet ud cirklen for at aktivere **CPU-brug** metrisk at få vist dets output i graph:  
![CPU-brug metrisk er aktiveret][EnabledMetric]

6. For at fjerne en metrikværdi fra visningen grafen og listen skal du vælge **Slette metrisk** på proceslinjen. Vælg **Tilføj metrisk** i proceslinjen for at føje metriske tilbage til listen, Markér metrikværdien, og vælg markering for at vende tilbage til fanen **skærm** . Vælg den gråtonet ud cirklen for at aktivere metrikværdien.

## <a name="Metrics"></a>Tilgængelige målepunkter
De følgende ydeevne tællere/målepunkter er tilgængelige:

<table border="1">

<tr>
<td><strong>RountdTrip ventetid</strong></td>
<td>Viser den gennemsnitlige tid i millisekunder (ms) til at behandle en meddelelse fra tidspunktet, hvor meddelelsen modtages, indtil meddelelsen behandles fuldt af BizTalk Service på tværs af alle broer. Kun meddelelser, der er blevet behandlet, tælles.<br/><br/>
Når følgende hændelser indtræffer, oprettes et tidsstempel:
<ul>
<li>Meddelelse indsætter gatewayen</li>
<li>Meddelelse sendes til destinationen</li>
<li>Destination svar</li>
<li>Destination bekræftelse svar sendt til gatewayen</li>
</ul>
<br/>
Denne metrikværdi viser resultatet af beregningen af følgende:
<br/><br/>
[Destination bekræftelse svar sendt til gatewayen] - [meddelelse indsætter gatewayen]</td>
</tr>
<tr>
<td><strong>Fejl ved kilden</strong></td>
<td>Viser det samlede antal meddelelser, der mislykkedes af BizTalk Service ved at trække meddelelser fra kilde slutpunkterne.</td>
</tr>
<tr>
<td><strong>CPU-brug</strong></td>
<td>Viser den gennemsnitlige % processortid af alle forekomster af rollen.</td>
</tr>
<tr>
<td><strong>Behandling af ventetid</strong></td>
<td>Viser den gennemsnitlige tid i millisekunder (ms) til at behandle en meddelelse ved BizTalk Service på tværs af alle broer, med undtagelse af den tid, der i destinationer. Kun meddelelser, der er blevet behandlet, tælles.<br/><br/>
Når hver af følgende hændelser indtræffer, oprettes et tidsstempel:

<ul>
<li>Meddelelse indsætter gatewayen</li>
<li>Meddelelse sendes til destinationen</li>
<li>Destination svar</li>
<li>Destination bekræftelse svar sendt til gatewayen</li>
</ul>
<br/>Denne metrikværdi viser resultatet af beregningen af følgende:<br/><br/>
[Destination bekræftelse svar sendt til gatewayen] - [meddelelse indsætter gatewayen] - [Destination svar] + [meddelelse sendes til destinationen]</td>
</tr>
<tr>
<td><strong>Fejl i processen</strong></td>
<td>Viser det samlede antal meddelelser, der gav fejl under behandling af BizTalk Service på tværs af alle broer inden for et tidsinterval.</td>
</tr>
<tr>
<td><strong>Hvis du sender</strong></td>
<td>Viser det samlede antal meddelelser, der sendes af BizTalk Service på tværs af alle broer inden for et tidsinterval. Denne metrikværdi øges, når en meddelelse, der sendes fra en rørledning rute destination. Denne metrikværdi angiver ikke, at en meddelelse er blevet behandlet.<br/><br/>
I et scenarie med anmodning om autosvar øges metrikværdien, når rute destination sender en kvittering bekræftelse tilbage til rørledningen.</td>
</tr>
<tr>
<td><strong>Meddelelser, der er modtaget</strong></td>
<td>Viser det samlede antal meddelelser, der modtages af BizTalk Service på tværs af alle broer inden for et tidsinterval. Denne metrikværdi øges, når der modtages en ny meddelelse af pipeline.</td>
</tr>
<tr>
<td><strong>Meddelelser i processen</strong></td>
<td>Viser det samlede antal meddelelser, der i øjeblikket behandles af BizTalk Service inden for et tidsinterval.</td>
</tr>
<tr>
<td><strong>Meddelelser, der behandles</strong></td>
<td>Viser det samlede antal meddelelser, der blev behandlet af BizTalk Service på tværs af alle broer inden for et tidsinterval. Denne metrikværdi øges, når en meddelelse blev modtaget af pipeline og blev sendt til destinationen.</td>
</tr>
</table>


## <a name="scale"></a>Skala
Du kan tilføje eller trække antallet enheder, der anvendes af din BizTalk Service i fanen skala. Som standard er der en enhed er konfigureret. Supplerende enheder kan føjes til skalere BizTalk Service. Når du øger skalaen, øger du overførselshastighed. Hvor mange ressourcer også øges, herunder udløst broer, aftaler, LOB forbindelser og behandling power. For eksempel kan du øge skala fra 1 enhed til 2 enheder. Du kan installere fordoble antallet af broer, dobbeltklik på aftaler, dobbeltklikke LOB forbindelser og dobbeltklikke behandling power i så fald.

Nogle BizTalk-udgaver tilbyder ikke en indstilling for skala. En enhed er tilladt i så fald. Hvis du vil finde ud af, hvor mange enheder din udgave kan skaleres, referere til [BizTalk-tjenester: udgaver diagram](biztalk-editions-feature-chart.md).

Øge antallet af enheder kan påvirke priser. Hvis du øger enhederne, vises Hvis du vælger **Gem** en meddelelse, der fortæller, hvis fakturering der påvirkes. Du vælger at fortsætte. Når du øge antallet af enheder, BizTalk Service status skifter fra aktiv til opdatering. I tilstanden opdatering fortsætter din BizTalk Service til at køre.

[BizTalk-tjenester: udgaver diagram](biztalk-editions-feature-chart.md) definerer en "enhed".


## <a name="configure"></a>Konfigurere
Gælder ikke for hybride forbindelser.

Sæt af Status for sikkerhedskopiering til ingen eller automatisk. Når den er indstillet til ingen, oprettes nogen sikkerhedskopier automatisk. Når den er indstillet til automatisk, konfigurerer du sikkerhedskopiplacering, hyppigheden for sikkerhedskopien, og hvor lang tid at holde sikkerhedskopifilerne. 

[BizTalk Services: sikkerhedskopiere og gendanne](biztalk-backup-restore.md) indeholder oplysninger. 


## <a name="HybridConnections"></a>Hybrid forbindelser
Et Azure-program, som Web Apps eller Mobile-Apps i Azure App Service hybrid forbindelser oprette forbindelse til en lokal ressource, der bruger en statisk TCP-port, såsom SQL Server, MySQL, HTTP Web API'er og de fleste brugerdefineret Web-tjenester. Hybrid forbindelser administreres i BizTalk Services i portalen Azure klassisk.

Se [Access lokale ressourcer ved hjælp af hybrid forbindelser i Azure App Service](../app-service-web/web-sites-hybrid-connection-get-started.md)for at oprette hybride forbindelser i Azure App Service.

Oprette eller administrere hybride forbindelser i Azure BizTalk Services, skal du se [Hybrid forbindelser](integration-hybrid-connection-overview.md).



## <a name="next"></a>Næste
Nu, hvor du kender til de forskellige faner, du kan få mere at vide om funktionerne Azure BizTalk Services:

- [BizTalk tjenester: (throttling)](biztalk-throttling-thresholds.md)  
- [BizTalk-tjenester: Udstedernavn og udsteder nøgle](biztalk-issuer-name-issuer-key.md)  
- [BizTalk-tjenester: Sikkerhedskopiering og gendannelse](biztalk-backup-restore.md)

## <a name="see-also"></a>Se også
- [Hybrid forbindelser](integration-hybrid-connection-overview.md)  
- [BizTalk Services: Udvikler, Basic, Standard og Premium udgaver diagram](biztalk-editions-feature-chart.md)  
- [BizTalk tjenester: Ved hjælp af Azure klassisk portal klargøring](biztalk-provision-services.md)  
- [BizTalk tjenester: BizTalk tjenestetilstand diagram](biztalk-service-state-chart.md)  
- [Hvordan får jeg Start med at bruge Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[QuickStart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png
 
