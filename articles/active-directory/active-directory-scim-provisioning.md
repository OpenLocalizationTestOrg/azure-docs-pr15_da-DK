<properties
    pageTitle="Brug af SCIM til at aktivere automatisk klargøring af brugere og grupper fra Azure Active Directory til programmer | Microsoft Azure"
    description="Azure Active Directory kan automatisk klargøring af brugere og grupper til en hvilken som helst program eller identitet store, der er fronted ved en webtjeneste med grænsefladen er defineret i SCIM protocol specifikationen"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#<a name="using-scim-to-enable-automatic-provisioning-of-users-and-groups-from-azure-active-directory-to-applications"></a>Brug af SCIM til at aktivere automatisk klargøring af brugere og grupper fra Azure Active Directory til programmer

##<a name="overview"></a>Oversigt

Azure Active Directory kan automatisk klargøring af brugere og grupper til en hvilken som helst program eller identitet store, der er fronted ved en webtjeneste med grænsefladen er defineret i [SCIM 2.0 protokolspecifikation](https://tools.ietf.org/html/draft-ietf-scim-api-19). Azure Active Directory kan sende anmodninger om at oprette, ændre og slette tildelte brugere og grupper til denne webtjeneste, som kan derefter oversætte disse anmodninger til handlinger på målet identitet store. 

![][1]
*Figur: Klargøring fra Azure Active Directory til en identitet store via en webtjeneste*

Denne funktion kan bruges sammen med muligheden for "[tage dine egne app](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)" i Azure AD til at aktivere enkeltlogon og automatisk bruger klargøring til programmer, der indeholder eller er fronted ved en SCIM webtjeneste.

Der er to use cases for SCIM i Azure Active Directory:

* **Klargøring af brugere og grupper til programmer, der understøtter SCIM** - programmer, der understøtter SCIM 2.0 og bruge OAuth bæreren tokens til godkendelse fungerer med Azure AD i dialogboksen.

* **Oprette din egen klargøring løsning til programmer, der understøtter andre API-baserede klargøring** - For ikke-SCIM programmer, kan du oprette en SCIM slutpunkt oversætte mellem Azure AD SCIM slutpunkt og uanset API programmet understøtter til brugerklargøring af.  For at hjælpe i udviklingen af et SCIM slutpunkt, giver vi CLI biblioteker sammen med kodeeksempler, der viser, hvordan du angive et SCIM slutpunkt og oversætte SCIM meddelelser.  

##<a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Klargøring af brugere og grupper til programmer, der understøtter SCIM

Azure Active Directory kan konfigureres automatisk tildelt klargøring af brugere og grupper til programmer, der implementerer et [System til tværs af domæner identitet Management 2 (SCIM)](https://tools.ietf.org/html/draft-ietf-scim-api-19) Web service og acceptere OAuth bæreren tokens til godkendelse. I specifikationen SCIM 2.0 skal programmer opfylde følgende krav:

* Understøtter oprettelse af brugere og/eller grupper, ud fra punkt 3.3 af SCIM-protokollen.  

* Understøtter ændring af brugere og/eller grupper med programrettelse anmodninger ud fra punkt 3.5.2 af SCIM-protokollen.  

* Understøtter hentning af en kendte ressource ud fra sektionen 3.4.1 af SCIM-protokollen.  

*  Understøtter forespørgsler brugere og/eller grupper, ud fra sektionen 3.4.2 af SCIM-protokollen.  Som standard brugere spørges efter externalId og grupper spørges efter vist navn.  

* Understøtter forespørgsler bruger-ID og manager ud fra sektionen 3.4.2 af SCIM-protokollen.  

* Understøtter forespørgsler grupper efter ID og efter medlem ud fra sektionen 3.4.2 af SCIM-protokollen.  

* Accepterer OAuth bæreren tokens om tilladelse ud fra punkt 2.1 af SCIM-protokollen.

Du skal kontakte udbyderen af programmet eller udbyderen programmet dokumentation for sætninger for kompatibilitet med disse krav.
 
###<a name="getting-started"></a>Kom godt i gang

Programmer, der understøtter den SCIM profil, der er beskrevet ovenfor kan være forbundet til Azure Active Directory ved hjælp af funktionen "Brugerdefineret" app i galleriet Azure AD-program. Når forbindelse, kører Azure AD en synkroniseringen hver 5 minutter, hvor den forespørger programmets SCIM slutpunkt for tildelte brugere og grupper, og opretter eller ændrer dem ifølge tildelingsoplysninger.

**Hvis du vil forbinde et program understøtter, der SCIM:**

1.  Start Azure management-portalen på https://manage.windowsazure.com i en webbrowser.
2.  Gå til **Active Directory > bibliotek > [dine Directory] > programmer**, og vælg **Tilføj > tilføje et program i galleriet**.
3.  Vælg fanen **brugerdefineret** til venstre, Angiv et navn til dit program, og klik på ikonet markering for at oprette en app-objekt.

![][2]

4.  Vælg knappen **Konfigurer konto klargøring** andet på skærmbilledet resulterende.
5.  Angiv URL-adressen på programmets SCIM slutpunkt i feltet **Klargøring slutpunkt URL-adresse** .
6.  Hvis SCIM slutpunktet kræver en OAuth-bærertoken fra en udsteder end Azure AD, derefter kopiere påkrævet OAuth-bærertokenet i feltet **Godkendelse Token (valgfrit)** . Er dette felt er tomt, og klik derefter Azure AD indeholder en OAuth-bærertoken udstedt fra Azure AD med hver enkelt anmodning. Apps, der bruger Azure AD, som en udbyder af identitet kan validere denne Azure AD-udstedt token.
7.  Klik på **Næste**, og klik på knappen **Start Test** have Azure Active Directory forsøger at oprette forbindelse til SCIM slutpunkt. Hvis forsøg ikke, vises diagnostiske oplysninger.  
8.  Hvis forsøg på at oprette forbindelse til succes program, klik derefter på **Næste** i de resterende skærmbilleder, og klik på **udført** for at lukke dialogboksen.
9.  Vælg knappen tredje **Tildele konti** på skærmbilledet resulterende. I den resulterende brugere og grupper sektion at tildele brugere eller grupper, du vil at blive klargjort til programmet.
10. Når brugere og grupper tildeles, skal du klikke på fanen **Konfigurer** tæt på toppen af skærmen.
11. Under **Konto klargøring**kan bekræfte, at Status er indstillet på. 
12. Klik på **Genstart konto klargøring** til kick-start klargøring processen under **værktøjer**.

Bemærk, at 5-10 minutter kan gå, før klargøring processen begynder at sende anmodninger til SCIM slutpunkt.  Er angivet en oversigt over forsøg på forbindelser under fanen Dashboard i det program, og både en rapport over klargøring aktivitet og klargøring fejl kan hentes fra fanen mappens rapporter.

##<a name="building-your-own-provisioning-solution-for-any-application"></a>Bygge din egen klargøring løsning For et program

Ved at oprette en SCIM webtjeneste, der grænseflader med Azure Active Directory, kan du aktivere enkelt sign-on og automatisk brugerklargøring til næsten alle programmer, der indeholder en RESTEN eller SOAP bruger klargøring API.

Sådan virker det:

1.  Azure AD indeholder et almindelige sprog infrastruktur bibliotek med navnet [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Systemintegratorer og udviklere kan bruge dette bibliotek til at oprette og installere en SCIM-baserede web tjenesteslutpunkt kan oprette forbindelse Azure AD til et program identitet store.
2.  Tilknytninger er implementeret i webtjenesten tilknytte standardiseret bruger skemaet til bruger skemaet og protokol, der kræves af programmet.
3.  Slutpunkt URL-adressen er registreret i Azure AD som en del af et brugerdefineret program i galleriet programmet på computeren.
4.  Brugere og grupper tildeles til dette program i Azure AD. På opgaven, er de placeret i en kø skal synkroniseres med destinationsprogrammet. Håndtering af køen synkroniseringen kører hver 5 minutter.

###<a name="code-samples"></a>Kodeeksempler

For at gøre denne proces nemmere, er et sæt af [kode eksempler](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) leveret, oprette et SCIM web service slutpunkt og viser automatisk klargøring. Et eksempel er en udbyder, der fører en fil med rækker med kommaseparerede værdier, der repræsenterer brugere og grupper.  Den anden er en udbyder, der fungerer på tjenesten Amazon Web Services identitet og administration af Access.  

**Forudsætninger**

* Visual Studio 2013 eller nyere
* [Azure SDK til .NET](https://azure.microsoft.com/downloads/)
* Windows computer, der understøtter ASP.NET framework 4.5 som skal bruges som SCIM slutpunkt. Denne computer skal kunne åbnes fra skyen
* [Et Azure-abonnement med en prøveversion eller et licenseret version af Azure AD Premium](https://azure.microsoft.com/services/active-directory/)
* Eksemplet Amazon AWS kræver biblioteker fra [AWS-værktøjskassen til Visual Studio](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html). Se filen med vigtige oplysninger, der følger med eksempel få mere at vide

###<a name="getting-started"></a>Kom godt i gang

Den nemmeste måde at implementere en SCIM slutpunkt, der kan acceptere klargøring anmodninger fra Azure AD er at opbygge og anvende kodeeksemplet, som producerer klargjort brugere til en fil med kommaseparerede værdier (CSV).

**Sådan oprettes et eksempel SCIM slutpunkt:**

1.  Hent pakken kode eksempel på [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2.  Udpakke pakken, og Placer det på din Windows-computer på en placering som C:\AzureAD-BYOA-Provisioning-Samples\.
3.  Start FileProvisioningAgent løsningen i Visual Studio i denne mappe.
4.  Vælg **Værktøjer > bibliotek Package Manager > pakke Manager-konsollen**, og udføre kommandoerne under for FileProvisioningAgent projektet til at fortolke løsning referencer:

    Installer-pakke Microsoft.SystemForCrossDomainIdentityManagement Installer-pakke Microsoft.IdentityModel.Clients.ActiveDirectory Installer-pakke Microsoft.Owin.Diagnostics Installer-pakke Microsoft.Owin.Host.SystemWeb

5.  Opbygge FileProvisioningAgent projektet.
6.  Start programmet kommandoprompt i Windows (som Administrator), og bruge kommandoen **cd** til at ændre mappen til mappen **\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug** .
7.  Kør kommandoen nedenfor, erstatte < ip-adresse > med IP-adresse eller domæne navnet på Windows-computer.

    FileAgnt.exe http://<ip-address>:9000 TargetFile.csv

8.  I Windows under **Windows-indstillinger > netværk og Internet-indstillinger**, skal du markere den **Windows Firewall > Avancerede indstillinger for**, og oprette en **Indgående regel** , der tillader indgående adgang til port 9000.
9.  Hvis Windows-computer er bag en router, skal router være konfigureret til at udføre netværk Access oversættelse mellem dens port 9000, der vises på internettet og port 9000 på Windows-computer. Dette er påkrævet til Azure AD skal kunne få adgang til dette slutpunkt i skyen.


**Til at registrere eksempel SCIM slutpunkt i Azure AD:**

1.  Start Azure management-portalen på https://manage.windowsazure.com i en webbrowser.
2.  Gå til **Active Directory > bibliotek > [dine Directory] > programmer**, og vælg **Tilføj > tilføje et program i galleriet**.
3.  Vælg fanen **brugerdefineret** til venstre, Angiv et navn som "SCIM Test App", og klik på ikonet markering for at oprette en app-objekt. Bemærk, at objektet application oprettet er har til hensigt at repræsentere appen destinationsadresse, du vil klargøring til og implementere single sign-on til og ikke blot SCIM slutpunkt.

![][2]

4.  Vælg knappen **Konfigurer konto klargøring** andet på skærmbilledet resulterende.
5.  I dialogboksen Angiv internet-vises URL-adresse og port på din SCIM slutpunkt. Dette kan være noget, som http://testmachine.contoso.com:9000 eller http://<ip-address>:9000/, hvor < ip-adresse > er internettet vises IP address.  
6.  Klik på **Næste**, og klik på knappen **Start Test** have Azure Active Directory forsøger at oprette forbindelse til SCIM slutpunkt. Hvis forsøg ikke, vises diagnostiske oplysninger.  
7.  Hvis forsøg på at oprette forbindelse til din webtjeneste lykkes, derefter skal du klikke på **Næste** på de resterende skærmbilleder, og klik på **udført** for at lukke dialogboksen.
8.  Vælg knappen tredje **Tildele konti** på skærmbilledet resulterende. I den resulterende brugere og grupper sektion at tildele brugere eller grupper, du vil at blive klargjort til programmet.
9.  Når brugere og grupper tildeles, skal du klikke på fanen **Konfigurer** tæt på toppen af skærmen.
10. Under **Konto klargøring**kan bekræfte, at Status er indstillet på. 
11. Klik på **Genstart konto klargøring** til kick-start klargøring processen under **værktøjer**.

Bemærk, at 5-10 minutter kan gå, før klargøring processen begynder at sende anmodninger til SCIM slutpunkt.  Er angivet en oversigt over forsøg på forbindelser under fanen Dashboard i det program, og både en rapport over klargøring aktivitet og klargøring fejl kan hentes fra fanen mappens rapporter.

Det sidste trin i bekræfte prøven er at åbne filen TargetFile.csv i mappen \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug på din Windows-computer. Når klargøring processen kører, vises denne fil detaljerne for alle tildelt og klargjort brugere og grupper.

###<a name="development-libraries"></a>Udvikling biblioteker

Hvis du vil udvikle din egen webtjeneste, der er i overensstemmelse med specifikationen SCIM, først blive fortrolig med de følgende biblioteker, der leveres af Microsoft for at fremskynde udviklingsprocessen: 

**1:**  Almindelige sprog infrastruktur biblioteker tilbydes til brug med sprog, der er baseret på denne infrastruktur, såsom C#.  En af disse biblioteker, [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), erklærer en grænseflade, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, vist i nedenstående illustration.  En udvikler, ved hjælp af bibliotekerne vil implementere denne grænseflade med en klasse, der kan være refereres til, generisk som en udbyder.  Bibliotekerne aktivere udvikler skal installeres nemt en webtjeneste, der svarer til specifikationen SCIM, enten som vært i Internet Information Services eller en hvilken som helst eksekverbare fælles sprog infrastruktur enhed.  Anmodninger om til denne webtjeneste skal oversættes til opkald til metoder, den udbyder, der ville programmeres af udvikleren til at betjene på nogle identitet store.    

![][3]

**2:** [Express distribuere programmer](http://expressjs.com/guide/routing.html) er tilgængelige for parsing af node.js anmodning objekter repræsenterer opkald (som defineret af specifikationen SCIM), har foretaget i en node.js webtjeneste.     

###<a name="building-a-custom-scim-endpoint"></a>Opbygning af en brugerdefineret SCIM slutpunkt

Brug af de biblioteker, der er beskrevet ovenfor, kan udviklere, der bruger disse biblioteker hoste deres tjenester en eksekverbare fælles sprog infrastruktur enhed, eller inden for Internet Information Services.  Her er eksempelkode for værtstjeneste inden for en eksekverbare samling på adressen http://localhost:9000: 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  
    
    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

Det er vigtigt at være opmærksom på, at denne tjeneste skal have en HTTP-adresse og server-godkendelsescertifikat som rodnøglecenteret er et af følgende: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Et servergodkendelsescertifikat kan være bundet til en port på en Windows-vært ved hjælp af værktøjet netværk shell som vist her: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  
 
Her er den angivne værdi for argumentet certhash miniature for certifikatet, mens den angivne værdi for argumentet sikkerhedskontrol er en vilkårlig globalt entydigt id.  

Hvis du vil placere tjenesten inden for Internet Information Services, vil en udvikler opbygge en fælles sprog infrastruktur kode bibliotek samling med en klasse, kaldet Start i samlingen standardnavneområde.  Her er et eksempel på sådanne en klasse: 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

###<a name="handling-endpoint-authentication"></a>Håndtering af slutpunkt godkendelse

Anmodninger fra Azure Active Directory indeholder en 2.0 OAuth-bærertokenet.   En hvilken som helst tjenesten modtager anmodningen skal godkende udsteder som værende Azure Active Directory på vegne af den forventede Azure Active Directory-lejeren, for at få adgang til Azure Active Directory's Graph webtjeneste.  I dette token på udsteder er identificeret med et iss krav, som "iss": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  I dette eksempel basisadressen for værdien af krav, Azure Active Directory identificerer https://sts.windows.net, som udstederen, mens segmentet relativ adresse, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, er et entydigt id for den Azure Active Directory-lejer på vegne af som Tokenet er udstedt.  Hvis Tokenet er udstedt til adgang til Azure Active Directory's Graph-webtjeneste, bør id for tjenesten, 00000002-0000-0000-c000-000000000000, være i værdien af det token aud krav.  

Udviklere, der bruger fælles sprog infrastruktur biblioteker, der leveres af Microsoft til oprettelse af en SCIM tjeneste kan godkende anmodninger fra Azure Active Directory ved hjælp af pakken Microsoft.Owin.Security.ActiveDirectory ved at følge disse trin: 

**1:**  Implementere egenskaben Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior ved at få det returnere en metode til at blive kaldt, når tjenesten er startet i en udbyder: 

    public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }

**2:**  Føj følgende kode til denne metode skal have en hvilken som helst anmodning for tjenestens slutpunkter godkendte som forsynet med et token, der er udstedt af Azure Active Directory på vegne af en bestemt lejer, for at få adgang til Azure Active Directory's Graph webtjeneste: 

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }

##<a name="user-and-group-schema"></a>Bruger- og gruppe skema

Azure Active Directory kan klargøre to typer ressourcer til SCIM webtjenester.  Disse typer ressourcer er brugere og grupper.  

Bruger ressourcer er identificeret med skema-id, urn: ietf:params:scim:schemas:extension:enterprise:2.0:User, som er inkluderet i denne protokolspecifikation: http://tools.ietf.org/html/draft-ietf-scim-core-schema.  Standardtilknytning af egenskaber for brugere i Azure Active Directory i egenskaberne for urn: ietf:params:scim:schemas:extension:enterprise:2.0:User ressourcer er angivet i tabel 1, herunder.  

Gruppere ressourcer er identificeret med id skema http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  Tabel 2 nedenfor viser standardtilknytning af egenskaber for grupper i Azure Active Directory til attributterne http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group ressourcer.  

###<a name="table-1-default-user-attribute-mapping"></a>Tabel 1: Standard bruger attributten tilknytning

| Azure Active Directory-bruger | urn: ietf:params:scim:schemas:extension:enterprise:2.0:User |
| ------------- | ------------- |
| IsSoftDeleted | aktive |
| vist navn | vist navn |
| Fax til TelephoneNumber | phoneNumbers type eq "fax".value |
| givenName | name.givenName |
| Stilling | titel |
| mail | mails type eq "arbejde".value |
| mailNickname | externalId |
| Manager | Manager |
| Mobile | phoneNumbers type eq "mobile".value |
| objectId | id |
| Postnummer | adresser type eq "arbejde".postalCode |
| proxy-adresser | mails [Skriv eq "andet"]. Værdi |
| fysisk-levering-OfficeName | adresser [Skriv eq "andet"]. Formateret |
| streetAddress | adresser type eq "arbejde".streetAddress |
| Efternavn | name.familyName |
| Telefonnummer | phoneNumbers type eq "arbejde".value |
| bruger PrincipalName | Brugernavn |


###<a name="table-2-default-group-attribute-mapping"></a>Tabel 2: Standard gruppe attributtilknytning

| Azure Active Directory-gruppe | http://schemas.Microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| ------------- | ------------- |
| vist navn | externalId |
| mail | mails type eq "arbejde".value |
| mailNickname | vist navn |
| medlemmer | medlemmer |
| objectId | id |
| proxyAddresses | mails [Skriv eq "andet"]. Værdi |


##<a name="user-provisioning-and-de-provisioning"></a>Brugerklargøring af og deaktivere klargøring

Figur nedenfor viser de meddelelser, Azure Active Directory sender til en SCIM tjeneste til at administrere livscyklus for en bruger i en anden identitet store.  Diagrammet viser også en SCIM tjeneste, der er implementeret ved hjælp af de almindelige sprog infrastruktur biblioteker, der leveres af Microsoft til oprettelse af disse tjenester kan oprettes disse anmodninger som opkald til metoder til en udbyder.  

![][4]
*Figur: Brugerklargøring og deaktivere klargøring sekvens*

**1:**  Azure Active Directory forespørgsler tjenesten for en bruger med en værdi for attributten externalId, der matcher attributværdien mailNickname for en bruger i Azure Active Directory.  Forespørgslen, der angives som en Hypertext Transfer Protocol anmodning som denne, hvori jyoung er et eksempel på en mailNickname for en bruger i Azure Active Directory: 

    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...

Hvis tjenesten blev oprettet ved hjælp af de almindelige sprog infrastruktur biblioteker, der leveres af Microsoft til implementering af SCIM tjenester, skal anmodningen oversættes til et opkald til metoden forespørgsel for den tjenesteudbyder.  Her er signaturen for denne metode: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);

Her er definitionen af grænsefladen Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 

    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }
    
    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }

Hvis det er det foregående eksempel på en forespørgsel for en bruger med en given værdi for attributten externalId bliver værdierne for de argumenter, der overføres til metoden Query på følgende måde: 

* parametre. AlternateFilters.Count: 1
* parametre. AlternateFilters.ElementAt(0). AttributePath: "externalId"
* parametre. AlternateFilters.ElementAt(0). Sammenligningsoperator: ComparisonOperator.Equals
* parametre. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"
* correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin. RequestId"] 

**2:**  Hvis svar på en forespørgsel til tjenesten for en bruger med en værdi for attributten externalId, der matcher attributværdien mailNickname for en bruger i Azure Active Directory ikke returnerer alle brugere, derefter Azure Active Directory beder om, at tjenesten klargøring af en bruger, der svarer til en i Azure Active Directory.  Her er et eksempel på anmodningen: 

    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}

Fælles sprog infrastruktur biblioteker, der leveres af Microsoft til implementering af SCIM tjenester vil oversætte forespørgsel til et opkald til metoden Create af den tjenesteudbyder.  Metoden Create har denne signatur: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);

Hvis det er en anmodning om at tildele en bruger, bliver værdien af argumentet ressource en forekomst af Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser klasse defineret i biblioteket Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Hvis anmodningen til at klargøre brugeren lykkes, er der forventes implementering af metoden til at returnere en forekomst af den på Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser klasse med værdien af egenskaben id, der er angivet til det entydige id for den bruger, der netop klargjort.  

**3:**  Hvis du vil opdatere en bruger, der er blevet konstateret, at der findes i en identitet store fronted ved en SCIM, fortsætter Azure Active Directory ved at anmode om den aktuelle status for denne bruger fra tjenesten med en anmodning om som denne: 

    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...

I en tjeneste, der er oprettet ved hjælp af de almindelige sprog infrastruktur biblioteker, der leveres af Microsoft til implementering af SCIM services, skal anmodningen oversættes til et opkald til den tjenesteudbyder Hent betalingsmåde.  Her er signaturen for metoden Hent: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);
    
    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }

Hvis det er det foregående eksempel på en anmodning om at hente den aktuelle status for en bruger, bliver værdierne i egenskaberne for det objekt, der er angivet som værdien af argumentet parametre på følgende måde: 

* Id: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

**4:**  Hvis attributten reference er der skal opdateres, så Azure Active Directory beder tjenesten for at afgøre, om den aktuelle værdi af attributten reference i identitet store fronted af tjenesten allerede svarer til værdien for attributten i Azure Active Directory.  I forbindelse med brugere er den eneste attribut, hvor den aktuelle værdi forespørges på denne måde attributten manager.  Her er et eksempel på en anmodning om at fastslå, om attributten manager for et bestemt brugerobjekt i øjeblikket har en bestemt værdi: 

    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...

