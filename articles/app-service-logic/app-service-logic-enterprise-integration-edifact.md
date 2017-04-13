<properties 
    pageTitle="Virksomhedsintegration med EDIFACT | Microsoft Azure" 
    description="Lær at bruge EDIFACT aftaler til at oprette logik apps" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="jeffhollan" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="app-service-logic" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/26/2016" 
    ms.author="jonfan"/>

# <a name="enterprise-integration-with-edifact"></a>Virksomhedsintegration med EDIFACT 

> [AZURE.NOTE] Denne side beskrives EDIFACT funktionerne i logik Apps. Oplysninger om X12 klikke [her](app-service-logic-enterprise-integration-x12.md).

## <a name="create-an-edifact-agreement"></a>Oprette en EDIFACT aftale 
Før du kan udveksle EDIFACT meddelelser, skal du oprette en aftale EDIFACT og gemme den i din integration-konto. Følgende trin fører dig gennem processen med at oprette en EDIFACT aftale.

### <a name="heres-what-you-need-before-you-get-started"></a>Her er, hvad du skal før du går i gang
- En [integration konto](./app-service-logic-enterprise-integration-accounts.md) , der er defineret i abonnementet Azure  
- Mindst to [partnere](./app-service-logic-enterprise-integration-partners.md) allerede er defineret i kontoen integration  

>[AZURE.NOTE]Når du opretter en aftale, skal indholdet i de meddelelser du vil modtage/send til og fra partner til aftaletypen.    


Når du har [oprettet en integration konto](./app-service-logic-enterprise-integration-accounts.md) og [føjet partnere](./app-service-logic-enterprise-integration-partners.md), kan du oprette en EDIFACT aftale ved at følge disse trin:  

### <a name="from-the-azure-portal-home-page"></a>Fra Azure portalwebstedets startside

Når du logger på [Azure portal](http://portal.azure.com "Azure-portalen"):  
1. Vælg **Find** i menuen til venstre.  

>[AZURE.TIP]Hvis du ikke kan se linket **Gennemse** , skal du muligvis først udvide menuen. Gør dette ved at vælge linket **Vis menu** , der er placeret på øverst til venstre i menuen skjult.  

![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-0.png)    
2. Skriv *integration* i feltet filter Søg og vælg derefter **Integration konti** på listen over resultater.       
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-3.png)    
3. Vælg kontoen integration, hvor du vil oprette aftalen i bladet **Integration konti** , der åbnes. Hvis du ikke kan se integration konti lister, [opretter en første](./app-service-logic-enterprise-integration-accounts.md "All about integration accounts").  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-4.png)  
4.  Vælg feltet **aftaler** . Hvis du ikke kan se feltet aftaler, tilføje den første.   
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-5.png)     
5. Vælg knappen **Tilføj** i bladet aftaler, der åbnes.  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-agreement-2.png)  
6. Angiv et **navn** til din aftale, og vælg den **type aftale** for EDIFACT, **Host Partner**, **Host identitet**, **Gæst Partner**, **Gæst identitet**, i bladet aftaler, der åbnes.  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1.png)  
7. Når du har angivet egenskaberne aftale, kan du vælge **Indstillinger for Modtag** til at konfigurere, hvordan meddelelser, der er modtaget via denne aftale er skal håndteres.  
8. Indstillinger for Modtag kontrolelementet er inddelt i de følgende afsnit, herunder id'er, bekræftelse, skemaer, kontrolelement tal, validering, interne indstillinger og batchbehandling. Konfigurere disse egenskaber, der er baseret på din aftale med partneren, du vil udveksle meddelelser med. Her er en visning af disse objekter skal du konfigurere dem baseret på, hvordan denne aftale til at identificere og håndtere indgående meddelelser:  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-2.png)  
9. Vælg **OK** for at gemme dine indstillinger.  

### <a name="identifiers"></a>Id'er

|Egenskaben|Beskrivelse |
|---|---|
|UNB6.1 (modtager Reference adgangskode)|Angiv en alfanumerisk værdi mellem 1 og 14 tegn.|
|UNB6.2 (modtager Reference operator)|Angiv en alfanumerisk værdi med mindst ét tegn og op til to tegn.|

### <a name="acknowledgments"></a>Bekræftelse 

