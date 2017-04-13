<properties 
    pageTitle="Oprette og gendanne en sikkerhedskopi i BizTalk Services | Microsoft Azure" 
    description="BizTalk Services omfatter sikkerhedskopiering og gendannelse. Lær, hvordan du kan oprette og gendanne en sikkerhedskopi, og finde ud af, hvad får sikkerhedskopieres. MABS, WABS" 
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


# <a name="biztalk-services-backup-and-restore"></a>BizTalk-tjenester: Sikkerhedskopiering og gendannelse

Azure BizTalk Services omfatter funktioner til sikkerhedskopiering og gendannelse. Dette emne beskrives det, hvordan til at sikkerhedskopiere og gendanne BizTalk-tjenester ved hjælp af portalen Azure klassisk.

Du kan også sikkerhedskopiere BizTalk-tjenester ved hjælp af [BizTalk-tjenester REST-API](http://go.microsoft.com/fwlink/p/?LinkID=325584). 

> [AZURE.NOTE] Hybrid forbindelser sikkerhedskopieres ikke, uanset udgaven. Du skal genoprette dine hybrid forbindelser.

## <a name="before-you-begin"></a>Inden du begynder

- Sikkerhedskopiere og gendanne måske ikke er tilgængelig for alle versioner. Se [BizTalk-tjenester: udgaver diagram](biztalk-editions-feature-chart.md).

- Ved hjælp af portalen Azure klassisk, kan du oprette en sikkerhedskopi af On Demand eller oprette en planlagt sikkerhedskopiering. 

- Ekstra indhold kan gendannes til den samme BizTalk Service eller til en ny BizTalk Service. Hvis du vil gendanne den BizTalk Service med det samme navn, den eksisterende BizTalk Service skal slettes, og navnet skal være tilgængelige. Når du sletter en BizTalk Service, kan det tage længere tid end ønskede til det samme navn skal være tilgængeligt. Hvis du ikke kan vente det samme navn skal være tilgængeligt, gendanne til en ny BizTalk Service.

- BizTalk-tjenester kan gendannes til den samme version eller en nyere udgave. Gendanne BizTalk-tjenester til en lavere udgave, understøttes fra hvornår som sikkerhedskopien er taget, ikke.

    For eksempel kan en sikkerhedskopi, ved hjælp af Basic Edition gendannes til Premium Edition. En sikkerhedskopi, ved hjælp af Premium Edition kan ikke gendannes til Standard Edition.

- Eller-Brugergrupper kontrolelement tallene sikkerhedskopieres til at vedligeholde løbende talsæt kontrolelement. Hvis meddelelser behandles efter den sidste sikkerhedskopiering, kan gendanne dette ekstra indhold medføre dublerede kontrolelement tal.

- Hvis en gruppe har aktive meddelelser, skal du behandle batchen **før du** kører en sikkerhedskopi. Når du opretter en sikkerhedskopi (som nødvendige eller planlagte), gemmes meddelelser i batches aldrig. 

    **Hvis er taget en sikkerhedskopi med aktive meddelelser i en gruppe, ikke er sikkerhedskopieret disse meddelelser, og derfor går tabt.**

- Valgfrit: I portalen BizTalk-tjenester, stop alle handlinger i administration.


## <a name="create-a-backup"></a>Oprette en sikkerhedskopi

En sikkerhedskopi kan blive ført til enhver tid og styres fuldstændigt af dig. Dette afsnit indeholder trinnene, hvis du vil oprette sikkerhedskopier portalen Azure klassisk, herunder:

[Sikkerhedskopiér behov](#backupnow)

[Planlægge en sikkerhedskopi](#backupschedule)

#### <a name="backupnow"></a>Sikkerhedskopiér behov
1. Vælg **BizTalk-tjenester**på Azure-klassisk portalen, og vælg derefter den BizTalk Service du vil sikkerhedskopiere.
2. Vælg fanen **Dashboard** **sikkerhedskopiere** nederst på siden.
3. Angiv et navn på sikkerhedskopi. For eksempel angive *myBizTalkService*BU*dato*.
4. Vælg en blob storage-konto, og vælg markering til at starte sikkerhedskopieringen.

Når sikkerhedskopieringen er fuldført, oprettes en objektbeholder med navnet på sikkerhedskopien du angiver i kontoen lagerplads. Denne beholder indeholder konfigurationen BizTalk Service sikkerhedskopiering.

#### <a name="backupschedule"></a>Planlægge en sikkerhedskopi

1. I Azure klassisk portalen, Vælg **BizTalk-tjenester**, Vælg det ønskede du planlægger at sikkerhedskopieringen BizTalk Service navn og derefter vælge fanen **Konfigurer** .
2. Angive **Status for sikkerhedskopiering** til **automatisk**. 
3. Vælg den **Lagerplads konto** til at gemme sikkerhedskopien, Angiv **hyppigheden** for at oprette sikkerhedskopier, og hvor lang tid at holde sikkerhedskopier (**Opbevaring dage**):

    ![][AutomaticBU]

    **Noter**   
    - I **Opbevaring dage**være opbevaringsperioden større end sikkerhedskopiering hyppigheden.
    - Vælg **altid holde mindst én sikkerhedskopi**, selvom den har overskredet opbevaringsperioden.
    

4. Vælg **Gem**.


Når en planlagt sikkerhedskopiering kører, opretter en objektbeholder (Hvis du vil gemme sikkerhedskopien data) i kontoen lagerplads du har angivet. Navnet på objektbeholderen hedder *BizTalk Service navn-dato / klokkeslæt*. 

Hvis dashboardet BizTalk Service viser en **mislykket** status:

![Sidst planlagt status for sikkerhedskopiering][BackupStatus] 

Linket åbner Management Services handlingen loggene hjælp til fejlfinding af. Se [BizTalk Services: ved hjælp af handlingen logge](http://go.microsoft.com/fwlink/p/?LinkId=391211).

## <a name="restore"></a>Gendanne

Fra portalen Azure klassisk eller [Gendanne BizTalk-tjenesten REST-API](http://go.microsoft.com/fwlink/p/?LinkID=325582), kan du gendanne sikkerhedskopier. Dette afsnit beskrives disse trin for at gendanne ved hjælp af portalen klassisk.

#### <a name="before-restoring-a-backup"></a>Før du gendanner en sikkerhedskopi

- Nye sporing, arkivering og overvågning butikker kan angives under gendannelse af en BizTalk Service.

- De samme data eller-Brugergrupper Runtime genoprettes. Eller-Brugergrupper Runtime sikkerhedskopien gemmer kontrolelement tal. Gendannede kontrolelement tallene er i rækkefølge fra tidspunktet for sikkerhedskopien. Hvis meddelelser behandles efter den sidste sikkerhedskopiering, kan gendanne dette ekstra indhold medføre dublerede kontrolelement tal.

#### <a name="restore-a-backup"></a>Gendanne en sikkerhedskopi

1. Vælg **Ny**på Azure klassisk portalen > **App Services** > **BizTalk Service** > **gendanne**:

    ![Gendanne en sikkerhedskopi][Restore]

2. Vælg mappeikonet i **Sikkerhedskopiering URL-adressen**, og udvid kontoen Azure-lager, der gemmer BizTalk Service konfiguration sikkerhedskopi. Udvide objektbeholderen, og vælg den tilsvarende Sikkerhedskopiér .txt-fil i højre rude. 
<br/><br/>
Vælg **Åbn**.

3. Angiv et **Navn på BizTalk-tjeneste** , og Bekræft **Domæne URL-adresse**, **Edition**og **Region** for gendannede BizTalk Service på siden **Gendanne BizTalk Service** . **Opret en ny forekomst af SQL-database** til sporing databasen:

    ![][RestoreBizTalkService]

    Vælg pilen Næste.

4.  Kontrollere navnet på SQL-databasen skal du angive den fysiske server, hvor SQL-databasen skal oprettes, og et brugernavn og adgangskode for den pågældende server.


    Hvis du vil konfigurere SQL-database edition, vælge størrelse og andre egenskaber, **Konfigurere avancerede databaseindstillinger**. 

    Vælg pilen Næste.

5. Oprette en ny firmapost lagerplads eller angive en eksisterende konto lagerplads for BizTalk Service.

7. Vælg markering til at starte gendannelsen.

Når gendannelsen er fuldført, vises en ny BizTalk Service i pausetilstand på siden BizTalk Services i portalen Azure klassisk.



### <a name="postrestore"></a>Når du har gendanne en sikkerhedskopi

BizTalk Service genoprettes altid i tilstanden **suspenderet** . I denne tilstand, kan du foretage eventuelle ændringer i konfigurationen, før det nye miljø er funktionelle, herunder:

- Hvis du har oprettet ved hjælp af Azure BizTalk Services SDK BizTalk Service-programmer, du muligvis opdatere Access ACS (Control) legitimationsoplysninger i disse programmer til at arbejde med det gendannede miljø.

- Du gendanner en BizTalk Service for at gentage et eksisterende BizTalk Service-miljø. I dette tilfælde, hvis der er aftaler, der er konfigureret i portalen oprindelige BizTalk-tjenester, der bruger en kilde FTP-mappe, du muligvis opdatere aftaler i det netop er gendannet miljø til brug af en anden kilde FTP-mappe. Ellers kan der være to forskellige aftaler, der forsøger at hente den samme meddelelse.

- Hvis du har gendannet for at få flere BizTalk Service-miljøer, skal du kontrollere du tilpasser det korrekte miljø i Visual Studio-programmer, PowerShell-cmdletter, REST API'er eller handel Partner administration OM-API'er.

- Det er en god ide at konfigurere automatiske sikkerhedskopier på det netop er gendannet BizTalk Service-miljø.

Vælg det gendannede BizTalk Service for at starte BizTalk Service på portalen Azure klassisk, og vælg **CV** på proceslinjen. 



## <a name="what-gets-backed-up"></a>Hvad bliver sikkerhedskopieres

Når der oprettes en sikkerhedskopi, sikkerhedskopieres følgende elementer:

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Elementer, der er sikkerhedskopieret</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Azure BizTalk Services Portal</strong></td>
</tr> 
<tr>
<td>Konfiguration og Runtime</td> 
<td>
<ul>
<li>Oplysninger om partner og profil</li>
<li>Partner aftaler</li>
<li>Brugerdefineret assemblies, der er installeret</li>
<li>Broer, der er installeret</li>
<li>Certifikater</li>
<li>Transformeringer, der er installeret</li>
<li>Rørledninger</li>
<li>Skabeloner, der er oprettet og gemt i portalen BizTalk-tjenester</li>
<li>X12 ST01 og GS01 tilknytninger</li>
<li>Kontrolelementet tal (eller-Brugergrupper)</li>
<li>AS2 meddelelse Mikrofon værdier</li>
</ul>
</td>
</tr> 
 
<tr>
<td colspan="2">
 <strong>Azure BizTalk Service</strong></td>
</tr> 
<tr>
<td>SSL-certifikat</td> 
<td>
<ul>
<li>SSL-certifikat Data</li>
<li>SSL-certifikat adgangskode</li>
</ul>
</td>
</tr> 
<tr>
<td>BizTalk-Tjenesteindstillinger</td> 
<td>
<ul>
<li>Skala enhed TÆL</li>
<li>Edition</li>
<li>Produktversion</li>
<li>Område/Datacenter</li>
<li>Access (ACS Control Service) navneområde og nøgle</li>
<li>Sporing af forbindelsesstrengen databasen</li>
<li>Arkivering lagerplads konto forbindelsesstreng</li>
<li>Overvåge lagerplads konto forbindelsesstreng</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Flere elementer</strong></td>
</tr> 
<tr>
<td>Sporing af Database</td> 
<td>Når der oprettes BizTalk Service, angives sporing Database oplysninger, herunder Azure SQL-databaseserver og sporing af databasenavnet. Sporing af databasen er ikke sikkerhedskopieret automatisk.
<br/><br/>
<strong>Vigtige</strong><br/>
Hvis databasen sporing slettes, og databasen behov gendannes, skal der findes en tidligere sikkerhedskopi. Hvis der ikke findes en sikkerhedskopi, er sporing af databasen og dens data ikke genoprettes. I dette tilfælde skal du oprette en ny Database sporing med det samme navn på databasen. Geografisk gentagelse anbefales.</td>
</tr> 
</table>

## <a name="next"></a>Næste

Hvis du vil oprette Azure BizTalk Services i portalen Azure klassisk, gå til [BizTalk-tjenester: klargøring ved hjælp af Azure klassisk portal](http://go.microsoft.com/fwlink/p/?LinkID=302280). For at starte oprettelsen af programmer skal du gå til [Azure BizTalk-tjenester](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Se også
- [Sikkerhedskopiér BizTalk Service](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Gendanne BizTalk Service fra sikkerhedskopi](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [BizTalk-tjenester: Udvikler, Basic, Standard og Premium udgaver diagram](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [BizTalk-tjenester: Ved hjælp af Azure klassisk portal klargøring](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [BizTalk-tjenester: Klargøring Status diagram](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [BizTalk-tjenester: Dashboard, overvåge og skala faner](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [BizTalk-tjenester: (throttling)](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [BizTalk-tjenester: Udstedernavn og udsteder nøgle](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Hvordan får jeg Start med at bruge Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png
 