Værdien af forespørgselsparameter attributter-id, betyder, hvis der findes en brugerobjekt, der opfylder det udtryk, der er angivet som værdien af forespørgselsparameter filter, og klik derefter tjenesten forventes at svare med en urn: ietf:params:scim:schemas:core:2.0:User eller urn: ietf:params:scim:schemas:extension:enterprise:2.0:User ressource, herunder kun værdien af denne ressource-id-attributten.  Naturligvis værdien af id-attributten kendes til person – det er angivet i værdien af forespørgselsparameter filter; Formålet med beder om det er faktisk til at anmode om en minimale repræsentation af en ressource, der opfylder filterudtrykket som angivelse af, om der findes sådanne objektet.   

Hvis tjenesten blev oprettet ved hjælp af de almindelige sprog infrastruktur biblioteker, der leveres af Microsoft til implementering af SCIM tjenester, skal anmodningen oversættes til et opkald til metoden forespørgsel for den tjenesteudbyder.  Værdien af egenskaberne for det objekt, der er angivet som værdien af argumentet parametre bliver på følgende måde: 

* parametre. AlternateFilters.Count: 2
* parametre. AlternateFilters.ElementAt(x). AttributePath: "-id"
* parametre. AlternateFilters.ElementAt(x). Sammenligningsoperator: ComparisonOperator.Equals
* parametre. AlternateFilter.ElementAt(x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parametre. AlternateFilters.ElementAt(y). AttributePath: "manager"
* parametre. AlternateFilters.ElementAt(y). Sammenligningsoperator: ComparisonOperator.Equals
* parametre. AlternateFilter.ElementAt(y). ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parametre. RequestedAttributePaths.ElementAt(0): "-id"
* parametre. SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

Her værdien af indekset x kan være 0 og værdien af indeks y kan være 1, eller værdien af x kan være 1 og værdien for y kan være 0, afhængigt af rækkefølgen af udtryk af forespørgselsparameter filter.   

**5:**  Her er et eksempel på en anmodning fra Azure Active Directory til en SCIM service for at opdatere en bruger: 

    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}

