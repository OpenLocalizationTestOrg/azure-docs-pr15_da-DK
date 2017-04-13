<properties 
    pageTitle="Godkende med Mobile aftale REST API'er"
    description="Beskriver, hvordan du vil godkende med Azure Mobile aftale REST API'er" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="10/05/2016"
    ms.author="wesmc;ricksal"/>

# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Godkende med Mobile aftale REST API'er

## <a name="overview"></a>Oversigt

Dette dokument beskrives, hvordan du får en gyldig AAD Oauth token for at godkende med Mobile aftale REST API'er. 

Det antages, at du har et gyldigt Azure-abonnement, og du har oprettet en aftale Mobile-app, ved hjælp af en af vores [Udvikler selvstudier](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Godkendelse

Et Microsoft Azure Active Directory baseret OAuth token bruges til godkendelse. 

I rækkefølge til anmodning om godkendelse en API, skal en authorization-header føjes til hver anmodning, der er i formularen følgende:

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

>[AZURE.NOTE] Azure Active Directory-tokens udløbe i 1 time.

Der er flere måder at få et token. Da API'erne kaldes generelt fra en skybaseret tjeneste, vil du bruge en API-nøgle. En API-nøgle i Azure terminologi kaldes en tjeneste vigtigste adgangskode. Den følgende procedure beskriver en metode til at konfigurere den manuelt.

### <a name="one-time-setup-using-script"></a>Enkeltstående installation (ved hjælp af script)

Du skal følge vejledningen nedenfor for at udføre konfigurationen ved hjælp af en PowerShell-script, tager det mindste tidspunkt for installationen, men bruges mest tilladte standardindstillingerne sæt. Du kan eventuelt også følge vejledningen i [manuel konfiguration](mobile-engagement-api-authentication-manual.md) for at gøre det direkte fra Azure-portalen og gøre bedre konfiguration. 

1. Få den seneste version af Azure PowerShell fra [her](http://aka.ms/webpi-azps). Du kan se dette [link](../powershell-install-configure.md)kan finde flere oplysninger om instruktionerne for overførsel.  

2. Når Azure PowerShell er installeret, skal du bruge følgende kommandoer til at sikre dig, at du har det **Azure modul** , der er installeret:

    en. Kontrollér, at Azure PowerShell-modulet findes på listen over tilgængelige moduler. 
    
        Get-Module –ListAvailable 

    ![Tilgængelige Azure moduler][1]
        
    b. Hvis du ikke kan finde Azure PowerShell-modulet i ovenstående liste skal du køre følgende:
        
        Import-Module Azure 
        
3. Logon til Azure ressourcestyring fra PowerShell ved at køre følgende kommando og give dit brugernavn og din adgangskode for kontoen Azure: 
        
        Login-AzureRmAccount

4. Hvis du har flere abonnementer, og derefter skal du køre følgende:

    en. Få en liste over alle dine abonnementer, og Kopiér SubscriptionId af det abonnement, du vil bruge. Kontrollér, at dette abonnement er den samme, som har den Mobile aftale-App, du vil interagere med ved hjælp af API'erne. 

        Get-AzureRmSubscription

    b. Kør følgende kommando for at give SubscriptionId at konfigurere abonnementet skal bruges.

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>

5. Kopiér teksten for [Ny AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) scriptet til din lokale computer og gemme den som en PowerShell-cmdlet (fx `APIAuth.ps1`) og udføre den `.\APIAuth.ps1`. 
    
6. Scriptet beder dig om at angive et input til **principalName**. Angiv et passende navn her, du vil bruge til at oprette din Active Directory-program (f.eks. APIAuth). 

7. Når scriptet er fuldført, vises følgende fire værdier, du skal godkende fra et program med AD så sørg for at kopiere dem.. 
        
    **TenantId**, **SubscriptionId**, **ApplicationId**og **hemmeligt**.

    Du skal bruge TenantId som `{TENANT_ID}`, ApplicationId som `{CLIENT_ID}` og hemmeligt som `{CLIENT_SECRET}`.

    > [AZURE.NOTE] Din standard sikkerhedspolitik kan blokere du i at køre en PowerShell-scripts. Hvis det er tilfældet, skal konfigurere du midlertidigt din udførelse af politik for at tillade udførelse af script ved hjælp af følgende kommando:

        > Set-ExecutionPolicy RemoteSigned

8. Her er, hvordan sæt af PS cmdletter vil se ud. 

    ![][3]

9. Markér i portalen Azure Management, der blev oprettet et nyt AD-program med det navn, du har angivet til scriptet kaldet **principalName** under **Vis programmer firmaet ejer**.

    ![][4]

#### <a name="steps-to-get-a-valid-token"></a>Trin for at få et gyldigt token

1. Kalde API med følgende parametre, og Sørg for at erstatte LEJEREN\_-ID, KLIENTEN\_-ID og KLIENTEN\_HEMMELIGT:

    - **Anmode om URL-adressen** som *https://login.microsoftonline.com/ {LEJER\_ID} / oauth2/token*
    - **Http-indholdstype sidehoved** som *programmet/x-www-form-urlencoded*
    - **HTTP anmode om brødteksten** efter *give\_type = klienten\_legitimationsoplysninger og client_id = {klient\_ID} & client_secret = {klient\_HEMMELIGT} & resource=https%3A%2F%2Fmanagement.core.windows.net%2F*

    Følgende er en anmodning om en eksempel:

        POST /{TENANT_ID}/oauth2/token HTTP/1.1
        Host: login.microsoftonline.com
        Content-Type: application/x-www-form-urlencoded
        grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
        urce=https%3A%2F%2Fmanagement.core.windows.net%2F

    Her er et eksempel svar:

        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Content-Length: 1234
    
        {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
        5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}

    I dette eksempel følger URL-adressen kodning af parametre INDLÆG `resource` værdi er faktisk `https://management.core.windows.net/`. Sørg for at også URL-adressen kode `{CLIENT_SECRET}` som det kan indeholde specialtegn.

    > [AZURE.NOTE] Du kan bruge en HTTP client værktøj som [Fiddler](http://www.telerik.com/fiddler) eller [Chrome Postman udvidelse](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) til test, 

2. Omfatte nu godkendelse anmodning om sidehovedet i hvert API-opkald:

        Authorization: Bearer {ACCESS_TOKEN}

    Hvis du får en returnerede 401 statuskode, Markér brødteksten svar det kan fortælle dig Tokenet er udløbet. I så fald få et nyt id.

##<a name="using-the-apis"></a>Ved hjælp af API'erne

Nu hvor du har et gyldigt token, er du klar til at foretage API-opkald.

1. I hver API-anmodning skal du overfører et gyldigt, resterende token, du har hentet i det forrige afsnit.

2. Du skal Tilslut nogle parametre i indkaldelsen URI som identificerer dit program. Anmodningen URI ser ud på følgende

        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/

    For at få parametrene, klik på programnavn, og klik på Dashboard, og du får vist en side som følger med alle 3 parametrene.

    - **1**`{subscription-id}`
    - **2**`{app-collection}`
    - **3**`{app-resource-name}`
    - **4** dine ressourcegruppe navn, skal være **MobileEngagement** , medmindre du har oprettet en ny. 

    ![Mobile aftale API URI parametre][2]

>[AZURE.NOTE] <br/>
>1. Ignorer API rod adresse, som er til den forrige API'er.<br/>
>2. Hvis du har oprettet den app, ved hjælp af Azure klassisk portalen skal du bruge programmet ressourcenavnet der er forskellig fra programnavnet sig selv. Hvis du oprettede appen i portalen Azure skal du bruge Appnavnet selve (der er ingen graduering mellem programmet ressourcenavn og App-navn til apps, der er oprettet i den nye portal).  

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/ad-app-creation.png



