<properties
    pageTitle="Angive og hente egenskaber og metadataene for objekter i Azure-lager | Microsoft Azure"
    description="Gemme brugerdefinerede metadata på objekter i Azure-lager, og angive og hente egenskaber for system."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="set-and-retrieve-properties-and-metadata"></a>Angive og hente egenskaber og Metadata #

## <a name="overview"></a>Oversigt

Objekter i Egenskaber for support system Azure-lager og brugerdefinerede metadata, ud over de data, de indeholder:

*   **Egenskaber for system.** Egenskaber for system findes på hver lagerplads ressource. Nogle af dem kan læses eller indstille, mens andre er skrivebeskyttet. Under dækker svarer nogle egenskaber for system til bestemte standard HTTP-headere. Biblioteket Azure lagerplads klienten opdaterer disse for dig.  

*   **Brugerdefinerede metadata.** Brugerdefinerede metadata er metadata, du angiver på en given ressource i form af et navn / værdi-par. Du kan bruge metadata til at gemme flere værdier med en lagerplads ressource. disse værdier til dine egne formål kun og påvirker ikke hvordan skal reagere, ressourcen.  

Henter egenskaben og metadata værdier for en ressource lagerplads er en proces. Før du kan læse disse værdier, skal du hente dem eksplicit ved at ringe til metoden **FetchAttributes** .

> [AZURE.IMPORTANT] Egenskaben og metadata værdier for en ressource lagerplads udfyldes ikke, medmindre du ringe til en af **FetchAttributes** metoderne. 

## <a name="setting-and-retrieving-properties"></a>Indstilling og hentning af egenskaber

For at hente egenskabsværdier skal ringe til metoden **FetchAttributes** på din blob eller objektbeholder til at udfylde egenskaberne, og Læs derefter værdierne.

For at indstille egenskaber for et objekt skal du angive egenskabsværdien, og derefter kalde metoden **SetProperties** .

Følgende kodeeksempel opretter en objektbeholder og skriver nogle af dens egenskabsværdier til et console vindue:

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    
    //Create the service client object for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it does not already exist.
    container.CreateIfNotExists();

    // Fetch container properties and write out their values.
    container.FetchAttributes();
    Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
    Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
    Console.WriteLine("ETag: {0}", container.Properties.ETag);
    Console.WriteLine();

## <a name="setting-and-retrieving-metadata"></a>Angive og hentning af Metadata

Du kan angive metadata som en eller flere navn / værdi-par af en blob eller objektbeholder ressource. Føje navn / værdi-par til samlingen **Metadata** på ressourcen for at angive metadata, og derefter kalde metoden **SetMetadata** for at gemme værdierne i tjenesten.

> [AZURE.NOTE] Navnet på din metadata skal overholde navngivningskonventioner for C#-id'er.
 
Følgende kodeeksempel angiver metadata på objektbeholderen. Én værdi er angivet ved hjælp af den samling **Tilføj** metode. Anden værdien er angivet ved hjælp af implicitte nøgleværdi syntaks. Begge er gyldige.

    public static void AddContainerMetadata(CloudBlobContainer container)
    {
        //Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        //Set the container's metadata.
        container.SetMetadata();
    }

Kalde metoden **FetchAttributes** på din blob eller objektbeholder til at udfylde samlingen **Metadata** for at hente metadata, og derefter læse værdierne, som vist i eksemplet nedenfor.

    public static void ListContainerMetadata(CloudBlobContainer container)
    {
        //Fetch container attributes in order to populate the container's properties and metadata.
        container.FetchAttributes();

        //Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }

## <a name="see-also"></a>Se også  

- [Azure-lager klientbibliotek til .NET Reference](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [Azure-lager klient-bibliotek til .NET-pakke](https://www.nuget.org/packages/WindowsAzure.Storage/) 
