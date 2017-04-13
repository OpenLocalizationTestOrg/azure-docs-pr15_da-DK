<properties 
    pageTitle="Logik Apps priser model | Microsoft Azure" 
    description="Oplysninger om, hvordan priser fungerer i logik Apps" 
    authors="kevinlam1" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="klam"/>

# <a name="logic-apps-pricing-model"></a>Logik Apps priser model

Azure logik Apps gør det muligt at skalere og udføre integration af arbejdsprocesser i skyen.  Nedenfor finder du oplysninger om faktureringen- og priser planer for logik Apps.

## <a name="consumption-pricing"></a>Forbrug priser

Nyligt oprettede Brug logik Apps en forbrug plan. Med logik Apps forbrug priser modellen betale du kun for du bruger.  Logik Apps er ikke begrænset, når du bruger en forbrug plan.
Alle handlinger, der udføres i en kørsel af en forekomst af logik app er forbrugsafregnede.

### <a name="what-are-action-executions"></a>Hvad er handling udførelser?

Alle trin i en logik app definition er en handling.  Dette omfatter udløsere, flow trinnene til styring af ligesom betingelser, områder, for hver løkker gør indtil løkker, opkald til forbindelser og opkald til oprindelige handlinger.
Udløsere er lige speciel handlinger, der er designet til at oprette en ny forekomst af en logik app, når en bestemt hændelse indtræffer.  Der findes en række forskellige funktionsmåder for udløsere, kan det påvirke hvordan logik app er forbrugsafregnede.

-   Et slutpunkt undersøger **forespørgsel blandt udløser** – denne udløser kontinuerligt, indtil den modtager en meddelelse, der opfylder kriterier til at oprette en ny forekomst af en logik app.  Forespørgselsintervallet kan konfigureres på udløser i logik Apps designer.  Hver afstemning anmodning, tæller selvom den ikke oprette en ny forekomst af en logik app, som en handling udførelse.

-   **Webhook udløser** – denne udløser venter på en klient til at sende den en anmodning på et bestemt slutpunkt.  Hver enkelt anmodning, der sendes til webhook slutpunktet tæller som en handling udførelse. Anmodningen og HTTP Webhook udløser er begge webhook udløsere.

-   **Gentagelse udløser** – denne udløser opretter en ny forekomst af appen logik, der er baseret på den gentagelsesinterval, der er konfigureret i udløseren.  En gentagelse udløser kan for eksempel være konfigureret til at køre hver 3 dage eller endda hvert minut.

Udløser udførelser kan ses i bladet logik Apps ressource i delen udløser oversigt.

Alle handlinger, der blev udført, om det var vellykkede eller mislykkede er forbrugsafregnede som en handling udførelse.  Handlinger, der blev ignoreret på grund af en betingelse, der ikke er opfyldt eller handlinger, der ikke udføres, fordi logik app afbrudt inden fuldførelse tælles ikke som handling udførelser.

Handlinger, der udføres i løkker, tælles per i aldrig.  For eksempel en enkelt handling i en for hver løkke gentagelse gennem en liste over 10 elementer der skal tælles som antallet af elementer på listen (10) multipliceret med antallet af handlinger i løkke (1) plus én for indledning af løkke som i dette eksempel ville være (10 * 1) + 1 = 11 handling udførelser.

Logik Apps, der er deaktiveret kan ikke have nye forekomster, der er en forekomst af og derfor på det tidspunkt, som de er deaktiveret, bliver ikke få debiteret.  Vær opmærksom, efter deaktivering af en logik app kan det tage lidt tid for forekomsterne skal suspendere før deaktiveres helt.

## <a name="app-service-plans"></a>App Service-planer

App-tjenesten plan skal ikke længere oprette en logik App.  Du kan også referere til en App-tjenesten planlægge med en eksisterende logik app.  Logik apps tidligere har oprettet med en App-serviceaftale fortsat fungerer som før hvor, afhængigt af planen der vælges, vil få begrænset efter et antal daglige udførelser overskrides og vil ikke blive faktureret bruger handling udførelse af tælleren.

App-tjenesten planer og deres daglige tilladte handling udførelser:

| |Ledig/delt/Basic|Standard|Premium|
|---|---|---|---|
|Handling udførelser dagen| 200|10.000|50000|

### <a name="convert-from-consumption-to-app-service-plan-pricing"></a>Konvertere fra forbrug til App-tjenesten planlægge priser

Hvis du vil henvise til en App-tjenesten Plan til et forbrug logik App, kan du blot [køre den under PowerShell-script](https://github.com/logicappsio/ConsumptionToAppServicePlan).  Sørg for, at du først har hvor [Azure PowerShell værktøjer](https://github.com/Azure/azure-powershell) , der er installeret.

``` powershell
Param(
    [string] $AppService_RG = '<app-service-resource-group>',
    [string] $AppService_Name = '<app-service-name>',
    [string] $LogicApp_RG = '<logic-app-resource-group>',
    [string] $LogicApp_Name = '<logic-app-name>',
    [string] $subscriptionId = '<azure-subscription-id>'
)

Login-AzureRmAccount 
$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId
$appserviceplan = Get-AzureRmResource -ResourceType "Microsoft.Web/serverFarms" -ResourceGroupName $AppService_RG -ResourceName $AppService_Name
$logicapp = Get-AzureRmResource -ResourceType "Microsoft.Logic/workflows" -ResourceGroupName $LogicApp_RG -ResourceName $LogicApp_Name

$sku = @{
    "name" = $appservicePlan.Sku.tier;
    "plan" = @{
      "id" = $appserviceplan.ResourceId;
      "type" = "Microsoft.Web/ServerFarms";
      "name" = $appserviceplan.Name  
    }
}

$updatedProperties = $logicapp.Properties | Add-Member @{sku = $sku;} -PassThru

$updatedLA = Set-AzureRmResource -ResourceId $logicapp.ResourceId -Properties $updatedProperties -ApiVersion 2015-08-01-preview
```

### <a name="convert-from-app-service-plan-pricing-to-consumption"></a>Konvertere fra App Service planlægge priser til forbrug

Hvis du vil ændre Fjern en logik App, der har en App-tjenesten planlægge knyttet til en forbrug model referencen til App-tjenesten planlægge i definitionen af logik App.  Dette kan gøres med et opkald til en PowerShell-cmdlet:

`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`

## <a name="pricing"></a>Priser

Priser detaljer skal du se [Logik Apps priser](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Næste trin

- [En oversigt over logik Apps][whatis]
- [Oprette din første logik-app][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: app-service-logic-what-are-logic-apps.md
[create]: app-service-logic-create-a-logic-app.md

