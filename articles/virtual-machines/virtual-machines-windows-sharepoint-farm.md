<properties
    pageTitle="Oprette SharePoint server-farme | Microsoft Azure"
    description="Hurtigt oprette en ny SharePoint 2013 eller 2016 SharePoint-farm i Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="JoeDavies-MSFT"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="josephd"/>

# <a name="create-sharepoint-server-farms"></a>Oprette SharePoint server-farme

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisk model.

## <a name="sharepoint-2013-farms"></a>SharePoint 2013 farme

Med Microsoft Azure portalen marketplace, kan du hurtigt oprette forudkonfigurerede SharePoint Server 2013 farme. Dette kan du spare en masse tid når du har brug for en grundlæggende eller høj tilgængelighed SharePoint-farm til et Udviklingscenter/testmiljø, eller hvis du evaluerer SharePoint Server 2013 som en samarbejde løsning for organisationen.

> [AZURE.NOTE] **SharePoint-serverfarm** element i Azure Marketplace af portalen Azure er blevet fjernet. Det er blevet erstattet med **SharePoint 2013 ikke-HA Farm** og **SharePoint 2013 HA Farm** elementer.

Den grundlæggende SharePoint-farm består af tre virtuelle maskiner i denne konfiguration.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/Non-HAFarm.png)

Du kan bruge denne farmkonfiguration til en forenklet konfiguration til udvikling af SharePoint-app eller din førstegangsbrugere evaluering af SharePoint 2013.

Sådan oprettes den grundlæggende (tre-server) SharePoint-farm:

1. Klik på [her](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/).
2. Klik på **installation**.
3. Klik på **Opret**i ruden **SharePoint 2013 ikke-HA Farm** .
4. Angive indstillinger i trinnene i ruden **Opret SharePoint 2013 ikke-HA-Farm** , og klik derefter på **Opret**.

Høj tilgængelighed SharePoint-farm består af ni virtuelle maskiner i denne konfiguration.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/HAFarm.png)

Du kan bruge denne farmkonfiguration til at teste nyere klient indlæse, høj tilgængelighed af eksterne SharePoint-webstedet og SQL Server AlwaysOn tilgængelighed grupper for en SharePoint-farm. Du kan også bruge denne konfiguration til SharePoint-app udvikling i et miljø med høj tilgængelighed.

Sådan oprettes høj tilgængelighed (niende-server) SharePoint-farm:

1. Klik på [her](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/).
2. Klik på **installation**.
3. Klik på **Opret**i ruden **SharePoint 2013 HA Farm** .
4. Angive indstillinger i syv trinnene i ruden **Opret SharePoint 2013 HA-Farm** , og klik derefter på **Opret**.

> [AZURE.NOTE] Du kan ikke oprette **SharePoint 2013 ikke-HA Farm** eller **SharePoint 2013 HA Farm** med en gratis prøveversion Azure.

Portalen Azure opretter begge af disse farme i et skybaseret kun virtuelt netværk med en forbindelse til internettet web-tilstedeværelse. Der er ingen til websted VPN- eller ExpressRoute forbindelse tilbage til organisationens netværk.

> [AZURE.NOTE] Når du opretter grundlæggende eller høj tilgængelighed SharePoint-farme ved hjælp af portalen Azure, du kan ikke angive en eksisterende ressourcegruppe. Du kan løse problemet ved at oprette disse farme med Azure PowerShell. Se [oprette SharePoint 2013 Udviklingscenter/afprøve farme med Azure PowerShell](https://technet.microsoft.com/library/mt743093.aspx#powershell)kan finde flere oplysninger.

## <a name="sharepoint-2016-farms"></a>SharePoint-2016 farme

I [denne artikel](https://technet.microsoft.com/library/mt723354.aspx) finder instruktioner til at oprette følgende enkelt server SharePoint Server 2016 farmen.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/SP2016Farm.png)

## <a name="managing-the-sharepoint-farms"></a>Administration af SharePoint-farme

Du kan administrere serverne af disse farme gennem Fjernskrivebord forbindelser. Yderligere oplysninger finder du se [logge på den virtuelle maskine](virtual-machines-windows-hero-tutorial.md#log-on-to-the-virtual-machine).

Du kan konfigurere Mine websteder, SharePoint-programmer og andre funktioner fra til Central Administration af SharePoint-webstedet. Se [Konfigurere SharePoint](http://technet.microsoft.com/library/ee836142.aspx)kan finde flere oplysninger.

## <a name="next-steps"></a>Næste trin

- Se flere [SharePoint konfigurationer](https://technet.microsoft.com/library/dn635309.aspx) i Azure infrastrukturtjenester.
