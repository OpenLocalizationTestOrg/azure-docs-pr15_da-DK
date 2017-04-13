<properties
    pageTitle="Produktbemærkninger til Azure BizTalk Services | Microsoft Azure BizTalk-tjenester"
    description="Viser en liste over kendte problemer for Azure BizTalk-tjenester" 
    services="biztalk-services"
    documentationCenter=""
    authors="msftman"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="deonhe"/>

# <a name="release-notes-for-azure-biztalk-services"></a>Produktbemærkninger til Azure BizTalk-tjenester

Produktbemærkninger til Microsoft Azure BizTalk Services indeholder de kendte problemer i denne udgave.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>Hvad er nyt i November opdateringen af BizTalk-tjenester
* Kryptering på resten kan aktiveres på portalen BizTalk-tjenester. Se [aktivere kryptering på resten BizTalk Services-portalen](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## <a name="update-history"></a>Opdateringsoversigt

### <a name="october-update"></a>Oktober opdatering

* Organisationskonti understøttes:  
 * **Scenarie**: du har registreret en BizTalk Service-installation, ved hjælp af en Microsoft-konto (som user@live.com). I dette scenarie kan kun Microsoft Account brugere administrere de BizTalk Service ved hjælp af portalen BizTalk-tjenester. En organisationskonto kan ikke bruges.  
 * **Scenarie**: du har registreret en BizTalk Service-installation med en organisationskonto i et Azure Active Directory (som user@fabrikam.com eller user@contoso.com). I dette scenarie kan kun Azure Active Directory-brugere i samme organisation administrere de BizTalk Service ved hjælp af portalen BizTalk-tjenester. En Microsoft-konto kan ikke bruges.  
* Når du opretter en BizTalk Service på portalen Azure klassisk, registreres du automatisk i portalen BizTalk-tjenester.
 * **Scenarie**: du logge på Azure klassisk portalen, oprette en BizTalk Service, og klik derefter på **Administrer** for første gang. Når portalen BizTalk Services åbnes, skal BizTalk Service automatisk registrerer og er klar til dine installationer.  
 Se [registrere og opdatere en BizTalk Service-installation på portalen BizTalk-tjenester](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### <a name="august-14-update"></a>August 14 opdatering
* Aftale og bro afkobling – handel partner aftaler og broer er nu frakoblet i portalen BizTalk-tjenester. Du nu oprette aftaler og broer separat, og på kørselstidspunktet broer løse til en aftale, der er baseret på værdierne i meddelelsen eller-Brugergrupper. Se [Oprette aftaler i Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689908.aspx), [oprette en eller-Brugergrupper bro portalen til BizTalk-tjenester](https://msdn.microsoft.com/library/azure/dn793986.aspx), [Opret en AS2 bro portalen til BizTalk-tjenester](https://msdn.microsoft.com/library/azure/dn793993.aspx), og [hvordan løser broer aftaler på kørselstidspunktet?](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* Muligheden for at oprette skabeloner til aftaler er udgået.  
* Du kan nu angive forskellige afgrænser sæt for hvert skema for aftalen send side. Denne konfiguration er angivet under protocol indstillinger for send side aftale. Kan finde flere oplysninger under [Opret en X12 aftale i Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689847.aspx) og [oprette en EDIFACT aftale i Azure BizTalk-tjenester](https://msdn.microsoft.com/library/azure/dn606267.aspx). To nye objekter også tilføjes til TPM OM API til samme formål. Se [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) og [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* Standard XSD konstruktioner, herunder afledt datatyper understøttes nu. Se [Brug standard XSD konstruktioner i dit kort](https://msdn.microsoft.com/library/azure/dn793987.aspx) og [Brug afledt typer i tilknytning scenarier og eksempler](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* AS2 understøtter nye Mikrofon algoritmer til meddelelse signering og krypteringsalgoritmer, der er nye. Se [oprette en AS2 aftale i Azure BizTalk-tjenester](https://msdn.microsoft.com/library/azure/hh689890.aspx).  
## <a name="know-issues"></a>Kendte problemer

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>Problemer med serverforbindelsen efter BizTalk Services opdatering af portalen

  Hvis du har portalen BizTalk-tjenester, der åbner, mens BizTalk Services er opgraderet til at rulle ændringer til tjenesten, kan du står over forbindelsesproblemer med portalen BizTalk-tjenester.  
  Som en løsning, kan du genstarte browseren, slette browserens cache eller starte portalen i privat tilstand.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>Visual Studio IDE kan ikke finde genstand, hvis du klikker på en fejl eller advarsel i et BizTalk Services-projekt
Installer Visual Studio 2012 Update 3 RC 1 for at løse problemet.  

### <a name="custom-binding-project-reference"></a>Brugerdefinerede binding projektreference
Overvej følgende situationer med et BizTalk Services-projekt i et Visual Studio-løsning:  
* I den samme Visual Studio-løsning er der et BizTalk Services-projekt og et brugerdefineret binding projekt. BizTalk Service projektet har en reference til denne brugerdefinerede binding projektfil.
* BizTalk Service projektet har en reference til en brugerdefineret binding/funktionsmåde DLL.

Du oprette' ' løsningen i Visual Studio korrekt. Derefter skal du 'Genopbygge' eller 'Rydde' løsningen. Når du genopbygger eller rydde igen, opstår herefter følgende fejl:  
  Fil kan ikke kopieres <Path to DLL> til "bin\Debug\FileName.dll". Processen kan ikke få adgang til filen 'bin\Debug\FileName.dll', fordi den bruges af en anden proces.  

#### <a name="workaround"></a>Løsning
* Hvis [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) er installeret, har du følgende to muligheder:

  * Genstart Visual Studio, eller

  * Genstart løsningen. Udfør derefter kun et Build på løsningen.  

* Hvis [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) ikke er installeret, åbne Jobliste, klik på fanen processer, klik på MSBuild.exe processen og derefter klikke på knappen afslutningsproces.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>Routing til BasicHttpRelay slutpunkter understøttes ikke fra broer og BizTalk Services Portal, hvis du ikke kan udskrives tegn er overført som HTTP-headere

Hvis du bruger ikke kan udskrives tegn som en del af overførte egenskaber for meddelelser, kan disse meddelelser dirigeres til relay destinationer, der bruger BasicHttpRelay binding. Desuden overførte egenskaber, der er tilgængelige som en del af registrering er URL-kodet for BLOB og ophævelse kodet for destinationer.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>MDN sendes asynkront, selvom indstillingen Send asynkron MDN er markeret  
Overvej følgende scenarie – Hvis du markerer afkrydsningsfeltet **Send asynkron MDN** og angive en URL-adresse for at sende asynkron MDN til, og fjern derefter markeringen i afkrydsningsfeltet **Send asynkron MDN** igen, MDN stadig sendes til den angivne URL-adresse selvom mulighed for at sende asynkron MDNs ikke er markeret.  
Du kan løse problemet, skal du rydde den angivne URL-adresse, før du fjerne markeringen i afkrydsningsfeltet **Send asynkron MDN** og derefter installere AS2 aftalen.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>Mellemrumstegn ud over en gyldig udveksling medføre en tom meddelelse skal sendes til slutpunktet pause  
Hvis der er mellemrum ud over en IEA segmentet, disassembler behandler dette som slutningen af aktuelle udveksling og ser på den næste række mellemrum som næste meddelelse. Da dette ikke er gyldige udveksling, kan du se, at en vellykket meddelelse sendes til den rute destination og en tom meddelelse sendes pause slutpunktet.  
### <a name="tracking-in-biztalk-services-portal"></a>Sporing af BizTalk Services-portalen  
Sporing hændelser er hentet op til eller-Brugergrupper meddelelsesbehandling og sammenhængen. Hvis meddelelsen ikke uden for fasen protokol, vises registrering som vellykket. I så fald se afsnittet LOG under kolonnen **Detaljer** i **registrering** for flere oplysninger om fejlen.
X12 sende og modtage indstillinger ([Opret en X12 aftale i Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689847.aspx)) giver oplysninger om fasen Protocol.  

### <a name="update-agreement"></a>Opdater aftaler  
Portalen BizTalk-tjenester kan du ændre operator for en identitet, når en aftale, der er konfigureret. Dette kan medføre inconsistence egenskaber. Der er for eksempel en aftale, ved hjælp af ZZ:1234567 og ZZ:7654321 operatoren. Du kan ændre ZZ:1234567 for at være 01:ChangedValue i profilindstillinger BizTalk Services Portal. Du åbner aftalen, og 01:ChangedValue vises i stedet for ZZ:1234567.
Hvis du vil ændre operator for en identitet, slette aftalen, opdatere **identiteter** i partner profil og derefter genskabe aftalen.  
> AZURE. Advarsel om dette problem påvirker X12 og AS2.  

### <a name="as2-attachments"></a>AS2 vedhæftede filer  
Vedhæftede filer til AS2 meddelelser der ikke understøttes i sende eller modtage. Nærmere betegnet kan vedhæftede filer automatisk, ignoreres og meddelelsesteksten behandles som en almindelig AS2 meddelelse.  
### <a name="resources-remembering-path"></a>Ressourcer: Huske sti  
Når du tilføjer **ressourcer**, kan dialogboksvinduet ikke kan huske den sti, der tidligere har brugt til at føje en ressource. For at huske stien tidligere har brugt, skal du prøve at tilføje BizTalk Services Portal-websted til **Zonen med websteder** i Internet Explorer.  
### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>Hvis du omdøber enhed navnet på en bro og lukke projektet uden at gemme ændringerne, medfører åbne objektet igen en fejl
Overvej et scenarie i følgende rækkefølge:  
* Føje en bro (for eksempel en XML-One-Way bro) til et BizTalk Service-projekt  

* Omdøb bro ved at angive en værdi for egenskaben objektnavn. Dette omdøber filen tilknyttede .bridgeconfig med det angivne navn.  

* Luk filen .bcs (ved at lukke fanen i Visual Studio) uden at gemme ændringerne.  

* Åbn filen .bcs igen fra Solution Explorer.  
Du vil bemærke, selvom filen tilknyttede .bridgeconfig har det nye navn, du har angivet, navnet på den enhed på designoverfladen er stadig det gamle navn. Hvis du forsøger at åbne bro konfigurationen ved at dobbeltklikke på komponenten bro, får du vist følgende fejlmeddelelse:  
  '<old name>'Enhed er tilknyttet filen'<old name>.bridgeconfig' findes ikke  
Kontrollér, at du gemmer ændringer, når du omdøber objekterne i et BizTalk Service projekt for at undgå at løbe i dette scenarie.  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>BizTalk Service project opbygger korrekt, selvom en genstand har er udeladt i en Visual Studio-projekt
Forestil dig en situation, hvor du har tilføjet en genstand (for eksempel en XSD-fil) til et BizTalk Service projekt, medtage genstand i konfigurationen af bro (for eksempel ved at angive den som en anmodning om meddelelse) og udelukke det fra Visual Studio-projekt. I så fald får opbygning af projektet ikke en fejl som den slettede genstand findes på disken på det samme sted, hvor det blev inkluderet i Visual Studio-projekt.
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>BizTalk Service projektet søger ikke efter skema tilgængelighed under konfigurationen af broer
I et projekt BizTalk Service Hvis et skema, der er føjet til projektet importerer et andet skema kontrollerer BizTalk Service projektet ikke, om det importerede skema er føjet til projektet. Hvis du forsøger at oprette disse et projekt, får du ikke buildfejl.
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>Svarmeddelelse til en XML-anmodning om autosvar bro er altid af tegnsættet UTF-8
I denne udgave angives altid tegnsættet meddelelsens svar fra en XML-anmodning om autosvar bro til UTF-8.
### <a name="user-defined-datatypes"></a>Brugerdefinerede datatyper
BizTalk-Adapter Pack kort i funktionen BizTalk Adapter Service kan anvende brugerdefinerede datatyper for adapter handlinger.
Når du bruger brugerdefinerede datatyper, kan du kopiere-filerne til drev: \Programmer\Microsoft BizTalk-Adapter Service\BAServiceRuntime\bin\ eller til den globale Cache (assembly) på serveren værtstjeneste BizTalk Adapter Service. Ellers kan følgende fejl opstå på klienten:  
```<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
  <faultcode>s:Client</faultcode>
  <faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
  <detail>
    <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
    </AFConnectRuntimeFault>
  </detail>
</s:Fault> ```  
> [AZURE.IMPORTANT] Det anbefales at bruge GACUtil.exe til at installere en fil i den globale Cache samling. GACUtil.exe dokumenter, hvordan du bruger dette værktøj og Visual Studio-kommandolinjeparametre.  

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>Genstart webstedet BizTalk-Adapter Service
Installere **BizTalk-Adapter Service Runtime** * opretter den * *BizTalk Adapter Service* * webstedet i IIS, der indeholder den * *BAService* * programmet.* *BAService** program bruger internt relay binding til at udvide omfanget af lokal tjenesteslutpunkt i skyen. En tjeneste er vært for lokalt registreres tilsvarende relay slutpunktet på Service Bus kun, når tjenesten lokale starter.  

Hvis du stopper og starter et program, er konfigurationen til at starte et program ikke accepteret. Så når **BAService** stoppes, skal du altid genstarte **BizTalk Adapter Service** -websted i stedet. Ikke starte eller stoppe programmet **BAService** .
### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>Specialtegn, der ikke bør benyttes til adresse og enhed navnene på LOB komponenter
Du skal ikke bruge specialtegn til adresse og enhed navnene på LOB komponenter. Hvis du gør dette, får du en fejl under installation af BizTalk Service projektet. For visse tegn som '%' webstedet BizTalk Adapter Service kan gå til en standset tilstand, og du nødt til at starte den manuelt.
### <a name="test-map-with-get-context-property"></a>Test kort med få kontekstegenskab
Hvis en transformering, der indeholder en handling af **Få Kontekstegenskaben** kort, mislykkes **Test kort** . Erstat handlingen **Få Kontekstegenskaben** kort med en streng Concatenate kort handling med dummy data som en midlertidig løsning. Dette kan udfylde target skemaet og tillade du teste andre transformation funktioner.
### <a name="test-map-property-does-not-display"></a>Test Tilknyt egenskab, vises ikke
**Test kort** egenskaber vises ikke i Visual Studio. Dette kan ske, hvis vinduet **Egenskaber** og vinduet **Solution Explorer** ikke er samtidigt er forankret. Forankre **Egenskaber** og **Solution Explorer** -vinduerne for at løse dette problem.  
### <a name="datetime-reformat-drop-down-is-grayed-out"></a>DateTime omformatere rullelisten er nedtonet
Når en DateTime omformatere handling kortet er føjet til designoverfladen og konfigureret, kan være nedtonet rullelisten Format. Dette kan ske, hvis computeren visning er angivet **mellem – 125%** eller **større – 150%**. Angive visningen **mindre – 100% (standard)** ved hjælp af nedenstående trin for at løse:  
1. Åbn **Kontrolpanel** , og klik på **udseende og tilpasning**.
2. Klik på **skærmen**.
3. Klik på **mindre – 100% (standard)** , og klik på **Anvend**.

Rullelisten **Format** skal nu fungerer som forventet.
### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>Duplikere aftaler i portalen BizTalk-tjenester
Forestil dig følgende:
1. Oprette en aftale med handel Partner administration OM API.
2. Åbn aftalen i portalen BizTalk-tjenester i to forskellige faner.
3. Installer aftale fra begge fanerne.
4. Som et resultat installeres begge aftalerne medfører dubletter i portalen BizTalk-tjenester

**Løsning**. Åbn en af de dublerede aftaler i portalen BizTalk-tjenester, og fjernes.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>Broer Brug ikke opdateret certifikat, selv efter at et certifikat er blevet opdateret i genstand store
Overvej følgende scenarier:  

**Scenarie 1: Bruge miniature-baserede certifikater til sikring af overførsel af e-mail fra en bro til en tjenesteslutpunkt**  
Overvej et scenarie, hvor du bruger miniature-baserede certifikater i projektet BizTalk Service. Du opdatere certifikatet i portalen BizTalk-tjenester med det samme navn, men en anden miniature, men opdatere ikke BizTalk Service projektet, i overensstemmelse hermed. I et scenarie, kan bro fortsætte med at behandle meddelelserne, fordi ældre certifikatets data kan stadig være i cachen kanal. Herefter mislykkes behandling af meddelelse.  

**Løsning**: Opdater certifikatet i BizTalk Service projektet og geninstaller projektet.  

**Scenarie 2: Brug af navn-baserede funktionsmåder til at identificere certifikater til sikring af overførsel af e-mail fra en bro til en tjenesteslutpunkt**

Overvej et scenarie, hvor du bruger navn-baserede funktionsmåder til at identificere certifikater i projektet BizTalk Service. Du opdaterer certifikatet i portalen BizTalk-tjenester, men opdatere ikke BizTalk Service projektet, i overensstemmelse hermed. I et scenarie, kan bro fortsætte med at behandle meddelelserne, fordi ældre certifikatets data kan stadig være i cachen kanal. Herefter mislykkes behandling af meddelelse.  

**Løsning**: Opdater certifikatet i BizTalk Service projektet og geninstaller projektet.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>Broer fortsætte med at behandle meddelelser, selvom SQL-databasen er offline
BizTalk Services broer fortsætte med at behandle meddelelser til et stykke tid, selvom den Microsoft Azure SQL-Database (som indeholder de oplysninger, der kører som udløst elementer og rørledninger), er offline. Dette skyldes, at BizTalk Services bruger den cachelagrede elementer og Brokonfiguration.
Hvis du ikke vil broer til at behandle meddelelserne, når den SQL-Database er offline, kan du bruge BizTalk Services PowerShell-cmdlet'er til at stoppe eller annullere BizTalk Service. Se [Azure BizTalk Service Management eksempel](http://go.microsoft.com/fwlink/p/?LinkID=329019) for Windows PowerShell-cmdlet'er til at administrere handlinger.  
### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>Læse XML-meddelelsen i en bro brugerdefineret kode komponent indeholder et ekstra Stykliste tegn
Overvej et scenarie, hvor du vil læse en XML-meddelelse i en bro brugerdefineret kode. Hvis du bruger .NET API System.Text.Encoding.UTF8.GetString(bytes) et ekstra Stykliste tegn er medtaget i outputtet i starten af meddelelsen. Hvis du ikke vil medtage tegnet ekstra Stykliste output, skal du så bruge ```System.IO.StreamReader().ReadToEnd()```.
### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>Sende meddelelser til en bro ved hjælp af WCF kan ikke følge
Meddelelser, der sendes til en bro ved hjælp af WCF kan ikke følge. I stedet skal du bruge HttpWebRequest, hvis du vil have en SVG-klient.
### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>OPGRADERING: Token udbyder fejl efter opgradering fra BizTalk Services Preview til generelle tilgængelighed (GA)
Der er en eller-Brugergrupper eller AS2 aftale med aktive batches. Når BizTalk Service er opgraderet fra forhåndsvisning til GA, forekomme følgende:
* Fejl: Provideren sikkerhedstoken kunne ikke angive et sikkerhedstoken. Token udbyder returnerede meddelelse: remote navnet blev ikke fundet.

* Batchopgaver er annulleret.

**Løsning**: efter feltet BizTalk Service opdateres til generelle tilgængelighed (GA), geninstaller aftalen.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>OPGRADERING: Værktøjskassen viser ikonerne gamle bro efter opgradering af BizTalk Services SDK
Når du opgraderer en tidligere version af BizTalk Services SDK, som var det gamle ikoner, der repræsenterer broer, fortsætter værktøjskassen til at vise de gamle ikoner for broer. Hvis du føjer en bro til BizTalk Service project Forespørgselsdesigner grundflade, viser overfladen dog det nye ikon.  

**Løsning**. Du kan løse dette problem ved at slette .tbd filer under <system drive>: \Users\<bruger > \AppData\Local\Microsoft\VisualStudio\11.0.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>OPGRADERING: BizTalk Portal opdatering fra forhåndsvisning til GA vises muligvis en fejlmeddelelse om, at muligheden for eller-Brugergrupper ikke er tilgængelig
Hvis du er logget på portalen BizTalk-tjenester, mens BizTalk-tjenesterne er opgraderet fra forhåndsvisning til GA, kan du få vist følgende fejlmeddelelse på portalen:  

Denne funktion findes ikke som en del af denne udgave af Microsoft Azure BizTalk-tjenester. Hvis du vil bruge disse funktioner skal du skifte til en relevante udgave.  

**Opløsning**: Log ud fra portalen, Luk og Åbn browseren, og derefter logge på portalen.  
### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>OPGRADERING: Spore nye data ikke vises efter BizTalk Services er opgraderet til GA
Forudsætter et scenarie, hvor du har en XML-bro, der er installeret på BizTalk Services Preview-abonnement. Du sender meddelelser til bro og den tilhørende spore data er tilgængelige på portalen BizTalk-tjenester. Nu, hvis BizTalk-tjenester Portal og BizTalk Services runtime bit er opgraderet til GA, og du sender en meddelelse til den samme bro slutpunkt installeret tidligere, datatypen registrering vises ikke for meddelelser, der er sendt, efter opgraderingen.  

### <a name="pipelines-vs-bridges"></a>Rørledninger v/s broer
I resten af dokumentet bruges ordet 'rørledninger' og 'broer' i flæng. Begge betyder grundlæggende det samme, som er en meddelelse behandling enhed, der er installeret på BizTalk-tjenester.  

### <a name="concepts"></a>Begreber  

[BizTalk-tjenester](https://msdn.microsoft.com/library/azure/hh689864.aspx)   
