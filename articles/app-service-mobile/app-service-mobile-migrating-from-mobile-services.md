<properties
    pageTitle="Overføre fra Mobile-tjenester til en App Service-Mobilappen"
    description="Lær, hvordan du nemt overføre din Mobile Services-program til en App-tjenesten Mobile-App"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="adrianha"/>

# <a name="article-top"></a>Overføre din eksisterende Azure Mobile-tjeneste til Azure App Service

Med [Generelt tilgængelig Azure App-tjenesten], kan Azure Mobile Services-websteder nemt overføres i stedet for at kunne udnytte alle funktionerne i tjenesten Azure-App.  Dette dokument forklares det, hvad du kan forvente, når du overfører webstedet fra Azure Mobile-tjenester til Azure App-tjenesten.

## <a name="what-does-migration-do"></a>Hvad gør overførsel til dit websted

Overførsel af Azure Mobile Service omdannes din Mobile-tjeneste til en [Azure App Service] app uden at påvirke koden.  Din meddelelse Hubs SQL-dataforbindelse, godkendelsesindstillinger, planlagte opgaver og domænenavn forbliver uændret.  Mobilklienter ved hjælp af din Azure Mobile Service fortsætte med at fungere normalt.  Overførsel genstarter din tjeneste, når det er overført til Azure App Service.

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Hvorfor du skal overføre dit websted

Microsoft anbefale, at du overfører din Azure Mobile Service for at drage fordel af funktionerne i Azure App-tjeneste, herunder:

  *  Nye host-funktioner, herunder [WebJobs] og [brugerdefinerede domænenavne].
  *  Forbindelse til din lokale-ressourcer, ved hjælp af [VNet] ud over [Hybrid forbindelser].
  *  Overvågning og fejlfinding i forbindelse med nye Relic eller [Program indsigt].
  *  Indbyggede DevOps værktøj, herunder [arrangere pladser], Rollback- og i-produktiv test.
  *  [Automatisk skalering], justering af belastning og [overvågning].

Finde flere oplysninger om fordelene Azure App-tjenesten, under emnet [Mobile kontra App tjenester] .

## <a name="before-you-begin"></a>Inden du går i gang

Inden du starter et større arbejde på dit websted, skal du [sikkerhedskopiere din Mobile-tjeneste] scripts og SQL-database.

## <a name="migrating-site"></a>Overføre dine websteder

Overførselsprocessen overfører alle websteder i et enkelt Azure område.

Du vil overføre dit websted:

  1.  Log på [Azure klassisk Portal].
  2.  Vælg en Mobile Service i det område, du vil overføre.
  3.  Klik på knappen **Overfør til App-tjenesten** .

    ![Knappen Overfør][0]

  4.  Læs overføre til App Service dialogboks.
  5.  Skriv navnet på din Mobile-tjeneste i det angivne felt.  Eksempelvis hvis domænenavnet er contoso.azure mobile.net, Angiv derefter _contoso_ i det angivne felt.
  6.  Klik på knappen aksemærketype.

Overvåge status for overførslen i aktivitetsovervågningen. Webstedet er angivet som *overførsel* i portalen Azure klassisk.

  ![Overførsel aktivitetsovervågning][1]

Hver overflytningen kan tage et vilkårligt sted fra 3 til 15 minutter i mobile-tjenesten bliver overflyttet.  Webstedet er tilgængelig under overførslen.
Webstedet genstartes i slutningen af overførselsprocessen.  Webstedet er ikke tilgængelig under genstartsprocessen, som kan vare et par sekunder.

## <a name="finalizing-migration"></a>Færdiggør overførslen

Planlægge at afprøve webstedet fra en mobilklient ved undersøgelsens overførselsprocessen.  Sikre, at du kan udføre alle almindelige klient handlinger uden at ændre den mobilklient.  

### <a name="update-app-service-tier"></a>Vælg en passende App-tjeneste priser niveau

