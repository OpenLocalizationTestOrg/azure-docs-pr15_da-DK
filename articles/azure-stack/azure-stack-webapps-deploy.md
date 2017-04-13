<properties
    pageTitle="Føje en udbyder af Web Apps ressource til Azure stak | Microsoft Azure"
    description="Detaljeret vejledning til installation af Web Apps i Azure stak"
    services="azure-stack"
    documentationCenter=""
    authors="ccompy, apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>

# <a name="add-a-web-apps-resource-provider-to-azure-stack"></a>Føje en ressource udbyder af Web Apps til Azure stak

> [AZURE.NOTE] Følgende oplysninger gælder kun for Azure stak TP1 installationer.

Føje en ressource udbyder af Web Apps til Azure stak har syv trin:

1.  Hente nødvendige komponenter.
2.  Oprette certifikater der skal bruges i Azure stak Web Apps.
3.  Bruge installationsprogrammet til at hente fase og installere Azure stak Web Apps. 
4.  Validere Web Apps-Installation.
5.  Oprette DNS-poster til Front End- og indholdsstyringsserver Indlæs balancere.
6.  Registrere den nyligt udløst Onlines ressource udbyder ARM.
7.  Prøvekør provideren Web Apps ressource.

## <a name="download-required-components"></a>Hente nødvendige komponenter

