<properties 
    pageTitle="Fase en skybaseret tjeneste installation (Node.js) | Microsoft Azure" 
    description="Lær at installere dit Azure program til et midlertidigt miljø, og derefter installere til et produktionsmiljø ved hjælp af Byt om virtuelle IP-adresse (VIP)." 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>



# <a name="staging-an-application-in-azure"></a>Arrangere et program i Azure

En pakket programmet kan installeres på det midlertidige miljø i Azure der skal kontrolleres, før du flytte den til den produktionsmiljø, hvor programmet, der er tilgængelige på internettet. Det midlertidige miljø er præcis som produktionsmiljø, bortset fra at du kun har adgang til faseinddelt programmet med en sløret URL-adresse, som genereres af Azure. Når du har bekræftet, at dit program fungerer korrekt, kan det skal installeres til produktionsmiljøet ved at udføre en Byt om virtuelle IP-adresse (VIP).

> [AZURE.NOTE] Trinnene i denne artikel gælder kun for node programmer hostet som en Azure skybaseret tjeneste.

## <a name="step-1-stage-an-application"></a>Trin 1: Forberede et program

Denne opgave beskrives, hvordan du forbereder et program ved hjælp af **Microsoft Azure PowerShell**.

1.  Når du udgiver en tjeneste, skal du blot overfører de **-Slot** -parameter til Cmdletten **Publicer AzureServiceProject** .

    ```powershell
    Publish-AzureServiceProject -Slot staging
    ```

2.  Logge på [Azure klassisk portal] , og vælg **Cloud Services**. Når skytjenesten er oprettet og **midlertidige** kolonne status er blevet opdateret til at **køre**, klik på navnet på tjenesten.

    ![Portal viser en aktiv tjeneste][cloud-service]

3.  Vælg **dashboardet**, og vælg derefter **midlertidige**.

    ![skybaseret tjeneste dashboard][cloud-service-dashboard]

4. Læg mærke til værdien i posten **Webstedets URL-adresse** til højre. DNS-navnet er et sløret interne ID, som genererede beskeden Azure.

    ![webstedets URL-adresse][cloud-service-staging-url]

Nu kan du kontrollere, at programmet fungerer korrekt i det midlertidige miljø ved hjælp af den midlertidige webstedets URL-adresse.

## <a name="step-2-upgrade-an-application-in-production-by-swapping-vips"></a>Trin 2: Opgradere et program i ved ombyttede VIPs

Når du har bekræftet den opgraderede version af et program i det midlertidige miljø, kan du hurtigt gøre det tilgængeligt i fremstilling ved Skift den virtuelle IP'er (VIPs) af test- og miljøerne.

> [AZURE.NOTE] Dette trin forudsætter, at du allerede har installeret et program til fremstilling og midlertidigt den opgraderede version af programmet.

1.  Log på [Azure klassisk portal], skal du klikke på **Cloud Services** , og vælg derefter navnet på tjenesten.

2.  Vælg **midlertidige** **Dashboard**, og klik derefter på **Ombyt** nederst på siden. Dette åbner dialogboksen VIP Udskift.

    ![Vip Byt om dialogboksen][vip-swap-dialog]

3.  Gennemse oplysningerne, og klik derefter på **OK**. De to installationer begynde at opdatere som den midlertidige installation skifter til fremstilling og installationen i et produktionsmiljø skifter til midlertidige.

Du har gemt en installation og opgraderet et produktionsmiljø ved Skift VIPs med distributionen i midlertidige.

## <a name="additional-resources"></a>Yderligere ressourcer

- [Sådan installeres et Tjenesteopgraderingen til fremstilling ved Skift VIPs i Azure]

[Azure klassisk portal]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
[cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
[vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
[Sådan installeres et Tjenesteopgraderingen til fremstilling ved Skift VIPs i Azure]: cloud-services-how-to-manage.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