|Egenskaben|Beskrivelse |
|----|----|
|Modtagelse af meddelelse (CONTRL)|Markér dette afkrydsningsfelt for at returnere en tekniske (CONTRL) bekræftelse til afsenderen udveksling. Bekræftelsen sendes til udveksling afsenderen baseret på siden Send indstillinger for aftalen.|
|Bekræftelse (CONTRL)|Markér dette afkrydsningsfelt for at returnere en funktionelle (CONTRL) bekræftelse til afsenderen udveksling bekræftelsen sendes til udveksling afsenderen baseret på siden Send indstillinger for aftalen.|

### <a name="schemas"></a>Ved hjælp af skemaer

|Egenskaben|Beskrivelse |
|----|----|
|UNH2.1 (TYPE)|Vælg typen transaktion sæt.|
|UNH2.2 (VERSION)|Angiv versionsnummeret meddelelse. (Minimum, ét tegn og maksimum, tre tegn).|
|UNH2.3 (UDGIVET VERSION)|Skriv meddelelsen release tal. (Minimum, ét tegn og maksimum, tre tegn).|
|UNH2.5 (TILKNYTTEDE TILDELTE KODE)|Angiv den tildelte kode. (Maksimum, seks tegn. "Skal være alfanumeriske).|
|UNG2.1 (APP AFSENDER-ID)|Angiv en alfanumerisk værdi med mindst ét tegn og højst 35 tegn.|
|UNG2.2 (APP AFSENDER KODE OPERATOR)|Angiv en alfanumerisk værdi med op til fire tegn.|
|SKEMA|Vælg det tidligere overførte skema, du vil bruge fra din tilknyttede Integration-konto.|

### <a name="control-numbers"></a>Kontrolelementet tal

|Egenskaben|Beskrivelse |
|----|----|
|Tillad ikke dubletter Interchange kontrolelementtal|Markér dette afkrydsningsfelt for at blokere dublerede udfletninger. Hvis markeret, kontrollerer handlingen EDIFACT afkode, udveksling kontrolelement antallet (UNB5) til modtagne udveksling ikke stemmer overens med et tidligere behandlede udveksling kontrolelementtal. Hvis der er fundet et match, og klik derefter på udveksling behandles ikke.
|Kontrollér, om dubletter UNB5 hver (dage)|Hvis du har valgt at Tillad ikke dubletter udveksling kontrolelement tal, kan du angive antallet dage, hvor kontrollen udføres ved at give den relevante værdi for at **kontrollere, om dubletter UNB5 hver (dage)** indstilling.|
|Tillad ikke gruppe kontrolelement tal dubletter|Markér dette afkrydsningsfelt for at blokere udfletninger med dublerede gruppe kontrolelement tal (UNG5).|
|Tillad ikke transaktion sæt kontrolelement tal dubletter|Markér dette afkrydsningsfelt for at blokere udfletninger med dublerede transaktion sæt kontrolelement tal (UNH1).|
|EDIFACT bekræftelse kontrolelementtal|Hvis du vil angive transaktion sæt referencenumre skal bruges i en bekræftelse, indtast en værdi for præfikset, et celleområde referencenumre og et suffiks.|

### <a name="validations"></a>Valideringer

|Egenskaben|Beskrivelse |
|----|----|
|Meddelelsestype|Angiv meddelelse. Hver række med datavalidering er fuldført, en anden, føjes automatisk. Hvis ingen regler ikke er angivet, markeret rækken som standard bruges til validering.|
|Eller-Brugergrupper validering|Markér dette afkrydsningsfelt for at udføre eller-Brugergrupper validering på datatyper, som defineret af eller-Brugergrupper egenskaberne for det skema, begrænsninger længde, tom dataelementer og efterstillede separatorer.|
|Udvidet validering|Markér dette afkrydsningsfelt for at aktivere udvidede (XSD) validering af udfletninger modtaget fra udveksling afsenderen. Dette omfatter validering af feltlængde, valgfrihed og antal gentagelser ud over XSD type datavalidering.|
|Tillad linjeafstand/efterstillede nuller|Vælge **Tillad** for at tillade linjeafstand/efterstillede nuller; **NotAllowed** til ikke at tillade linjeafstand/efterstillede nuller eller **trimme** trimme linjeafstanden og efterstillede nuller.|
|Trimme linjeafstand/efterstillede nuller|Markér dette afkrydsningsfelt for at trimme en hvilken som helst foranstillede eller efterstillede nuller|
|Efterstillede Separator politik|Vælg **Ikke er tilladt** , hvis du ikke vil tillade efterstillede afgrænsere og separatorer i en udveksling, der er modtaget fra udveksling afsenderen. Hvis udveksling indeholder efterstillede afgrænsere og separatorer, er det angivet ugyldige. Vælg **valgfrit** at acceptere udfletninger med eller uden efterstillede afgrænsere og separatorer. Vælg **obligatorisk** , hvis modtaget udveksling skal indeholde efterstillede afgrænsere og separatorer.|

### <a name="internal-settings"></a>Interne indstillinger

|Egenskaben|Beskrivelse |
|----|----|
|Oprette tomme XML-koder, hvis efterstillede separatorer er tilladt|Markér dette afkrydsningsfelt for at få udveksling afsenderen til at medtage tomme XML-koder til efterstillede separatorer.|
|Indgående samling behandling|Indstillinger, omfatter:</br></br>**Opdel udveksling som posteringen sæt - suspendere transaktion sæt om fejl**: fortolker hver transaktion, der er angivet i en udveksling i et separat XML-dokument ved at anvende den relevante konvolut til sættet transaktion. Med denne indstilling, hvis en eller flere transaktion angiver i udveksling ikke består validering, og derefter kun disse transaktion sæt er suspenderet. Opdel udveksling som posteringen sæt - suspendere udveksling om fejl: fortolker hver transaktion, der er angivet i en udveksling i et separat XML-dokument ved at anvende den relevante konvolut. Med denne indstilling, hvis en eller flere transaktion angiver i udveksling ikke består validering, og klik derefter på hele udveksling suspenderet.</br></br>**Bevare Interchange - suspendere transaktion sæt om fejl**: efterlader udveksling intakt, oprette en XML-dokument til hele batchopdelte udveksling. Med denne indstilling, hvis en eller flere transaktion angiver i udveksling ikke består validering, og derefter kun disse transaktion sæt er afbrudt, mens alle andre transaktion sæt behandles.</br></br>**Bevare Interchange - suspendere udveksling om fejl**: efterlader udveksling intakt, oprette en XML-dokument til hele batchopdelte udveksling. Med denne indstilling, hvis en eller flere transaktion angiver i udveksling ikke består validering, og klik derefter på hele udveksling er afbrudt.|

Aftalen, du er klar til at håndtere indgående meddelelser, der er i overensstemmelse med de valgte indstillinger.

Sådan konfigureres de indstillinger, der håndtere meddelelser, du sender til partnere:  
10. Vælg **Send indstillinger** til at konfigurere, hvordan meddelelser, der sendes via denne aftale er skal håndteres.  

Indstillinger for Send kontrolelementet er inddelt i de følgende afsnit, herunder id'er, bekræftelse, skemaer, konvolutter, tegnsæt og separatorer, kontrolelement tal og validering. 

Her er en visning af disse kontrolelementer. Foretage valg baseret på, hvordan du vil håndtere meddelelser, du sender til partnere via denne aftale:   
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-3.png)    
11. Vælg **OK** for at gemme dine indstillinger.  

