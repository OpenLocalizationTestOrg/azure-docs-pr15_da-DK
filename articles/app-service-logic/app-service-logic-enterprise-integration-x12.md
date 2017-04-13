<properties 
    pageTitle="Oversigt over X12 og Enterprise-Integration Pack | Microsoft Azure App Service | Microsoft Azure" 
    description="Lær at bruge X12 aftaler til at oprette logik apps" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="app-service-logic" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/08/2016" 
    ms.author="deonhe"/>

# <a name="enterprise-integration-with-x12"></a>Virksomhedsintegration med X12 

>[AZURE.NOTE]Denne side dækker X12 funktioner i logik Apps. Oplysninger om EDIFACT klikke [her](app-service-logic-enterprise-integration-edifact.md).

## <a name="create-an-x12-agreement"></a>Oprette en X12 aftale 
Før du kan udveksle X12 meddelelser, du vil oprette en X12 aftale og gemme den i din integration-konto. Følgende trin fører dig gennem processen med at oprette en X12 aftale.

### <a name="heres-what-you-need-before-you-get-started"></a>Her er, hvad du skal før du går i gang
- En [integration konto](./app-service-logic-enterprise-integration-accounts.md) , der er defineret i abonnementet Azure  
- Mindst to [partnere](./app-service-logic-enterprise-integration-partners.md) allerede er defineret i kontoen integration  

>[AZURE.NOTE]Når du opretter en aftale, skal indholdet i filen aftale til aftaletypen.    


Når du har [oprettet en integration konto](./app-service-logic-enterprise-integration-accounts.md) og [føjet partnere](./app-service-logic-enterprise-integration-partners.md), kan du oprette en X12 aftale ved at følge disse trin:  

### <a name="from-the-azure-portal-home-page"></a>Fra Azure portalwebstedets startside

