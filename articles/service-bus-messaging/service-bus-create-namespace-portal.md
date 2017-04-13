<properties
    pageTitle="Oprette et Service Bus navneområde ved hjælp af portalen Azure | Microsoft Azure"
    description="For at komme i gang med Service Bus, skal du et navneområde. Her er, hvordan du opretter en ved hjælp af portalen Azure."
    services="service-bus"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="jotaub"/>

# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Oprette et Service Bus navneområde ved hjælp af portalen Azure

Et navneområde er en fælles beholder for alle dine SMS komponenter. Flere køer og emner kan være placeret i et enkelt navneområde, og navneområder fungerer ofte som programmet objektbeholdere. Der findes to forskellige måder at oprette et Service Bus navneområde.

1.  Azure-portalen (i denne artikel)

2.  [Ressourcestyring skabeloner][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Oprette et navneområde i portalen Azure

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Tillykke! Du har nu oprettet et Service Bus Messaging navneområde.

## <a name="next-steps"></a>Næste trin

Se vores [GitHub eksempler] [ github-samples] som viser nogle af de mere avancerede funktioner i Azure Service Bus Messaging.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples