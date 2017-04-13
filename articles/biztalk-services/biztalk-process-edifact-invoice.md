<properties
   pageTitle="Selvstudium: Behandle EDIFACT fakturaer ved hjælp af Azure BizTalk Services | Microsoft Azure BizTalk-tjenester"
   description="Hvordan du kan oprette og konfigurere app'en feltet forbindelse eller API og bruge det i en logik app i Azure App Service"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="msftman"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="deonhe"/>

# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>Selvstudium: Proces EDIFACT fakturaer ved hjælp af Azure BizTalk Services
Du kan bruge portalen BizTalk-tjenester til at konfigurere og implementere X12 og EDIFACT aftaler. I dette selvstudium se vi på hvordan du opretter en EDIFACT aftale til udveksling af fakturaer mellem handel partnere. Dette selvstudium skrives omkring en til slut virksomhedsløsning, der involverer to handel partnere, Northwind og Contoso, udveksle EDIFACT meddelelser.  

## <a name="sample-based-on-this-tutorial"></a>Eksempel, der er baseret på dette selvstudium
Dette selvstudium skrives omkring en prøve og **Sende EDIFACT fakturaer ved hjælp af BizTalk-tjenester**, som er tilgængelig til at hente fra [MSDN Code Gallery](http://go.microsoft.com/fwlink/?LinkId=401005). Du kan bruge stikprøvernes og gennemgå dette selvstudium at forstå, hvordan stikprøvernes blev oprettet. Eller du kan bruge dette selvstudium til at oprette din egen løsning på bunden af. Dette selvstudium er rettet mod den anden metode, så du forstå, hvordan denne løsning blev oprettet. Også som muligt, selvstudiet er i overensstemmelse med stikprøvernes og bruger de samme navne for elementer (for eksempel ved hjælp af skemaer, transformeringer) som anvendt i stikprøven.  

>[AZURE.NOTE] Da denne løsning omfatter at sende en meddelelse fra en EAI bro til en eller-Brugergrupper bro, genbruger eksemplet [BizTalk Services bro sammenkædning eksempel](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) .  

## <a name="what-does-the-solution-do"></a>Hvad gør løsningen?

I denne løsning modtager Northwind EDIFACT fakturaer fra Contoso. Disse fakturaer, der ikke er i et standardformat eller-Brugergrupper. Så før du sender fakturaen til Northwind, skal den omdannes til et dokument med EDIFACT faktura (også kaldet INVOIC). Modtager, skal Northwind behandle EDIFACT fakturaen og returnere Kontrolmeddelelsen (også kaldet CONTRL) til Contoso.

![][1]  

For at opnå denne dette scenario, bruger Contoso funktionerne med Microsoft Azure BizTalk-tjenester.

*   Contoso bruger EAI broer til at transformere den oprindelige faktura til EDIFACT INVOIC.

*   EAI bro derefter sender meddelelsen til en eller-Brugergrupper send bro, installeres som en del af en aftale, der er konfigureret i portalen BizTalk-tjenester.

*   Eller-Brugergrupper send bro behandler EDIFACT INVOIC og sender den til Northwind.

*   Når du modtager fakturaen, modtager Northwind returnerer en CONTRL meddelelse til eller-Brugergrupper bro installeres som en del af aftalen.  

> [AZURE.NOTE] Du kan også viser denne løsning også, hvordan du bruger "samling" til at sende fakturaerne i batches, i stedet for at sende hver faktura separat.  

For at fuldføre dette scenario skal bruge vi Service Bus køer til at sende fakturaen fra Contoso til Northwind eller modtage bekræftelse fra Northwind. Disse køer kan oprettes ved hjælp af en klientprogrammet, som er tilgængelig til overførsel og er inkluderet i pakken eksempel, der er tilgængelige som en del af dette selvstudium.  

## <a name="prerequisites"></a>Forudsætninger

*   Du skal have et Service Bus navneområde. Flere oplysninger om oprettelse af et navneområde, under [Sådan: oprette eller ændre en tjeneste Bus Service Namespace](https://msdn.microsoft.com/library/azure/hh674478.aspx). Lad os antage, at du allerede har et Service Bus navneområde klargjort, kaldet **edifactbts**.

*   Du skal have et BizTalk Services-abonnement. Flere oplysninger under [oprette en BizTalk-tjenesten ved hjælp af Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=302280). I dette selvstudium, Lad os antage du har et BizTalk Services-abonnement, kaldet **contosowabs**.

*   Registrere abonnementet BizTalk-tjenester på portalen BizTalk-tjenester. Yderligere oplysninger finder du [registrere en installation af BizTalk-tjenesten på portalen BizTalk-tjenester](https://msdn.microsoft.com/library/hh689837.aspx)

*   Du skal have Visual Studio er installeret.

*   Du skal have BizTalk Services SDK installeret. Du kan hente SDK fra [http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>Trin 1: Oprette Service Bus køerne  
Denne løsning bruger Service Bus køer til at udveksle meddelelser mellem handel partnere. Sende meddelelser til køerne fra hvor EAI og/eller eller-Brugergrupper broer forbruge dem, Contoso og Northwind. I denne løsning skal du tre Service Bus køer:

*   **northwindreceive** – Northwind modtager fakturaen fra Contoso over denne kø.

*   **contosoreceive** – Contoso modtager bekræftelsen fra Northwind via denne kø.

*   **suspenderet** – alle suspenderet dirigeres meddelelser til denne kø. Meddelelser er afbrydes midlertidigt, hvis de går ned under behandling.

Du kan oprette disse Service Bus køer ved hjælp af en inkluderet i pakken eksempel-klientprogrammet.  

1.  Åbn **Selvstudium sende fakturaer ved hjælp af BizTalk-tjenester eller-Brugergrupper Bridges.sln**fra den placering, hvor du har hentet stikprøvernes.

2.  Tryk på **F5** for at opbygge og starte **Selvstudium** klientprogrammet.

3.  På skærmbilledet skal du angive Service Bus ACS navneområde, udstedernavn og udsteder nøgle.

    ![][2]  
4.  En meddelelsesboks beder om, at der tre køer oprettes i din Service Bus navneområde. Klik på **OK**.

5.  Lad den selvstudium klient, der kører. Åbn den, skal du klikke på **Service Bus** > **_din Service Bus navneområde_** > **køer**, og Bekræft, at de tre køer oprettes.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>Trin 2: Oprette og installere handel partneraftale
Oprette en handel partneraftale mellem Contoso og Northwind. En handel partneraftale definerer en handel kontrakt mellem de to forretningsforbindelser, såsom hvilke meddelelsesskema til brug af, hvilken SMS protokol du skal bruge, osv. En handel partneraftale omfatter to eller-Brugergrupper broer, én til at sende meddelelser til handel partnere (kaldet **eller-Brugergrupper sende bro**) og én til at modtage meddelelser fra handel partnere (kaldet **eller-Brugergrupper modtage bro**).

I forbindelse med denne løsning, eller-Brugergrupper send bro svarer til send siden af aftalen og bruges til at sende EDIFACT fakturaen fra Contoso til Northwind. På samme måde, eller-Brugergrupper Modtag bro svarer til modtag siden af aftalen og bruges til at Modtag godkendelser fra Northwind.  

### <a name="create-the-trading-partners"></a>Oprette handel partnere

Oprette skal starte med handel partnere til Contoso og Northwind.  

1.  Klik på **Tilføj**i portalen BizTalk-tjenester under fanen **partnere** .

2.  Skriv **Contoso** som navnet på en partner på siden nyt partner, og klik derefter på **Gem**.

3.  Gentag trin for at oprette den anden partner **Northwind**.  

### <a name="create-the-agreement"></a>Oprette aftalen
Handel partner aftaler er oprettet mellem business profiler af handel partnere. Denne løsning bruger standard partner-profiler, der oprettes automatisk, når vi oprettede partnerne.  

1.  I portalen BizTalk-tjenester, skal du klikke på **aftaler** > **Tilføj**.

2.  Angive værdier, som vist i billedet nedenfor den nye aftale **Generelle indstillinger** på siden, og klik derefter på **Fortsæt**.

    ![][3]  

    Når du klikker på **Fortsæt**, blive to faner, **Modtager indstillinger** og **Sende indstillinger** tilgængelige.

3.  Oprette send aftalen mellem Contoso og Northwind. Denne aftale styrer, hvordan Contoso sender EDIFACT fakturaen til Northwind.

    1.  Klik på **Send indstillinger**.

    2.  Bevare standardværdierne under fanerne **Indgående URL-adresse**, **transformere**og **Batching** .

    3.  Overfør **EFACT_D93A_INVOIC.xsd** skemaet under afsnittet **ved hjælp af skemaer** under fanen **Protocol** . Dette skema er tilgængeligt med pakken eksempel.

        ![][4]  
    4.  Angiv oplysninger om Service Bus køerne under fanen **Transport** . Send side-aftale Brug vi **northwindreceive** køen at sende EDIFACT faktura til Northwind og **suspenderet** køen til at dirigere alle meddelelser, der mislykkes under behandling og er suspenderet. Du har oprettet disse køer i **trin 1: oprette Service Bus køerne** (i dette emne).

        ![][5]  

        Under **Transport Indstillinger > Transport type** og **annullering meddelelsesindstillingerne > Transport type**skal du vælge Azure Service Bus og leverer værdierne, som vist i billedet.

4.  Oprette Modtag aftalen mellem Contoso og Northwind. Denne aftale styrer, hvordan Contoso modtager bekræftelsen fra Northwind.

    1.  Klik på **Indstillinger for Modtag**.

    2.  Bevare standardværdierne under fanerne **Transport** og **transformere** .

    3.  Overfør **EFACT_4.1_CONTRL.xsd** skemaet under afsnittet **ved hjælp af skemaer** under fanen **Protocol** . Dette skema er tilgængeligt med pakken eksempel.

    4.  Oprette et filter for at sikre, at kun godkendelser fra Northwind, distribueres til Contoso under fanen **distribution** . Klik på **Tilføj** for at oprette filteret routing under **Indstillinger for distribution**.

        ![][6]  
        1.  Indeholder værdier for **Regelnavn**, **distribuere regel**og **distribuere destination** , som vist i billedet.

        2.  Klik på **Gem**.

    5.  På fanen **rute** igen, Angiv, hvor afbrudt godkendelser (godkendelser, mislykkes under behandling), distribueres til. Angiv Overførselstype til Azure Service Bus, distribuere destinationstype til **kø**, godkendelsestype til **Delt Access signatur** (SAS), give forbindelsesstrengen SAS til navneområdet Service Bus, og angiv derefter kø navnet som **suspenderet**.

5.  Til sidst skal du klikke på **Installer** for at installere aftalen. Bemærk slutpunkterne hvor send og modtag aftaler installeres.

    *   Bemærk slutpunktet under fanen **Send indstillinger** under **Indgående URL-adresse**. Hvis du vil sende en meddelelse fra Contoso til Northwind ved hjælp af eller-Brugergrupper send bro, skal du sende en meddelelse til dette slutpunkt.

    *   Bemærk slutpunktet under **Transport**, under fanen **Indstillinger for Modtag** . Sende en meddelelse fra Northwind til Contoso ved hjælp af eller-Brugergrupper modtage bro, skal du sende en meddelelse til dette slutpunkt.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>Trin 3: Oprette og installere BizTalk Services-projekt

I det forrige trin, du installeret eller-Brugergrupper send og modtage aftaler til at behandle EDIFACT fakturaer og godkendelser. Disse aftaler kan kun behandle meddelelser, der svarer til standard EDIFACT meddelelse skemaet. Dog per scenarie for denne løsning sender Contoso en faktura til Northwind i et internt beskyttede skema. Så før meddelelsen er sendt til eller-Brugergrupper send bro, skal den være transformeret fra det interne skema til standard EDIFACT faktura skemaet. BizTalk Services EAI project gør.

BizTalk Services-projekt, **InvoiceProcessingBridge**, der transformerer meddelelsen er også inkluderet som en del af eksemplet, som du har hentet. Projektet indeholder følgende elementer:

*   **INHOUSEINVOICE. XSD** – skema for den interne faktura, der sendes til Northwind.

*   **EFACT_D93A_INVOIC. XSD** – skema af standard EDIFACT faktura.

*   **EFACT_4.1_CONTRL. XSD** – skema af EDIFACT bekræftelsen, som Northwind sender til Contoso.

*   **INHOUSEINVOICE_to_D93AINVOIC. TRFM** – den transformation, der knytter interne faktura skemaet til standard EDIFACT faktura skemaet.  

### <a name="create-the-biztalk-services-project"></a>Oprette BizTalk Services-projekt
1.  Udvid InvoiceProcessingBridge projektet i Visual Studio-løsningen, og Åbn derefter filen **MessageFlowItinerary.bcs** .

2.  Klik et vilkårligt sted på lærredet og indstille **BizTalk-URL-adressen** i egenskabsfeltet til at angive BizTalk Services abonnementets navn. For eksempel `https://contosowabs.biztalk.windows.net`.

    ![][7]  
3.  Træk en **XML-One-Way bro** fra værktøjskassen til lærredet. Angiv egenskaberne **Objektnavn** og **Relativ adresse** i bro til **ProcessInvoiceBridge**. Dobbeltklik på **ProcessInvoiceBridge** for at åbne bro konfiguration overfladen.

4.  I feltet **Meddelelsestyper** skal du klikke på plustegnet (**+**) for at angive skemaet for den indgående meddelelse. Da den indgående meddelelse til EAI bro altid er den interne faktura, kan du indstille det til **INHOUSEINVOICE**.

    ![][8]  
5.  Klik på **XML-transformering** figuren, og klik på knappen ellipse (**...**) i egenskabsfeltet for egenskaben **kort** . Vælg fil, transformation **INHOUSEINVOICE_to_D93AINVOIC** i dialogboksen **Kort markeringen** , og klik derefter på **OK**.

    ![][9]  
6.  Gå tilbage til **MessageFlowItinerary.bcs**og fra værktøjskassen, skal du trække et **Two-Way eksterne Service slutpunkt** til højre for **ProcessInvoiceBridge**. Angiv egenskaben **Objektnavn** til **EDIBridge**.

7.  Udvid **MessageFlowItinerary.bcs** i Solution Explorer, og dobbeltklik på filen **EDIBridge.config** . Erstat indholdet af **EDIBridge.config** med følgende.

    > [AZURE.NOTE] Hvorfor skal jeg bruge til at redigere filen .config? Ekstern Tjenesteslutpunktet, der er tilføjet til bro Forespørgselsdesigner lærredet repræsenterer de eller-Brugergrupper broer, som vi tidligere installeret. Eller-Brugergrupper broer er tovejs broer, med en send og modtag side. EAI-bro, der er tilføjet til bro designer er dog en envejs bro. Hvis du vil håndtere de forskellige meddelelse exchange mønstre af de to broer, skal vi så, bruge en brugerdefineret bro funktionsmåde ved at medtage konfigurationen i .config-filen. Desuden håndterer brugerdefinerede funktionsmåden også godkendelse til eller-Brugergrupper send bro slutpunkt. Problemet brugerdefinerede findes som en separat eksempel på [BizTalk Services bro sammenkædning eksempel - EAI til eller-Brugergrupper](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104). Denne løsning genbruger stikprøvernes.  
    
    ```
<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <extensions>
          <behaviorExtensions>
            <add name="BridgeAuthentication" 
                  type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
          </behaviorExtensions>
        </extensions>
        <behaviors>
          <endpointBehaviors>
            <behavior name="BridgeAuthenticationConfiguration">
              <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
              <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                    issuername="owner" 
                                    issuersecret="[YOUR ACS SECRET]" />
              <webHttp />
            </behavior>
          </endpointBehaviors>
        </behaviors>
        <bindings>
          <webHttpBinding>
            <binding name="BridgeBindingConfiguration">
              <security mode="Transport" />
            </binding>
          </webHttpBinding>
        </bindings>
        <client>
          <clear />
          <!--
            Go BizTalk Portal > Agreement > Send Settings > Inbound URL
            Copy the Endpoint URL and paste it in the below address field
          -->
          <endpoint name="TwoWayExternalServiceEndpointReference1" 
                    address="[YOUR EDI BRIDGE SEND URI]" 
                    behaviorConfiguration="BridgeAuthenticationConfiguration" 
                    binding="webHttpBinding" 
                    bindingConfiguration="BridgeBindingConfiguration" 
                    contract="System.ServiceModel.Routing.IRequestReplyRouter" />
        </client>
      </system.serviceModel>
    </configuration>

    ```
8.  Opdatere filen EDIBridge.config indeholder oplysninger om konfiguration

    *   Under _<behaviors>_, angive ACS navneområde og nøgle, der er knyttet til abonnementet BizTalk-tjenester.

    *   Under _<client>_, giver det slutpunkt, hvor eller-Brugergrupper send aftalen er installeret.

    Gem ændringerne, og Luk konfigurationsfilen.

9.  Klik på **forbindelsen** , og deltage i **ProcessInvoiceBridge** og **EDIBridge** komponenterne fra værktøjskassen. Vælg forbindelsen, og angiv **Filterbetingelse** i Egenskaber for **Match**alle. Dette sikrer, at alle de meddelelser, der er behandlet af EAI bro distribueres til eller-Brugergrupper bro.

    ![][10]  
10.  Gemme ændringer i løsningen.  

### <a name="deploy-the-project"></a>Anvende projektet

1.  På den computer, hvor du har oprettet BizTalk Services-projekt, skal du hente og installere SSL-certifikat til abonnementet BizTalk-tjenester. Klik på **Dashboard**under BizTalk-tjenester fra, og klik derefter på **Hent SSL-certifikat**. Dobbeltklik på certifikatet, og følg prompten for at fuldføre installationen. Kontrollér, at du installerer certifikatet under **Rodnøglecentre** lager.

2.  Højreklik på **InvoiceProcessingBridge** projektet i Visual Studio Solution Explorer, og klik derefter på **Installer**.

3.  Indeholder værdierne, som vist i billedet, og klik derefter på **Installer**. Du kan få ACS legitimationsoplysningerne for BizTalk-tjenester ved at klikke på **Oplysninger om forbindelsen** fra dashboardet for BizTalk-tjenester.

    ![][11]  

    Kopiere det slutpunkt, hvor EAI bro er installeret, for eksempel fra ruden output `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Du skal bruge denne URL-adresse til slutpunkt senere.  

## <a name="step-4-test-the-solution"></a>Trin 4: Test løsningen


I dette emne, kan vi se på Sådan testes løsningen ved hjælp af **Selvstudium** klientprogrammet leveres som en del af prøven.  

1.  Tryk på F5 for at starte **Selvstudium klient**i Visual Studio.

2.  Skærmbilledet skal have værdierne på forhånd fra trinnet, hvor vi oprettede Service Bus køerne. Klik på **Næste**.

3.  I det næste vindue skal du angive ACS legitimationsoplysninger for BizTalk Services-abonnement og slutpunkterne hvor EAI- og eller-Brugergrupper (modtager) broer er installeret.

    Du har kopieret EAI bro slutpunkt i ovenstående trin. Eller-Brugergrupper modtager bro slutpunkt i portalen BizTalk-tjenester, skal du gå til aftalen > Indstillinger for Modtag > Transport > slutpunkt.

    ![][12]  
4.  Klik på knappen **Send interne faktura** under Contoso, i det næste vindue. Åbne dialogboksen i filen, Åbn filen INHOUSEINVOICE.txt. Undersøge indholdet af fil, og klik derefter på **OK** for at sende fakturaen.

    ![][13]  
5.  I et par sekunder modtages fakturaen på Northwind. Klik på linket **Vis meddelelse** for at få vist fakturaen modtages af Northwind. Bemærk, hvordan den faktura, der modtages af Northwind findes i standard EDIFACT skema, mens den, der er sendt af Contoso var et internt skema.

    ![][14]  
6.  Vælg fakturaen, og klik derefter på **Send bekræftelse**. Bemærk, at udveksling ID er samme i den modtagne faktura og bekræftelsen sendes i den dialogboks, der vises. Klik på OK i dialogboksen **Send bekræftelse** .

    ![][15]  
7.  I et par sekunder modtaget bekræftelsen korrekt hos Contoso.

    ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>Trin 5 (valgfrit): sende EDIFACT faktura i batches 
BizTalk-tjenester eller-Brugergrupper broer understøtter også samling i udgående meddelelser. Denne funktion bruges til at modtage partnere, som ønsker at modtage en mængde meddelelser (møde visse kriterium) i stedet for bestemte meddelelser.

Det vigtigste aspekt, når du arbejder med batches er den faktiske udgave af batchen, også kaldet kriteriet version. Kriteriet version kan være baseret på hvordan modtagelse partner ønsker at modtage meddelelser. Hvis "samling" er aktiveret, sender eller-Brugergrupper bro ikke den udgående meddelelse til modtagelse partneren indtil release kriterierne er opfyldt. For eksempel en batching kriterier baseret på meddelelse størrelse forsendelser en batch kun, når n meddelelser er batches. Kriterier for en batch kan også være et tidsbaserede, så en gruppe sendes på et fast tidspunkt hver dag. I denne løsning kan du prøve kriteriet størrelsen på e-mails baseret.

1.  Klik på den aftale, du oprettede tidligere i portalen BizTalk-tjenester. Klik på Indstillinger for Send > samling > Tilføj batchen.

2.  Angiv **InvoiceBatch**batchen navn, angive en beskrivelse, og derefter klikke på **Næste**.

3.  Angive et batchen kriterium, der definerer, hvilke meddelelser der skal være batches. I denne løsning batch vi alle meddelelser. Så, markere afkrydsningsfeltet Brug avancerede definitioner indstilling, og skriv **1 = 1**. Dette er en betingelse, som altid være SAND, og derfor alle meddelelser være batches. Klik på **Næste**.

    ![][17]  
4.  Angive et batchen release kriterium. Vælg **MessageCountBased**fra rullelisten, og for **antal**, angive **3**. Det betyder, at en mængde tre meddelelser, der sendes til Northwind. Klik på **Næste**.

    ![][18]  
5.  Gennemse oversigten, og klik derefter på **Gem**. Klik på **Installer** for at geninstallere aftalen.

6.  Gå tilbage til **Selvstudium klienten**, skal du klikke på **Send interne faktura**, og følg vejledningen for at sende fakturaen. Du vil bemærke, at ingen faktura er modtaget på Northwind, fordi batchstørrelsen ikke er opfyldt. Gentag dette trin to gange, så du har tre faktura meddelelser sendes til Northwind. Dette opfylder kriteriet batchen udgave af 3 meddelelser, og du bør nu se en faktura på Northwind.


<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG  
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG  
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG  
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG  
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG  
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG  
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG  
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG  
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG  
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG  
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG  
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG  
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG  
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG  
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG

