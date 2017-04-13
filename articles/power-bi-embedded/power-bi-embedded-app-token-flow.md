<properties
   pageTitle="Kontrollere og godkende med Power BI integreret"
   description="Kontrollere og godkende med Power BI integreret"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="authenticating-and-authorizing-with-power-bi-embedded"></a>Kontrollere og godkende med Power BI integreret

Tjenesten Power BI integrerede bruger **nøgler** og **App Tokens** for godkendelse og autorisation, i stedet for eksplicitte slutbrugerlicensaftale godkendelse. I denne model administrerer dit program godkendelse og dine slutbrugere. Når det er nødvendigt, din app opretter og sender den App Tokens, der fortæller vores tjeneste til at gengive den ønskede rapport. Dette design kræver ikke din app til at bruge Azure Active Directory til brugergodkendelse og autorisation, selvom du stadig kan.

## <a name="two-ways-to-authenticate"></a>To måder at godkende

**Nøgle** – du kan bruge taster for alle Power BI integrerede REST-API-opkald. De pågældende taster kan findes i **Azure-portalen** ved at klikke på **alle indstillinger** og derefter på **Access-taster**. Behandl altid produktnøglen, som om det var en adgangskode. Disse taster har tilladelse til at foretage et opkald på en bestemt arbejdsområde samling REST-API.

For at bruge en nøgle på et RESTEN opkald skal du tilføje følgende godkendelse sidehovedet:            

    Authorization: AppKey {your key}

**App token** - App tokens bruges til alle integrering anmodninger. De er udviklet til at blive kørt klientsiden, så de er begrænset til en enkelt rapport og det er bedste fremgangsmåde at angive en udløbsdato.

App tokens er en JWT (JSON Web Token), der er signeret af en af dine nøgler.

Din app token kan indeholde følgende krav:

| Gør krav      | Beskrivelse        |
|--------------|------------|
| **ver**      | Versionen af app tokenet. 0.2.0 er den aktuelle version.       |
| **AUD**      | Tokenet tilsigtede modtagere. Power BI integrerede bruges: "https://analysis.windows.net/powerbi/api".  |
| **ISS**      |  En streng, der angiver det program, der har udstedt tokenet.    |
| **type**     | Typen app token, der oprettes. Aktuelle den eneste understøttede type er **integrere**.   |
| **WCN**      | Navn på arbejdsområde samling tokenet udstedes.  |
| **arbejdsgruppe-id'et**      | Arbejdsområde ID tokenet udstedes.  |
| **fjerne**      | Rapport-ID tokenet udstedes.     |
| **brugernavn** (valgfrit) |  Anvendes sammen med RLS, er dette en streng, der kan hjælpe med at identificere brugeren, når du anvender RLS regler. |
| **roller** (valgfrit)   |   En streng, der indeholder rollerne vælge når anvende sikkerhed på brugerniveau række regler. Hvis der passerer mere end én rolle, skal de overføres som en matrix med sting.    |
| **EXP** (valgfrit)    |   Angiver den tid, hvor tokenet udløber. Disse skal overføres i som Unix tidsstempler.   |
| **NBF** (valgfrit)    |   Angiver det tidspunkt, hvor tokenet starter er gyldige. Disse skal overføres i som Unix tidsstempler.   |

Et eksempel app token ser sådan ud:

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-coded.png)


Når afkodes, ser det sådan ud:

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-decoded.png)


## <a name="heres-how-the-flow-works"></a>Sådan virker strømmen

1. Kopiere tasterne API til dit program. Du kan få tasterne **Azure**-portalen.

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

2. Token imperative forudsætninger et krav og har et udløbstidspunkt.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-2.png)

3. Token bliver signeret med en API access-taster.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-3.png)

4. Bruger anmodninger om at få vist en rapport.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-4.png)

5.  Token valideres med en API access-taster.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-5.png)

6.  Power BI integrerede sender en rapport til brugeren.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-6.png)

Når **Power BI integrerede** sender en rapport til brugeren, kan brugeren se rapporten i din brugerdefinerede app. Eksempelvis hvis du har importeret [analyse af salg Data PBIX eksempel](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), ville online eksempel se således ud:

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

## <a name="see-also"></a>Se også
- [Introduktion til Microsoft Power BI integrerede eksempel](power-bi-embedded-get-started-sample.md)
- [Almindelige Microsoft Power BI integrerede scenarier](power-bi-embedded-scenarios.md)
- [Introduktion til Microsoft Power BI integreret](power-bi-embedded-get-started.md)
