<properties 
   pageTitle="Oprette en B2B proces i Azure App Service | Microsoft Azure" 
   description="Oversigt over, hvordan du opretter en Business to Business-proces" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>

# <a name="creating-a-b2b-process"></a>Oprette en B2B-proces

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


## <a name="business-scenario"></a>Dette Scenario 
Contoso og Northwind er to forretningsforbindelser. Contoso (forhandler) sender indkøbsordrer til Northwind (leverandør) over en branche niveau transport som AS2. Northwind gemmer alle indgående ordrer i dens skylagring. Indkøbsordrerne er XML-meddelelser mellem disse to partnere. Når meddelelsen er gemt i Northwinds skylagring håndtere Northwinds interne processer rækkefølgen fra det pågældende tidspunkt på.
 
Formålet med dette selvstudium er at fastlægge, hvordan Northwind kan oprette en forretningsproces via som det kan modtage meddelelser (indkøbsordrer i XML) fra dens partner Contoso over AS2 og bevare den i dens skylagring.


## <a name="capabilities-demonstrated"></a>Funktioner, der er vist 
Dette selvstudium hjælper med at vise følgende funktioner: 

- **Meddelelse transport**: forhandler og leverandør kan være på forskellige platforme, men de kan stadig opnår kommunikation mellem to. I dette selvstudium de kommunikerer via AS2 (anvendelse sætningen 2). AS2 er en populær måde at transport data mellem handel partnere business to business kommunikation.
- **Data brugerdata**: Når meddelelsen er modtaget via AS2, og derefter Northwind ønsker at bevare før videre behandling. Det kan bruge en forbindelse til fastholdes meddelelser i dens skylagring. I dette selvstudium er Azure BLOB der opgraderede som skylagring for Northwind.
- **Oprette en forretningsproces**: I et flow flere API-apps kan være stitched sammen for at opnå en business-resultatet, som vist her.


## <a name="before-you-begin"></a>Inden du går i gang
Dette selvstudium antages det, at du har et grundlæggende kendskab til Azure App Services, ved at oprette API apps, og kan du beskære et flow sammen.


## <a name="steps-to-achieve-the-business-scenario"></a>Trin til at opnå business-scenariet
**Oprette og konfigurere de nødvendige API-apps**

1. Oprette en forekomst af **Azure lagerplads Blob forbindelse**. Dette kræver legitimationsoplysninger til en Azure-lager-konto. Sørg for, at det er klar, før du går i gang med at oprette dette.
2. Oprette en forekomst af **BizTalk handel Partner administration**. Dette kræver en tom SQL-Database til funktionen. Sørg for, at det er klar, før du går i gang med at oprette dette.
3. Oprette en forekomst af **AS2 forbindelse**. Dette kræver også en tom SQL-Database til funktionen. Sørg for, at det er klar, før du går i gang med at oprette dette. Hvis du ønsker at arkivere meddelelser som en del af AS2 behandling, kan du også angive legitimationsoplysninger til en Azure Blob under oprettelsen.
4. Konfigurere tjenesten TPM (handel Partner Management), der er oprettet:  
    1. Gå til forekomsten af tjenesten TPM oprettet som en del af ovenstående trin.
    2. Brug indstillingen **partnere** under *komponenter* til **Tilføj** en ny partner med navnet **Contoso** og i dens profil Tilføj den nødvendige AS2 identitet.
    3. Brug indstillingen **partnere** under *komponenter* til **Tilføj** en ny partner med navnet **Northwind** og i dens profil Tilføj den nødvendige AS2 identitet.
    4. Brug indstillingen **aftaler** under *komponenter* til **Tilføj** en ny AS2 aftale mellem Northwind og Contoso. Northwind bliver hostet partner her, og Contoso bliver gæst partner. Efter behov konfigurere signering, kryptering, komprimering og godkendelser under oprettelse af denne aftale. I tilfælde af certifikater, der skal bruges, kan de overføres via indstillingen **certifikater** , når du navigerer tjenesten TPM, der er oprettet.


## <a name="create-a-flow--business-process"></a>Oprette et rutediagram / business behandle
1. Oprette en ny flow, hvor det første trin er AS2. Træk og slip **AS2 forbindelse** , og vælg den forekomst, der allerede har oprettet. Vælg udløser som funktionaliteten:  
    ![][1]  
2. Næste træk og slip **Azure lagerplads Blob forbindelse** , og vælg den forekomst, der allerede har oprettet. Vælg handling, som funktionen og i, skal du vælge **Overfør Blob** som den ønskede funktionalitet. Konfigurere efter behov.
3. Nu oprette/implementere strømmen.


## <a name="message-processing--troubleshooting"></a>Behandling af meddelelse og fejlfinding
1. Det er tid til at teste strømmen vi har installeret. Send XML meddelelser ombrudte i AS2 (ud fra AS2 aftalen oprettede ovenfor) til AS2 slutpunktet fået ved den AS2Connector-forekomst, du har oprettet. Du skal muligvis konfigurere godkendelse for slutpunktet, så det er offentligt tilgængelig.
2. Udførelse af oplysninger om strømmen fået ved at navigere til strømmen og derefter væk i forekomsten flow som har udført
3. Gå til den AS2Connector-forekomst AS2 behandling oplysninger, og følg derefter ved væk i webdelen registrering. Du kan bruge filtre til at begrænse visningen til de oplysninger, der er behov.

![][2]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-b2b-process/Flow.png
[2]: ./media/app-service-logic-create-a-b2b-process/Tracking.png
 