Du får større fleksibilitet i priserne, efter du har overført til Azure App-tjenesten.

  1.  Log på [Azure-portalen].
  2.  Vælg **alle de ressourcer** eller **App tjenester** , og klik derefter på navnet på din overførte Mobile-tjeneste.
  3.  Bladet indstillinger åbnes som standard.
  4.  Klik på **App Service Plan** i menuen Indstillinger.
  5.  Klik på feltet **Priser niveau** .
  6.  Klik på feltet relevante til dine behov, og klik derefter på **Vælg**.  Du kan være nødvendigt at klikke på **Vis alle** for at se de tilgængelige priser niveauer.

Som udgangspunkt anbefaler vi følgende niveauer:

| Mobile Service priser niveau | App Service priser niveau |
| :-------------------------- | :----------------------- |
| Gratis                        | F1 gratis                  |
| Grundlæggende                       | B1 Basic                 |
| Standard                    | S1 Standard              |

Der er meget mere fleksibel i at vælge den rigtige priser niveau for dit program.  Referere til [App servicepriser] udførlige detaljer på priser for den nye App-tjeneste.

> [AZURE.TIP]App Service Standard niveauet indeholder adgang til mange funktioner, som du muligvis vil bruge, herunder [arrangere pladser], automatisk sikkerhedskopiering og automatisk skalering.  Tjekke de nye funktioner, mens du er til stede!

### <a name="review-migration-scheduler-jobs"></a>Gennemse de overførte Scheduler job

Planlægger-job kan ikke ses indtil cirka 30 minutter efter overførslen.  Planlagte job fortsætte med at køre i baggrunden.
Sådan vises dit planlagte opgaver, når de er synlige igen:

  1.  Log på [Azure-portalen].
  2.  Vælg **Gennemse >**, angive **tidsplan** i feltet _Filter_ og derefter vælge **Scheduler af websteder**.

Der findes et begrænset antal gratis scheduler job tilgængelige efter overførsel.  Gennemse dine brugen og [Azure Scheduler planer].

### <a name="configure-cors"></a>Konfigurere CORS, hvis det er nødvendigt

Deling af ressourcer i tværs origin er en metode til at tillade, at et websted til at få adgang til en Web API på et andet domæne.  Hvis du bruger Azure Mobile-tjenester med et tilknyttet websted, skal du konfigurere CORS som en del af overførslen.  Hvis du får adgang til Azure Mobile Services udelukkende fra mobilenheder, derefter behøver CORS ikke at blive konfigureret undtagen i sjældne tilfælde.

Dine overførte CORS indstillinger er tilgængelige som indstillingen **MS_CrossDomainWhitelist** App.  Kan du overføre dit websted til funktionen App Service CORS:

  1.  Log på [Azure-portalen].
  2.  Vælg **alle de ressourcer** eller **App tjenester** , og klik derefter på navnet på din overførte Mobile-tjeneste.
  3.  Bladet indstillinger åbnes som standard.
  4.  Klik på **CORS** i menuen API.
  5.  Angiv en hvilken som helst tilladt baggrunden i feltet, trykke på Enter efter hver enkelt.
  6.  Når listen over tilladte baggrunden er korrekt, skal du klikke på knappen Gem.

