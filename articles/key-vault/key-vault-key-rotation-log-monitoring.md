<properties
    pageTitle="Sådan konfigureres nøgle samling med start til slut vigtige rotation og revision | Microsoft Azure"
    description="Brug denne vejledning til at hjælpe dig med at få konfiguration med vigtige rotation og overvågning vigtige samling logge"
    services="key-vault"
    documentationCenter=""
    authors="swgriffith"
    manager="mbaldwin"
    tags=""/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="jodehavi;stgriffi"/>
#<a name="how-to-setup-key-vault-with-end-to-end-key-rotation-and-auditing"></a>Sådan konfigureres nøgle samling med start til slut vigtige rotation og overvågning

##<a name="introduction"></a>Introduktion

Når du har oprettet din Azure-tasten samling, vil du kunne starte udnytte denne samling af legitimationsoplysninger for at gemme dine nøgler og hemmeligheder. Dine programmer ikke længere har brug for at bevare dine nøgler eller hemmeligheder, men anmoder om dem fra den vigtige samling af legitimationsoplysninger efter behov. Dette giver dig mulighed at opdatere nøgler og hemmeligheder uden at påvirke den funktionsmåde i dit program, der åbnes en lang række muligheder rundt om din nøgle og funktionalitet, hemmeligt administration.

I denne artikel vejledes gennem et eksempel på udnytte Azure-tasten samling for at gemme en hemmeligt i dette tilfælde en Azure-lager-kontonøgle, som bruges af et program. Det kan også demonstrerer implementeringen af en planlagt rotation i den pågældende lagerplads kontonøgle. Til sidst skal fører det gennem en demonstration af, hvordan du kan overvåge overvågningslogge vigtige samling og hæve beskeder, når uventede anmodninger.

> \[AZURE. Bemærk\] dette selvstudium er ikke beregnet til at forklare detaljerede oplysninger om det første sæt op af din Azure-tasten samling af legitimationsoplysninger. Disse oplysninger, skal du se [Introduktion til Azure nøgle samling](key-vault-get-started.md). Eller på tværs af platforme kommandolinjen vejledning i [selvstudiet tilsvarende](key-vault-manage-with-cli.md).

##<a name="setting-up-keyvault"></a>Konfiguration af KeyVault

For at aktivere et program til at hente en hemmeligt fra Azure-tasten samling, skal du først oprette hemmeligheden og overføre den til din samling af legitimationsoplysninger. Dette kan gøres nemt via PowerShell som vist nedenfor.

Starte en session med Azure PowerShell og logge på din Azure-konto med følgende kommando:

```powershell
Login-AzureRmAccount
```

Angiv dit brugernavn til Azure-konto og din adgangskode i pop op-browservinduet. Azure PowerShell får alle de abonnementer, der er knyttet til denne konto, og som standard, skal du bruger den første.

Hvis du har flere abonnementer, du muligvis angive en bestemt én, der blev brugt til at oprette din Azure-tasten samling af legitimationsoplysninger. Skriv følgende for at se abonnementer til din konto:

```powershell
Get-AzureRmSubscription
```