Når du logger på [Azure portal](http://portal.azure.com "Azure-portalen"):  
1. Vælg **Find** i menuen til venstre.  

>[AZURE.TIP]Hvis du ikke kan se linket **Gennemse** , skal du muligvis først udvide menuen. Gør dette ved at vælge linket **Vis menu** , der er placeret på øverst til venstre i menuen skjult.  

![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Skriv *integration* i feltet filter Søg og vælg derefter **Integration konti** på listen over resultater.       
![](./media/app-service-logic-enterprise-integration-x12/x12-1-3.png)    
3. Vælg kontoen integration, hvor du vil oprette aftalen i bladet **Integration konti** , der åbnes. Hvis du ikke kan se integration konti lister, [opretter en første](./app-service-logic-enterprise-integration-accounts.md "All about integration accounts").  
![](./media/app-service-logic-enterprise-integration-x12/x12-1-4.png)  
4.  Vælg feltet **aftaler** . Hvis du ikke kan se feltet aftaler, tilføje den første.   
![](./media/app-service-logic-enterprise-integration-x12/x12-1-5.png)     
5. Vælg knappen **Tilføj** i bladet aftaler, der åbnes.  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-2.png)  
6. Angiv et **navn** til din aftale, og vælg den **Aftaletype** **Host Partner** **Host identitet**, **Gæst Partner**, og **Gæst identitet**, i bladet aftaler, der åbnes.  
![](./media/app-service-logic-enterprise-integration-x12/x12-1.png)  
7. Når du har oprettet Modtag indstillinger, klik på knappen **OK**  
Lad os fortsætte:  
8. Vælg **Modtager indstillinger** til at konfigurere, hvordan meddelelser, der er modtaget via denne aftale er skal håndteres.  
9. Indstillinger for Modtag kontrolelementet er inddelt i de følgende afsnit, herunder id'er, bekræftelse, skemaer, konvolutter, kontrolelement tal, valideringer og interne indstillinger. Konfigurere disse egenskaber, der er baseret på din aftale med partneren, du vil udveksle meddelelser med. Her er en visning af disse objekter skal du konfigurere dem baseret på, hvordan denne aftale til at identificere og håndtere indgående meddelelser:  
![](./media/app-service-logic-enterprise-integration-x12/x12-2.png)  

![](./media/app-service-logic-enterprise-integration-x12/x12-3.png)  
10. Vælg **OK** for at gemme dine indstillinger.  

### <a name="identifiers"></a>Id'er

|Egenskaben|Beskrivelse |
|---|---|
|ISA1 (godkendelse operator)|Vælg værdien godkendelse operator på rullelisten.|
|ISA2|Valgfrit. Angiv tilladelse oplysninger værdi. Hvis den værdi, du har angivet for ISA1 er end 00, angive mindst ét alfanumeriske tegn og op til 10.|
|ISA3 (sikkerhed operator)|Vælg værdien sikkerhed operator på rullelisten.|
|ISA4|Valgfrit. Angiv sikkerhed oplysninger værdien. Hvis den værdi, du har angivet for ISA3 er end 00, angive mindst ét alfanumeriske tegn og op til 10.|

### <a name="acknowledgments"></a>Bekræftelse 

|Egenskaben|Beskrivelse |
|----|----|
|TA1 forventet|Markér dette afkrydsningsfelt for at returnere en tekniske (TA1) bekræftelse til afsenderen udveksling. Disse bekræftelse er sendt til udveksling afsenderen baseret på siden Send indstillinger for aftalen.|
|FA forventet|Markér dette afkrydsningsfelt for at returnere en funktionelle (FA) bekræftelse til afsenderen udveksling. Vælg derefter, om du vil 997 eller 999 godkendelser, baseret på de skema versioner, du arbejder med. Disse bekræftelse er sendt til udveksling afsenderen baseret på siden Send indstillinger for aftalen.|
|Medtage AK2/IK2 løkke|Markér dette afkrydsningsfelt for at aktivere oprettelse af AK2 løkker i funktionelle bekræftelse for accepteret transaktion sæt. Bemærk: Dette afkrydsningsfelt er aktiveret, hvis du markerede afkrydsningsfeltet FA forventet.|

### <a name="schemas"></a>Ved hjælp af skemaer

Vælg et skema for hver transaktionstype (ST1) og afsenderen programmet (GS2). Modtag pipeline opdeler indgående meddelelser ved at sammenligne værdierne for ST1 og GS2 i den indgående meddelelse med de værdier, du angiver her, og skemaet for den indgående meddelelse med skemaet, du angiver her.

|Egenskaben|Beskrivelse |
|----|----|
|Version|Vælg X12 version|
|Transaktionstype (ST01)|Vælg typen|
|Afsenderen program (GS02)|Vælg programmet afsender|
|Skema|Vælg den ønskede til os skemafil. Skemafiler er placeret i kontoen integration.|

### <a name="envelopes"></a>Konvolutter

|Egenskaben|Beskrivelse |
|----|----|
|ISA11 brugen|Brug dette felt til at angive separatoren i et sæt transaktion:</br></br>Vælg Standard-id'et til at bruge decimal tegnet "." Modtag pipeline i stedet for decimal Notationen for indgående dokumentet i eller-Brugergrupper.</br></br>Vælg gentagelse separator, der skal angive separatoren for gentagne forekomster af et enkelt data-element eller en gentagen datastruktur. For eksempel bruges (^) normalt som gentagelse separator. Du kan kun bruge (^) til HIPAA skemaer.|

### <a name="control-numbers"></a>Kontrolelementet tal

|Egenskaben|Beskrivelse |
|----|----|
|Tillad ikke dubletter Interchange kontrolelementtal|Markér denne indstilling for at blokere dublerede udfletninger. Hvis markeret, kontrollerer portalen BizTalk-tjenester, udveksling kontrolelement antallet (ISA13) til modtagne udveksling ikke stemmer overens udveksling kontrolelementtal. Hvis der er fundet et match, behandler Modtag pipeline ikke udveksling.<br/>Hvis du har valgt at Tillad ikke dubletter udveksling kontrolelement tal, kan du angive antallet dage, hvor kontrollen udføres ved at give den relevante værdi for Kontrollér, om dubletter ISA13 hver n dage.|
|Tillad ikke gruppe kontrolelement tal dubletter|Markér denne indstilling for at blokere udfletninger med dublerede gruppering kontrol af tal.|
|Tillad ikke transaktion sæt kontrolelement tal dubletter|Markér denne indstilling for at blokere udfletninger med dublerede transaktion sæt kontrolelement tal.|

### <a name="validations"></a>Valideringer

|Egenskaben|Beskrivelse |
|----|----|
|Meddelelsestype|Skriv til eller-Brugergrupper meddelelse, som 850 indkøbsordre eller 999 implementering bekræftelse.|
|Eller-Brugergrupper validering|Udfører eller-Brugergrupper validering på datatyper, som defineret af eller-Brugergrupper egenskaberne for det skema, begrænsninger længde, tom dataelementer og efterstillede separatorer.|
|Udvidet validering|Hvis datatypen ikke er eller-Brugergrupper, datavalidering er på kravet data element og tilladt gentagelse, optællinger og element længde datavalidering (min/Maks).|
|Tillad linjeafstand/efterstillede nuller|Yderligere plads og nul tegn, der foranstillede eller efterstillede bevares. De fjernes ikke.|
|Efterstillede Separator politik|Genererer efterstillede separatorer på udveksling modtaget. Indstillingerne for inkluderer NotAllowed, valgfrit og obligatorisk.|

### <a name="internal-settings"></a>Interne indstillinger

|Egenskaben|Beskrivelse |
|----|----|
|Konvertere implicit decimalformat Nn basere 10 numeriske værdi|Konverterer et tal i eller-Brugergrupper, der er angivet i formatet Nn til en base-10 numeriske værdi i mellemliggende XML i portalen BizTalk-tjenester.|
|Oprette tomme XML-koder, hvis efterstillede separatorer er tilladt|Markér dette afkrydsningsfelt for at få udveksling afsenderen til at medtage tomme XML-koder til efterstillede separatorer.|
|Indgående samling behandling|Opdel udveksling som posteringen sæt - suspendere transaktion sæt om fejl: fortolker hver transaktion, der er angivet i en udveksling i et separat XML-dokument ved at anvende den relevante konvolut til sættet transaktion. Med denne indstilling, hvis en eller flere transaktion angiver i udveksling ikke består validering, og derefter BizTalk Services afbryder kun disse transaktion sæt. </br></br>Opdel udveksling som posteringen sæt - suspendere udveksling om fejl: fortolker hver transaktion, der er angivet i en udveksling i et separat XML-dokument ved at anvende den relevante konvolut. Med denne indstilling, hvis en eller flere transaktion angiver i udveksling ikke består validering, og derefter BizTalk Services afbryder hele udveksling.</br></br>Bevare Interchange - suspendere transaktion sæt om fejl: efterlader udveksling intakt, oprette en XML-dokument til hele batchopdelte udveksling. Med denne indstilling, hvis onAe eller flere transaktion angiver i udveksling ikke består validering, og derefter BizTalk Services afbryder kun disse transaktion sæt, mens du fortsætter med at behandle alle andre transaktions-sæt.</br></br>Bevare Interchange - suspendere udveksling om fejl: efterlader udveksling intakt, oprette en XML-dokument til hele batchopdelte udveksling. Med denne indstilling, hvis en eller flere transaction angiver i udveksling ikke består validering, og derefter BizTalk Services afbryder hele udveksling.</br></br>|

Aftalen, du er klar til at håndtere indgående meddelelser, der svarer til det valgte skema.

Sådan konfigureres de indstillinger, der håndtere meddelelser, du sender til partnere:  
11. Vælg **Send indstillinger** til at konfigurere, hvordan meddelelser, der sendes via denne aftale er skal håndteres.  

Indstillinger for Send kontrolelementet er inddelt i de følgende afsnit, herunder id'er, bekræftelse, skemaer, konvolutter, kontrolelement tal, tegnsæt og separatorer og validering. 

Her er en visning af disse kontrolelementer. Foretage valg baseret på, hvordan du vil håndtere meddelelser, du sender til partnere via denne aftale:   
![](./media/app-service-logic-enterprise-integration-x12/x12-4.png)  

![](./media/app-service-logic-enterprise-integration-x12/x12-5.png)  

![](./media/app-service-logic-enterprise-integration-x12/x12-6.png)  
12. Vælg **OK** for at gemme dine indstillinger.  

### <a name="identifiers"></a>Id'er
|Egenskaben|Beskrivelse |
|----|----|
|Godkendelse operator (ISA1)|Vælg værdien godkendelse operator på rullelisten.|
|ISA2|Angiv tilladelse oplysninger værdi. Hvis denne værdi er end 00, Angiv derefter mindst ét alfanumeriske tegn og op til 10.|
|Sikkerhed operator (ISA3)|Vælg værdien sikkerhed operator på rullelisten.|
|ISA4|Angiv sikkerhed oplysninger værdien. Hvis denne værdi er en anden end 00 for tekstfeltet værdi (ISA4), Angiv derefter mindst én alfanumerisk værdi og op til 10.|

### <a name="acknowledgment"></a>Bekræftelse
|Egenskaben|Beskrivelse |
|----|----|
|TA1 forventet|Markér dette afkrydsningsfelt for at returnere en tekniske (TA1) bekræftelse til afsenderen udveksling. Denne indstilling angiver, at den host-partner, som bruges til at sende meddelelsen anmoder om en bekræftelse fra gæst partner i aftalen. Disse bekræftelse forventes ved host partner baseret på indstillingerne modtager af aftalen.|
|FA forventet|Markér dette afkrydsningsfelt for at returnere en funktionelle (FA) bekræftelse til afsenderen udveksling, og vælg derefter, om du vil 997 eller 999 godkendelser, baseret på de skema versioner, du arbejder med. Disse bekræftelse forventes ved host partner baseret på indstillingerne modtager af aftalen.|
|FA Version|Vælg FA versionen|

### <a name="schemas"></a>Ved hjælp af skemaer
|Egenskaben|Beskrivelse |
|----|----|
|Version|Vælg X12 version|
|Transaktionstype (ST01)|Vælg typen|
|SKEMA|Vælge det skal bruges. Ved hjælp af skemaer kan findes i kontoen integration. For at få adgang til dine skemaer ved først at sammenkæde kontoen integration med din logik app.|

### <a name="envelopes"></a>Konvolutter
|Egenskaben|Beskrivelse |
|----|----|
|ISA11 brugen|Brug dette felt til at angive separatoren i et sæt transaktion:</br></br>Vælg Standard-id'et til at bruge decimal tegnet "." Modtag pipeline i stedet for decimal Notationen for indgående dokumentet i eller-Brugergrupper.</br></br>Vælg gentagelse separator, der skal angive separatoren for gentagne forekomster af et enkelt data-element eller en gentagen datastruktur. For eksempel bruges (^) normalt som gentagelse separator. Du kan kun bruge (^) til HIPAA skemaer.</br>|
|Gentagelse separator|Angiv separatoren, der gentagelse|
|Kontrolelementet versionsnummeret (ISA12)|Vælg den version af de almindelige X12, der bruges af portalen BizTalk-tjenester til at oprette en udgående udveksling.|
|Indikator for brugen (ISA15)|Angiv, om konteksten af en udveksling er oplysninger (I), fremstilling data (P) eller testdata (T). Eller-Brugergrupper modtage pipeline gør reklame for denne egenskab for konteksten.|
|Skema|Du kan angive, hvordan portalen BizTalk Services genererer GS og ST segmenter for en X12-kodet udveksling, der sendes til den sende rørledning.</br></br>Du kan knytte værdierne af GS1, GS2, GS3, GS4, GS5, GS7 og GS8 dataelementer med værdier af posttypen og versionen/dataelementer. Når portalen BizTalk Services bestemmer, som en XML-meddelelse med de værdier, der er angivet for typen og versionen/elementer i en række i gitteret, og derefter det udfylder GS1, GS2, GS3, GS4, GS5, GS7 og GS8 dataelementer i konvolutten af udgående udveksling med værdierne fra den samme række i gitteret. Værdierne af typen og versionen/elementer skal være entydig.</br></br>Valgfrit. Vælg en værdi for den funktionelle kode på rullelisten for GS1.</br></br>Der er påkrævet. Angiv en alfanumerisk værdi for GS2, for programmet afsenderen med mindst to tegn og maksimalt være på 15 tegn.</br></br>Der er påkrævet. Angiv en alfanumerisk værdi for GS3, for programmet modtageren med mindst to tegn og maksimalt være på 15 tegn.</br></br>Valgfrit. Vælg CCYYMMDD eller ÅÅMMDD for GS4.</br></br>Valgfrit. Vælg TTMM, TTMMSS eller HHMMSSdd for GS5.</br></br>Valgfrit. Vælg en værdi for ansvarlig kontoret på rullelisten for GS7.</br></br>Valgfrit. Angiv en alfanumerisk værdi for GS8, for det dokument, der er identificeret med mindst ét tegn og højst 12 tegn.</br></br>**Bemærk**: Dette er de værdier, der angiver portalen BizTalk-tjenester i felterne GS for den opretter Hvis posteringen skriver udveksling og versionen/elementer i samme række er en match for dem, der er knyttet til udveksling.|

### <a name="control-numbers"></a>Kontrolelementet tal
|Egenskaben|Beskrivelse |
|----|----|
|Interchange kontrolelementtal (ISA13)|Der er påkrævet. Angiv en række værdier for udveksling kontrolelement antallet bruges af portalen BizTalk-tjenester til at oprette en udgående udveksling. Angiv en numerisk værdi med mindst 1 og maksimalt 999999999.|
|Gruppen kontrolelementtal (GS06)|Der er påkrævet. Angiv området af tal, der skal bruge portalen BizTalk-tjenester for gruppen kontrolelement antallet. Angiv en numerisk værdi med mindst ét tegn og maksimalt ni tegn.|
|Transaktions angive kontrolelementtal (ST02)|Angiv et område af numeriske værdier for de påkrævede midterste felter og alfanumeriske værdier for valgfrit præfiks og suffiks for Transaction angive Control-nummer (ST02). Den maksimale længde på alle fire felter er ni tegn.|
|Præfiks|Hvis du vil angive talområde transaction sæt kontrol bruges i en bekræftelse, ved at angive værdier i felterne ACK kontrolelement tal (ST02). Angiv en numerisk værdi for de midterste to felter, og en alfanumerisk værdi (hvis det er nødvendigt) for præfiks og et suffiks felter. Felterne midterste er påkrævet og indeholder de mindste og største værdier for kontrolelementet sæt; præfiks og suffiks er valgfrit. Den maksimale længde for alle tre felter er ni tegn.|
|Suffiks|Hvis du vil angive talområde transaktion sæt kontrol bruges i en bekræftelse, ved at angive værdier i felterne ACK kontrolelement tal (ST02). Angiv en numerisk værdi for de midterste to felter, og en alfanumerisk værdi (hvis det er nødvendigt) for præfiks og et suffiks felter. Felterne midterste er påkrævet og indeholder de mindste og største værdier for kontrolelementet sæt; præfiks og suffiks er valgfrit. Den maksimale længde for alle tre felter er ni tegn.|

### <a name="character-sets-and-separators"></a>Tegnet sæt og separatorer
Bortset fra tegnsæt, kan du angive et andet sæt afgrænsere skal bruges til hver meddelelsestype. Hvis et tegnsæt ikke er angivet for en given meddelelsesskema, anvendes standardtegnsættet.

|Egenskaben|Beskrivelse |
|----|----|
|Tegnsæt, der skal bruges|Vælg X12 tegnsæt for at validere de egenskaber, du angiver for aftalen.</br></br>**Bemærk**: feltet BizTalk Services Portal kun bruger denne indstilling til at validere de værdier, der er angivet for egenskaberne relaterede aftale. Modtag pipeline eller send pipeline ignorerer denne tegnsæt egenskab, når du udfører Runtime-behandling.|
|Skema|Klikke på (+) symbol, og vælg et skema fra på rullelisten. Vælg separatorer oprette skal bruges for det valgte skema:</br></br>Komponent element separator – Angiv et enkelt tegn til at adskille sammensatte dataelementer.</br></br>Data Element Separator – Angiv et enkelt tegn til at adskille enkel dataelementer i sammensatte dataelementer.</br></br></br></br>Erstatningstegn – Markér dette afkrydsningsfelt, hvis de data, der er data indeholder tegn, der bruges også som data, segmentet eller komponent separatorer. Derefter kan du angive et erstatningstegn. Når du genererer den udgående meddelelse X12, alle forekomster af separatortegn i overførslen data erstattes med det angivne tegn.</br></br>Segment skal tildeles terminalpunkt – Angiv et enkelt tegn for at angive slutningen af en eller-Brugergrupper segment.</br></br>Suffiks – Vælg det tegn, der bruges med segmentet-id. Hvis du angiver et suffiks, kan målgruppen terminalpunkt dataelementet være tomt. Hvis segmentet terminalpunkt stå tomt, skal du angive et suffiks.|

### <a name="validation"></a>Datavalidering
|Egenskaben|Beskrivelse |
|----|----|
|Meddelelsestype|Hvis du vælger denne indstilling giver mulighed for validering på Udvekslingsmodtageren. Denne validering udfører eller-Brugergrupper validering på transaktions-sæt dataelementer, validering af datatyper, begrænsninger længde og tomme dataelementer og efterstillede separatorer.|
|Eller-Brugergrupper validering||
|Udvidet validering|Hvis du vælger denne indstilling giver mulighed for udvidede validering af udfletninger modtaget fra udveksling afsenderen. Dette omfatter validering af feltlængde, valgfrihed og antal gentagelser ud over XSD type datavalidering. Du kan aktivere lokalnummer validering uden at aktivere eller-Brugergrupper validering eller omvendt.|
|Tillad foranstillet / efterstillede nuller|Hvis du vælger denne indstilling angiver, at en eller-Brugergrupper udveksling modtaget fra part ikke ikke valideres, hvis et dataelement i en eller-Brugergrupper udveksling ikke stemmer overens med dens længde krav på grund af eller efterstillede mellemrum, men stemmer overens med dens længde krav, når de fjernes.|
|Efterstillede separator|Hvis du vælger denne indstilling angiver en eller-Brugergrupper udveksling modtaget fra part ikke ikke valideres, hvis et dataelement i en eller-Brugergrupper udveksling ikke stemmer overens med dens længde krav på grund af foranstillede (eller efterstillede) nuller eller efterstillede mellemrum, men stemmer overens med dens længde krav, når de fjernes.</br></br>Vælg ikke er tilladt, hvis du ikke vil tillade efterstillede afgrænsere og separatorer i en udveksling, der er modtaget fra udveksling afsenderen. Hvis udveksling indeholder efterstillede afgrænsere og separatorer, er det angivet ugyldige.</br></br>Vælg valgfrit at acceptere udfletninger med eller uden efterstillede afgrænsere og separatorer.</br></br>Vælg obligatorisk, hvis modtaget udveksling skal indeholde efterstillede afgrænsere og separatorer.|

Når du har markeret **OK** på de åbne blade:  
13. Vælg feltet **aftaler** på bladet Integration konto, og du får vist nyligt tilføjede aftalen angivet.  
![](./media/app-service-logic-enterprise-integration-x12/x12-7.png)   

## <a name="learn-more"></a>Lær mere
- [Lær mere om Enterprise Integration Pack] (./app-service-logic-enterprise-integration-overview.md "Få mere at vide om Enterprise Integration Pack")  
