<properties 
   pageTitle="Oprette DNS-zoner og registrere sæt med Azure DNS ved hjælp af .NET SDK | Microsoft Azure" 
   description="Hvordan du opretter DNS-zoner og postsæt i Azure DNS ved hjælp af .NET SDK." 
   services="dns" 
   documentationCenter="na" 
   authors="jtuliani" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/19/2016"
   ms.author="jtuliani"/>


# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Oprette DNS-zoner og ved hjælp af .NET SDK postsæt

Du kan automatisere handlinger for at oprette, slette eller opdatere DNS-zoner, postsæt og poster ved hjælp af DNS SDK med .NET DNS Management-bibliotek. En fuld Visual Studio-projekt findes [her.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Oprette en vigtigste tjenestekonto

Typisk er programmeringsmæssig adgang til Azure ressourcer tildelt via en dedikeret konto i stedet for din egen brugerlegitimationsoplysninger. Disse dedikeret konti kaldes ' vigtigste ' tjenestekonti. Hvis du vil bruge Azure DNS SDK eksempelprojektet, skal du først oprette en vigtigste tjenestekonto og tildele den til de rette tilladelser.

1. Følg [disse instruktioner](../resource-group-authenticate-service-principal.md) for at oprette en vigtigste tjenestekonto (Azure DNS SDK eksempelprojektet antager adgangskode-baseret godkendelse).

2. Oprette en ressourcegruppe ([her er, hvordan](../azure-portal/resource-group-portal.md)).

3. Brug Azure RBAC til at give den primære servicekonto 'DNS-Zone bidragyder' tilladelser til at ressourcegruppen ([her er, hvordan](../active-directory/role-based-access-control-configure.md).)

4. Hvis bruger Azure DNS SDK eksempelprojektet, skal du redigere filen 'program.cs' på følgende måde:
    * Indsætte de korrekte værdier for tenantId, clientId (også kaldet konto-ID), hemmeligt (service vigtigste kontoadgangskode) og subscriptionId, som bruges i trin 1.
    * Angiv navnet for på ressourcen, der er valgt i trin 2.
    * Angiv et navn til DNS-zone efter eget valg.

## <a name="nuget-packages-and-namespace-declarations"></a>NuGet-pakker og erklæringer af navneområder

Hvis du vil bruge Azure DNS .NET SDK, skal du installere pakken **Azure DNS Management bibliotek** NuGet og andre nødvendige Azure pakker.
 
1. I **Visual Studio**, skal du åbne et projekt eller et nyt projekt. 

2. Gå til **Funktioner** **>** **NuGet Package Manager** **>** **administrere NuGet pakker til løsning...**. 

3. Klik på **Gennemse**, aktivere afkrydsningsfeltet **Medtag foreløbig version** , og skriv **Microsoft.Azure.Management.Dns** i søgefeltet.

4. Vælg pakken, og klik på **Installer** for at føje det til dit Visual Studio-projekt.
 
5. Gentag processen ovenfor for også at installere følgende pakker: **Microsoft.Rest.ClientRuntime.Azure.Authentication** og **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Tilføje erklæringer af navneområder

Tilføj følgende navneområdeerklæringer

    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.Dns;
    using Microsoft.Azure.Management.Dns.Models;

## <a name="initialize-the-dns-management-client"></a>Initialiseret DNS management-klient

*DnsManagementClient* indeholder metoder og egenskaber, der er nødvendige for at administrere DNS-zoner og postsæt.  Følgende kode logger på den primære servicekonto og opretter en DnsManagementClient objekt.

    // Build the service credentials and DNS management client
    var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
    var dnsClient = new DnsManagementClient(serviceCreds);
    dnsClient.SubscriptionId = subscriptionId;

## <a name="create-or-update-a-dns-zone"></a>Oprette eller opdatere en DNS-zone

Hvis du vil oprette en DNS-zone, først "Zone" der oprettes et objekt for at indeholde DNS-zone parametre. Fordi DNS zones ikke er knyttet til et bestemt område, er hvor angivet til 'global'. I dette eksempel tilføjes også et [Azure ressourcestyring 'mærke'](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) til zonen.

For at oprette eller opdatere zonen i Azure DNS faktisk skal overføres objektet zone, der indeholder zone parametrene til metoden *DnsManagementClient.Zones.CreateOrUpdateAsyc* .

>[AZURE.NOTE] DnsManagementClient understøtter tre måder at handlingen: synkron ('CreateOrUpdate'), asynkron ('CreateOrUpdateAsync'), eller asynkron med adgang til HTTP-svaret (CreateOrUpdateWithHttpMessagesAsync).  Du kan vælge en af følgende tilstande, afhængigt af dit program behov.

Azure DNS understøtter optimistisk på dokumentsammenfald, kaldet [Etags](dns-getstarted-create-dnszone.md). I dette eksempel, der angiver "*" til 'If-None-Match' sidehoved fortæller Azure DNS for at oprette en DNS-zone, hvis der ikke allerede findes.  Opkaldet mislykkes, hvis et område med det angivne navn findes allerede i den angivne ressourcegruppe.

    // Create zone parameters
    var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"
    
    // Create a Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
    dnsZoneParams.Tags = new Dictionary<string, string>();
    dnsZoneParams.Tags.Add("dept", "finance");
    
    // Create the actual zone.
    // Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
    // Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
    // Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
    var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");

## <a name="create-dns-record-sets-and-records"></a>Oprette DNS-post sæt og poster

DNS-poster administreres som et postsæt. Et postsæt er et sæt poster med samme navn og posttype inden for en zone.  Der er navnet på postsæt i forhold til navnet på zone ikke det fulde DNS-navn.

Hvis du vil oprette eller opdatere en post angivet, en "postsættet" parametre objektet er oprettet og overføres til *DnsManagementClient.RecordSets.CreateOrUpdateAsync*. Som med DNS-zoner, der er tre måder at handlingen: synkron ('CreateOrUpdate'), asynkron ('CreateOrUpdateAsync'), eller asynkron med adgang til HTTP-svaret (CreateOrUpdateWithHttpMessagesAsync).

Som med DNS zones indeholder handlinger på postsæt support til optimistisk.  I dette eksempel, fordi hverken 'If-Match' eller 'If-None-Match' ikke er angivet, oprettes postsættet altid.  Dette opkald overskriver en eksisterende post, der er konfigureret med det samme navn og posttype i denne DNS-zone.

    // Create record set parameters
    var recordSetParams = new RecordSet();
    recordSetParams.TTL = 3600;

    // Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
    recordSetParams.ARecords = new List<ARecord>();
    recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

    // Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
    recordSetParams.Metadata = new Dictionary<string, string>();
    recordSetParams.Metadata.Add("user", "Mary");

    // Create the actual record set in Azure DNS
    // Note: no ETAG checks specified, will overwrite existing record set if one exists
    var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);

## <a name="get-zones-and-record-sets"></a>Få zoner og postsæt

Metoderne *DnsManagementClient.Zones.Get* og *DnsManagementClient.RecordSets.Get* Hent individuelle zoner og postsæt, henholdsvis. Postsæt identificeres ved deres type, navn og gruppen tidszone- og de findes i. Zoner identificeres efter deres navn og de findes i ressourcegruppen.

    var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
    
## <a name="update-an-existing-record-set"></a>Opdatere en eksisterende postsæt

Hvis du vil opdatere en eksisterende DNS postsæt, først hente den postsæt og derefter opdatere postsæt indholdet, derefter sende ændringen.  I dette eksempel skal angive vi 'Etag' fra det hentede post er angivet i parameteren If-Match. Opkaldet mislykkes, hvis handlingen samtidige har ændret postsættet i mellemtiden.

    var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

    // Add a new record to the local object.  Note that records in a record set must be unique/distinct
    recordSet.ARecords.Add(new ARecord("5.6.7.8"));

    // Update the record set in Azure DNS
    // Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
    recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);

## <a name="list-zones-and-record-sets"></a>Listen zoner og postsæt

Vist zoner ved at bruge metoderne *DnsManagementClient.Zones.List...* , som understøtter listen over enten alle zoner i en given ressourcegruppe eller alle zoner i en given Azure abonnement (på tværs af grupper.) Vist postsæt ved at bruge *DnsManagementClient.RecordSets.List...* metoder, som understøtter enten listen over alle poster sæt i en bestemt zone eller kun disse poster sæt af en bestemt type.

Bemærk når listen over zoner og post sæt, der er resultatet kan være paginated.  I følgende eksempel vises, hvordan du kan navigere gennem siderne i resultaterne. (En kunstigt lille sidestørrelse '2' bruges til at gennemtvinge sideopdeling; i praksis denne parameter bør udelades og sidestørrelse standard bruges.)

    // Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
    // In practice, to improve performance you would use a large page size or just use the system default
    int recordSets = 0;
    var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
    recordSets += page.Count();

    while (page.NextPageLink != null)
    {
        page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
        recordSets += page.Count();
    }

## <a name="next-steps"></a>Næste trin

Hent [Azure DNS .NET SDK eksempel-projekt](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), som omfatter yderligere eksempler på, hvordan du bruger Azure DNS .NET SDK, herunder eksempler på andre DNS-posttyper.
