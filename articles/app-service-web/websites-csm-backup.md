<properties
    pageTitle="Bruge RESTEN til at sikkerhedskopiere og gendanne App Service apps"
    description="Lær, hvordan du bruger RESTful API-kald til at sikkerhedskopiere og gendanne en app i Azure App Service"
    services="app-service"
    documentationCenter=""
    authors="NKing92"
    manager="wpickett"
    editor="" />

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="nicking"/>
# <a name="use-rest-to-back-up-and-restore-app-service-apps"></a>Bruge RESTEN til at sikkerhedskopiere og gendanne App Service apps

> [AZURE.SELECTOR]
- [PowerShell](../app-service/app-service-powershell-backup.md)
- [REST-API](websites-csm-backup.md)

[App Service apps](https://azure.microsoft.com/services/app-service/web/) kan sikkerhedskopieres som BLOB i Azure-lager. Sikkerhedskopien kan også indeholde den app databaser. Hvis appen nogensinde ved et uheld bliver slettet, eller hvis APP'en skal gendannes en tidligere version, kan du gendanne den fra en tidligere sikkerhedskopi. Sikkerhedskopier kan udføres på en hvilken som helst tidspunkt efter behov, eller sikkerhedskopier kan planlægges med passende intervaller.

I denne artikel forklarer, hvordan du sikkerhedskopiere og gendanne en app RESTful API-anmodninger. Hvis du vil have til at oprette og administrere app sikkerhedskopier grafisk via Azure-portalen, se [sikkerhedskopiere en WebApp i Azure App Service](web-sites-backup.md)

<a name="gettingstarted"></a>
## <a name="getting-started"></a>Kom godt i gang
Hvis du vil sende RESTEN anmodninger, skal du kender din app- **navn**, **ressourcegruppe**og **abonnement-id**. Disse oplysninger kan findes ved at klikke på din app i bladet **App Service** [Azure-portalen](https://portal.azure.com). Eksemplerne i denne artikel vil vi konfigurere websted **backuprestoreapiexamples.azurewebsites.net**. Det er gemt i standard-Web-WestUS ressourcegruppe og kører på et abonnement med ID 00001111-2222-3333-4444-555566667777.

![Eksempel websted oplysninger][SampleWebsiteInformation]

<a name="backup-restore-rest-api"></a>
## <a name="backup-and-restore-rest-api"></a>Sikkerhedskopiere og gendanne REST-API
Vi nu dækker flere eksempler på, hvordan du bruger REST-API til at sikkerhedskopiere og gendanne en app. Hvert eksempel indeholder en URL-adresse og HTTP-anmodningsteksten. Eksempel URL-adressen indeholder pladsholdere ombrudt i klammeparenteser, som {abonnement-id}. Erstat pladsholderne med de tilsvarende oplysninger for din app. Her er en forklaring af hver enkelt pladsholder, der vises i eksempel URL-adresser til reference.

* abonnement-id-ID'ET for det Azure abonnement, der indeholder app
* ressource-gruppe-navn – navnet på den ressourcegruppe, der indeholder app
* navn – navnet på app'en
* sikkerhedskopi-id-ID'ET for app-sikkerhedskopi

Du kan finde den komplette dokumentation af API'EN, herunder række valgfrie parametre, der skal indgå i HTTP-anmodningen, [Azure ressource Explorer](https://resources.azure.com/).

<a name="backup-on-demand"></a>
## <a name="backup-an-app-on-demand"></a>Sikkerhedskopiere en app efter behov
Hvis du vil sikkerhedskopiere en app med det samme, skal du sende en indkaldelse til **INDLÆG** til **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backup/**.

Her er, hvad URL-adressen ligner ved hjælp af vores eksempel websted. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/Sites/backuprestoreapiexamples/backup/**

Angiv et JSON objekt i brødteksten i din anmodning om at angive, hvilken lagerplads konto du bruger til at gemme sikkerhedskopien. Objektet JSON skal have en egenskab med navnet **storageAccountUrl**, der indeholder en [SAS URL-adresse](../storage/storage-dotnet-shared-access-signature-part-1.md) , når du tildeler skriveadgang til objektbeholderen Azure-lager, der indeholder den ekstra blob. Hvis du vil sikkerhedskopiere dine databaser, skal du også angive en liste, der indeholder navne, datatyper og strenge af de databaser, der skal sikkerhedskopieres.

```
{
    "properties":
    {
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        “databases”: [
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”,
                "connectionString": "<your database connection string>"
            }
        ]
    }
}
```

En sikkerhedskopi af appen begynder med det samme, når der modtages anmodningen. Sikkerhedskopieringen kan tage lang tid at fuldføre. HTTP-svaret indeholder et ID, der kan bruges i en anden anmodning for at se status for sikkerhedskopien. Her er et eksempel på brødteksten i vores anmodning om sikkerhedskopiering HTTP svaret.

```
{
    "id": "/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples",
    "name": " backuprestoreapiexamples ",
    "type": "Microsoft.Web/sites",
    "location": "WestUS",
    "properties":    {
        "id": 1,
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        "blobName": “backup_201509291825.zip”,
        "name": "backup_201509291825",
        "status": 4,
        "sizeInBytes": 0,
        "created": "2015-09-29T18:25:26.3992707Z",
        "log": null,
        "databases": [
            {
                "databaseType": "SqlAzure",
                "name": "MyDatabase1",
                "connectionString": "<your database connection string>"
            }
        ],
        "scheduled": false,
        "correlationId": "ea730f4d-dd06-4f7f-a090-9aa09k662h36",
    }
}
```

>[AZURE.NOTE] Fejlmeddelelser kan findes i egenskaben log af HTTP-svaret.

<a name="schedule-automatic-backups"></a>
## <a name="schedule-automatic-backups"></a>Planlægge automatisk sikkerhedskopiering
Ud over sikkerhedskopiering af en app efter behov, kan du også planlægge en sikkerhedskopi, der skal ske automatisk.

### <a name="set-up-a-new-automatic-backup-schedule"></a>Konfigurere en ny tidsplan for automatisk sikkerhedskopiering
For at konfigurere en plan for sikkerhedskopiering skal du sende en indkaldelse til **PLACERE** til **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup**.

Her er, hvordan URL-adressen ser ud til vores eksempel websted. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/Sites/backuprestoreapiexamples/config/backup**

Anmodningsteksten skal have et JSON-objekt, der angiver konfigurationen af sikkerhedskopien. Her er et eksempel med alle de nødvendige parametre.

```
{
    "location": "WestUS",
    "properties": // Represents an app restore request
    {
        "backupSchedule": { // Required for automatically scheduled backups
            "frequencyInterval": "7",
            "frequencyUnit": "Day",
            "keepAtLeastOneBackup": "True",
            "retentionPeriodInDays": "10",
        },
        "enabled": "True", // Must be set to true to enable automatic backups
        "name": “mysitebackup”,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

I dette eksempel konfigurerer app til automatisk blive sikkerhedskopieret hver syv dage. Den parametre **frequencyInterval** og **frequencyUnit** sammen bestemmer, hvor ofte sikkerhedskopier af sker. Gyldige værdier for **frequencyUnit** er **time** og **dag**. For eksempel for at sikkerhedskopiere en app hver 12 timer, du angive frequencyInterval til 12 og frequencyUnit til time.

Gamle sikkerhedskopier fjernes automatisk fra kontoen lagerplads. Du kan styre, hvordan gamle sikkerhedskopier af kan være ved at angive parameteren **retentionPeriodInDays** . Hvis du vil altid have mindst én sikkerhedskopi, der er gemt, uanset hvor gammel, indstilles **keepAtLeastOneBackup** til sand.

### <a name="get-the-automatic-backup-schedule"></a>Få tidsplanen for automatisk sikkerhedskopiering
For at få en app sikkerhedskopi af en konfiguration skal du sende en indkaldelse til **INDLÆG** til URL-adressen **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup/list**.

Fanen KILDE i vores eksempel websted er **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup/list**.

<a name="get-backup-status"></a>
## <a name="get-the-status-of-a-backup"></a>Hent status for en sikkerhedskopi
Afhængigt af hvor stor appen er, tage en sikkerhedskopi et stykke tid at fuldføre. Sikkerhedskopier kan også mislykkes sluttid, eller delvist lykkes. For at se status for alle en app's sikkerhedskopier, skal du sende en indkaldelse til **få** til URL-adressen **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups**.

Sende en GET-anmodning til URL-adressen **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**for at få vist status for en bestemt sikkerhedskopi.

Her er, hvordan URL-adressen ser ud til vores eksempel websted. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/Sites/backuprestoreapiexamples/backups/1**

Svar brødteksten indeholder et JSON-objekt, der svarer til dette eksempel.

```
{
    "properties":  {
        "id": 1,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl",
        "blobName": "backup_201509291734.zip",
        "name": "backup_201509291734",
        "status": 2,
        "sizeInBytes": 151973,
        "created": "2015-09-29T17:34:57.2091605",
        "scheduled": false,
        "lastRestoreTimeStamp": null,
        "finishedTimeStamp": "2015-09-29T17:35:11.3293602",
        "correlationId": "2379fc13-418a-4b9c-920d-d06e73c5028d",
        "websiteSizeInBytes": 209920
    }
}
```

Status for en sikkerhedskopi er en optalt type. Her er hver mulig tilstand.

* 0 – InProgress: at sikkerhedskopieringen er startet, men endnu ikke er fuldført.
* 1 – mislykkedes: Sikkerhedskopien, mislykkedes.
* 2 – gennemført: Sikkerhedskopieringen er gennemført.
* 3 – har fået timeout: Sikkerhedskopien, blev ikke fuldført tidspunkt og er annulleret.
* 4 – oprettet: Den anmodning om sikkerhedskopiering er i kø men er ikke startet.
* 5 – ignoreret: Sikkerhedskopien ikke fortsætte på grund af en tidsplan for udløser for mange sikkerhedskopier.
* 6 – PartiallySucceeded: Sikkerhedskopien fuldført, men nogle filer blev ikke sikkerhedskopieret, fordi de ikke kan læses. Dette sker normalt, fordi en eksklusiv lås var placeret på filerne.
* 7 – DeleteInProgress: Sikkerhedskopien har anmodet om at blive slettet, men endnu ikke er blevet slettet.
* 8 – DeleteFailed: Sikkerhedskopien blev ikke slettet. Dette kan ske, fordi SAS URL-adressen, der blev brugt til at oprette sikkerhedskopien er udløbet.
* 9 – slettet: Sikkerhedskopien blev slettet.

<a name="restore-app"></a>
## <a name="restore-an-app-from-a-backup"></a>Gendanne en app fra en sikkerhedskopi
Hvis din app er blevet slettet, eller hvis du vil nulstille din app til en tidligere version, kan du gendanne app fra en sikkerhedskopi. Hvis du vil anvende en gendannelse, skal du sende en indkaldelse til **INDLÆG** til URL-adressen **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/restore**.

Her er, hvordan URL-adressen ser ud til vores eksempel websted. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/Sites/backuprestoreapiexamples/backups/1/Restore**

Sende et JSON-objekt, der indeholder egenskaberne for gendannelsen i anmodningsteksten. Her er et eksempel, der indeholder alle de obligatoriske egenskaber:

```
{
    "location": "WestUS",
    "properties":
    {
        "blobName": "backup_201509280431.zip",
        "databases": [ // Not required unless you’re restoring databases
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”
        }],
        "overwrite": "true",
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl" // SAS URL to storage container containing your website backup
    }
}
```

### <a name="restore-to-a-new-app"></a>Gendanne til en ny app
Undertiden vil du kan oprette en ny app, når du gendanner en sikkerhedskopi, i stedet for at overskrive en eksisterende app. For at gøre dette, ændre URL-anmodningen til at pege på den nye app, du vil oprette og ændre egenskaben **overskrive** i JSON til **Falsk**.

<a name="delete-app-backup"></a>
## <a name="delete-an-app-backup"></a>Slette en app sikkerhedskopi
Hvis du vil slette en sikkerhedskopi, kan du sende en **slette** anmodning til URL-adressen **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**.

Her er, hvordan URL-adressen ser ud til vores eksempel websted. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/Sites/backuprestoreapiexamples/backups/1**

<a name="manage-sas-url"></a>
## <a name="manage-a-backups-sas-url"></a>Administrere en sikkerhedskopi SAS URL-adresse
Azure App Service forsøger at slette sikkerhedskopien fra Azure-lager, med SAS URL-adressen, der blev angivet, da sikkerhedskopien blev oprettet. Hvis denne SAS URL-adresse er ikke længere gyldig, kan ikke slettes sikkerhedskopien gennem REST-API. Du kan dog opdatere den SAS URL-adresse, der er knyttet til en sikkerhedskopi ved at sende en anmodning om **INDLÆG** til URL-adressen **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/list**.

Her er, hvordan URL-adressen ser ud til vores eksempel websted. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/Sites/backuprestoreapiexamples/backups/1/List**

Sende et JSON-objekt, der indeholder den nye SAS URL-adresse i anmodningsteksten. Her er et eksempel.

```
{
    "properties":
    {
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

>[AZURE.NOTE] Af sikkerhedsmæssige årsager returneres den SAS URL-adresse, der er knyttet til en sikkerhedskopi ikke, når du sender en GET-anmodning for sikkerhedskopiering af en bestemt. Hvis du vil have vist den SAS URL-adresse, der er knyttet til en sikkerhedskopi, kan du sende en anmodning om INDLÆG til samme URL-adressen ovenfor. Medtage et tomt JSON-objekt i anmodningsteksten. Svar fra serveren indeholder alle oplysninger om denne sikkerhedskopi, herunder SAS URL-adressen.

<!-- IMAGES -->
[SampleWebsiteInformation]: ./media/websites-csm-backup/01siteconfig.png
