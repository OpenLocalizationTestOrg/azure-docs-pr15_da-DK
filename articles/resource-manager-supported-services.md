<properties
   pageTitle="Ressourcestyring understøttes services | Microsoft Azure"
   description="I denne artikel beskrives udbyderne ressource, der understøtter Ressourcestyring, deres skemaer og tilgængelige API versioner og de områder, der kan være vært for ressourcerne."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="magoedte;tomfitz"/>

# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>Ressourcestyring udbydere, områder, API versioner og skemaer

Azure ressourceleder, der indeholder en ny metode til at installere og administrere de tjenester, der udgør dine programmer. De fleste, men ikke alle tjenester understøtter Ressourcestyring, og nogle af tjenesterne understøtter ressourcestyring kun delvist. Dette emne indeholder en liste over understøttede ressource udbydere til Azure ressourcestyring.

Når du installerer dine ressourcer, skal du også vide, hvilke områder understøtter disse ressourcer, og hvilke API versioner er tilgængelige for ressourcerne. Sektionen [understøttede områder](#supported-regions) viser, hvordan du finder ud af, hvilke områder arbejde for dit abonnement og ressourcer. Sektionen [understøttes API versioner](#supported-api-versions) viser, hvordan du bestemme, hvilke API-versioner, du kan bruge.

For at se, hvilke tjenester der understøttes i Azure portalen og klassisk portal skal du se [Azure portalen tilgængelighed diagram](https://azure.microsoft.com/features/azure-portal/availability/). For at se, hvilke tjenester glidende supportressourcer skal du se [flytte ressourcer til ny ressourcegruppe eller et andet abonnement](resource-group-move-resources.md).

De følgende tabeller viser, hvilke Microsoft services support installation og administration ved hjælp af Ressourcestyring og som ikke. Linket i kolonnen **Hurtig start skabeloner** sender en forespørgsel til Azure Hurtig start skabeloner lager for den angivne ressource-udbyder. Hurtig start skabeloner er tilføjet og opdateret ofte. Tilstedeværelsen af et link til en bestemt tjeneste, betyder ikke nødvendigvis forespørgslen returnerer skabeloner fra lageret. Der er også mange tredjeparts ressource udbydere, der understøtter ressourcestyring. Lærer du at få vist alle udbydere ressource i sektionen [ressource udbydere og datatyper](#resource-providers-and-types) .


## <a name="compute"></a>Beregne

| Tjenesten | Ressourceleder, der er aktiveret | REST-API | Skema | Hurtig start skabeloner |
| ------- | ------------------------ |-------- | ------ | ------ |
| Batchen   | Ja     | [Batchen RESTEN](https://msdn.microsoft.com/library/azure/dn820158.aspx) | [2015-12-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-12-01/Microsoft.Batch.json)       |  |
|Objektbeholder | Ja | [Objektbeholder tjenesten RESTEN](https://msdn.microsoft.com/library/azure/mt711470.aspx) | [2016-03-30](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.ContainerService.json) | [Microsoft.ContainerService](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ContainerService%22&type=Code) |
| Dynamics livscyklus Services | Ja  |      |        |  |
| Skala sæt | Ja | [Skala angive RESTEN](https://msdn.microsoft.com/library/azure/mt705635.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [virtualMachineScaleSets](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=virtualMachineScaleSets&type=Code) | 
| Tjenesten struktur | Ja  | [Tjenesten strukturen resten](https://msdn.microsoft.com/library/azure/dn707692.aspx)  |      | [Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Virtuelle maskiner | Ja | [VM RESTEN](https://msdn.microsoft.com/library/azure/mt163647.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [virtualMachines](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%2Fvirtualmachines%22&type=Code) |
| Virtuelle maskiner (klassisk) | Begrænset | - | - | - |
| Remote-App | Nej      | -  | - | - |
| Cloud Services (klassisk) | Begrænset (se nedenfor) | - | - | - |

Virtuelle maskiner (klassisk) refererer til ressourcer, der blev installeret gennem modellen Klassisk installation i stedet for via implementeringsmodel ressourcestyring. Generelt disse ressourcer understøtter ikke ressourcestyring handlinger, men der er nogle handlinger, som er blevet aktiveret. Se [forstå ressourcestyring installation og klassisk installation](resource-manager-deployment-model.md)kan finde flere oplysninger om disse installation-modeller. 

Cloud Services (klassisk) kan bruges sammen med andre klassisk Ressourcer dog benytte ikke sig af alle ressourcestyring funktioner klassisk ressourcer, og der ikke er et godt valg til fremtidige løsninger. I stedet skal du overveje at ændre programinfrastrukturen for at bruge ressourcer fra Microsoft.Compute, Microsoft.Storage og Microsoft.Network navneområder.


## <a name="networking"></a>Netværk

| Tjenesten | Ressourceleder, der er aktiveret | REST-API | Skema | Hurtig start skabeloner |
| ------- | -------  | -------- | ------ | ------ |
| Application Gateway | Ja | [Application Gateway RESTEN](https://msdn.microsoft.com/library/azure/mt684939.aspx)   |        | [applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS     | Ja | [DNS-RESTEN](https://msdn.microsoft.com/library/azure/mt163862.aspx)         | [2016-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Network.json) | [dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| ExpressRoute | Ja  | [ExpressRoute RESTEN](https://msdn.microsoft.com/library/azure/mt586720.aspx)  |       | [expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |
| Justering af belastning | Ja  | [Justering af belastning RESTEN](https://msdn.microsoft.com/library/azure/mt163651.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Trafik Manager | Ja | [Trafik Manager RESTEN](https://msdn.microsoft.com/library/azure/mt163667.aspx) | [2015-11-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json)  | [trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| Virtuelt netværk | Ja| [Virtuelt netværk RESTEN](https://msdn.microsoft.com/en-us/library/azure/mt163650.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| VPN-Gateway | Ja | [Netværksgateway RESTEN](https://msdn.microsoft.com/library/azure/mt163859.aspx) |  | [virtualNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworkGateways%22&type=Code) <br /> [localNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FlocalNetworkGateways%22&type=Code) <br />[forbindelser](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Fconnections%22&type=Code) |


## <a name="storage"></a>Lagerplads

| Tjenesten | Ressourceleder, der er aktiveret | REST-API | Skema | Hurtig start skabeloner |
| ------- | ------- | -------- | ------ | ------- | ------ |
| Lagerplads | Ja  | [Lagerplads RESTEN](https://msdn.microsoft.com/library/azure/mt163683.aspx) | [Lagerplads konto](resource-manager-template-storage.md) | [Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| StorSimple | Ja  |         |        |  |

## <a name="databases"></a>Databaser

| Tjenesten | Ressourceleder, der er aktiveret | REST-API | Skema | Hurtig start skabeloner |
| ------- | ------- | -------- | ------ | ------- | ------ |
| DocumentDB | Ja  | [DocumentDB RESTEN](https://msdn.microsoft.com/library/azure/dn781481.aspx) | [2015-04-08](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json)  | [Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Redis Cache | Ja |   | [2016-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Cache.json) | [Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| SQL-Database | Ja | [SQL-Database RESTEN](https://msdn.microsoft.com/library/azure/mt163571.aspx) | [2014-04-01-eksempel](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) | [Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| SQL datawarehouse | Ja |   |      |


## <a name="web--mobile"></a>Web og Mobile

| Tjenesten | Ressourceleder, der er aktiveret | REST-API | Skema | Hurtig start skabeloner |
| ------- | ------- | -------- | ------ | ------ |
| API Apps | Ja |   | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [API Apps](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| Administration af API | Ja  | [API Management RESTEN](https://msdn.microsoft.com/library/azure/dn776326.aspx) | [2016-07-07](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-07-07/Microsoft.ApiManagement.json)       | [Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) | 
| Indhold redaktør | Ja |   |   |   |
| Funktionen App | Ja |   |   | [functionApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22functionApp%22&type=Code) |
| Logik Apps | Ja   | [Arbejdsproces Service REST-API](https://msdn.microsoft.com/library/azure/mt643787.aspx) | [2016-06-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.Logic.json) | [Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Mobilapps | Ja |  | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json)  | [mobileApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22mobileApp%22&type=Code)   |
| Mobile aftaler | Ja  |  [Mobile aftale RESTEN](https://msdn.microsoft.com/library/azure/mt683754.aspx)        |        | [Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |
| Søgning | Ja  | [Søg RESTEN](https://msdn.microsoft.com/library/azure/dn798935.aspx) |  | [Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| Webapps | Ja |          | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |

## <a name="analytics"></a>Analyser

| Tjenesten | Ressourceleder, der er aktiveret | REST-API | Skema | Hurtig start skabeloner |
| ------- | -------  | -------- | ------ | ------ |
| Datakatalog | Ja  | [Datakatalog RESTEN](https://msdn.microsoft.com/library/azure/mt267595.aspx)  | [2016-03-30](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) |  |
| Data Factory | Ja | [Data Factory RESTEN](https://msdn.microsoft.com/library/azure/dn906738.aspx) |    | [Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Data sø Analytics | Ja |   |   [2015-10-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) | [Microsoft.DataLakeAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeAnalytics%22&type=Code) |
| Sø datalager | Ja  | [Sø datalager RESTEN](https://msdn.microsoft.com/library/azure/mt693424.aspx) | [2015-10-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) | [Microsoft.DataLakeStore](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeStore%22&type=Code) |
| HDInsights | Ja | [HDInsights RESTEN](https://msdn.microsoft.com/library/azure/mt622197.aspx) |        | [Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Machine Learning | Ja | [Machine Learning RESTEN](https://msdn.microsoft.com/library/azure/mt767538.aspx)        | [2016-05-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-01-preview/Microsoft.MachineLearning.json) |
| Stream Analytics | Ja  | [God Analytics RESTEN](https://msdn.microsoft.com/library/azure/dn835031.aspx)   |        |  |
| Power BI | Ja | [Power BI integreret RESTEN](https://msdn.microsoft.com/library/azure/mt712303.aspx) | [29-01-2016](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-01-29/Microsoft.PowerBI.json) |  |

## <a name="intelligence"></a>Intelligence

| Tjenesten | Ressourceleder, der er aktiveret | REST-API | Skema | Hurtig start skabeloner |
| ------- | ------- | -------- | ------ | ------ |
| Kognitiv tjenester | Ja |  | [2016-02-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-01-preview/Microsoft.CognitiveServices.json) |   |

## <a name="internet-of-things"></a>Internet ting

| Tjenesten | Ressourceleder, der er aktiveret | REST-API | Skema | Hurtig start skabeloner |
| ------- | ------- | -------- | ------ | ------ |
| Begivenhed Hub | Ja  | [Begivenhed Hub RESTEN](https://msdn.microsoft.com/library/azure/dn790674.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.EventHub.json) | [Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code)  |
| IoTHubs | Ja | [IoT Hub RESTEN](https://msdn.microsoft.com/library/azure/mt589014.aspx) | [2016-02-03](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-03/Microsoft.Devices.json) | [Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Meddelelse om Hubs | Ja | [Meddelelse om Hub RESTEN](https://msdn.microsoft.com/library/azure/dn495827.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) | [Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |

## <a name="media--cdn"></a>Medier og CDN

| Tjenesten | Ressourceleder, der er aktiveret | REST-API | Skema | Hurtig start skabeloner |
| ------- | ------- | -------- | ------ | ------ |
| LEVERANDØR(ER) | Ja | [LEVERANDØR(ER) RESTEN](https://msdn.microsoft.com/library/azure/mt634456.aspx)  | [2016-04-02](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-02/Microsoft.Cdn.json) | [Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Tjenesten Media | Ja | [Media Services RESTEN](https://msdn.microsoft.com/library/azure/hh973617.aspx)  | [2015-10-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01/Microsoft.Media.json) |


## <a name="hybrid-integration"></a>Integration af hybrid

| Tjenesten | Ressourceleder, der er aktiveret | REST-API | Skema | Hurtig start skabeloner |
| ------- | ------- | -------- | ------ | ------ |
| BizTalk-tjenester | Ja |          | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |  |
| Gendannelse Service | Ja | [Gendannelse RESTEN af websteder](https://msdn.microsoft.com/library/azure/mt750497.aspx) | [2016-06-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.RecoveryServices.json) | [Microsoft.RecoveryServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.RecoveryServices%22&type=Code) |
| Service Bus | Ja | [Service Bus RESTEN](https://msdn.microsoft.com/library/azure/mt639375.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.ServiceBus.json)       | [Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |

## <a name="identity--access-management"></a>Identitet og administration af adgang 

Azure Active Directory fungerer med ressourcestyring til at aktivere rollebaseret adgangskontrol for dit abonnement. Se [Azure rollebaseret adgangskontrol](./active-directory/role-based-access-control-configure.md)for at få mere for at vide om brugen af rollebaseret adgangskontrol og Active Directory.

## <a name="developer-services"></a>Developer Services 

| Tjenesten | Ressourceleder, der er aktiveret | REST-API | Skema | Hurtig start skabeloner |
| ------- | ------- | -------- | ------ | ------ |
| Programmet indsigt | Ja  | [Indsigt RESTEN](https://msdn.microsoft.com/library/azure/dn931943.aspx)  | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) | [Microsoft.insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Bing Maps | Ja  |          |        |  |
| DevTest øvelser | Ja |   | [2016-05-15](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-15/Microsoft.DevTestLab.json) | [Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code)  |
| Visual Studio-konto | Ja   |          | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |  |

## <a name="management-and-security"></a>Administration og sikkerhed

| Tjenesten | Ressourceleder, der er aktiveret | REST-API | Skema | Hurtig start skabeloner |
| ------- | ------- | -------- | ------ | ------ |
| Automatisering | Ja | [Automatisering RESTEN](https://msdn.microsoft.com/library/azure/mt662285.aspx) | [2015-10-31](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-31/Microsoft.Automation.json)       | [Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Vigtige samling | Ja | [Vigtige samling RESTEN](https://msdn.microsoft.com/library/azure/dn903609.aspx) | [Vigtige samling](resource-manager-template-keyvault.md)<br />[Vigtige samling hemmeligt](resource-manager-template-keyvault-secret.md)   | [Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Funktionsdygtige indsigt | Ja |          |        |  |
| Opgavestyring | Ja  | [Scheduler RESTEN](https://msdn.microsoft.com/library/azure/mt629143.aspx)     | [2016-03-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-01/Microsoft.Scheduler.json) | [Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Sikkerhed (preview) | Ja | [Sikkerhed RESTEN](https://msdn.microsoft.com/library/azure/mt704034.aspx)  |  | [Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code)  |

## <a name="resource-manager"></a>Ressourcestyring

| Funktion | Ressourceleder, der er aktiveret | REST-API | Skema | Hurtig start skabeloner |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Godkendelse | Ja | [Administration af låse](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[Rollebaseret adgangskontrol](https://msdn.microsoft.com/library/azure/dn906885.aspx)  | [Ressource Lås](resource-manager-template-lock.md)<br />[Rolletildelinger](resource-manager-template-role.md)  | [Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Ressourcer | Ja | [Sammenkædede ressourcer](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [Ressourcehyperlinks](resource-manager-template-links.md) | [Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |


## <a name="resource-providers-and-types"></a>Udbydere af ressource og typer

Når du installerer ressourcer, bruger du ofte til at hente oplysninger om ressource udbydere og datatyper. Du kan hente denne oplysninger via REST-API, Azure PowerShell eller Azure CLI.

Hvis du vil arbejde med en ressource-udbyder, skal være registreret, ressource-udbyder med din konto. Mange ressource udbydere er som standard automatisk registreret; Du kan dog skal for manuelt at registrere nogle udbydere af ressource. Eksemplerne nedenfor viser, hvordan du få registreringsstatus for en ressource udbyder og registrere provideren ressource, hvis det er nødvendigt.

### <a name="portal"></a>Portal

Du kan nemt se en liste over understøttede ressourcer udbydere med følgende trin:

1. Vælg **Mine tilladelser**.

    ![Mine tilladelser](./media/resource-manager-supported-services/my-permissions.png)

2. Vælg **udbyder Ressourcestatus**

    ![udbyder Ressourcestatus](./media/resource-manager-supported-services/resource-provider-status.png)

3. Du kan se den komplette liste over udbydere af ressource for dit abonnement.

    ![liste over udbydere af ressource](./media/resource-manager-supported-services/list-providers.png)

### <a name="rest-api"></a>REST-API

Hvis du vil have alle tilgængelige ressourcen Brug udbydere, herunder deres typer, placeringer, API versioner og registreringsstatus, handlingen [liste over alle ressource-udbydere](https://msdn.microsoft.com/library/azure/dn790524.aspx) . Hvis du vil registrere en ressource-udbyder, skal du se [registrere et abonnement med en ressource-udbyder](https://msdn.microsoft.com/library/azure/dn790548.aspx).

### <a name="powershell"></a>PowerShell

I følgende eksempel vises, hvordan du får alle tilgængelige ressource-udbydere.

    Get-AzureRmResourceProvider -ListAvailable
    

Det næste eksempel viser, hvordan at hente ressourcetyperne til en bestemt ressource-udbyder.

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
    
Output ligner:

    ResourceTypeName                Locations                                         
    ----------------                ---------                                         
    sites/extensions                {Brazil South, East Asia, East US, Japan East...} 
    sites/slots/extensions          {Brazil South, East Asia, East US, Japan East...} .
    ...
    
Angiv navneområdet for at registrere en udbyder af ressource:

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement

### <a name="azure-cli"></a>Azure CLI

I følgende eksempel vises, hvordan du får alle tilgængelige ressource-udbydere.

    azure provider list
    
Output ligner:

    info:    Executing command provider list
    + Getting registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.ApiManagement          Unregistered
    data:    Microsoft.AppService             Registered
    data:    Microsoft.Authorization          Registered
    ...

Du kan gemme oplysningerne om en bestemt ressource-provider til en fil med følgende kommando.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Angiv navneområdet for at registrere en udbyder af ressource:

    azure provider register -n Microsoft.ServiceBus

## <a name="supported-regions"></a>Understøttede områder

Når du installerer ressourcer, skal du typisk til at angive et område for ressourcerne. Ressourceleder, der understøttes i alle områder, men de ressourcer, du installerer understøttes muligvis ikke i alle områder. Der kan desuden være begrænsninger på dit abonnement, der forhindrer dig i at bruge visse områder, der understøtter ressourcen. Disse begrænsninger kan være relateret til meget moms problemer for dit hjemmenetværk land eller resultatet af en politik, der er placeret ved administratoren abonnement for at bruge kun visse områder. 

Se en komplet liste over alle understøttede områder for alle Azure tjenester [tjenester efter område](https://azure.microsoft.com/regions/#services); Denne liste kan dog omfatte områder, der ikke understøtter oprettelse af dit abonnement. Du kan bestemme områder for en bestemt ressourcetype, der understøtter dit abonnement via portalen, REST-API, PowerShell eller Azure CLI.

### <a name="portal"></a>Portal

Du kan se de understøttede områder for en af ressourcetypen gennem følgende trin:

1. Vælg **flere tjenester** > **Ressource Explorer**.

    ![ressource explorer](./media/resource-manager-supported-services/select-resource-explorer.png)

2. Åbn noden **udbydere** .

    ![Vælg udbydere](./media/resource-manager-supported-services/select-providers.png)

3. Vælg en ressource-udbyder, og se understøttede områder og API versioner.

    ![Vis udbyder](./media/resource-manager-supported-services/view-provider.png)

### <a name="rest-api"></a>REST-API

For at se, hvilke områder er tilgængelige for en bestemt ressourcetype i dit abonnement, kan bruge handlingen [liste over alle ressource-udbydere](https://msdn.microsoft.com/library/azure/dn790524.aspx) . 

### <a name="powershell"></a>PowerShell

I følgende eksempel viser, hvordan at hente de understøttede områder til websteder.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
Output ligner:

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

### <a name="azure-cli"></a>Azure CLI

I følgende eksempel returnerer alle de understøttede placeringer for hver ressourcetype.

    azure location list

Du kan også filtrere resultaterne placering med en JSON funktion som [jq](https://stedolan.github.io/jq/).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Der returneres:

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## <a name="supported-api-versions"></a>Understøttede API-versioner

Når du installerer en skabelon, skal du angive en API-version, der skal bruges til at oprette hver ressource. API-version svarer til en version af REST-API handlinger, som er udgivet af provideren ressource. Som en ressource udbyder gør det muligt for nye funktioner, udgiver en ny version af REST-API. Derfor påvirker versionen af API du angiver i skabelonen hvilke egenskaber, du kan angive i skabelonen. Generelt, vil du markere den seneste version af API, når du opretter skabeloner. For eksisterende skabeloner, kan du bestemme, om du vil fortsætte med at bruge en tidligere version af API eller opdatere din skabelon til den nyeste version at kunne udnytte nye funktioner.

### <a name="portal"></a>Portal

Du kan se de understøttede API-versioner på samme måde, du har besluttet dig understøttes områder (vist tidligere).

### <a name="rest-api"></a>REST-API

For at se, hvilke API versioner er tilgængelige for ressourcetyper, kan bruge handlingen [liste over alle ressource-udbydere](https://msdn.microsoft.com/library/azure/dn790524.aspx) . 

### <a name="powershell"></a>PowerShell

I følgende eksempel viser, hvordan at hente de tilgængelige API versioner til en bestemt ressourcetype.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    
Output ligner:
    
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

### <a name="azure-cli"></a>Azure CLI

Du kan gemme oplysningerne om (herunder de tilgængelige API versioner) for en ressource udbyder til en fil med følgende kommando.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Du kan åbne filen og finde elementet **apiVersions**

## <a name="next-steps"></a>Næste trin

- Se [redigering Azure ressourcestyring skabeloner](resource-group-authoring-templates.md)for at få mere for at vide om oprettelse af Ressourcestyring skabeloner.
- Se [Implementer et program med Azure ressourcestyring skabelon](resource-group-template-deploy.md)for at få mere for at vide om installation af ressourcer.
