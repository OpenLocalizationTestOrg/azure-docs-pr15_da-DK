<properties 
    pageTitle="Flytte ressourcer til ny ressourcegruppe | Microsoft Azure" 
    description="Brug Azure ressourcestyring til at flytte ressourcer til en ny ressourcegruppe eller et andet abonnement." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/21/2016" 
    ms.author="tomfitz"/>

# <a name="move-resources-to-new-resource-group-or-subscription"></a>Flytte ressourcer til ny ressourcegruppe eller et andet abonnement

Dette emne beskrives, hvordan flytter ressourcer til et nyt abonnement eller en ny ressourcegruppe i samme-abonnement. Du kan bruge portalen, PowerShell, Azure CLI eller REST-API til at flytte ressource. Flyt handlinger i dette emne er tilgængelige for dig uden en hvilken som helst hjælp fra Azure support.

Du flytter typisk ressourcer, når du vurderer, at:

- Til fakturering formål skal en ressource direkte i et andet abonnement.
- En ressource deler ikke længere de samme livscyklus som i den tidligere var grupperet med ressourcer. Du vil flytte den til en ny ressourcegruppe, så du kan administrere ressourcen uafhængigt af andre ressourcer.

Når du flytter ressourcer, er både kildegruppen og målgruppen låst under handlingen. Skrive og slette handlinger er blokeret på grupperne, indtil Flyt er fuldført.

Du kan ikke ændre placeringen af ressourcen. Flytte en ressource kun flytter den til en ny ressourcegruppe. Den nye ressourcegruppe kan have en anden placering, men, ændrer ikke placeringen af ressourcen.

> [AZURE.NOTE] I denne artikel beskrives, hvordan du flytter ressourcer inden for en eksisterende Azure-konto tilbuddet. Hvis du rent faktisk vil ændre kontoen Azure tilbyder (såsom opgradering fra pay-as-you-go at betale forud), mens du fortsætter med at arbejde med dine eksisterende ressourcer, skal du se [skifte abonnementet Azure til et andet tilbud](billing-how-to-switch-azure-offer.md). 

## <a name="checklist-before-moving-resources"></a>Tjekliste før flytning af ressourcer

Der er nogle vigtige trin til at udføre før du flytter en ressource. Ved at bekræfte disse betingelser, kan du undgå fejl.