### <a name="identifiers"></a>Id'er
|Egenskaben|Beskrivelse |
|----|----|
|UNB1.2 (syntaksen version)|Vælg en værdi mellem **1** og **4**.|
|UNB2.3 (omvendt Routing afsenderadressen)|Angiv en alfanumerisk værdi med mindst ét tegn og maksimalt 14 tegn.|
|UNB3.3 (modtager omvendt Routing adresse)|Angiv en alfanumerisk værdi med mindst ét tegn og maksimalt 14 tegn.|
|UNB6.1 (modtager Reference adgangskode)|Angiv en alfanumerisk værdi med mindst én og maksimalt 14 tegn.|
|UNB6.2 (modtager Reference operator)|Angiv en alfanumerisk værdi med mindst ét tegn og op til to tegn.|
|UNB7 (program Reference-ID)|Angiv en alfanumerisk værdi med mindst ét tegn og maksimalt 14 tegn|

### <a name="acknowledgment"></a>Bekræftelse
|Egenskaben|Beskrivelse |
|----|----|
|Modtagelse af meddelelse (CONTRL)|Markér dette afkrydsningsfelt, hvis den tilknyttede partner forventer at modtage for at modtage en tekniske bekræftelse (CONTRL). Denne indstilling angiver, at hostet partneren, der bruges til at sende meddelelsen, anmoder om en bekræftelse fra gæst partner.|
|Bekræftelse (CONTRL)|Markér dette afkrydsningsfelt, hvis den tilknyttede partner forventer at modtage en funktionelle bekræftelse (CONTRL). Denne indstilling angiver, at hostet partneren, der bruges til at sende meddelelsen, anmoder om en bekræftelse fra gæst partner.|
|Generere SG1/SG4 løkke for accepteret transaktion sæt|Hvis du vælger at anmode om en funktionelle bekræftelse, markere dette afkrydsningsfelt for at gennemtvinge oprettelse af SG1/SG4 løkker i funktionelle CONTRL bekræftelse for accepteret transaktion sæt.|

