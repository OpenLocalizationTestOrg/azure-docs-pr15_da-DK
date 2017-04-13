<properties
   pageTitle="Brug af HTTP lytteren og forbindelse i logik Apps | Microsoft Azure App Service "
   description="Hvordan du kan oprette og konfigurere HTTP lytteren og HTTP handling forbindelse eller API app og bruge det i en logik app i Azure App Service"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/31/2016"
   ms.author="prkumar"/>


# <a name="get-started-with-the-http-listener-and-http-action-and-add-it-to-your-logic-app"></a>Komme i gang med HTTP lytteren og HTTP handling og tildele den til din logik App

> [AZURE.NOTE]Vi slutter support for denne forbindelse, fordi dens funktionalitet indgår nu som standard som **Manuel udløser** , når du opretter nye logik apps.  Vi anbefaler, at du opgraderer din logik-apps, der bruger denne forbindelse.  
> Denne version i denne artikel gælder for logik apps 2014-12-01-skema prøveversionen.

Oprette forbindelse direkte til HTTP-ressourcer til at lytte til HTTP-anmodninger og konfigurere HTTP-webanmodninger. Der er nogle scenarier, hvor du muligvis arbejde med direkte HTTP-forbindelser, herunder:

1.  Hvis du vil udvikle en logik App understøtter, der et websted eller mobilbrugere interaktive front-end.
2.  At hente og behandle data fra en webtjeneste, der ikke har en forældet feltet forbindelse.
3.  Til at udføre handlinger, der allerede er tilgængelig som en webtjeneste, men ikke tilgængeligt som en API-app.

Disse scenarier er der to muligheder:

1. **Http-lytteren**: denne forbindelse fungerer som en udløser og lytter efter HTTP-anmodninger på et konfigureret slutpunkt. Når du modtager et opkald på konfigurerede slutpunktet, den udløser en ny forekomst af strømmen og sender de modtagne i anmodningen til strømmen til behandling af data. Det kan også være konfigureret til at svare automatisk på indgående anmodningen, når strømmen er i gang, eller gør det muligt at oprette et svar, der er baseret på flow udførelsen og sende et svar til kalderen.
2. **Http-handling**: Dette kan du konfigurere en webforespørgsel til en hvilken som helst slutpunkt, der er tilgængelige på internettet, får et svar tilbage, og gør det tilgængeligt tilgængeligt for flere handlinger i strømmen til at bruge.

Logik apps kan udløse baseret på en række forskellige datakilder og tilbud forbindelser for at hente og behandle data som en del af strømmen. Du kan føje HTTP-forbindelse til virksomhedens arbejdsgange og processer data som en del af denne arbejdsproces i en logik App. 

## <a name="creating-an-http-listener-for-your-logic-app"></a>Oprette en HTTP lytteren for din logik App
En forbindelse kan oprettes i en logik app eller oprettes direkte fra Azure Marketplace. Sådan oprettes en forbindelse fra Marketplace:  

1. Azure startboard, Vælg **Marketplace**.
2. Søge efter "HTTP", Vælg HTTP lytteren, og vælg **Opret**.
3.  Konfigurere HTTP lytteren på følgende måde:  
![][1]

4.  Når du konfigurerer indstillingerne for pakke, ser du følgende indstilling om, hvorvidt lytteren skal svare automatisk eller kræver, at du sende en eksplicit svar. Angive dette til **Falsk** til at sende dit eget svar:  
![][2]

5.  Klik på **OK** for at oprette.
6.  Når forekomsten API app er oprettet, skal du åbne indstillinger for at konfigurere sikkerheden. HTTP-lytteren understøtter i øjeblikket basisgodkendelse. Du kan konfigurere dette ved hjælp af indstillingen sikkerhed, når du åbner HTTP lytteren:  
![][3]
  
    **Kendt problem** Indstillinger for *feltet sikkerhed viser "Ingen" som standardværdi, men det er ikke defineret. Du skal ændre indstillingen til Basic og tilbage til ingen før du gemmer den for at sikre, at HTTP lytteren er konfigureret korrekt.*  

