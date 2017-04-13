<properties
 pageTitle="Administrere udløb af Azure-lager blob indhold i Azure CDN | Microsoft Azure"
 description="Få mere at vide om muligheder for at styre time to live til BLOB i Azure CDN cachelagring."
 services="cdn"
 documentationCenter=""
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="multiple"
 ms.topic="article"
 ms.date="09/15/2016"
 ms.author="casoper"/>


# <a name="manage-expiration-of-azure-storage-blob-content-in-azure-cdn"></a>Administrere udløb af Azure-lager blob indhold i Azure CDN

> [AZURE.SELECTOR]
- [Azure Web Apps/Cloud Services, ASP.NET eller IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Azure blob-lagringstjeneste](cdn-manage-expiration-of-blob-content.md)

[Blob-tjenesten](../storage/storage-introduction.md#blob-storage) i [Azure-lager](../storage/storage-introduction.md) er et af flere Azure-baserede baggrunden integreret med Azure CDN.  En offentligt tilgængelig blob indhold kan cachelagres i Azure CDN, indtil dens time to live (TTL) går.  TTL bestemmes af [ *Cache-Control* sidehoved](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) i HTTP-svar fra Azure-lager.

>[AZURE.TIP] Du kan vælge at angive ingen TTL på en blob.  I dette tilfælde anvendes Azure CDN automatisk standard TTL af syv dage.
>
>Du kan finde flere oplysninger om, hvordan Azure CDN fungerer til hastighed for adgang til BLOB og andre filer, [Azure CDN oversigt](./cdn-overview.md).
>
>Du kan finde flere oplysninger om Azure-lager blob-tjenesten, [Blob Service begreber](https://msdn.microsoft.com/library/dd179376.aspx). 

Dette selvstudium viser flere måder, du kan angive TTL på en blob i Azure-lager.  

## <a name="azure-powershell"></a>Azure PowerShell

[Azure PowerShell](../powershell-install-configure.md) er en af de hurtigste, mest effektive måder til at administrere dine Azure tjenester.  Brug den `Get-AzureStorageBlob` til at få en reference til blob, Angiv derefter den `.ICloudBlob.Properties.CacheControl` egenskab. 

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

>[AZURE.TIP] Du kan også bruge PowerShell til at [administrere din CDN profiler og slutpunkter](./cdn-manage-powershell.md).

## <a name="azure-storage-client-library-for-net"></a>Azure-lager klientbibliotek til .NET

For at angive en blob TTL ved hjælp af .NET skal du bruge [Azure lagerplads klientbibliotek til .NET](../storage/storage-dotnet-how-to-use-blobs.md) til at angive egenskaben [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) .

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
        
        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        
        // Create a reference to the container
        CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob blob = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "public, max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

>[AZURE.TIP] Der findes mange flere .NET kodeeksempler i [Azure Blob Storage prøver til .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).

## <a name="other-methods"></a>Andre metoder

- [Azure kommandolinjen](../xplat-cli-install.md)

    Når du overfører blob, angive den *cacheControl* egenskab ved hjælp af den `-p` skifte.  I dette eksempel angiver TTL til en time (3600 sekunder).

    ```text
    azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
    ```

- [Azure-lager Services REST-API](https://msdn.microsoft.com/library/azure/dd179355.aspx)

    Eksplicit er angivet egenskaben *x-ms-blob-cache-objekter* på en anmodning om [Placere Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx), [Placere blokeringsliste](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx)eller [Angiv Blob egenskaber](https://msdn.microsoft.com/library/azure/ee691966.aspx) .

- Værktøjer til administration af tredjeparters lagerplads

    Nogle værktøjer til administration af tredjeparters Azure-lager giver dig mulighed at indstille egenskaben *CacheControl* på BLOB. 

## <a name="testing-the-cache-control-header"></a>Test *Cache-Control* sidehovedet

Du kan nemt kontrollere TTL på din BLOB.  Brug af din browser [udviklerværktøjer](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)test, at din blob er herunder svarheaderen *Cache-styring* .  Du kan også bruge et værktøj som **wget**, [Postman](https://www.getpostman.com/)eller [Fiddler](http://www.telerik.com/fiddler) undersøge svar overskrifterne.

## <a name="next-steps"></a>Næste trin

- [Læs mere om *Cache-Control* sidehovedet](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
- [Lær, hvordan du administrerer udløb af skytjeneste indhold i Azure CDN](./cdn-manage-expiration-of-cloud-service-content.md)