Hvis du vil angive det abonnement, der er knyttet til din nøgle samling, du vil logføring, Skriv:

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID> 
```

Som i denne artikel viser lagring af en lagerplads kontonøgle som et hemmeligt, skal du få lagerplads konto nøglen.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Når du har hentet din hemmeligt, i dette tilfælde din lagerplads kontonøgle, du skal konvertere, til en sikker streng og derefter oprette en hemmeligt med den pågældende værdi i din nøgle samling.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
Næste skal du hente URI til hemmeligheden du lige har oprettet. Der bruges på et senere tidspunkt, når du ringer til den tast samling af legitimationsoplysninger til at hente dine hemmeligt. Kør følgende PowerShell-kommando, og noter værdien 'Id', som er den hemmelige URI.

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

##<a name="setting-up-application"></a>Konfiguration af programmet

Nu hvor du har en gemt hemmeligt vil du hente hemmeligt og bruge det fra kode. Der er et par trin, der kræves for at opnå dette, den første og vigtigste som registrering af dit program med Azure Active Directory, og derefter fortæller Azure-tasten samling din programoplysninger, så den kan give tilladelse anmodninger fra dit program.

> \[AZURE. Bemærk\] dit program skal oprettes på samme Azure Active Directory-lejer som din nøgle samling. 

Først åbne fanen programmer af Azure Active Directory

![Åbne programmer i Azure AD](./media/keyvault-keyrotation/AzureAD_Header.png)

Vælg "Tilføj" for at tilføje et nyt program til din Azure AD

![Vælg Tilføj program](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

Forlade programtype som 'Web Application og/eller WEB API' og navngive dit program.

![Navnet på programmet på computeren](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

Giv dit program, en 'URL Sign-On' og en "App-ID-URI'. Disse kan være noget, du vil for denne demonstration og kan ændres senere, hvis det er nødvendigt.

![Angive nødvendige URI'er](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

Når programmet er føjet til Azure AD, vil du blive bragt i programmets side. Klik på fanen 'Konfigurer' fra det pågældende tidspunkt og derefter finde og kopiere værdien klient-ID. Noter klient-ID for efterfølgende trin.

Derefter skal du oprette en nøgle for dit program kunne interagere med dine Azure AD. Du kan oprette under afsnittet 'Taster' i fanen 'Konfiguration'. Noter på den nyligt oprettede nøgle fra dit Azure AD-program til brug på et senere tidspunkt.

![Azure AD App taster](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Inden du opretter et opkald fra dit program til tasten samling skal du fortælle nøgle samling om dit program og dens ' tilladelser. Følgende kommando tager samling navn og klient-ID fra din Azure AD-app og giver 'Get' adgang til dine vigtige samling af legitimationsoplysninger for programmet.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

På dette tidspunkt er du klar til at begynde at opbygge dit program opkald. I dit program skal du først installere de NuGet-pakker, der kræves for at interagere med Azure-tasten samling og Azure Active Directory. Skriv følgende kommandoer fra konsollen Visual Studio Package Manager. Bemærk, at på skriver i denne artikel den aktuelle version af Active Directory-pakke 3.10.305231913, så du kan eventuelt til at bekræfte den nyeste version og opdatere i overensstemmelse hermed.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

I dit programkode, skal du oprette en klasse for at holde metoden for din Active Directory-godkendelse. I dette eksempel, at klassen kaldes 'af ' websted. Derefter skal du tilføje følgende ved hjælp af.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Dernæst skal du tilføje følgende metode for at hente JWT token fra Azure AD. Kodet strengværdier til vedligeholde du overveje at flytte det hårde i konfigurationen af internettet eller program.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Til sidst, kan du tilføje den nødvendige kode for at ringe nøgle samling af legitimationsoplysninger og hente din hemmeligt værdi. Først skal du tilføje følgende ved hjælp af sætning.

```csharp
using Microsoft.Azure.KeyVault;
```

Dernæst skal du tilføje metode opkald til at kalde nøgle samling af legitimationsoplysninger og hente din hemmeligt. I denne metode kan du angive hemmeligt URI, du har gemt i et tidligere trin. Bemærk brugen af metoden GetToken fra klassen af websted, der er oprettet ovenfor.
    
```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Når du kører dit program, du skulle nu være godkendelse til Azure Active Directory og derefter hente din hemmeligt værdi fra din Azure-tasten samling.

##<a name="key-rotation-using-azure-automation"></a>Tasten Rotation ved hjælp af Azure automatisering

Der er forskellige indstillinger for implementering af en rotation strategi for værdier, du gemmer som Azure-tasten samling hemmeligheder. Hemmeligheder kan roteres som en del af en manuel proces, de kan være roteret programmeringsmæssigt ved at udnytte API-kald, eller de kan være roteret ved hjælp af et automatiseringsscript. I forbindelse med denne artikel vil vi udnytte Azure PowerShell kombineres med Azure automatisering ændre hurtigtast Azure-lager-konto, og derefter opdaterer vi en vigtige samling hemmeligt med den nye tast. 

For at tillade Azure automatisering til at angive hemmeligt værdier i din nøgle samling skal du få klient-ID for forbindelsen med navnet 'AzureRunAsConnection', som blev oprettet, når du har oprettet din Azure automatisering forekomst. Du kan åbne dette ID ved at vælge 'Aktiver' fra din Azure automatisering forekomst. Du kan vælge 'Forbindelser' og derefter vælge 'AzureRunAsConnection' service princippet derfra. Du vil lægge mærke til det 'program-ID'. 