1. Tjenesten skal aktivere muligheden for at flytte ressourcer. Se listen nedenfor for oplysninger om hvilke [tjenester aktivere glidende ressourcer](#services-that-enable-move).
1. Kilde- og destinationstabellerne abonnementer der skal findes i samme [Active Directory-lejer](./active-directory/active-directory-howto-tenant.md). Ring til support for at flytte til en lejer.
2. Abonnementets destination skal være registreret for ressource-udbyder ved en ressource, der flyttes. Hvis ikke, du modtager en fejlmeddelelse om, **abonnement ikke er registreret for en ressourcetype**. Du kan støde på dette problem, når du flytter en ressource til et nyt abonnement, men som abonnement har aldrig brugt med pågældende ressourcetype. For at lære at kontrollere registreringsstatus for og registrere ressource udbydere, skal du se [ressource udbydere og typer](../resource-manager-supported-services.md#resource-providers-and-types).
4. Hvis du vil flytte App Service app, kan du har gennemgået [App tjenestebegrænsningerne](#app-service-limitations).
4. Hvis du vil flytte ressourcer, der er knyttet til gendannelsestjenester, har du gennemgået [gendannelse Services begrænsninger](#recovery-services-limitations)
5. Hvis du vil flytte ressourcer, der er installeret gennem klassisk model, har du gennemgået [Klassisk installation begrænsninger](#classic-deployment-limitations).

## <a name="when-to-call-support"></a>Hvornår skal du ringe til support

Du kan flytte de fleste ressourcer via selvbetjening handlingerne vises i dette emne. Bruge handlingerne selvbetjening til:

- Flytte ressourcestyring ressourcer.
- Flytte klassisk Ressourcer efter [Klassisk installation begrænsninger](#classic-deployment-limitations). 

Kontakt support, når du skal:

- Flytte dine ressourcer til en ny Azure-konto (og Active Directory-lejer).
- Flytte klassisk ressourcer, men har problemer med begrænsninger.

## <a name="services-that-enable-move"></a>Tjenester, der aktiverer Flyt

Nu er de tjenester, der aktiverer flytte både en ny ressourcegruppe og abonnement:

- Administration af API
- App Service apps (online) - se [App tjenestebegrænsningerne](#app-service-limitations)
- Automatisering
- Batchen
- LEVERANDØR(ER)
- Cloud Services – se [Klassisk installation begrænsninger](#classic-deployment-limitations)
- Data Factory
- DNS
- DocumentDB
- HDInsight klynger
- IoT Hubs
- Vigtige samling 
- Media Services
- Mobile aftale
- Meddelelse om Hubs
- Funktionsdygtige indsigt
- Redis Cache
- Opgavestyring
- Søgning
- Service Bus
- Lagerplads
- Lagerplads (klassisk) - se [Klassisk installation begrænsninger](#classic-deployment-limitations)
- SQL-Database server - databasen og serveren ligge i samme ressourcegruppe. Når du flytter en SQL server, flyttes alle dens databaser også.
- Virtuelle maskiner - dog bruges ikke support flytte til et nyt abonnement, når dens certifikater gemmes i en samling af legitimationsoplysninger nøgle
- Virtuelle maskiner (klassisk) - se [Klassisk installation begrænsninger](#classic-deployment-limitations)
- Virtuelt netværk

## <a name="services-that-do-not-enable-move"></a>Tjenester, der ikke aktiverer Flyt

De tjenester, der i øjeblikket ikke aktiverer flytning af en ressource er:

- Application Gateway
- Programmet indsigt
- Express rute
- Samling gendannelse Services - også ikke flytte Beregn-, Netværks- og lagerplads ressourcer, der er knyttet til samling af legitimationsoplysninger gendannelsestjenester, skal du se [gendannelse Services begrænsninger](#recovery-services-limitations).
- Virtuelle maskiner med certifikat, der er gemt i nøgle samling
- Virtuelle maskiner skala sæt
- Virtuelt netværk (klassisk) - se [Klassisk installation begrænsninger](#classic-deployment-limitations)
- VPN-Gateway

## <a name="app-service-limitations"></a>App tjenestebegrænsningerne

Når du arbejder med App Service apps, kan du ikke flytte en App Service plan. Hvis du vil flytte App Service apps, er følgende indstillinger:

- Flytte App serviceaftale og alle andre App Service ressourcer i ressourcegruppen til en ny ressourcegruppe, der ikke allerede har App Service ressourcer. Dette krav betyder, skal du flytte selv App Service ressourcer, der ikke er knyttet til App serviceaftale. 
- Flytte apps til en anden ressourcegruppe, men bevare alle App Service planer i den oprindelige ressourcegruppe.

Hvis din oprindelige ressourcegruppe indeholder også en programmet indsigt ressource, kan du ikke flytte ressourcen, fordi programmet indsigt i øjeblikket ikke aktiverer flytningen. Hvis du medtager programmet indsigt ressourcen, når du flytter App Service apps, mislykkes hele flytningen. Programmet indsigt og App-serviceaftale behøver dog ikke skal ligge i samme ressourcegruppe som app for appen til at fungere korrekt.

For eksempel, hvis din ressourcegruppe indeholder:

- **Web-a** som er knyttet til **plan en** og **app-indsigt-a**
- **web-b** , der er knyttet til **plan-b** og **app-indsigt-b**

Er følgende indstillinger:

- Flytte **web-a**, **plan-a**, **web-b**og **plan-b**
- Flytte **web-a-** og **web-b**
- Flytte **web-a**
- Flytte **web-b**

Alle andre kombinationer involverer enten flytte en ressourcetype, der ikke kan flytte (programmet indsigt) eller forlader bag en ressourcetype, der ikke kan blive efterladt, når du flytter en App-serviceaftale (en hvilken som helst type App Service ressource).

Hvis din online er placeret i en anden ressourcegruppe, end dens App-serviceaftale, men du vil flytte begge dele til en ny ressourcegruppe, skal du udføre Flyt i to trin. Eksempel:

- **Web-a** er placeret i **web-gruppe**
- **planlægge en** er placeret i **plan-gruppe**
- Du vil **web-a** og **planlægge en** skal placeres i **kombineret gruppe**

For at opnå denne Flyt, skal du udføre to separate flytning i følgende rækkefølge:

1. Flytte den **web-a** til **plan-gruppe**
2. Flytte **web-a** og **planlægge et** til **kombineret gruppe**.

Du kan flytte et App-tjenestecertifikat til en ny ressourcegruppe eller et andet abonnement uden problemer. Hvis din online omfatter et SSL-certifikat, du har købt eksternt og overføres til app, skal du slette certifikatet før du flytter WebApp. For eksempel kan du udføre følgende trin:

1. Slette det overførte certifikat fra WebApp
2. Flytte WebApp
3. Upload certifikatet til WebApp

## <a name="recovery-services-limitations"></a>Gendannelse Services begrænsninger

Flyt ikke er aktiveret til lagring, netværk, eller beregne ressourcer, der bruges til at konfigurere nedbrud med Azure gendannelse af websteder. 

Antag f.eks, at du har konfigureret replikering af dine lokale maskiner til en lagerplads konto (Storage1) og vil beskyttet maskinen at komme frem efter failover til Azure som et virtuelt (VM1), der er knyttet til et virtuelt netværk (Network1). Du kan ikke flytte en af disse Azure ressourcer - Storage1, VM1 og Network1 - på tværs af grupper i det samme abonnement eller på tværs af abonnementer.

## <a name="classic-deployment-limitations"></a>Klassisk installation begrænsninger

Indstillinger til at flytte ressourcer, der er installeret gennem klassisk modellen være forskellige afhængigt af om du vil flytte ressourcerne, inden for et abonnement eller til et nyt abonnement. 

### <a name="same-subscription"></a>Samme-abonnement

Når du flytter ressourcer fra én ressourcegruppe til en anden ressourcegruppe inden for samme abonnement, gælder følgende begrænsninger:

- Virtuelt netværk (klassisk) kan ikke flyttes.
- Virtuelle maskiner (klassisk) skal flyttes til skyen-tjenesten. 
- Skybaseret tjeneste kan kun flyttes, når Flyt indeholder alle dens virtuelle computere.
- Kun en skybaseret tjeneste, kan flyttes ad gangen.
- Kun én lagerplads konto (klassisk) kan flyttes ad gangen.
- Lagerplads konto (klassisk) kan ikke flyttes i den samme handling med en virtuel computer eller en skybaseret tjeneste.

Brug standard flytning via [portalen](#use-portal), [Azure PowerShell](#use-powershell), [Azure CLI](#use-azure-cli)eller [REST-API](#use-rest-api)til at flytte klassisk Ressourcer til en ny ressourcegruppe inden for samme abonnement. Du kan bruge de samme handlinger, når du bruger til at flytte ressourcestyring ressourcer.

### <a name="new-subscription"></a>Nyt abonnement

Når du flytter ressourcer til et nyt abonnement, gælder følgende begrænsninger:

- Alle klassisk ressourcer i abonnementet skal flyttes på samme tid.
- Abonnementets target må ikke indeholde andre klassisk ressourcer.
- Flyt kan kun anmodes via en separat REST-API til klassisk flytter. Flyt standardkommandoerne Ressourcestyring fungerer ikke, når du flytter klassisk Ressourcer til et nyt abonnement.

Hvis du vil flytte klassisk Ressourcer til et nyt abonnement, skal du bruge RESTEN handlinger, som er specifikke for klassisk ressourcer. Udfør følgende trin for at flytte klassisk Ressourcer til et nyt abonnement.

1. Markér Hvis kilde-abonnement kan deltage i en overgang i tværs-abonnement. Brug handlingen følgende:

         POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
    
     I anmodningsteksten, omfatte:

         {
           "role": "source"
         }

     Svaret for handlingen datavalidering er placeret i følgende format:

         {
           "status": "{status}",
           "reasons": [
             "reason1",
             "reason2"
           ]
         }

2. Markér hvis abonnementets destination kan deltage i en overgang i tværs-abonnement. Brug handlingen følgende:

         POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01

     I anmodningsteksten, omfatte:

         {
           "role": "target"
         }

     Svaret er i samme format som kilde abonnement validering.

3. Hvis begge abonnementer valideres, kan du flytte alle klassisk ressourcer fra et abonnement til et andet abonnement med handlingen følgende:

         POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01

    I anmodningsteksten, omfatte:

         {
           "target": "/subscriptions/{target-subscription-id}"
         }

Handlingen kan tage adskillige minutter. 

## <a name="use-portal"></a>Bruge portal

Vælg den ressourcegruppe, der indeholder disse ressourcer for at flytte ressourcer til en ny ressourcegruppe i **samme-abonnement**, og vælg derefter knappen **Flyt** .

![flytte ressourcer](./media/resource-group-move-resources/edit-rg-icon.png)

Eller hvis du vil flytte ressourcer til et **nyt abonnement**, Vælg den ressourcegruppe, der indeholder ressourcerne, og vælg derefter ikonet Rediger abonnement.

![flytte ressourcer](./media/resource-group-move-resources/change-subscription.png)

Vælg ressourcer til at flytte og ressourcegruppe destination. Bekræfte, at du har brug at opdatere scripts til disse ressourcer, og vælg **OK**. Hvis du har valgt ikonet Rediger abonnement i det forrige trin, skal du også vælge abonnementets destination.

![Vælg destination](./media/resource-group-move-resources/select-destination.png)

I **meddelelser**, kan du se, at flytningen kører.

![Vis Flyt status](./media/resource-group-move-resources/show-status.png)

Når den er fuldført, kan du får besked om resultatet.

![Vis flytte resultat](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>Bruge PowerShell

Hvis du vil flytte eksisterende ressourcer til en anden ressourcegruppe eller et andet abonnement, skal du bruge kommandoen **Flyt AzureRmResource** .

Det første eksempel viser, hvordan du flytter en ressource til en ny ressourcegruppe.

    $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

Det andet eksempel viser, hvordan du flytter flere ressourcer til en ny ressourcegruppe.

    $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

Hvis du vil flytte til et nyt abonnement, omfatte en værdi for parameteren **DestinationSubscriptionId** .

Du bliver bedt om at bekræfte, at du vil flytte de angivne ressourcer.

    Confirm
    Are you sure you want to move these resources to the resource group
    '/subscriptions/{guid}/resourceGroups/newRG' the resources:

    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

## <a name="use-azure-cli"></a>Bruge Azure CLI

Hvis du vil flytte eksisterende ressourcer til en anden ressourcegruppe eller et andet abonnement, skal du bruge kommandoen **azure ressource flytte** . Du skal angive ressource-id'erne for ressourcerne, der skal flyttes. Du kan få ressource id'er med følgende kommando:

    azure resource list -g sourceGroup --json

Som returnerer følgende format:

    [
      {
        "id": "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo",
        "name": "storagedemo",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "southcentralus",
        "tags": {},
        "kind": "Storage",
        "sku": {
          "name": "Standard_RAGRS",
          "tier": "Standard"
        }
      }
    ]

I følgende eksempel viser, hvordan du flytter en lagerplads konto til en ny ressourcegruppe. Angiv en kommasepareret liste over ressource-id er for at flytte parameteren **-i** .

    azure resource move -i "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo" -d "destinationGroup"
    
Du bliver bedt om at bekræfte, at du vil flytte den angivne ressource.

## <a name="use-rest-api"></a>Brug af REST-API

Hvis du vil flytte eksisterende ressourcer til en anden ressourcegruppe eller et andet abonnement, skal du køre:

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

I anmodningsteksten angiver du målressourcegruppe og ressourcerne til at flytte. Finde flere oplysninger om RESTEN flytningen, [flytte ressourcer](https://msdn.microsoft.com/library/azure/mt218710.aspx).


## <a name="next-steps"></a>Næste trin
- Se [Bruge Azure PowerShell med ressourcestyring](powershell-azure-resource-manager.md)for at få mere for at vide om PowerShell-cmdletter til administration af dit abonnement.
- Hvis du vil vide mere om Azure CLI-kommandoer til at administrere dit abonnement, kan du se [Brug af Azure CLI med ressourcestyring](xplat-cli-azure-resource-manager.md).
- For at få mere for at vide om portalen funktioner for administration af dit abonnement, kan du se [ved hjælp af portalen Azure for at administrere ressourcer](./azure-portal/resource-group-portal.md).
- Se [Brug af mærker til at organisere dine ressourcer](resource-group-using-tags.md)for at få mere for at vide om at anvende en logisk struktur til dine ressourcer.