### <a name="schemas"></a>Ved hjælp af skemaer
|Egenskaben|Beskrivelse |
|----|----|
|UNH2.1 (TYPE)|Vælg typen transaktion sæt.|
|UNH2.2 (VERSION)|Angiv versionsnummeret meddelelse.|
|UNH2.3 (UDGIVET VERSION)|Skriv meddelelsen release tal.|
|SKEMA|Vælge det skal bruges. Ved hjælp af skemaer kan findes i kontoen integration. For at få adgang til dine skemaer ved først at sammenkæde kontoen integration med din logik app.|

### <a name="envelopes"></a>Konvolutter
|Egenskaben|Beskrivelse |
|----|----|
|UNB8 (Processing prioritetskode)|Angiv en alfabetisk værdi, som ikke er mere end ét tegn lang.|
|UNB10 (kommunikation aftalen)|Angiv en alfanumerisk værdi med mindst ét tegn og højst 40 tegn.|
|UNB11 (teste indikator)|Markér dette afkrydsningsfelt for at angive, at udveksling genereres er testdata|
|Anvende UNA segmentet (Service streng råd)|Markér dette afkrydsningsfelt for at oprette en UNA målgruppe til udveksling til at blive sendt.|
|Anvende UNG segmenter (funktionen gruppehovedet)|Markér dette afkrydsningsfelt for at oprette gruppering målgrupper i funktionelle gruppehovedet i de meddelelser, der er sendt til gæst partneren. Følgende værdier bruges til at oprette UNG målgrupper:</br></br>Angiv en alfanumerisk værdi med mindst ét tegn og op til seks tegn for **UNG1**.</br></br>Angiv en alfanumerisk værdi med mindst ét tegn og højst 35 tegn for **UNG2.1**.</br></br>Angiv en alfanumerisk værdi med op til fire tegn for **UNG2.2**.</br></br>Angiv en alfanumerisk værdi med mindst ét tegn og højst 35 tegn for **UNG3.1**.</br></br>Angiv en alfanumerisk værdi med op til fire tegn for **UNG3.2**.</br></br>Angiv en alfanumerisk værdi med mindst én og op til tre tegn for **UNG6**.</br></br>Angiv en alfanumerisk værdi med mindst ét tegn og op til tre tegn for **UNG7.1**.</br></br>Angiv en alfanumerisk værdi med mindst ét tegn og op til tre tegn for **UNG7.2**.</br></br>Angiv en alfanumerisk værdi med mindst 1 tegn og højst 6 tegn for **UNG7.3**.</br></br>Angiv en alfanumerisk værdi med mindst ét tegn og maksimalt 14 tegn for **UNG8**.|

### <a name="character-sets-and-separators"></a>Tegnet sæt og separatorer
Bortset fra tegnsæt, kan du angive et andet sæt afgrænsere skal bruges til hver meddelelsestype. Hvis et tegnsæt ikke er angivet for en given meddelelsesskema, anvendes standardtegnsættet.

|Egenskaben|Beskrivelse |
|----|----|
|UNB1.1 (System-id)|Vælg det EDIFACT tegnsæt der skal anvendes på udgående udveksling.|
|Skema|Vælg et skema på rullelisten. En ny række, føjes, som hver række er fuldført. Vælg separatorer oprette skal bruges for det valgte skema:</br></br>**Komponent element separator** – Angiv et enkelt tegn til at adskille sammensatte dataelementer.</br></br>**Data Element Separator** – Angiv et enkelt tegn til at adskille enkel dataelementer i sammensatte dataelementer.</br></br></br></br>**Erstatningstegn** – Markér dette afkrydsningsfelt, hvis de data, der er data indeholder tegn, der bruges også som data, segmentet eller komponent separatorer. Derefter kan du angive et erstatningstegn. Når der genereres udgående EDIFACT meddelelsen, erstattes alle forekomster af separatortegn i selve dataene med det angivne tegn.</br></br>**Segmentet terminalpunkt** – Angiv et enkelt tegn til at angive slutningen af en eller-Brugergrupper segment.</br></br>**Suffiks** – Vælg det tegn, der bruges med segmentet-id. Hvis du angiver et suffiks, kan målgruppen terminalpunkt dataelementet være tomt. Hvis segmentet terminalpunkt stå tomt, skal du angive et suffiks.|