![Azure automatisering klient-ID](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Når du er stadig i vinduet Aktiver vil du også vælge 'Moduler'. Fra moduler skal du vælge 'Galleriet' og Søg efter og 'Import' opdaterede versioner af hver af følgende moduler.

    Azure
    Azure.Storage   
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage
    
> \[AZURE. Bemærk\] på skriver i denne artikel kun ovenstående noteret moduler, der skal bruges til at blive opdateret for scriptet delt under. Hvis du synes, at automatiske tingene går ned, skal du bekræfte, at du har alle nødvendige moduler og deres afhængigheder, der er importeret.

Når du har hentet program-ID for Azure automatisering forbindelsen, skal du se din Azure-tasten samling, dette program har adgang til at opdatere hemmeligheder i din samling af legitimationsoplysninger. Dette kan gøres med følgende PowerShell-kommando.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Derefter du vælger 'Runbooks' ressourcen under din Azure automatisering forekomst, og vælg 'Tilføj en Runbook'. Vælg Hurtig oprettelse. Navngive din runbook, og vælg 'PowerShell' runbook filtype. Du kan også tilføje en beskrivelse. Til sidst skal du klikke på 'Opret'.

![Oprette Runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Du vil indsætte følgende PowerShell-script i ruden editor til din nye runbook.

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

#Optionally you may set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -StorageAccountName $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

Du kan vælge 'Test ruden' fra ruden editor til at teste dit script. Når scriptet kører uden fejl kan du vælge indstillingen 'Publicer', og derefter kan du anvende en tidsplan for runbook tilbage på ruden runbook konfiguration.

##<a name="key-vault-auditing-pipeline"></a>Tasten samling overvågning pipeline

Når du konfigurerer en Azure-tasten samling kan du aktivere overvågning for at indsamle logge på adgangsanmodninger, der er foretaget i den nøgle samling af legitimationsoplysninger. Disse logfiler gemmes i en dedikeret Azure-lager-konto og kan derefter være trukket ud, overvåges og analysere. Nedenfor beskrives gennem et scenarie, der bruger Azure funktioner overvågningsloggen Azure logik Apps og nøgle samling for at oprette en pipeline for at sende en mail, når hemmeligheder fra samling af legitimationsoplysninger hentes af en app, der stemmer overens med app-id af WebApp.

Du skal først aktivere logføring på din nøgle samling. Dette kan gøres via følgende PowerShell-kommandoer (detaljerede oplysninger kan ses [her](key-vault-logging.md)):

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>' 
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Når dette er aktiveret, derefter starter overvågningslogge indsamling af hensyn udpegede lagerplads. Disse logfiler skal indeholde begivenheder om, hvordan og hvornår din nøgle Vaults er åbnet, og af hvem. 

> \[AZURE. Bemærk\] du kan få adgang til dine oplysninger om logføring højst, 10 minutter efter tasten samling handling. I de fleste tilfælde, vil det være hurtigere end dette.

Næste trin er at [oprette en Azure Service Bus kø](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Dette er hvor vigtige samling overvågningslogge publiceres. Én gang på køen, logik App kan vælge dem og reagere på dem. Oprette en tjeneste Bus er relativt simple og nedenfor finder du de overordnede trin:

1. Oprette et Service Bus navneområde (Hvis du allerede har en, du vil bruge i dette, skal du gå til trin 2).
2. Gå til Service Bus i portalen, og vælg det navneområde, du vil oprette køen på.
3. Vælg ny, og vælg Service Bus -> kø, og Angiv de nødvendige oplysninger.
4. Tage forbindelsesoplysningerne Service Bus ved at vælge navneområdet og klikke på _Oplysninger om forbindelsen_. Du skal bruge disse oplysninger til den næste del.

Derefter skal vil du [oprette en Azure-funktion](../azure-functions/functions-create-first-azure-function.md) for at afstemning loggene nøgle samling i lagerplads konto og vælge nye begivenheder. Dette er en funktion, der skal udløses en tidsplan.

Oprette en Azure-funktionen (Vælg Ny -> funktionen App på portalen). Du kan bruge en eksisterende hosting-plan eller oprette en ny under oprettelse. Du kan også vælge dynamisk vært. Du kan finde flere oplysninger om funktionen vært indstillinger [her](../azure-functions/functions-scale.md).

Når funktionen Azure oprettes, gå til den, og vælg en timer, funktionen og C\# klik derefter på **Opret** fra startskærmen.

![Azure funktioner Start Blade](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Erstat run.csx koden i fanen _udvikling_ med følgende:

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging; 
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log) 
{ 
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //required to order by time as they may not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob) 
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```
> \[AZURE. Bemærk\] Sørg for at erstatte variablerne i ovenstående til at pege til kontoen lagerplads, hvor loggene nøgle samling skrives, den Service Bus, du oprettede tidligere og bestemte stien til loggene vigtige samling lagerplads kode.

Funktionen henter den seneste logfil fra kontoen lagerplads hvor loggene nøgle samling skrives, fanger de seneste hændelser fra denne fil og flytter dem til en tjeneste Bus kø. Da en enkelt fil kan have flere begivenheder, f.eks. oprette over en fuld time derefter vi en _sync.txt_ -fil, som funktionen ser også på at finde ud af tidsstempel for den sidste hændelse, der blev valgte op. Dette sikrer, at vi ikke push samme hændelse flere gange. Denne _sync.txt_ fil indeholder blot et tidsstempel for den sidste fundet hændelse. Logfiler, når indlæses, skal sorteres baseret på tidsstemplet at sikre, at de er sorteret korrekt.

For denne funktion referere vi til et par ekstra biblioteker, der ikke er tilgængelige af feltet i Azure funktioner. Hvis du vil medtage disse, vi har brug for Azure funktioner til at trække dem ved hjælp af nuget. Vælg indstillingen _Vis filer_ 

![Få vist filer](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

og tilføje en ny fil kaldet _project.json_ med følgende indhold:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```
Ved at _gemme_ udløser dette Azure funktioner for at hente den nødvendige binære kode. 

Skifte til fanen **integrere** og giver parameteren timer et beskrivende navn til brug i funktionen. I ovenstående kode forventes timer ringes _myTimer_. Angiv en [CRON udtryk](../app-service-web/web-sites-create-web-jobs.md#CreateScheduledCRON) på følgende måde: 0 \* \* \* \* \* for de timer, som medfører, at funktionen til at køre én gang i minuttet. 

Fanen samme **integrere** Tilføj input, vil være af typen _Azure Blob-lager_. Dette vil pege på filen _sync.txt_ , der indeholder tidsstemplet for hændelsen sidste set på af funktionen. Dette kan gøres tilgængelige i funktionen ved parameternavnet. I ovenstående kode forventer Azure Blob-lager input parameternavn skal være _inputBlob_. Vælg kontoen lagerplads, hvor filen _sync.txt_ er placeret (det kan være den samme eller en anden lagerplads konto) og i stifeltet indeholder den sti, hvor filen bor, i formatet af {container-name}/path/to/sync.txt.

Tilføje en output, vil være af typen _Azure Blob-lager_ output. Dette vil også pege på den _sync.txt_ -fil, du lige har defineret i input. Dette bruges med funktionen til at skrive tidsstempel for den sidste begivenhed, der blev vist på skærmen. Ovenstående kode forventer, at denne paramter skal ringes op _outputBlob_.

På dette tidspunkt er funktionen klar. Sørg for at skifte tilbage til fanen **udvikling** , og _Gem_ koden. Markér outputvinduet til kompileringsfejl og rette dem, der er i overensstemmelse hermed. Hvis den kompilerer, derefter koden bør nu køre og hvert minut kontrolleres loggene nøgle samling, og Skub alle nye begivenheder på definerede Service Bus køen. Du bør se oplysninger om logføring skrive til den log vinduet formular, hver gang funktionen udløses.

###<a name="azure-logic-app"></a>Azure logik App

Næste skal vi oprette en Azure logik-App, opfanger de hændelser, der skubber funktionen til Service Bus køen, analysere indholdet og sende en mail baseret på en betingelse, der matchet.

[Opret en logik App](../app-service-logic/app-service-logic-create-a-logic-app.md) ved at gå til ny -> logik App. 

Når Appen logik er oprettet, skal du gå til den og vælge _Rediger_. Vælg _Service Bus kø_ managed api i redigeringsprogrammet logik App, og Angiv dine Service Bus legitimationsoplysninger for at forbinde den til køen.

![Azure logik App Service Bus](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Vælg Næste for at _tilføje en betingelse_. Skift til _Avanceret editor_ i betingelsen, og Angiv følgende oplysninger, erstatter APP_ID med de faktiske APP_ID for din online:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Dette udtryk grundlæggende returnerer **Falsk** Hvis egenskaben **sikkerhedskontrol** indgående begivenheden (som er brødteksten i meddelelsen Service Bus) ikke er **sikkerhedskontrol** af appen. 

Opret nu en handling under indstillingen _Hvis Nej, ikke gør noget …_ .

![Azure logik App Vælg handling](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Vælge _Office 365 - sende mail_til handlingen. Udfyld felterne for at oprette en mail skal sendes, når betingelsen defineret returnerer falsk. Hvis du ikke har Office 365 kan du se alternativer til at opnå den samme.

På dette tidspunkt har du en start til slut rørledning, der én gang i minuttet, søger efter nye nøgle samling overvågningslogge. Alle nye logfiler, der er angivet, vil det push dem til en tjeneste Bus kø. Appen logik udløses, når en ny meddelelse lander i køen, og hvis sikkerhedskontrol inden for hændelsen ikke overens med app-id i programmet opkald og sende en mail. 