1.  Hente [Azure stak App Service preview installer](http://aka.ms/azasinstaller). 
2.  Hent de [Azure stak App Service Installation hjælper scripts](http://aka.ms/azashelper). 
3.  Udtrække filerne fra hjælper scripts zip-filen, der skal være tre scripts:
    - Oprette AppServiceCerts.ps1
    - Oprette AppServiceDnsRecords.ps1
    - Registrer AppServiceResourceProvider.ps1 

## <a name="create-certificates-to-be-used-by-azure-stack-web-apps"></a>Oprette certifikater der skal bruges i Azure stak Web Apps

Dette første script fungerer med Azure stak nøglecenter til at oprette 3 certifikater, der er nødvendige for Web Apps. Du kan køre scriptet på ClientVM at sikre, at du kører PowerShell som azurestack\administrator:
1.  Udføre **Opret AppServiceCerts.ps1** scriptet i en PowerShell-session, kører som **azurestack\administrator**.  Dette opretter tre certifikater, i samme mappe som scriptet, der skal bruges af Webapps.
2.  Angiv en adgangskode for at sikre pfx-filer og gøre den ned, som skal du angive det i Azure stak Web Apps-installationsprogrammet.

## <a name="use-the-installer-to-download-and-install-azure-stack-web-apps"></a>Brug installationsprogrammet til at hente og installere Azure stak Web Apps

Appservice.exe installationsprogrammet skal:
1.  Brugeren skal acceptere Microsoft og tredjepart slutbrugerlicensaftaler.
2.  Indsamle oplysninger om installation af Azure stak.
3.  Oprette en blob objektbeholder i den angivne Azure stak lagerplads-konto.
4.  Hent de filer, der kræves for at installere provideren Azure stak Web App ressource.
5.  Forberede installationen til at udrulle provideren online ressource i Azure stak-miljø.
6.  Du kan overføre filerne til lagerplads App tjenestekonto.
7.  Præsentere oplysninger, der er behov for at starte skabelonen Azure ressourcestyring.

Følgende trin fører dig gennem installation faserne:

>[AZURE.NOTE] Du skal bruge en øgede konto (lokal eller domæne administrator) til at køre installationsprogrammet. Hvis du logge på som azurestack\azurestackuser, vil du blive bedt om øgede legitimationsoplysninger. 

1.  Køre appservice.exe som **azurestack\administrator**. 
2.  Klik på **Implementer ved hjælp af Azure ressourcestyring**.

![Installer Azure stak App Service Technical Preview 1][1]

3.  Gennemse og Accepter licensvilkårene for Microsoft-Software foreløbige, og klik derefter på **Næste**.
4.  Gennemse og Acceptér vilkårene tredje partylicense, og klik derefter på **Næste**.
5.  Gennemse konfigurationsoplysninger App skybaseret tjeneste, og klik på **Næste**.

![Azure stak App Technical Preview 1 App Service skyen tjenestekonfiguration][2]

6. Klik på **Forbind** (ud for feltet Azure stak abonnementer).

![Azure stak App Service Technical Preview 1 App Service skyen konfiguration skærmen to][3]

7.  I vinduet Azure stak godkendelse giver dit **administratorkonto Azure Active Directory Service** og din **adgangskode**, og klik derefter på **Log på**.
**Note:** Dette er den Azure Active Directory-konto, som du angav, da du installerede Azure stablen.
    - Klik på **Pil ned** i højre side af feltet ud for **Azure stak abonnementer** , og vælg derefter dit abonnement.

![Azure stak App Service Technical Preview 1 abonnement markering][5]

8.  Klik på **Pil ned** i højre side af feltet ud for **Azure stak placeringer**.
    - Vælg **lokale**.
9. Skriv **navnet** for din administrator.
10. Angiv en **adgangskode** for administratoren.
11. Gennemgå **oplysninger om SQL Server** og foretage ændringer, hvis det er nødvendigt.
12. Gennemse **Systemadministrator logonkonto** og foretage ændringer, hvis det er nødvendigt.
13. Skriv **Din adgangskode systemadministrator**.
14. Klik på **Næste**.  På dette tidspunkt kontrollere installationsprogrammet nu forbindelsesoplysningerne til SQL Server, der er angivet.

![Azure stak App Service Technical Preview 1 abonnement markering][4]    

15. Klik på **Gennemse** ud for den **Web Apps standard SSL certifikatfil** , og gå til **WebApp. AzureStack.Local** certifikat [oprettede tidligere](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
16. Angiv den **adgangskode til certifikat** , du angav, da du har oprettet certifikater.
17. Klik på **Gennemse** ud for den **Ressource udbyder SSL-certifikatfil** , og gå til **management. AzureStack.Local** certifikat [oprettede tidligere](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
18. Angiv den **adgangskode til certifikat** , du angav, da du har oprettet certifikater.
19. Klik på **Gennemse** ud for den **Ressource udbyder rod-certifikatfil** , og gå til **management. AzureStack.Local** certifikat [oprettede tidligere](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
20. Klik på **Næste** installationsprogrammet kontrollere den angivne certifikat adgangskode.

![Azure stak App Service Technical Preview 1 Certifikatdetaljer][6]

Installationen tager om 45-60 minutter for at gennemføre.

![Status for Azure stak App Service Technical Preview 1 installationen][7]

21. Klik på **Afslut**, når installationsprogrammet er fuldført.

## <a name="validate-web-apps-installation"></a>Validere Web Apps-Installation

1.  Åbn **Hyper-V Manager**på **Azure stak Værtsmaskinen** .
2.  Find **CN0 VM** og **oprette forbindelse** til VM.
![Azure stak App Service Technical Preview 1 Hyper-V Manager][8]
3.  Dobbeltklik på **Web skyen Management Console**på skrivebordet på denne VM.
![Azure stak App Service Technical Preview 1 Management Console][9]
4.  Gå til **administrerede servere**.
5.  Når alle computere er fortsætte **klar** til næste trin. 
![Azure stak App Service Technical Preview 1 administrerede servere Status][10]

## <a name="create-dns-records-for-the-management-server-and-front-end-load-balancers"></a>Oprette DNS-poster for Management Server og Front End Indlæs balancere
1.  Åbn en forekomst af PowerShell som **azurestack\administrator**.
2.  Gå til sted, hvor de scripts hentede og udpakkede i det [forudgående trin](#Download-Required-Components).
3.  Kør scriptet **Opret AppServiceDnsRecords.ps1** , opretter dette DNS-poster for at aktivere portal og web app-anmodningerne skal dirigeres til Front End-servere.  Under ARM installation af Web Apps, to Software Indlæs balancere (SLBs), der blev oprettet i udbyderen af det netværk ressource. De Peg Management-servere og til Front End-servere. Portalen og ARM-baseret anmodninger til Azure stak App-ressource udbyder gå til Management Server.

## <a name="register-the-newly-deployed-azure-stack-web-apps-provider-with-arm"></a>Registrere den nyligt udløst Azure stak Web Apps-udbyder med ARM
1.  Åbn en forekomst af PowerShell som **azurestack\administrator**.
2.  Gå til sted, hvor de scripts hentede og udpakkede i det [forudgående trin](#Download-Required-Components).
3.  Kør scriptet **Register-AppServiceResourceProvider.ps1** . 

>[AZURE.NOTE] Skriv det brugernavn og adgangskoden **nøjagtigt (herunder store og små bogstaver)** , som det er angivet for felterne **Virtual_Machine Administrator** og **adgangskode** i installationen eller ressource udbyder registreringen mislykkes.

## <a name="test-drive-azure-stack-web-apps"></a>Test drev Azure stak Webapps

Nu, hvor du har installeret og registreret Onlines ressource udbyder, kan du teste det for at kontrollere, at lejere kan installere webapps.

1.  Klik på ny portalen Azure stak, skal du klikke på Web + Mobile, og klik på Web App.
2.  Skriv et navn i feltet Web app i bladet Web App.
3.  Klik på ny under ressourcegruppe, og skriv derefter et navn i feltet ressourcegruppe. 
4.  Klik på App Service plan/placering, og klik på Opret ny.
5.  Skriv et navn i feltet App Service plan i bladet App Service plan.
6.  Klik på priser niveau, skal du klikke på Frigør delt eller delt delt, skal du klikke på Vælg, klik på OK og klik derefter på Opret.
7.  I under et minut vises et felt for den nye WebApp på dashboardet. Klik på feltet.
8.  Klik på Gennemse for at få vist standard-webstedet for denne app bladet web app.


**Installere et WordPress, DNN eller Django websted (valgfrit)**

1. I portalen Azure stak, klik på "+", gå til Azure Marketplace, installere et Django websted, og vent på en vellykket gennemførelse. Django webplatform bruger en fil system-baseret database og kræver ikke en ekstra ressource udbydere som SQL eller MySQL.  

2. Hvis du også installeret en MySQL ressource-udbyder, kan du installere et WordPress websted fra Marketplace. Når du bliver bedt om for database parametre, indtast brugernavnet som *User1@Server1* (med det brugernavn og servernavn efter eget valg).

3. Hvis du også installeret en SQL Server ressource-udbyder, kan du installere et DNN websted fra Marketplace. Når du bliver bedt om for database parametre, Vælg en database på den computer, der kører SQL Server, der er forbundet til udbyderen ressource.

## <a name="next-steps"></a>Næste trin

Du kan også prøve andre [platform som en tjeneste (PaaS)-tjeneste](azure-stack-tools-paas-services.md), såsom [SQL Server ressource udbyder](azure-stack-sql-rp-deploy-short.md) og [MySQL ressource udbyder](azure-stack-mysql-rp-deploy-short.md).

<!--Image references-->
[1]: ./media/azure-stack-webapps-deploy/AppService_exe_Start.png
[2]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep1.png
[3]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2.png
[4]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2_populated.png
[5]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2_SubscriptionSelection.png
[6]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep3_Certificates.png
[7]: ./media/azure-stack-webapps-deploy/AppService_exe_InstallationProgress.png
[8]: ./media/azure-stack-webapps-deploy/HyperV.png
[9]: ./media/azure-stack-webapps-deploy/MMC.png
[10]: ./media/azure-stack-webapps-deploy/ManagedServers.png


<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[WebAppsDeployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