Microsoft fælles sprog infrastruktur biblioteker til implementering af SCIM tjenester vil oversætte anmodningen til et opkald til metoden Update af den tjenesteudbyder.  Her er signaturen for denne metode: 

    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }
    
    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }
      
      public string Value
      { get; set; }
    }



Hvis det er det foregående eksempel på en anmodning om at opdatere en bruger har det objekt, der er angivet som værdien af argumentet programrettelse disse egenskabsværdier: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest som PatchRequest2). Operations.Count: 1
* (PatchRequest som PatchRequest2). Operations.ElementAt(0). OperationName: OperationName.Add
* (PatchRequest som PatchRequest2). Operations.ElementAt(0). Path.AttributePath: "manager"
* (PatchRequest som PatchRequest2). Operations.ElementAt(0). Value.Count: 1
* (PatchRequest som PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest som PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Værdien: 2819c223-7f76-453a-919d-413861904646

**6:**  Hvis du vil deaktivere klargøre en bruger fra en identitet store fronted af en SCIM-tjeneste, sender Azure Active Directory en anmodning om som denne: 

    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    
Hvis tjenesten blev oprettet ved hjælp af de almindelige sprog infrastruktur biblioteker, der leveres af Microsoft til implementering af SCIM tjenester, skal anmodningen oversættes til et opkald til metoden Delete af den tjenesteudbyder.   Denne metode har denne signatur: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
 
Det objekt, der er angivet som værdien af argumentet resourceIdentifier har disse egenskabsværdier for det foregående eksempel på en anmodning om at deaktivere klargøring af en bruger: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

##<a name="group-provisioning-and-de-provisioning"></a>Gruppen klargøring og deaktivere klargøring

Figur nedenfor viser de meddelelser, Azure Active Directory sender til en SCIM-tjeneste til at administrere livscyklus for en gruppe i en anden identitet store.  Disse meddelelser adskiller sig fra de meddelelser, der vedrører brugere på tre måder: 

* Skemaet for en gruppe ressource vil blive identificeret som http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  
* Anmodninger om at hente grupper angiver attributten medlemmer der skal udelukkes fra alle de ressourcer, der er angivet som svar på anmodningen.  
* Anmodninger om at fastslå, om en referenceattribut har en bestemt værdi bliver anmodninger om attributten medlemmer.  

![][5]
*Figur: Gruppere klargøring og deaktivere klargøring sekvens*

##<a name="related-articles"></a>Relaterede artikler

- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
- [Automatisere bruger klargøring/ophævelse af klargøring til SaaS Apps](active-directory-saas-app-provisioning.md)
- [Tilpasse attributtilknytninger til klargøring af bruger](active-directory-saas-customizing-attribute-mappings.md)
- [Skrive udtryk for attributten tilknytninger](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Angivelse af område filtre til klargøring af bruger](active-directory-saas-scoping-filters.md)
- [Klargøring af beskeder-konto](active-directory-saas-account-provisioning-notifications.md)
- [Liste over selvstudier om, hvordan du integrerer SaaS Apps](active-directory-saas-tutorial-list.md)


    
<!--Image references-->
[1]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG
