<properties
    pageTitle="Hvordan du installerer og konfigurerer Azure PowerShell"
    description="Lær, hvordan du installerer og konfigurerer Azure PowerShell."
    editor="tysonn"
    manager="dongill"
    documentationCenter=""
    services=""
    authors="coreyp-at-msft"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="powershell"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="coreyp"/>

# <a name="how-to-install-and-configure-azure-powershell"></a>Hvordan du installerer og konfigurerer Azure PowerShell

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">Azure CLI</a></div>

##<a name="what-is-azure-powershell"></a>Hvad er Azure PowerShell?
Azure PowerShell er et sæt af moduler, som giver cmdletter for at administrere Azure med Windows PowerShell. Du kan bruge-cmdletter til at oprette, teste, installere og administrere løsninger og tjenester, der leveres via Azure-platformen. I de fleste tilfælde, kan den bruges til de samme opgaver som Azure-portalen, såsom at oprette og konfigurere skytjenester, virtuelle maskiner, virtuelle netværk og webapps.

## <a name="how-versioning-works"></a>Sådan fungerer versionsstyring

Azure PowerShell bruger semantisk versionsstyring, hvilket betyder, at hvis version A > version B, og klik derefter version A er den mest opdaterede API'er. Desuden betyder det, at ændringer i en overordnet version middelværdi en seneste ændrer i en eller flere cmdletter.  Så, for eksempel er version 1.7.0 en programrettelse til at løse en seneste ændring i 1.x versioner af Azure PowerShell.

Du kan finde flere oplysninger om semantisk versionsstyring fremgangsmåder i Azure PowerShell se specifikationen semantisk versionsstyring på: http://semver.org
 
For at få de seneste API'er, skal du bruge versionen 2.x. Men hvis du har scripts skrevet mod version 1.x, og du ikke vil opfanger de seneste ændringer i version 2.x, der er beskrevet i 2.x [Produktbemærkninger](https://github.com/Azure/azure-powershell/blob/dev/documentation/release-notes/migration-guide.2.0.0.md), og derefter skal du installere 1.7.0.

En uoverensstemmelse kan ske, hvis den seneste version af modulet profil er installeret, og en tidligere version af et modul, der afhænger af den efterfølgende er indlæst. Den nemmeste måde at løse dette problem er at installere fra seneste .msi-filen. .Msi-filen rydder automatisk op i ældre versioner af modulerne.
 
###<a name="installing-module-versions-side-by-side"></a>Installere modul versioner side om side

Version 2.1.0 (og version 1.2.6 for AzureStack) er de første modulversioner, der er udviklet til at være installeret og bruges side om side. Da Azure PowerShell bruger binære moduler, skal du åbne et nyt vindue i PowerShell og bruge **Import-modul** til at importere en bestemt version af AzureRM cmdletter:

    Import-Module AzureRM -RequiredVersion 2.1.0**

Versioner før 2.1.0 (foruden 1.2.6) ikke virker samt side om side med andre Azure PowerShell-modulversioner. Under indlæsning af en tidligere version af Azure PowerShell moduler ved hjælp af en kommando som det ovenfor, inkompatible versioner af modulet **AzureRM.Profile** vil blive indlæst, hvilket resulterer i cmdlet'erne beder dig om at logge på hver gang du udfører en-cmdlet, selv efter at du har logget ind.

Den nemmeste måde at løse dette er at installere den nyeste Azure PowerShell fra WebPI feed eller .msi-dette fjerner tidligere versioner af de moduler, der er installeret i galleriet. 

Bemærk, at både Azure og AzureRM moduler har afhængigheder fælles, så hvis du bruger både moduler, når du opdaterer en, skal du opdatere begge. Tidligere versioner af modulet Azure har det samme problem med side om side modul indlæsning af, at tidligere versioner af modulet, AzureRM har.

<a id="Install"></a>
## <a name="step-1-install"></a>Trin 1: installere

Følgende er de to metoder, som du kan installere Azure PowerShell. Du kan installere det enten fra galleriet PowerShell eller WebPI.

###<a name="installing-azure-powershell-from-the-powershell-gallery"></a>Installere Azure PowerShell fra galleriet PowerShell

Den foretrukne måde er at bruge PowerShell galleri. Du skal bruge modulet PowerShellGet til brug af galleriet PowerShell. Denne indstilling er tilgængelig her: [PowerShellGallery.com](https://www.powershellgallery.com/)

Installere Azure PowerShell 1.3.0 eller større fra galleriet PowerShell ved hjælp af en øgede Windows PowerShell eller PowerShell ISE Integrated Scripting-miljø () prompt ved hjælp af følgende kommandoer:

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

####<a name="more-about-these-commands"></a>Flere oplysninger om disse kommandoer

- **Installer modul AzureRM** installerer en rollup-modul til Azure ressourcestyring cmdletter. Afhænger af modulet AzureRM 
- en bestemt version område for hvert Azure ressourcestyring modul. Området inkluderede version sikrer, at ingen seneste modul ændringer kan skal medtages, når du installerer AzureRM moduler med det samme overordnet version. Når du har installeret modulet AzureRM, downloadet og installeret fra galleriet PowerShell en hvilken som helst Azure ressourcestyring modul, der tidligere ikke er installeret. Du kan finde flere oplysninger om den semantisk versionsstyring, der bruges af Azure PowerShell moduler, [semver.org](http://semver.org). 
- **Installer modul Azure** installerer modulet Azure. Dette modul er modulet Service fra Azure PowerShell 0.9.x. Det har ingen vigtige ændringer og kunne erstatte hinanden for den tidligere version af Azure modulet.

###<a name="installing-azure-powershell-from-webpi"></a>Installere Azure PowerShell fra WebPI

Installere Azure PowerShell 1,0 og større fra WebPI er den samme som den var for 0.9.x. Hente [Azure PowerShell](http://aka.ms/webpi-azps) og starte installationen. Hvis du har Azure PowerShell 0.9.x installeret, version 0.9.x fjernes som en del af opgraderingen. Hvis du har installeret Azure PowerShell moduler PowerShell galleriet, fjerner installationsprogrammet automatisk moduler før installationen for at sikre en ensartet Azure PowerShell-miljø.

> [AZURE.NOTE] Hvis du tidligere har installeret Azure moduler fra galleriet PowerShell, kan installationsprogrammet automatisk fjerne dem. Dette forhindrer forvirring om hvilke modulversioner du har installeret og hvor de er placeret. Galleri med PowerShell moduler installeres normalt i **%ProgramFiles%\WindowsPowerShell\Modules**. I modsætning WebPI installationsprogrammet installerer Azure moduler i * *% programmer (x86) %\Microsoft SDKs\Azure\PowerShell\**. Hvis der opstår en fejl under installationen, kan du manuelt fjerne Azure* mapper i din **%ProgramFiles%\WindowsPowerShell\Modules** mappe, og prøv at installere igen.

Når installationen er fuldført, din ```$env:PSModulePath``` indstilling skal indeholde de mapper, der indeholder Azure PowerShell-cmdletterne.

> [AZURE.NOTE] Der er et kendt problem med PowerShell **$env: PSModulePath** , der kan opstå, når du installerer fra WebPI. Hvis din computer kræver en genstart på grund af opdateringer eller andre installationer, kan det medføre opdateringer **$env: PSModulePath** ikke medtage den sti, hvor Azure PowerShell er installeret. Hvis dette forekommer, kan du vist en 'cmdlet ikke genkendt' meddelelse, når du forsøger at bruge Azure PowerShell-cmdletter efter installation eller opgradering. Hvis dette forekommer, skal ved at genstarte computeren løse problemet.

Hvis du modtager en meddelelse som følgende, når du forsøger at indlæse eller udføre cmdletter:

```
    PS C:\> Get-AzureRmResource
    Get-AzureRmResource : The term 'Get-AzureRmResource' is not recognized as the name of a cmdlet, function,
    script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is
    correct and try again.
    At line:1 char:1
    + Get-AzureRmResource
    + ~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ObjectNotFound: (get-azurermresourcefork:String) [], CommandNotFoundException
        + FullyQualifiedErrorId : CommandNotFoundException
```

Dette kan løses ved at genstarte computeren eller importere cmdlet'erne fra C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\ som følgende (hvor XXXX er versionen af PowerShell, der er installeret:

```
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\azure.psd1"
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\expressroute\expressroute.psd1"
```

## <a name="step-2-start"></a>Trin 2: Start
Du kan køre cmdlet'erne fra den standard Windows PowerShell console eller fra PowerShell ISE Integrated Scripting-miljø ().
Den metode, du kan bruge til at åbne enten konsol afhænger af versionen af Windows, du kører:

- På en computer med mindst Windows 8 eller Windows Server 2012, kan du bruge den indbyggede søgefunktionen. Begynd at skrive power fra skærmbilledet **Start** . Dette returnerer en relateret liste over apps, der indeholder Windows PowerShell. For at åbne konsollen skal du klikke på enten app. (Højreklik på ikonet for at Fastgør appen til **startskærmbilledet i** ).

- På en computer med en version tidligere end Windows 8 eller Windows Server 2012 kan bruge **menuen Start**. Fra menuen **Start** , klik på **Alle programmer**, klik på **Tilbehør**, klik på mappen **Windows PowerShell** og klik derefter på **Windows PowerShell**.

Du kan også køre **Windows PowerShell ISE** for at bruge menupunkter og tastaturgenveje til at udføre mange af de samme opgaver, du vil udføre i Windows PowerShell-konsollen. Hvis du vil bruge ISE, i Windows PowerShell-konsollen Cmd.exe, eller i feltet **Kør** , Skriv, **powershell_ise.exe**.

###<a name="commands-to-help-you-get-started"></a>Kommandoer til at hjælpe dig med at komme i gang

    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 
    
    # To log in to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster log in experience
    # Login-AzureRmAccount -TenantId xxxx

    # To view all subscriptions for your account
    Get-AzureRmSubscription

    # To select a default subscription for your current session
    Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

    # View your current Azure PowerShell session context
    # This session state is only applicable to the current session and will not affect other sessions
    Get-AzureRmContext

    # To select the default storage context for your current session
    Set-AzureRmCurrentStorageAccount –ResourceGroupName “your resource group” –StorageAccountName “your storage account name”

    # View your current Azure PowerShell session context
    # Note: the CurrentStorageAccount is now set in your session context
    Get-AzureRmContext

    # To list all of the blobs in all of your containers in all of your accounts
    Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob


## <a name="step-3-connect"></a>Trin 3: oprette forbindelse
Cmdlet'erne skal dit abonnement, så de kan administrere dine tjenester. Du kan købe et Azure-abonnement, hvis du ikke allerede har en. Finde en vejledning, se, [hvordan du køber Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Skriv **Logon-AzureRmAccount**

2. Skriv den mailadresse og adgangskode er knyttet til din konto. Azure godkender og gemmer legitimationsoplysninger for oplysningerne, og derefter lukker vinduet.

– ELLER –

Log på din arbejds- eller skolekonto:

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [AZURE.NOTE] Hvis du har mere end én lejer, der er knyttet til din virksomhedskonto, du Angiv parameteren TenantId:

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [AZURE.NOTE] Denne ikke-interaktiv log i metode fungerer kun med en arbejds- eller skolekonto. Arbejds-eller skolekonto er en bruger, der administreres af dit arbejde eller skole, og defineret i forekomsten af Azure Active Directory til dit arbejde eller skole. Hvis du ikke aktuelt har arbejds-eller skolekonto og bruger en Microsoft-konto til at logge på dit Azure-abonnement, kan du nemt oprette én at benytte følgende fremgangsmåde.

> 1. Log på [Azure klassisk portal](https://manage.windowsazure.com), og klik på **Active Directory**.

> 2. Hvis der findes ingen directory, Vælg **oprette mappen** , og Angiv de nødvendige oplysninger.

> 3. Vælg mappen, og tilføje en ny bruger. Den nye bruger kan logge på med en arbejds- eller skolekonto. Under oprettelse af brugeren, skal du leveres med både en e-mail-adresse til brugeren og en midlertidig adgangskode. Gem disse oplysninger, som den bruges i trin 5 nedenfor.

> 4. Vælg **Indstillinger** fra Azure klassisk-portalen, og vælg derefter **Administratorer**. Vælg **Tilføj**, og tilføje den nye bruger som administrator af en samtidig. Dette giver mulighed for kontoen arbejds- eller skolekonto til at administrere din Azure abonnement.

> 5. Til sidst skal du logger af portalen Azure klassisk og derefter logge på igen, ved hjælp af arbejdet, der eller skolekonto. Hvis dette er den første gang at logge på med denne konto, bliver du bedt om at ændre adgangskoden.

> Se [tilmelde sig Microsoft Azure som en organisation](./active-directory/sign-up-organization.md)kan finde flere oplysninger om tilmelding til Microsoft Azure med en arbejds- eller skolekonto.

> Du kan finde flere oplysninger om håndtering af godkendelse og abonnement i Azure, skal du se [Administrer konti, abonnementer og Administrationsroller](http://go.microsoft.com/fwlink/?LinkId=324796).

### <a name="view-account-and-subscription-details"></a>Se detaljer om konto og abonnement

Du kan have flere konti og abonnementer der er tilgængelige til brug af Azure PowerShell. Du kan tilføje flere konti ved at køre **Tilføj AzureRmAccount** mere end én gang.

Skriv **Get-AzureAccount**for at få vist de tilgængelige Azure konti.

Skriv **Get-AzureRmSubscription**for at få vist dine Azure abonnementer.

##<a id="Help"></a>Få hjælp##

Disse ressourcer hjælpe dig med bestemte cmdletter:


-   Fra konsollen, kan du bruge den indbyggede Hjælp-system. Cmdletten **Get-Help** giver adgang til dette system. 

- For at få hjælp fra community'et, kan du prøve disse populære fora:

 - [Azure-forum på MSDN]( http://go.microsoft.com/fwlink/p/?LinkId=320212)
 - [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

##<a name="learn-more"></a>Lær mere


Se følgende ressourcer for at lære mere om at bruge cmdlet'erne:

Du kan finde grundlæggende oplysninger om brug af Windows PowerShell, [Bruge Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=321939).

Du kan finde oplysninger om cmdlet'er, [Azure Cmdlet Reference](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

For eksempelscripts og instruktioner, der hjælper dig med at lære at bruge scripting til at administrere Azure, skal du se [Script Center](http://go.microsoft.com/fwlink/p/?LinkId=321940).

