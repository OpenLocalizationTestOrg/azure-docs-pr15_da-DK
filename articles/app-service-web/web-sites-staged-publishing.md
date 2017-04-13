<properties
    pageTitle="Konfigurere arrangere miljøer til webapps i Azure App Service"
    description="Lær at bruge faseinddelt udgivelse for webapps i Azure App Service."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    writer="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/09/2016"
    ms.author="cephalin"/>

# <a name="set-up-staging-environments-for-web-apps-in-azure-app-service"></a>Konfigurere arrangere miljøer til webapps i Azure App Service
<a name="Overview"></a>

Når du installerer din WebApp til [App-tjenesten](http://go.microsoft.com/fwlink/?LinkId=529714), kan du installere en separat installation slot i stedet for standard fremstilling slotten, når du kører i tilstanden **Standard** eller **Premium** App Service plan. Installation slot er virkeligheden findes online med deres egen værtsnavne. Web app-indhold og konfigurationer elementer kan byttes mellem to installation pladser, herunder fremstilling slot. Implementering af dit program til en installation plads har følgende fordele:

- Du kan kontrollere web app ændringer i en midlertidig installation slot før udskifte det med fremstilling slotten.

- Installerer en web-app i en slot først, og Skift til fremstilling sikrer, at alle forekomster af slotten varmet op før blive skiftet til fremstilling. Dette fjerner nedetid, når du installerer din online. Trafik omdirigering er problemfri, og ingen anmodninger om udelades, som et resultat af Byt om handlinger. Arbejdsprocessen hele kan automatisk ved at konfigurere [Automatisk Ombyt](#configure-auto-swap-for-your-web-app) når foreløbig Udskift datavalidering ikke er nødvendigt.

- Efter en Udskift har slot med tidligere faseinddelt online nu den forrige fremstilling WebApp. Hvis de ændringer, der er skiftet til fremstilling slot er ikke som forventet, kan du udføre de samme Udskift med det samme for at få "sidste kendte gode webstedet" tilbage.

Hver App Service plan tilstand understøtter et forskelligt antal installation pladser. Hvis du vil finde ud af antallet pladser din online-tilstand understøtter, skal du se [App servicepriser](/pricing/details/app-service/).

- Når din online har flere pladser, kan du ændre tilstanden.

- Skalering er ikke tilgængelig for ikke-produktiv pladser.

- Sammenkædede ressourcestyring understøttes ikke for ikke-produktiv pladser. Du kan undgå denne mulige indvirkning på en fremstilling plads ved at flytte midlertidigt ikke-produktiv stikket til en anden App Service plan tilstand på [Azure Portal](http://go.microsoft.com/fwlink/?LinkId=529715) kun. Bemærk, at den ikke-produktiv slot igen skal dele den samme tilstand med fremstilling slot før du kan udskifte de to pladser.


[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

<a name="Add"></a>
## <a name="add-a-deployment-slot-to-a-web-app"></a>Tilføje en installation plads til en WebApp ##

Web-appen skal køre i tilstanden **Standard** eller **Premium** i rækkefølge, du vil aktivere flere installation pladser.

1. Åbn din online blade i [Azure-portalen](https://portal.azure.com/).
2. Klik på **Indstillinger**, og klik derefter på **installation pladser**. Klik på **Tilføj Slot**på bladet **installation pladser** .

    ![Tilføje en ny installation slot][QGAddNewDeploymentSlot]

    > [AZURE.NOTE]
    > Hvis WebApp ikke allerede er i tilstanden **Standard** eller **Premium** , modtager du en meddelelse om de understøttede tilstande for aktivering af faseinddelt udgivelse. På dette tidspunkt har du mulighed for at markere **opgradere** , og gå til fanen **skala** for din online, før du fortsætter.

2. Navngiv slotten i bladet **Tilføj en slot** , og vælg, om at klone konfiguration af web app fra en anden eksisterende installation slot. Klik på markeringen i afkrydsningsfeltet at fortsætte.

    ![Konfiguration af kilde][ConfigurationSource1]

    Første gang du tilføjer en slot, du kun har to muligheder: Klon konfiguration fra standard slotten fremstilling eller slet ikke.

    Når du har oprettet flere pladser, vil du kunne klone konfiguration fra en slot end i fremstilling:

    ![Konfiguration af kilder][MultipleConfigurationSources]

5. Klik på installation slotten for at åbne en blade for slotten med et udvalg af målepunkter og konfiguration ligesom en anden WebApp i bladet **installation pladser** . **Your-Web-App-Name-Deployment-slot-Name** vises øverst i blade at minde dig, at du får vist installation slotten.

    ![Installation Slot titel][StagingTitle]

5. Klik på app URL-adressen i den slot blade. Bemærk installation slot har sin egen hostname og er også en direkte app. Hvis du vil begrænse offentlig adgang til installation plads, skal du se [App Service Web App-blok webadgang til ikke - produktionsmiljø pladser](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Der er ingen indhold efter installation slot oprettelsen. Du kan installere slotten fra en anden lager gren eller et helt forskellige lager. Du kan også ændre den slot konfiguration. Bruge de Publicer profil eller installation legitimationsoplysninger, der er knyttet til installation slotten for at hente opdateringer.  For eksempel kan du [publicere til denne slot med ciffer](app-service-deploy-local-git.md).

<a name="AboutConfiguration"></a>
## <a name="configuration-for-deployment-slots"></a>Konfiguration for installation pladser ##
Når du klone konfiguration fra en anden installation slot, kan klonede konfigurationen redigeres. Desuden skal nogle konfiguration elementer følge indholdet på tværs af en Udskift (ikke slot bestemt) mens andre konfiguration elementer forbliver i det samme stik efter en Udskift (slot bestemt). Følgende lister viser den konfiguration, der ændres, når du skifter mellem pladser.

**Indstillinger, der er skiftet**:

- Generelle indstillinger – som framework-version, 32/64-bit Web sockets
- App-indstillinger (kan konfigureres til at få en slot)
- Strenge (kan konfigureres til at få en slot)
- Handler tilknytninger
- Indstillinger for overvågning og diagnosticering
- WebJobs indhold

**Indstillinger, der ikke er skiftet**:

- Slutpunkter for publicering
- Brugerdefinerede domænenavne
- SSL-certifikater og bindinger
- Indstillinger for skalering
- WebJobs schedulers

Få adgang til bladet **Programindstillinger** for en bestemt slot for at konfigurere en app indstilling eller forbindelse streng for at overholde en slot (ikke skiftet), og derefter vælge boksen **Slot indstilling** for konfiguration af elementer, der skal overholder slotten. Bemærk, markere konfigurationselementet som slot bestemte har effekten af at etablere elementet som ikke kan udskiftes under drift på tværs af alle pladser for installation, der er knyttet til WebApp.

![Indstillinger for slot][SlotSettings]

<a name="Swap"></a>
## <a name="to-swap-deployment-slots"></a>Du kan udskifte installation pladser ##

>[AZURE.IMPORTANT] Før du skifter mellem en web-app fra en installation slot til fremstilling, skal du kontrollere, at alle ikke-slot specifikke indstillinger er konfigureret nøjagtigt, som du vil have den i Byt om destinationen.

1. Hvis du vil bytte installation pladser, klikke på **Byt om** i kommandolinjen af WebApp eller i kommandolinjen af en installation slot. Sørg for, at Byt om kilde- og Udskift mål er indstillet korrekt. Normalt, være Byt om målet fremstilling slot.  

    ![Udskift knappen][SwapButtonBar]

3. Klik på **OK** for at fuldføre handlingen. Når handlingen afsluttes, er blevet skiftet installation pladser.

## <a name="configure-auto-swap-for-your-web-app"></a>Konfigurere automatisk Ombyt til din online ##

Byt om automatisk strømliner DevOps scenarier, hvor du vil installere løbende din online med nul kolde start- og nul nedetid for kunder, slutningen af WebApp. Når en installation slot er konfigureret til automatisk Byt om på fremstilling, hver gang du push kode opdateringen til stikket, Ombyt App Service automatisk WebApp til fremstilling efter den allerede har varmet op i stikket.

>[AZURE.IMPORTANT] Når du aktiverer automatisk skifte til en slot, skal du kontrollere slot konfigurationen er præcis den konfiguration, der er beregnet til den destinationsadresse slot (som regel fremstilling slotten).

Det er nemt at konfiguration af automatisk skifte til en slot. Følg nedenstående trin:

1. Vælg en ikke-produktiv slot i bladet **Installation pladser** , klikke på **Alle indstillinger** for pågældende slot blade.  

    ![][Autoswap1]

2. Klik på **Indstillinger for et webprogram**. Vælge **til** for **Automatisk Ombyt**, Vælg den ønskede destination slot i **Automatisk Ombyt Slot**, og klik på **Gem** i kommandolinjen. Kontrollér, at konfiguration for slotten er præcis den konfiguration, der er beregnet til den destinationsadresse-slot.

    Fanen **meddelelser** blinker en grøn **succes** , når handlingen er fuldført.

    ![][Autoswap2]

    >[AZURE.NOTE] For at teste automatisk Ombyt til din online, skal vælge du først en ikke-produktiv target slot i at blive fortrolig med funktionen **Automatisk Ombyt Slot** .  

3. Udføre en kode opslagsnål til installation stikket. Byt om automatisk sker efter kort tid, og Opdater afspejles på din target slot URL-adresse.

<a name="Multi-Phase"></a>
## <a name="use-multi-phase-swap-for-your-web-app"></a>Bruge med flere faser Udskift til din online ##

Byt om med flere faser er tilgængelig til at forenkle datavalidering i konteksten af konfiguration elementer, der er udviklet til at få en slot som strenge. I disse tilfælde kan det være praktisk at anvende konfiguration elementer fra destinationen Byt om på Byt om kilden og validere før Udskift faktisk træder i kraft. Når Udskift target konfiguration elementer anvendes på Byt om kilden de tilgængelige handlinger enten at udføre Udskift eller vende tilbage til den oprindelige konfiguration af Byt om kilden som også har effekten af at annullere Udskift. Prøver til de Azure PowerShell-cmdletter tilgængelig for med flere faser Udskift medtages i Azure PowerShell-cmdlet'er til installation pladser sektion.

<a name="Rollback"></a>
## <a name="to-rollback-a-production-app-after-swap"></a>Til rollback en fremstilling app efter Udskift ##

Hvis fejl identificeres i virksomhed efter en slot Udskift, gå pladser tilbage til deres foreløbig Udskift tilstand ved at udskifte de samme to pladser med det samme.

<a name="Warm-up"></a>
## <a name="custom-warm-up-before-swap"></a>Brugerdefineret gennemført før Udskift ##

Visse apps kan kræve brugerdefinerede har gennemført handlinger. Elementet applicationInitialization konfiguration i web.config kan du angive brugerdefinerede initialisering handlinger skal udføres, før der modtages en anmodning. Handlingen Udskift venter på at fuldføre har denne brugerdefinerede gennemført. Her er et eksempel web.config fragment.

    <applicationInitialization>
        <add initializationPage="/" hostName="[web app hostname]" />
        <add initializationPage="/Home/About" hostname="[web app hostname]" />
    </applicationInitialization>

<a name="Delete"></a>
## <a name="to-delete-a-deployment-slot"></a>Slette en installation slot##

Klik på **Slet** i kommandolinjen i bladet for en installation slot.  

![Slette en installation Slot][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
##Azure PowerShell-cmdlet'er til installation pladser

Azure PowerShell er et modul, der indeholder cmdletter for at administrere Azure via Windows PowerShell, herunder support til administration af web app-installation pladser i Azure App-tjeneste.

- For oplysninger om installation og konfiguration af Azure PowerShell, og godkendelse Azure PowerShell med abonnementet Azure, se, [hvordan du installerer og konfigurerer Microsoft Azure PowerShell](../powershell-install-configure.md).  

----------

### <a name="create-web-app"></a>Oprette WebApp

```
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [web app name] -Location [location] -AppServicePlan [app service plan name]
```

----------

### <a name="create-a-deployment-slot-for-a-web-app"></a>Oprette en installation plads til en WebApp

```
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [web app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

----------

### <a name="initiate-multi-phase-swap-and-apply-target-slot-configuration-to-source-slot"></a>Påbegynd Byt om med flere faser og anvende target slot konfiguration til kilde slot

```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

----------

### <a name="revert-the-first-phase-of-multi-phase-swap-and-restore-source-slot-configuration"></a>Gendanne den første fase af Byt om med flere faser og gendanne konfigurationen af datakilden slot

```
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

----------

### <a name="swap-deployment-slots"></a>Udskift installation pladser

```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

----------

### <a name="delete-deployment-slot"></a>Slette installation slot

```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [web app name]/[slot name] -ApiVersion 2015-07-01
```

----------

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>
##Azure kommandolinjen (Azure CLI) kommandoer til installation pladser

Azure CLI indeholder på tværs af platforme kommandoer til at arbejde med Azure, herunder support til administration af Web App-installation pladser.

- Flere oplysninger om installation og konfiguration af Azure CLI, herunder oplysninger om, hvordan du knytter Azure CLI til abonnementet Azure under [installere og konfigurere Azure CLI](../xplat-cli-install.md).

-  Du kan få vist de tilgængelige kommandoer for Azure App-tjenesten i Azure CLI, ringe `azure site -h`.

----------
### <a name="azure-site-list"></a>Azure webstedslister
For at vide om webapps i det aktuelle abonnement skal du ringe til **azure webstedslister**, som i følgende eksempel.

`azure site list webappslotstest`

----------
### <a name="azure-site-create"></a>oprette Azure-websted
Ringe til **azure websted oprette** for at oprette en installation slot, og Angiv navnet på en eksisterende WebApp og navnet på slotten til at oprette, som i følgende eksempel.

`azure site create webappslotstest --slot staging`

For at aktivere versionsstyring for den nye slot skal du bruge indstillingen **– ciffer** som i følgende eksempel.

`azure site create --git webappslotstest --slot staging`

----------
### <a name="azure-site-swap"></a>Byt om Azure-websted
For at gøre den opdaterede installation slot fremstilling app, skal du bruge kommandoen **Ombyt azure websted** til at udføre en handling af Udskift, som i følgende eksempel. Appen fremstilling påvirkes ikke nogen nedetid og vil gennemgå en kolde start.

`azure site swap webappslotstest`

----------
### <a name="azure-site-delete"></a>sletning af Azure websted
Du kan slette en installation slot, der ikke længere er nødvendig ved at bruge kommandoen **azure websted slette** , som i følgende eksempel.

`azure site delete webappslotstest --slot staging`

----------

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

## <a name="next-steps"></a>Næste trin ##
[Azure App Service Web App-blok webadgang til ikke-produktionsmiljø pladser](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Microsoft Azure gratis prøveversion](/pricing/free-trial/)

## <a name="whats-changed"></a>Hvad er ændret
* Finde en vejledning til ændring fra websteder til App-tjenesten: [Azure App Service og dets indvirkning på eksisterende Azure Services](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png
 