### <a name="control-numbers"></a>Kontrolelementet tal
|Egenskaben|Beskrivelse |
|----|----|
|UNB5 (Interchange kontrolelementtal)|Angiv et præfiks, en række værdier til udveksling kontrolelementtal og et suffiks. Disse værdier, der bruges til at oprette en udgående udveksling. Præfiks og suffiks er valgfrie. kontrolelementet antallet er påkrævet. Kontrolelementet antallet øges for hver ny meddelelse præfiks og suffiks forbliver de samme.|
|UNG5 (gruppere kontrolelementtal)|Angiv et præfiks, en række værdier til udveksling kontrolelementtal og et suffiks. Disse værdier bruges til at generere gruppe kontrolelementtal. Præfiks og suffiks er valgfrie. kontrolelementet antallet er påkrævet. Kontrolelementet antallet øges for hver ny meddelelse, indtil den maksimale værdi er nået. præfiks og suffiks forbliver de samme.|
|UNH1 (Message sidehoved referencenummeret)|Angiv et præfiks, en række værdier til udveksling kontrolelementtal og et suffiks. Disse værdier, der bruges til at generere referencenummeret meddelelse sidehoved. Præfiks og suffiks er valgfrie. referencenummeret er påkrævet. Referencenummeret øges for hver ny meddelelse præfiks og suffiks forbliver de samme.|

### <a name="validations"></a>Valideringer
|Egenskaben|Beskrivelse |
|----|----|
|Meddelelsestype|Hvis du vælger denne indstilling giver mulighed for validering på Udvekslingsmodtageren. Denne validering udfører eller-Brugergrupper validering på transaktions-sæt dataelementer, validering af datatyper, begrænsninger længde og tomme dataelementer og kurser separatorer.|
|Eller-Brugergrupper validering|Markér dette afkrydsningsfelt for at udføre eller-Brugergrupper validering på datatyper, som defineret af eller-Brugergrupper egenskaberne for det skema, begrænsninger længde, tom dataelementer og efterstillede separatorer.|
|Udvidet validering|Hvis du vælger denne indstilling giver mulighed for udvidede validering af udfletninger modtaget fra udveksling afsenderen. Dette omfatter validering af feltlængde, valgfrihed og antal gentagelser ud over XSD type datavalidering. Du kan aktivere lokalnummer validering uden at aktivere eller-Brugergrupper validering eller omvendt.|
|Tillad linjeafstand/efterstillede nuller|Hvis du vælger denne indstilling angiver, at en eller-Brugergrupper udveksling modtaget fra part ikke ikke valideres, hvis et dataelement i en eller-Brugergrupper udveksling ikke stemmer overens med dens længde krav på grund af eller efterstillede mellemrum, men stemmer overens med dens længde krav, når de fjernes.|
|Trimme linjeafstand/efterstillede nuller|Hvis du vælger denne indstilling kan trimme de foranstillede og efterstillede nuller.|
|Efterstillede separator|Hvis du vælger denne indstilling angiver en eller-Brugergrupper udveksling modtaget fra part ikke ikke valideres, hvis et dataelement i en eller-Brugergrupper udveksling ikke stemmer overens med dens længde krav på grund af foranstillede (eller efterstillede) nuller eller efterstillede mellemrum, men stemmer overens med dens længde krav, når de fjernes.</br></br>Vælg **Ikke er tilladt** , hvis du ikke vil tillade efterstillede afgrænsere og separatorer i en udveksling, der er modtaget fra udveksling afsenderen. Hvis udveksling indeholder efterstillede afgrænsere og separatorer, er det angivet ugyldige.</br></br>Vælg **valgfrit** at acceptere udfletninger med eller uden efterstillede afgrænsere og separatorer.</br></br>Vælg **obligatorisk** , hvis modtaget udveksling skal indeholde efterstillede afgrænsere og separatorer.|

Når du har markeret **OK** på bladet åben:  
12. Vælg feltet **aftaler** på bladet Integration konto, og du får vist nyligt tilføjede aftalen angivet.  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-4.png)   

## <a name="learn-more"></a>Lær mere
- [Lær mere om Enterprise Integration Pack] (./app-service-logic-enterprise-integration-overview.md "Få mere at vide om Enterprise Integration Pack")  
