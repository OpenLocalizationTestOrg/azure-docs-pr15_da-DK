<properties
    pageTitle="Administrere Azure Service Bus ved hjælp af Azure automatisering | Microsoft Azure"
    description="Lær at bruge Azure Automation service til at administrere Azure Service Bus."
    services="service-bus, automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="magoedte;csand"/>

# <a name="managing-azure-service-bus-using-azure-automation"></a>Administrere Azure Service Bus med Automation Azure

Denne vejledning introducerer dig til Azure Automation service, og hvordan det kan bruges til at forenkle administration af Azure Service Bus.

## <a name="what-is-azure-automation"></a>Hvad er Azure automatisering?

[Azure automatisering](../automation/automation-intro.md) er en Azure service for at forenkle skyen administration ved hjælp af automatisering af områdeprocesser og ønskede tilstand konfiguration. Ved hjælp af Azure automatisering, manuel, kan gentages, køres lange og fejlbehæftede opgaver automatisere henblik på øget pålidelighed, effektivitet og klokkeslæt til værdi for din organisation.

Azure automatisering giver en høj grad af pålidelighed, let tilgængelige arbejdsproces udførelse af program, der skaleres til at opfylde dine behov. I Azure Automation kan processer sættes manuelt ved at 3 tredjepart systemer eller med planlagte intervaller så opgaver sker nøjagtigt, når det er nødvendigt.

Reducere funktionsdygtige omkostninger og frigøre IT og DevOps personale til at fokusere på arbejde, der tilføjer forretningsværdi ved at flytte dine skyen administrationsopgaver skal køres automatisk med Automation Azure.

## <a name="how-can-azure-automation-help-manage-azure-service-bus"></a>Hvordan kan Azure automatisering med at administrere Azure Service Bus?

Du kan administrere tjenesten Bus med Automation Azure ved hjælp af [Tjenesten Bus REST-API](https://msdn.microsoft.com/library/azure/mt639375.aspx). Du kan køre PowerShell-scripts, for at udføre mange af dine Service Bus opgaver ved hjælp af REST-API i Azure automatisering. Du kan også par disse REST-API opkald i Azure Automation med cmdletter for andre Azure tjenester til at automatisere komplekse opgaver på tværs af Azure tjenester og -3 part-systemer.

Her er nogle eksempler på bruge PowerShell til at administrere Azure Service Bus:

* [Brugerdefineret PowerShell-cmdlet'er til at administrere Azure Service Bus køer](https://blogs.technet.microsoft.com/uktechnet/2014/12/04/sample-of-custom-powershell-cmdlets-to-manage-azure-servicebus-queues)
* [Sådan oprettes Service Bus køer, emner og abonnementer ved hjælp af en PowerShell-script](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Oprette Azure Service Bus navneområder ved hjælp af PowerShell](http://buildazure.com/2015/09/24/create-azure-service-bus-namespaces-using-powershell-and-x-plat-cli/)

PowerShell-modulet til at arbejde med Azure service bus i automatisering runbooks kan hentes fra [PowerShell galleriet](https://www.powershellgallery.com/packages/AzureServiceBusCreation/1.0).


## <a name="next-steps"></a>Næste trin

Nu hvor du har lært det grundlæggende omkring af Azure automatisering, og hvordan det kan bruges til at administrere Azure Service Bus, skal du følge disse links for at få flere oplysninger om Azure automatisering.

* Se Azure automatisering [Introduktion selvstudium](../automation/automation-first-runbook-graphical.md)
* Se, hvordan du [administrerer Service Bus med PowerShell](service-bus-powershell-how-to-provision.md)