> [AZURE.TIP]En af fordelene ved hjælp af en Azure App tjeneste er, at du kan køre dit websted og mobile service på samme sted.  Yderligere oplysninger finder du i afsnittet [Næste trin](#next-steps) .

### <a name="download-publish-profile"></a>Hente en ny publicering profil

Profilen publicering af dit websted ændres, når overførsel til Azure App Service.  Hvis du vil publicere webstedet fra Visual Studio, skal du en ny publicering profil.  Sådan henter du den nye publicering profil:

  1.  Log på [Azure-portalen].
  2.  Vælg **alle de ressourcer** eller **App tjenester** , og klik derefter på navnet på din overførte Mobile-tjeneste.
  3.  Klik på **Hent Udgiv profil**.

PublishSettings filen er overført til din computer.  Den hedder normalt _sitename_. PublishSettings.  Importere indstillingerne for Udgiv til din eksisterende projekt:

  1.  Åbne Visual Studio og projektet Azure Mobile Service.
  2.  Højreklik på dit projekt i **Solution Explorer** , og vælg **Publicer...**
  3.  Klik på **Importér**
  4.  Klik på **Gennemse** og vælge din hentede publicere indstillingsfil.  Klik på **OK**
  5.  Klik på **Valider forbindelse** for at sikre, at Publicer indstillinger arbejdet.
  6.  Klik på **Publicer** for at udgive dit websted.


## <a name="working-with-your-site"></a>Arbejde med dit websted følger efter overførslen

Begynde at arbejde med din nye App-tjeneste i [Azure portal] efter overførslen.  Følgende er nogle noter på bestemte handlinger, som du brugte til at udføre i [Azure klassisk Portal], sammen med tilsvarende App Service.

### <a name="publishing-your-site"></a>Hente og publicere webstedet overførte

Webstedet er tilgængelige via ciffer eller FTP- og kan udgives med forskellige forskellige godkendelsesmetoder, herunder WebDeploy, TFS, betyder, GitHub og FTP.  Installation legitimationsoplysninger overføres med resten af dit websted.  Hvis du ikke har angivet legitimationsoplysningerne installation, eller du ikke kan huske dem, kan du nulstille dem:

  1. Log på [Azure-portalen].
  2. Vælg **alle de ressourcer** eller **App tjenester** , og klik derefter på navnet på din overførte Mobile-tjeneste.
  3. Bladet indstillinger åbnes som standard.
  4. Klik på **installation legitimationsoplysninger** i UDGIVELSE menu.
  5. Angiv de nye installation legitimationsoplysninger i felterne, der vises, og derefter klikke på knappen Gem.

Du kan bruge disse legitimationsoplysninger til at konfigurere automatiske installationer fra GitHub, TFS eller betyder eller klone webstedet med ciffer.  Yderligere oplysninger finder du i [Azure App Service installation dokumentation].

### <a name="appsettings"></a>Indstillinger for et webprogram

De fleste indstillinger for en overført mobile service er tilgængelige via App-indstillinger.  Du kan få en liste over app-indstillingerne fra [Azure-portalen].
Få vist eller ændre indstillingerne for app:

  1. Log på [Azure-portalen].
  2. Vælg **alle de ressourcer** eller **App tjenester** , og klik derefter på navnet på din overførte Mobile-tjeneste.
  3. Bladet indstillinger åbnes som standard.
  4. Klik på **Indstillinger** i menuen Generelt.
  5. Rul ned til sektionen indstillinger, og find din app-indstilling.
  6. Klik på værdien af indstillingen app til at redigere værdien.  Klik på **Gem** for at gemme værdien.

Du kan opdatere indstillinger for flere ad gangen.

> [AZURE.TIP]Der findes to programindstillinger med den samme værdi.  For eksempel du kan få vist _ApplicationKey_ og _MS\_ApplicationKey_.  Opdatere begge programindstillinger på samme tid.

### <a name="authentication"></a>Godkendelse

Alle godkendelsesindstillinger er tilgængelige som App indstillinger på webstedet overførte.  Hvis du vil opdatere indstillingerne for godkendelse, skal du ændre indstillingerne for relevante app.  I følgende tabel vises de relevante app-indstillinger for din udbyder af godkendelse:

| Udbyder          | Klient-ID                 | Klient hemmeligt                | Andre indstillinger             |
| :---------------- | :------------------------ | :--------------------------- | :------------------------- |
| Microsoft-konto | **MS\_MicrosoftClientID**  | **MS\_MicrosoftClientSecret** | **MS\_MicrosoftPackageSID** |
| Facebook          | **MS\_FacebookAppID**      | **MS\_FacebookAppSecret**     |                            |
| Twitter           | **MS\_TwitterConsumerKey** | **MS\_TwitterConsumerSecret** |                            |
| Google            | **MS\_GoogleClientID**     | **MS\_GoogleClientSecret**    |                            |
| Azure AD          | **MS\_AadClientID**        |                              | **MS\_AadTenants**          |

Bemærk: **MS\_AadTenants** er gemt som en kommasepareret liste over lejer domæner ("Tilladt lejere" felter i portalen Mobile-tjenester).

> [AZURE.WARNING] **Brug ikke godkendelsesmetoder i menuen Indstillinger**
>
> Azure App Service indeholder et separat "uden brug af kode" godkendelse og autorisation system under den _godkendelse / godkendelse_ menuen Indstillinger, og indstillingen (frarådede) _Mobile godkendelse_ under menuen Indstillinger.  Disse indstillinger er ikke kompatibelt med en overført Azure Mobile Service.  Du kan [opgradere dit websted](app-service-mobile-net-upgrading-from-mobile-services.md) for at kunne udnytte Azure App Service-godkendelse.

### <a name="easytables"></a>Data

Fanen _Data_ i Mobile-tjenester er blevet erstattet af _Nemt tabeller_ i Azure portal.  For at åbne nemt tabeller:

  1. Log på [Azure-portalen].
  2. Vælg **alle de ressourcer** eller **App tjenester** , og klik derefter på navnet på din overførte Mobile-tjeneste.
  3. Bladet indstillinger åbnes som standard.
  4. Klik på **nemt tabeller** i MOBILE-menuen.

Du kan tilføje en tabel ved at klikke på knappen **Tilføj** eller få adgang til din eksisterende tabeller ved at klikke på navnet på en tabel.  Der er forskellige handlinger, kan du gøre fra denne blade, herunder:

* Ændre tabel-tilladelser
* Redigering af funktionsdygtige scripts
* Administrere tabelskema
* Slette tabellen
* Rydde indholdsfortegnelse
* Slette bestemte rækker i tabellen

### <a name="easyapis"></a>API

Fanen _API_ i Mobile-tjenester er blevet erstattet af _Nemt API'er_ i Azure portal.  For at åbne nemt API'er:

  1. Log på [Azure-portalen].
  2. Vælg **alle de ressourcer** eller **App tjenester** , og klik derefter på navnet på din overførte Mobile-tjeneste.
  3. Bladet indstillinger åbnes som standard.
  4. Klik på **Nemt API'er** i MOBILE-menuen.

Dine overførte API'er findes allerede i bladet.  Du kan også tilføje en API fra denne blade.  For at administrere en bestemt API skal du klikke på API.
Du kan justere tilladelserne og redigere scriptene til API fra bladet nye.

### <a name="on-demand-jobs"></a>Planlægger-job

Alle scheduler job er tilgængelige via afsnittet Scheduler Job af websteder.  For at åbne dine scheduler sager:

  1. Log på [Azure-portalen].
  2. Vælg **Gennemse >**, angive **tidsplan** i feltet _Filter_ og derefter vælge **Scheduler af websteder**.
  3. Vælg samlingen jobstatus for webstedet.  Det hedder _sitename_-job.
  4. Klik på **Indstillinger**.
  5. Klik på **Scheduler sager** under ADMINISTRER.

Planlagte job vises med den hyppighed, du har angivet før overførslen.  Efter behov job er deaktiveret.  For at køre et job efter behov:

  1. Vælg den sag, du ønsker at køre.
  2. Klik på **Aktivér** for at aktivere jobbet, hvis det er nødvendigt.
  3. Klik på **Indstillinger**og derefter **tidsplan**.
  4. Vælg en gentagelse af **én gang**, og klik derefter på **Gem**

Dine efter behov sager er placeret i `App_Data/config/scripts/scheduler post-migration`.  Vi anbefaler, at du konverterer alle efter behov job til [WebJobs] eller [Funktioner].  Skriv nye scheduler job som [WebJobs] eller [Funktioner].

### <a name="notification-hubs"></a>Meddelelse om Hubs

Mobile-tjenester bruger meddelelse Hubs for pushmeddelelser.  Indstillingerne for følgende App er bruges til at sammenkæde meddelelse hubben til Mobile Service efter overflytningen:

| Programmet indstilling                    | Beskrivelse                              |
| :------------------------------------- | :--------------------------------------- |
| **MS\_PushEntityNamespace**             | Meddelelse om Hub Namespace           |
| **MS\_NotificationHubName**             | Meddelelse om Hub navn                |
| **MS\_NotificationHubConnectionString** | Meddelelse om Hub forbindelsesstrengen   |
| **MS\_NamespaceName**                   | Et alias for MS_PushEntityNamespace      |

Din meddelelse Hub administreres via [Azure-portalen].  Notér navnet meddelelse Hub (du kan finde det ved hjælp af indstillingerne for App):

  1. Log på [Azure-portalen].
  2. Vælg **Gennemse**>, vælg derefter **Meddelelse Hubs**
  3. Klik på meddelelse Hub navnet, der er knyttet til mobile service.

> [AZURE.NOTE]Hvis din meddelelse HUb er af typen "Blandet", er det ikke synlige.  "Blandet" Skriv meddelelse hubs udnytte både meddelelse Hubs og ældre Service Bus funktioner.  [Konvertere din blandede navneområder] før du fortsætter.  Når konverteringen er fuldført, vises din meddelelse hub i [Azure-portalen].

Gennemse dokumentationen til [Meddelelse Hubs] kan finde flere oplysninger.

> [AZURE.TIP]Meddelelse om Hubs administrationsfunktioner i [Azure portal] findes stadig i preview.  [Azure klassisk Portal] forbliver tilgængelig for administration af din meddelelse Hubs.

### <a name="legacy-push"></a>Indstillinger for ældre opslagsnål

Hvis du har konfigureret opslagsnål på din mobile service før Introduktion på meddelelse Hubs, bruger du _ældre opslagsnål_.  Hvis du bruger Push, og du ikke kan se en besked om-Hub, der er angivet i din konfiguration, er det sandsynligvis du bruger _ældre opslagsnål_.  Denne funktion er blevet overført med alle de andre funktioner.  Men, anbefales det, at du opgraderer til meddelelse hubber snart, når overførslen er fuldført.

I mellemtiden er alle ældre push-indstillinger (med væsentlige undtagelse af APNS-certifikat) tilgængelige i App-indstillinger.  Opdater APNS-certifikat ved at erstatte den pågældende fil på filsystemet.

### <a name="app-settings"></a>Andre App-indstillinger

Indstillingerne for følgende yderligere app er overført fra din Mobile-tjeneste og tilgængelige under *Indstillinger for* > *App-indstillinger*:

| Programmet indstilling              | Beskrivelse                             |
| :------------------------------- | :-------------------------------------- |
| **MS\_MobileServiceName**         | Navnet på din app                    |
| **MS\_MobileServiceDomainSuffix** | Præfikset domæne. Windows XP Azure-mobile.net |
| **MS\_ApplicationKey**            | Din programtasten                    |
| **MS\_MasterKey**                 | Din app master nøgle                     |

Programtasten og master nøgle er identisk med tasterne programmet fra din oprindelige Mobile-tjeneste.  Især sendes programtasten af mobilklienter til at validere deres brug af mobile API.

### <a name="cliequivalents"></a>Kommandolinjen tilsvarende

Du kan bruge kommandoen _azure mobile_ længere til at administrere webstedet Azure Mobile-tjenester.  I stedet er for mange funktioner blevet erstattet med kommandoen _azure-websted_ .  Brug tabellen nedenfor til at finde tilsvarende til almindelige kommandoer:

| _Azure Mobile_ Kommandoen                     | Tilsvarende _Azure websted_ -kommando            |
| :----------------------------------------- | :----------------------------------------- |
| Mobile placeringer                           | liste over websted                         |
| Mobile liste                                | liste over websteder                                  |
| Mobile Vis _navn_                         | _navn_ på websted Vis                           |
| Mobile genstart _navn_                      | _navn_ på websted genstart                        |
| Mobil genimplementeringen _navn_                     | webstedets installation genimplementeringen _commitId_ _navn_ |
| Mobile nøgle Angiv _navnet_ _type_ _værdi_       | webstedets appsetting Slet _nøgle_ _navn_ <br/> websted appsetting føje _nøgle_=_værdi_ _navn_ |
| Mobile config _navn_                  | websted appsetting _navn_                |
| Mobile config få _navn_ _nøgle_             | webstedets appsetting Vis _nøgle_ _navn_          |
| Mobile config Angiv _navnet_ _nøgle_             | webstedets appsetting Slet _nøgle_ _navn_ <br/> websted appsetting føje _nøgle_=_værdi_ _navn_ |
| Mobile domæne _navn_                  | webstedets domæne listen _navn_                    |
| Mobile domæne tilføje _navn_ _domæne_          | websted domæne Tilføj _domæne_ _navn_            |
| Mobile domæne Slet _navn_                | webstedets domæne Slet _domæne_ _navn_         |
| Mobile skala Vis _navn_                   | _navn_ på websted Vis                           |
| Mobile skala Skift _navn_                 | websted skala tilstand _tilstand_ _navn_ <br /> webstedets skala forekomster _forekomster_ _navn_ |
| Mobile appsetting _navn_              | websted appsetting _navn_                |
| Mobile appsetting Tilføj _navn_ _nøgle_ _værdi_ | websted appsetting føje _nøgle_=_værdi_ _navn_   |
| Mobile appsetting Slet _navnet_ _nøgle_      | webstedets appsetting Slet _nøgle_ _navn_        |
| Mobile appsetting Vis _navn_ _nøgle_        | webstedets appsetting Slet _nøgle_ _navn_        |

Opdatere godkendelse eller indstillinger for push meddelelser ved at opdatere den ønskede indstilling i programmet.
Redigere filer, og Publicer webstedet via ftp eller ciffer.

### <a name="diagnostics"></a>Diagnosticerings- og logføring

Logføring af diagnostik deaktiveres normalt i en Azure App Service.  Aktivere logføring af diagnostik:

  1. Log på [Azure-portalen].
  2. Vælg **alle de ressourcer** eller **App tjenester** , og klik derefter på navnet på din overførte Mobile-tjeneste.
  3. Bladet indstillinger åbnes som standard.
  4. Vælg **Diagnosticeringslogfiler** under menuen Funktioner.
  5. Klik **på** for at følgende loggene: **Programmet logføring (filsystem)**, **detaljerede fejlmeddelelser**og **mislykket anmodning sporing**
  6. Klik på **Filsystem** til Web server logføring
  7. Klik på **Gem**

Sådan får du vist loggene:

  1. Log på [Azure-portalen].
  2. Vælg **alle de ressourcer** eller **App tjenester** , og klik derefter på navnet på din overførte Mobile-tjeneste.
  3. Klik på knappen **Funktioner**
  4. Vælg **Log Stream** under menuen OBSERVE.

Logfiler over vises i vinduet, når de oprettes.  Du kan også hente loggene til efterfølgende analyse ved hjælp af din installation legitimationsoplysninger. Du kan finde flere oplysninger i dokumentationen [logføring] .

## <a name="known-issues"></a>Kendte problemer

### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>Slette en overflyttet Mobile App Klon medfører, at et websted afbrydelse

Hvis du klone overførte mobile tjenesten ved hjælp af Azure PowerShell og derefter slette klonen, fjernes DNS-posten for din fremstilling tjeneste.  Webstedet er ikke længere være tilgængeligt via internettet.  

Løsning: Hvis du vil klone dit websted, skal du gøre det via portalen.

### <a name="changing-webconfig-does-not-work"></a>Ændre Web.config virker ikke

Hvis du har en ASP.NET-websted, ændres til den `Web.config` fil ikke få anvendt.  Tjenesten Azure App opbygger en passende `Web.config` fil under start for at understøtte til kørsel af Mobile-tjenester.  Du kan tilsidesætte bestemte indstillinger (såsom brugerdefinerede sidehoveder) ved hjælp af en XML-transformation fil.  Oprette en fil i kaldet `applicationHost.xdt` -denne fil skal ender i den `D:\home\site` mappe på Azure Service.  Overføre den `applicationHost.xdt` arkiverer via en brugerdefineret installation script eller direkte ved hjælp af Kudu.  Herunder vises et eksempel på dokument:

```
<?xml version="1.0" encoding="utf-8"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" xdt:Transform="Replace" />
        <remove name="X-Powered-By" xdt:Transform="Insert" />
      </customHeaders>
    </httpProtocol>
    <security>
      <requestFiltering removeServerHeader="true" xdt:Transform="SetAttributes(removeServerHeader)" />
    </security>
  </system.webServer>
</configuration>
```

Se i dokumentationen til [XDT transformere eksempler] på GitHub kan finde flere oplysninger.

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>Overførte Mobile-tjenester kan ikke føjes til trafik Manager

Du kan ikke direkte vælge en overført mobile tjeneste til profilen, når du opretter en trafik Manager profil.  Bruge en "eksterne slutpunkt".  Det eksterne slutpunkt kan kun tilføjes via PowerShell.  Du kan finde yderligere oplysninger finder [trafik Manager selvstudium](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

## <a name="next-steps"></a>Næste trin

Nu, hvor dit program er blevet overført til App Service, er der endnu flere funktioner, du kan bruge:

  * Installation [arrangere pladser] gør det muligt at fase ændringer til dit websted og udføre A / B test.
  * [WebJobs] giver en erstatning for On demand planlagt job.
  * Du kan [installere løbende] dit websted ved at sammenkæde dit websted med GitHub, TFS eller betyder.
  * Du kan bruge [Programmet indsigt] til at overvåge dit websted.
  * Udskrive et websted og en Mobile API fra den samme kode.

### <a name="upgrading-your-site"></a>Opgradere dit websted til Mobile-tjenester til Azure Mobile Apps SDK

  * Den nye [Mobile Apps Node.js SDK] indeholder adskillige nye funktioner for Node.js-baseret server projekter. For eksempel kan du nu gøre lokale udvikling og fejlfinding, bruge en hvilken som helst Node.js version over 0,10 og tilpasse med en hvilken som helst Express.js programmer.

  * For. Nettooversigt-baseret server-projekter, den nye [Mobile Apps SDK NuGet pakker](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) har større fleksibilitet på NuGet afhængigheder.  Disse pakker understøtter den nye App Service-godkendelse, og skriv med en hvilken som helst ASP.NET-projekt. Hvis du vil have mere for at vide om at opgradere skal se [opgradere din eksisterende .NET Mobile-tjeneste til App-tjenesten](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[App Service priser]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Programmet indsigt]: ../application-insights/app-insights-overview.md
[Automatisk skalering]: ../app-service-web/web-sites-scale.md
[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[Azure App Service installation dokumentation]: ../app-service-web/web-sites-deploy.md
[Azure klassisk Portal]: https://manage.windowsazure.com
[Azure-portalen]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/en-us/regions/
[Azure Scheduler planer]: ../scheduler/scheduler-plans-billing.md
[installere løbende]: ../app-service-web/app-service-continuous-deployment.md
[Konvertere din blandede navneområder]: https://azure.microsoft.com/en-us/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[brugerdefinerede domænenavne]: ../app-service-web/web-sites-custom-domain-name.md
[Fiddler]: http://www.telerik.com/fiddler
[generelle tilgængeligheden af Azure App Service]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[Hybrid forbindelser]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[Logføring]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Mobilapps Node.js SDK]: https://github.com/azure/azure-mobile-apps-node
[Mobile tjenester kontra App Service]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Meddelelse om Hubs]: ../notification-hubs/notification-hubs-push-notification-overview.md
[overvågning af ydeevnen]: ../app-service-web/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[Sikkerhedskopiere din Mobile-tjeneste]: ../mobile-services/mobile-services-disaster-recovery.md
[midlertidige pladser]: ../app-service-web/web-sites-staged-publishing.md
[VNet]: ../app-service-web/web-sites-integrate-with-vnet.md
[WebJobs]: ../app-service-web/websites-webjobs-resources.md
[XDT transformation eksempler]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[Funktioner]: ../azure-functions/functions-overview.md
