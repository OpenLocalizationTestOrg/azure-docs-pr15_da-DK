<properties 
    pageTitle="Sammenkædning af ressourcer i Azure ressourcestyring | Microsoft Azure" 
    description="Oprette en kæde mellem relaterede ressourcer i forskellige grupper i Azure ressourcestyring." 
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
    ms.date="08/01/2016" 
    ms.author="tomfitz"/>

# <a name="linking-resources-in-azure-resource-manager"></a>Sammenkæde ressourcer i Azure ressourcestyring.

Du kan markere en ressource som afhænger af en anden ressource under installationen, men livscyklussen slutter på installation. Efter installation er der ingen identificerede relation mellem afhængige ressourcer. Ressourceleder, der indeholder en funktion, der hedder ressource sammenkædning for at oprette fast relationer mellem ressourcer.

Med ressource sammenkædning, kan du dokumentere relationer, der strækker sig over grupper. Det er for eksempel almindelige for at få en database med sin egen livscyklus er placeret i en ressourcegruppe, og en app med en anden livscyklus er placeret i en anden ressourcegruppe. Appen opretter forbindelse til databasen, så du vil markere et link mellem appen og databasen. 

Alle sammenkædede ressourcer skal tilhøre samme abonnement. Hver ressource kan sammenkædes med 50 andre ressourcer. Den eneste måde at forespørge på Relaterede ressourcer er gennem REST-API. Hvis nogen af de sammenkædede ressourcer er slettet eller flyttet, skal ejeren af link oprydning resterende linket. Du er **ikke** en advarsel, når du sletter en ressource, der er knyttet til andre ressourcer.

## <a name="linking-in-templates"></a>Sammenkædning i skabeloner

For at definere et link i en skabelon, skal medtage du en ressourcetype, der kombinerer navneområdet ressource udbyder og kilde ressourcetype med **/providers/links**. Navnet skal indeholde navnet på ressourcen, kilde. Du angiver ressource-id på ressourcen, mål. I følgende eksempel oprettes en kæde mellem et websted og en lagerplads konto.

    {
      "type": "Microsoft.Web/sites/providers/links",
      "apiVersion": "2015-01-01",
      "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
      "dependsOn": [ "[variables('siteName')]" ],
      "properties": {
        "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
        "notes": "This web site uses the storage account to store user information."
      }
    }


Du kan finde en fuldstændig beskrivelse af Skabelonformatet [ressourcehyperlinks - skabelon skemaet](resource-manager-template-links.md).

## <a name="linking-with-rest-api"></a>Sammenkædning med REST API'ER

For at definere et link mellem udløst ressourcer skal du køre:

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

Erstat {abonnement-id} med dit abonnement-id. Erstatte {ressource-gruppe}, {provider navneområde, {-ressourcetype} og {ressource-name} med de værdier, der identificerer den første ressource i feltet link. Erstat {link-name} med navnet på linket for at oprette. Brug 2015-01-01 til api-version.

Medtage et objekt, der definerer den anden ressource i feltet link i anmodningen:

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

Elementet Egenskaber indeholder et id for den anden ressource.

Du kan forespørge links i dit abonnement på:

    https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Resources/links?api-version={api-version}

Du kan finde flere eksempler, herunder hvordan du kan hente oplysninger om links, [Sammenkædede ressourcer](https://msdn.microsoft.com/library/azure/mt238499.aspx).

## <a name="next-steps"></a>Næste trin

- Du kan også organisere dine ressourcer med mærker. Se [Brug af mærker til at organisere dine ressourcer](resource-group-using-tags.md)for at få mere for at vide om mærkning ressourcer.
- Du kan finde en beskrivelse af hvordan du opretter skabeloner og definere ressourcerne, der skal installeres, [redigering skabeloner](resource-group-authoring-templates.md).
