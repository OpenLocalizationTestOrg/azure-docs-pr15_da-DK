<properties
    pageTitle="Sikre en app i Azure App-tjeneste"
    description="Lær, hvordan til at sikre en WebApp, mobilapp back end- eller API-app i Azure App Service."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="01/12/2016"
    ms.author="cephalin"/>


#<a name="secure-an-app-in-azure-app-service"></a>Sikre en app i Azure App-tjeneste

I denne artikel hjælper dig med at komme i gang på sikring af din online, mobilapp back end- eller API-app i Azure App Service. 

Sikkerhed i Azure App Service har to niveauer: 

- **Infrastruktur og platform sikkerhed** – du har tillid til Azure for at få de tjenester, skal du køre faktisk ting sikkert i skyen.
- **Application security** – du har brug for til at designe selve appen sikkert. Dette omfatter, hvordan du integrerer med Azure Active Directory, hvordan du administrerer certifikater, og hvordan du sikre dig, kan du sikkert tale med forskellige tjenester. 

#### <a name="infrastructure-and-platform-security"></a>Infrastruktur og platform sikkerhed
Fordi App Service fører FOS Azure-lager, netværksforbindelser, web strukturer, administration og funktioner for klientintegration og meget mere det aktivt er sikret og hærdet gennemgår omfattende overholdelse og kontrollerer løbende Sørg for, at:

- Din App Service apps er isolerede fra begge internettet og fra andre kunders Azure ressourcer.
- Kommunikation af hemmeligheder (fx forbindelsesstrenge) mellem din App Service app og andre Azure ressourcer (fx SQL-Database) i en ressourcegruppe forbliver i Azure og på tværs ikke af en hvilken som helst netværk grænser. Hemmeligheder er altid krypteret.
- Al kommunikation mellem din App Service app og eksterne ressourcer, som PowerShell management, kommandolinjen, Azure SDK'er, REST API'er og hybrid forbindelser, krypteres korrekt.
- 24-timers truslen management beskytter App Service ressourcer fra malware, distribueret nægtelse af service (DDoS), mand-i-feltet-midten (MITM) og andre trusler. 

Du kan finde flere oplysninger om infrastruktur og platform sikkerhed i Azure, [Azure Center for sikkerhed og rettighedsadministration](/support/trust-center/security/).

#### <a name="application-security"></a>Programmet sikkerhed

Mens Azure er ansvarlig for sikring af infrastruktur og platform, som dit program kører på, er det dit ansvar til at sikre dine selve programmet. Med andre ord, du har brug at udvikle, installere og administrere din programkode og indholdet i en sikker måde. Uden at det, kan dit programkode eller indhold stadig være udsatte for trusler f.eks.:

- SQL-indsættelse
- Session kapring
- Scripting i tværs med websteder
- Programmet niveau MITM
- Programmet niveau DDoS