7. Endelig Angiv sikkerhedsindstillingerne for Appen API til offentlige (anonym) for at give eksterne klienter mulighed for at få adgang til slutpunkt. Denne indstilling er tilgængelig under "alle indstillinger > Indstillinger for et webprogram" på http-lytteren API app'en:![][10]

Når det er gjort, kan du nu oprette en logik app for at bruge HTTP lytteren.

## <a name="using-the-http-listener-in-your-logic-app"></a>Brug af HTTP lytteren i din logik App
Når din API app er oprettet, kan du nu bruge HTTP lytteren som en udløser til din logik App. For at gøre dette, skal du:

4.  Oprette en ny logik App.
5.  Åbn "Udløsere og handlinger" for at åbne logik Apps Designer og konfigurere dit rutediagram. HTTP-lytteren er angivet i galleriet. Vælge den.
6.  Nu kan du angive metoden HTTP og den relative URL-adresse, som du har brug for lytteren at udløse strømmen:  
![][4]  
![][5]

7.  For at få den komplette URI skal du dobbeltklikke på http-lytteren for at få vist dets konfigurationsindstillinger og kopiere URL-adressen for "værten" af din API-app:  
![][6]
8.  Du kan nu bruge de data, der er modtaget i HTTP-anmodning i andre handlinger i strømmen på følgende måde:  
![][7]  
![][8]
9.  Tilføj en anden HTTP lytteren endelig for at sende et svar, og vælg handlingen Send HTTP-svar. Angive anmode om ID'ET til RequestID der fås fra HTTP lytteren og udfylde den svar brødtekst og HTTP-status du vil returnere tilbage:  
![][9]

## <a name="using-the-http-action"></a>Ved hjælp af handlingen HTTP
HTTP-handling er indbygget understøttelse af logik Apps og kræver ikke en API-app skal have oprettet første for at kunne bruge den. Du kan indsætte en HTTP-handling på en hvilken som helst sted i din logik App og vælge URI, overskrifter og brødtekst til opkaldet.
Handlingen HTTP understøtter flere indstillinger for klient side sikkerhed. Se [klient side sikkerhedsindstillinger](../scheduler/scheduler-outbound-authentication.md).

Output fra HTTP-handling er overskrifter og brødtekst, som kan bruges yderligere strømmen i strømmen ligner hvordan output fra andre handlinger og forbindelser er consumed.

## <a name="do-more-with-your-connector"></a>Få mere ud af forbindelsen
Nu hvor forbindelsen er oprettet, kan du tilføje den til en arbejdsprocesser for virksomheder ved hjælp af en logik App. Se [Hvad er logik Apps?](app-service-logic-what-are-logic-apps.md).

Få vist Swagger REST-API reference på [forbindelser og API Apps Reference](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Du kan også gennemse ydeevne statistik og kontrollere sikkerhed til forbindelsen. Se [administrere og overvåge dine indbyggede API Apps og forbindelser](app-service-logic-monitor-your-connectors.md).

> [AZURE.NOTE] Hvis du vil Introduktion til Azure logik Apps før tilmelding til en Azure-konto, skal du gå til [Prøve logik App](https://tryappservice.azure.com/?appservice=logic). Du kan straks oprette en forbigående starter logik app i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

<!--Image references-->
[1]: ./media/app-service-logic-connector-http/1.png
[2]: ./media/app-service-logic-connector-http/2.png
[3]: ./media/app-service-logic-connector-http/3.png
[4]: ./media/app-service-logic-connector-http/4.png
[5]: ./media/app-service-logic-connector-http/5.png
[6]: ./media/app-service-logic-connector-http/6.png
[7]: ./media/app-service-logic-connector-http/7.png
[8]: ./media/app-service-logic-connector-http/8.png
[9]: ./media/app-service-logic-connector-http/9.png
[10]: ./media/app-service-logic-connector-http/10.png
