<properties
    pageTitle="Oprette Azure BizTalk Services i portalen Azure | Microsoft Azure"
    description="Lær, hvordan du klargør eller oprette Azure BizTalk Services i portalen Azure; MABS, WABS"
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
    ms.topic="hero-article"
    ms.date="08/15/2016"
    ms.author="mandia"/>



# <a name="create-biztalk-services-using-the-azure-portal"></a>Oprette BizTalk-tjenester ved hjælp af portalen Azure

Oprette Azure BizTalk Services i portalen Azure.

> [AZURE.TIP] Du skal logge på Azure-portalen skal have du en Azure-konto og Azure-abonnement. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto i et par minutter. Se [Azure gratis prøveversion](http://go.microsoft.com/fwlink/p/?LinkID=239738).

## <a name="create-a-biztalk-service"></a>Oprette en BizTalk-tjeneste
Afhængigt af den version, du vælger, være ikke alle BizTalk Service indstillinger tilgængelige.

1. Log på [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Vælg **Ny**i navigationsruden nederst:  
![Vælg knappen Ny][NEWButton]

3. Vælg **APP tjenester** > **BIZTALK SERVICE** > **brugerdefinerede Opret**:  
![Vælg BizTalk Service, og vælg brugerdefineret oprette][NewBizTalkService]

4. Angiv indstillingerne for BizTalk Service:

    <table border="1">
    <tr>
    <td><strong>Navn på BizTalk-tjeneste</strong></td>
    <td>Du kan angive et navn, men vær specifik. Nogle eksempler kan nævnes:<br/><br/>
    <em>Hvis du</em>. biztalk.windows.net<br/>
    <em>mycompanymyapplication</em>. biztalk.windows.net<br/>
    <em>myapplication</em>. biztalk.windows.net<br/><br/>". biztalk.windows.net" føjes automatisk til det navn, du angiver. Dette opretter en URL-adresse, der bruges til at få adgang til dine BizTalk Service ønsker <strong>https://<em>myapplication</em>. biztalk.windows.net</strong>.
    </td>
    </tr>
    <tr>
    <td><strong>Edition</strong></td>
    <td>Hvis du er i fasen test/udvikling, vælge <strong>udvikler</strong>. Hvis du er i fasen fremstilling, kan du bruge den <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">BizTalk-tjenester: udgaver diagram</a> til at afgøre, om <strong>Premium</strong>, <strong>Standard</strong>eller <strong>grundlæggende</strong> er det korrekte valg til scenariet business.
    </td>
    </tr>
    <tr>
    <td><strong>Område</strong></td>
    <td>Vælg det geografiske område til at hoste din BizTalk Service.</td>
    </tr>
    <tr>
    <td><strong>Domæne URL-adresse</strong></td>
    <td><strong>Valgfrit</strong>. Domæne URL-adressen er som standard <em>YourBizTalkServiceName</em>. biztalk.windows.net. Også du kan angive et brugerdefineret domæne. Eksempelvis hvis domænet er <em>contoso</em>, kan du angive: <br/><br/>
    <em>Hvis du</em>. contoso.com<br/>
    <em>MyCompanyMyApplication</em>. contoso.com<br/>
    <em>MyApplication</em>. contoso.com<br/>
    <em>YourBizTalkServiceName</em>. contoso.com<br/>
    </td>
    </tr>
    </table>
Vælg pilen Næste.

5. Angiv opbevaring og Database indstillinger:

    <table border="1">
    <tr>
    <td><strong>Overvågning/arkivering lagerplads konto</strong></td>
    <td>Vælg en eksisterende konto lagerplads eller Opret en ny konto lagerplads. <br/><br/>Hvis du opretter en ny konto lagerplads, Skriv det <strong>Kontonavn-lager</strong>.</td>
    </tr>
    <tr>
    <td><strong>Sporing database</strong></td>
    <td>Hvis du bruger en eksisterende Azure SQL-Database, kan ikke bruges af en anden BizTalk Service. Du skal bruge login-navn og adgangskode angivet når, Azure SQL-databaseserver blev oprettet.<br/><br/><strong>Tip!</strong> Oprette sporing database og overvågnings/arkivering lagerplads konto i det samme område som BizTalk Service.</td>
    </tr>
    </table>
Vælg pilen Næste.

6. Angiv indstillingerne for Database:

    <table border="1">
    <tr>
    <td><strong>Navn</strong></td>
    <td>Tilgængelige, når <strong>Opret en ny forekomst af SQL-Database</strong> er markeret i det forrige skærmbillede.
    <br/><br/>
   Angiv et navn i SQL-Database, der anvendes af din BizTalk Service.</td>
    </tr>
    <tr>
    <td><strong>Server</strong></td>
    <td>Tilgængelige, når <strong>Opret en ny forekomst af SQL-Database</strong> er markeret i det forrige skærmbillede.
    <br/><br/>
   Vælg en eksisterende SQL-databaseserver, eller Opret en ny SQL-databaseserver.</td>
    </tr>
    <tr>
    <td><strong>Server login-navn</strong></td>
    <td>Angiv brugernavnet login.</td>
    </tr>
    <tr>
    <td><strong>Server-logonadgangskode</strong></td>
    <td>Angiv-logonadgangskode.</td>
    </tr>
    <tr>
    <td><strong>Område</strong></td>
    <td>Tilgængelig, når <strong>Opret en ny forekomst af SQL-Database</strong> er markeret. Vælg det geografiske område til at hoste din SQL-Database.</td>
    </tr>
    </table>

Markér afkrydsningsfeltet for at fuldføre guiden. Ikonet status vises:  
![Viser ikonet for status, når du er færdig][ProgressComplete]

Når du er færdig, er Azure BizTalk Service oprettes og er klar til dine programmer. Standardindstillingerne er tilstrækkelige. Hvis du vil ændre standardindstillingerne, Vælg **BizTalk-tjenester** i den venstre navigationsrude, og vælg derefter BizTalk Service. Yderligere indstillinger vises i [Dashboard, skærm, og skala faner](biztalk-dashboard-monitor-scale-tabs.md) øverst.

Afhængigt af tilstanden for BizTalk Service er der nogle handlinger, der ikke kan gennemføres. Gå til [BizTalk Services tilstand diagram](biztalk-service-state-chart.md)for en liste over disse handlinger.


## <a name="post-provisioning-steps"></a>Efter klargøring trin

-  [Installere certifikatet på en lokal computer](#InstallCert)
-  [Tilføje et fremstilling klar til certifikat](#AddCert)
-  [Få navneområdet adgangskontrol](#ACS)

#### <a name="InstallCert"></a>Installere certifikatet på en lokal computer
Som en del af BizTalk Service Klargør et selvsigneret certifikat oprettes og er knyttet til abonnementet BizTalk Service. Du skal hente dette certifikat og installere den på computere, hvorfra du enten installere BizTalk Service-programmer eller sende meddelelser til en BizTalk Service slutpunkt.

1. Log på [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Vælg **BizTalk-tjenester** i den venstre navigationsrude, og vælg derefter abonnementet BizTalk Service.
3. Vælg fanen **Dashboard** .
4. Klik på **Hent SSL-certifikat**:  
![Ændre SSL-certifikat][QuickGlance]
5. Dobbeltklik på certifikatet, og køre guiden installere certifikatet. Kontrollér, at du installerer certifikatet under **Der er tillid til rodnøglecentre** store.

#### <a name="AddCert"></a>Tilføje et fremstilling klar certifikat
Et selvsigneret certifikat, der oprettes automatisk, når du opretter BizTalk Services er beregnet til brug i development-miljøer. Erstatte den med et fremstilling klar certifikat for fremstilling scenarier.

1. Vælg **Opdater SSL-certifikat**under fanen **Dashboard** .
2. Gå til dit private SSL-certifikat (*CertificateName*.pfx), der indeholder navnet på din BizTalk Service, indtaste adgangskoden og derefter klikke på markeringen.

#### <a name="ACS"></a>Få navneområdet adgangskontrol

1. Log på [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Vælg **BizTalk-tjenester** i den venstre navigationsrude, og vælg derefter BizTalk Service.
3. Vælg **Forbindelsesoplysningerne**i proceslinjen:  
![Vælg forbindelsesoplysninger][ACSConnectInfo]

4. Kopiér værdierne adgangskontrol.

Når du installerer et BizTalk Service projekt fra Visual Studio, kan du angive dette adgangskontrol navneområde. Navneområdet adgangskontrol oprettes automatisk til din BizTalk Service.

Adgangskontrol værdierne kan bruges med et program. Når Azure BizTalk Services er oprettet, styrer dette adgangskontrol navneområde godkendelse med installationen BizTalk Service. Hvis du vil ændre abonnementet eller administrere navneområdet, Vælg **ACTIVE DIRECTORY** i den venstre navigationsrude og derefter vælge din navneområde. Proceslinjen viser dine muligheder.

Klik på **Administrer** åbnes Access Control Management Portal. På portalen Access Control Management bruger BizTalk Service **Service identiteter**:  
![ACS-tjenesten identiteter i dialogboksen Åbn styre Management-portalen][ACSServiceIdentities]

Adgangskontrol til tjenesteidentiteten er et sæt legitimationsoplysninger, der tillader programmer eller klienter mulighed for at godkende direkte med adgangskontrol og modtage et token.

> [AZURE.IMPORTANT]BizTalk Service bruger **ejer** for tjenesten standardidentiteten og værdien **Password** . Følgende fejl kan opstå, hvis du bruger den symmetriske nøgle-værdi i stedet for værdien Password.<br/><br/>*Kunne ikke oprettes forbindelse til Access Control Management Service-konto med de angivne legitimationsoplysninger*

[Administrere dine ACS Namespace](https://msdn.microsoft.com/library/azure/hh674478.aspx) viser nogle retningslinjer for og anbefalinger.

## <a name="requirements-explained"></a>Krav til forklaring

Disse krav gælder ikke for den gratis version.
<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Hvad du bør</strong></td>
        <td><strong>Hvorfor du skal bruge dem.</strong></td>
</tr>
<tr>
<td>Azure-abonnement</td>
<td>Abonnementets afgør, hvem der kan logge på portalen Azure. Kontoindehaver opretter abonnementet på <a HREF="https://account.windowsazure.com/Subscriptions">Azure abonnementer</a>.
<br/><br/>
Azure-konto kan have flere abonnementer og kan administreres af alle, der er tilladt. For eksempel din Azure-kontoindehaver opretter et abonnement med navnet <em>BizTalkServiceSubscription</em> og giver BizTalk-administratorer i din virksomhed (for eksempel ContosoBTSAdmins@live.com) adgang til dette abonnement. I dette scenarie skal BizTalk administratorer Log på portalen Azure og have fuld administratorrettigheder til alle de tilknyttede tjenester i det abonnement, herunder Azure BizTalk-tjenester. BizTalk-administratorer er ikke indehavere Azure-konto og derfor har ikke adgang til en hvilken som helst faktureringsoplysninger.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577">Administrere abonnementer og lagerplads konti i portalen Azure</a> indeholder flere oplysninger.
</td>
</tr>
<tr>
<td>Azure SQL-Database</td>
<td>Gemmer de tabeller, visninger og lagrede procedurer, der anvendes af BizTalk Service, herunder sporing dataene.
<br/><br/>
Når du opretter en BizTalk Service, kan du bruge en eksisterende Azure SQL Server, Azure SQL-Database, eller Opret automatisk en ny Server eller Database.
<br/><br/>
SQL-Database skalaen er automatisk konfigureret. Typisk er standardantallet tilstrækkelige til en BizTalk Service. Ændre skalaen påvirker priser. Se <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930">konti og fakturering i Azure SQL-Database</a>
<br/><br/>
<strong>Noter</strong>
<br/>
<ul>
<li> Når du opretter en ny Azure SQL Server og Database, er Azure Services automatisk aktiveret. BizTalk Service kræver Azure Services være aktiveret.</li>
<li>Hvis du opretter en ny Azure SQL-Database på en eksisterende Azure SQL Server, ændres firewallreglerne på serveren, ikke. Det er derfor muligt andre Azure-tjenester der ikke har adgang til den Server-databaser.</li>
</ul>
</td>
</tr>
<tr>
<td>Azure adgangskontrol navneområde</td>
<td>Godkender med Azure BizTalk-tjenester. Når du installerer et BizTalk Service projekt fra Visual Studio, kan du angive dette adgangskontrol navneområde. Når du opretter en BizTalk Service, oprettes der automatisk navneområdet adgangskontrol.</td>
</tr>

<tr>
<td>Azure lagerplads-konto</td>
<td>Giver adgang til tabeller, BLOB og køer, der anvendes af din BizTalk Service til at gemme følgende:

<ul>
<li>Logfiler, der overvåger BizTalk Service. Overvågning output vises også under fanen **overvågnings** i portalen Azure.</li>
<li>Når du opretter en X12 eller AS2 aftale mellem partnere, kan du aktivere funktionen arkivering til at gemme meddelelsesegenskaber. Disse data er gemt i kontoen lagerplads.</li>
</ul>
<br/>
Når du opretter en BizTalk Service, kan du kan bruge en eksisterende konto lagerplads eller automatisk oprette en ny lagerplads-konto.
<br/><br/>
Standardindstillingerne for lagring er tilstrækkelige til en BizTalk Service.
<br/><br/>
Når du opretter en konto med lagerplads, oprettes automatisk en primærnøgle og sekundær nøgle. Disse taster kan kontrollere adgang til kontoen lagerplads. BizTalk Service anvender automatisk den primærnøgle.
<br/><br/>
Du kan få flere oplysninger i <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">lagerplads</a> .
</td>
</tr>

<tr>
<td>Privat SSL-certifikat</td>
<td>
Når der oprettes en Azure BizTalk Service, oprettes der også en HTTPS URL-adresse, der indeholder navnet på din BizTalk Service. Denne URL-adresse er automatisk konfigureret til at bruge et selvsigneret certifikat kun udvikling. I fremstilling skal du et privat SSL-certifikat.
<br/><br/>
<strong>Vigtige SSL-certifikatoplysninger</strong>

<ul>
<li>Udløbsdatoen certifikat skal være mindre end 5 år.</li>
<li>Alle private certifikater kræver en adgangskode. Kender denne adgangskode, og som en bedste fremgangsmåde at dele denne adgangskode med dine administratorer.</li>
<li>Selvsignerede certifikater bruges i en test/udviklingsmiljø. Når du bruger selvsignerede certifikater, kan du importere certifikatet til dit personlige lager og butikken, der er tillid til rodnøglecentre certifikat.</li>
</ul>
<br/>Når du sender anmodning om fremstilling et certifikat til dit nøglecenter, give følgende certifikategenskaber:
<br/>

<ul>
<li><strong>Udvidet nøgle brugen</strong>: minimum Azure BizTalk Services kræver Server-godkendelse.</li>
<li><strong>Almindelige navn</strong>: Angiv det fulde domænenavn (fulde Domænenavn) af din URL-adresse på Azure BizTalk-tjenesten. Se <a HREF="#BizTalk">oprette en BizTalk-tjeneste</a> i denne artikel.</li>
</ul>
<br/>
En ny eller en anden certifikat kan tilføjes, når BizTalk Service er blevet oprettet.
</td>
</tr>
</table>



## <a name="hybrid-connections"></a>Hybrid forbindelser

Når du opretter en Azure BizTalk Service, findes under fanen **Hybrid forbindelser** :

![Under fanen forbindelser hybrid][HybridConnectionTab]

Hybrid forbindelser bruges til at oprette forbindelse til en lokal ressource, der bruger en statisk TCP-port, såsom SQL Server, MySQL, HTTP Web API'er, Mobile-tjenester og de fleste brugerdefineret Web-tjenester en Azure websted eller Azure mobile service.  Hybrid forbindelser og tjenesten BizTalk-Adapter er forskellige. Tjenesten BizTalk-Adapter, der bruges til at føje Azure BizTalk-tjenester til et lokalt linje for Business (LOB) system.

 Se [Hybrid forbindelser](integration-hybrid-connection-overview.md) for at lære mere, herunder oprettelse og administration af Hybrid forbindelser.


## <a name="next-steps"></a>Næste trin

Nu, hvor der oprettes en BizTalk Service, blive fortrolig med de forskellige [BizTalk-tjenester: fanerne Dashboard, overvåge og skala](biztalk-dashboard-monitor-scale-tabs.md). BizTalk Service er klar til dine programmer. For at starte oprettelsen af programmer skal du gå til [Azure BizTalk-tjenester](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Se også
- [BizTalk-tjenester: Udgaver diagram](biztalk-editions-feature-chart.md)<br/>
- [BizTalk-tjenester: Stat diagram](biztalk-service-state-chart.md)<br/>
- [BizTalk-tjenester: Sikkerhedskopiering og gendannelse](biztalk-backup-restore.md)<br/>
- [BizTalk-tjenester: (throttling)](biztalk-throttling-thresholds.md)<br/>
- [BizTalk-tjenester: Udstedernavn og udsteder nøgle](biztalk-issuer-name-issuer-key.md)<br/>
- [Hvordan får jeg Start med at bruge Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
- [Hybrid forbindelser](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