En fulde diskussion af sikkerhedsovervejelser i web-baserede programmer er ikke medtaget i dette dokument. Som et udgangspunkt for at få yderligere vejledning om sikring af dit program skal du se de [Åbne Web Application sikkerhed projekt (OWASP)](https://www.owasp.org/index.php/Main_Page), specifikt de [10 vigtigste projektet.](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), som viser de aktuelle øverste 10 kritiske web application sikkerhedsbrist, som bestemt af OWASP medlemmer.

## <a name="perform-penetration-testing-on-your-app"></a>Udføre markedsindtrængning forsøg på din app

Den nemmeste måde at endnu i gang med test efter svagheder på din App Service app er at bruge [integration med Tinfoil sikkerhed](/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) til at udføre et enkelt klik sikkerhedsrisiko scanning på din app. Du kan få vist resultaterne i en rapport, der er nemt at forstå, og Lær, hvordan du løser hver sikkerhedsrisiko med trinvise instruktioner.

Hvis du foretrækker at udføre dine egne penetrationstest eller vil du bruge en anden scanner pakke eller udbyder, skal du følge [Azure markedsindtrængning test godkendelsesprocessen](https://security-forms.azure.com/penetration-testing/terms) og få forudgående godkendelse for at udføre de ønskede penetrationstest.

##<a name="https"></a>Sikker kommunikation med kunder

Hvis du bruger den ** \*. azurewebsites.net** domænenavn, der er oprettet for din App Service-app, du kan straks bruge HTTPS, som et SSL-certifikat er beregnet til alle ** \*. azurewebsites.net** domænenavne. Hvis webstedet bruger et [brugerdefineret domænenavn](web-sites-custom-domain-name.md), kan du overføre et SSL-certifikat til at [aktivere HTTPS](web-sites-configure-ssl-certificate.md) til dit eget domæne.

Aktivere [HTTPS](https://en.wikipedia.org/wiki/HTTPS) kan hjælpe med at beskytte mod MITM angreb på kommunikationen mellem din app og dens brugere.

## <a name="secure-data-tier"></a>Sikre data niveau

App Service integrerer meget med SQL-Database, så alle forbindelse strengene er krypteret på tværs af tavlen og er kun dekrypteres på VM, der app'en kører på *og* kun, når app'en kører. Desuden indeholder Azure SQL-Database mange sikkerhedsfunktioner for at hjælpe dig med at sikre dine programmet data fra cyber trusler, herunder [kryptering på resten](https://msdn.microsoft.com/library/dn948096.aspx), [Altid krypteret](https://msdn.microsoft.com/library/mt163865.aspx), [Dynamiske Data Masking](../sql-database/sql-database-dynamic-data-masking-get-started.md)og [Truslen registrering af](../sql-database/sql-database-threat-detection-get-started.md). Hvis du har følsomme data eller overholdelseskrav, skal du se [sikring af SQL-Database](../sql-database/sql-database-security.md) kan finde flere oplysninger om, hvordan du sikrer dine data.

Hvis du bruger en tredjeparts database-udbyder, som ClearDB, skal du kontakte med udbyderens dokumentation direkte på bedste fremgangsmåder for sikkerhed.  

##<a name="develop"></a>Sikker udvikling og installation

### <a name="publishing-profiles-and-publish-settings"></a>Udgivelse af brugerprofiler og indstillinger for publicering

Når udviklingsprogrammer, udføre administrationsopgaver eller automatisere opgaver ved hjælp af funktioner som **Visual Studio**, **Web Matrix**, **Azure PowerShell** eller **Azure kommandolinjen (Azure CLI)**, kan du bruge enten en *Indstillinger for publicering* fil eller en *publicering profil*. Begge filtyper godkende du med Azure og skal være fastgjort for at forhindre uautoriseret adgang.

* Indeholder en fil med **Indstillinger for publicering**

    * Dit Azure abonnement-ID

    * Et management-certifikat, der gør det muligt at udføre administrationsopgaver for dit abonnement *uden at de skal angive et kontonavn eller adgangskode*.

* Indeholder en **publicering profil** -fil

    * Oplysninger til publicering til din app

Hvis du bruger et værktøj, der bruger en Publicer indstillingsfil eller Udgiv profil-filen, kan du importere filen med indstillinger for Udgiv eller profil i værktøjet og derefter **slette** filen. Hvis du skal beholde filen, for at dele med andre arbejder på projektet for eksempel kan du gemme den i et sikkert sted som en *krypteret* mappe med begrænsede tilladelser.

Desuden skal du sørge for de importerede legitimationsoplysninger er sikret. For eksempel **Azure PowerShell** og **Azure kommandolinjen (Azure CLI)** store importerede oplysninger i dit **eget bibliotek** (*~* på Linux eller OS X-systemer og */users/yourusername* på Windows-systemer.) Ekstra sikkerhed, kan du **kryptere** disse placeringer, ved hjælp af krypteringsværktøjer for dit operativsystem.

### <a name="configuration-settings-and-connection-strings"></a>Indstillinger for søgekonfiguration og forbindelsesstrenge af
Det er almindelig praksis at gemme forbindelsesstrenge, legitimationsoplysninger og andre følsomme oplysninger i konfigurationsfiler. Desværre kan disse filer vises på dit websted, eller tjekket ind i en offentlig lager, udsætte disse oplysninger. En enkel søgning på [GitHub](https://github.com), kan for eksempel afdække utallige konfigurationsfiler med udsatte hemmeligheder i offentlige typer lagre.

Den bedste fremgangsmåde er at gemme oplysningerne af din app konfigurationsfiler. App Service kan du gemme oplysninger om konfigurationen som en del af runtime-miljø som **app indstillinger** og **strenge**. Værdierne, der vises i dit program på kørselstidspunktet via *miljøvariabler* for de fleste programmeringssprog. Disse værdier er tilføjet i din konfiguration af .NET på kørselstidspunktet til .NET-programmer. Ud over disse situationer disse konfigurationsindstillinger, forbliver krypterede medmindre du får vist eller konfigurere dem ved hjælp af [Azure Portal](https://portal.azure.com) eller funktioner som PowerShell eller Azure CLI. 

Gemme oplysninger om konfiguration i App Service gør det muligt for den app-administratoren for at låse følsomme oplysninger til fremstilling apps. Udviklere kan bruge en separat række indstillinger for søgekonfiguration til app udvikling og indstillingerne for automatisk kan tilsidesættes af de indstillinger, der er konfigureret i App-tjeneste. Heller ikke udviklere skal kende de hemmeligheder, der er konfigureret til appen fremstilling. Du kan finde flere oplysninger om konfiguration af indstillinger for app og strenge i App-tjeneste, [konfiguration af webapps](web-sites-configure.md).

### <a name="ftps"></a>FTPS

Azure App Service giver sikker FTP-adgang til filsystemet for din app gennem **FTPS**. Dette giver mulighed for sikker adgang til programkode på online samt diagnosticering logfiler. Det anbefales, at du altid bruger FTPS i stedet for FTP. 

Du kan finde linket FTPS for din app med følgende trin:

1. Åbn [Azure-portalen](https://portal.azure.com).
2. Vælg **Gennemse alt**.
3. Vælg **App Services**bladet **Gennemse** .
4. Vælg den ønskede app bladet **App tjenester** .
5. Vælg den app blade **alle indstillinger**.
6. Vælg **Egenskaber**bladet **Indstillinger** .
7. Linkene FTP- og FTPS er angivet i bladet **Indstillinger** . 

Du kan finde flere oplysninger om FTPS, [File Transfer Protocol](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

## <a name="next-steps"></a>Næste trin

Flere oplysninger om sikkerheden af Azure-platformen oplysninger til rapportering af en **sikkerhedshændelse eller misbrug**eller til at informere Microsoft, du vil udføre **markedsindtrængning test** af dit websted, i afsnittet sikkerhed i [Microsoft Azure Sikkerhedscenter](https://azure.microsoft.com/support/trust-center/security/).

Se [konfigurationsindstillinger åbnes ved angivelse i Azure App Service webapps](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)kan finde flere oplysninger om **web.config** eller **applicationhost.config** filer i App Service apps.

Finde oplysninger om oplysninger om logføring for App Service apps, som kan være nyttige til at opdage angreb, under [aktivere logføring af diagnostik](web-sites-enable-diagnostic-log.md).

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter app i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

## <a name="whats-changed"></a>Hvad er ændret

* Finde en vejledning til ændring fra websteder til App-tjenesten: [Azure App Service og dets indvirkning på eksisterende Azure Services](http://go.microsoft.com/fwlink/?LinkId=529714)
