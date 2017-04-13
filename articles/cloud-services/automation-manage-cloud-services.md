<properties
    pageTitle="Administrere Azure Cloud Services med Automation Azure | Microsoft Azure"
    description="Få mere at vide om, hvordan Azure Automation service kan bruges til at administrere Azure skytjenester skaleres til brug."
    services="cloud-services, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/20/2016"
    ms.author="jolevy"/>



#<a name="managing-azure-cloud-services-using-azure-automation"></a>Administrere Azure Cloud Services med Automation Azure

Denne vejledning introducerer dig til Azure Automation service, og hvordan det kan bruges til at forenkle administration af din Azure skytjenester.

## <a name="what-is-azure-automation"></a>Hvad er Azure automatisering?

[Azure automatisering](https://azure.microsoft.com/services/automation/) er en Azure service for at forenkle skyen administration ved hjælp af automatisering af områdeprocesser for. Brug af Azure automatisering, kan længerevarende, manuel, fejlbehæftede og ofte gentagne opgaver automatisere henblik på øget pålidelighed, effektivitet og klokkeslæt til værdi for din organisation.

Azure automatisering giver en meget pålidelig og meget tilgængelig arbejdsproces udførelse af program, der skaleres til at opfylde dine behov, som organisationen vokser. I Azure Automation kan processer sættes manuelt ved at 3 tredjepart systemer eller med planlagte intervaller så opgaver sker nøjagtigt, når det er nødvendigt.

Sænke funktionsdygtige omkostninger og frigøre IT / DevOps personale til at fokusere på arbejde, der tilføjer business værdien ved at flytte dine skyen administrationsopgaver skal køres automatisk med Automation Azure.


## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Hvordan kan Azure automatisering med at administrere Azure skytjenester?

Azure-skytjenester kan administreres i Azure Automation ved hjælp af PowerShell-cmdlet'er, der er tilgængelige i [Azure PowerShell værktøjer](https://msdn.microsoft.com/library/azure/jj156055.aspx). Azure automatisering har disse skyen service PowerShell-cmdlet'er tilgængelige af feltet, så du kan udføre alle skyen service management opgaver i tjenesten. Du kan også par disse cmdlet'er i Azure Automation med cmdletter for andre Azure tjenester til at automatisere komplekse opgaver på tværs af Azure tjenester og -3 part systemer.

Nogle eksempler på brug af Azure automatisering til at administrere Azure Cloud Services omfatter:

- [Uafbrudt installation af en skybaseret tjeneste, når cscfg eller cspkg opdateres i Azure Blob-lager](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
- [At du har genstartet skybaseret tjeneste forekomster parallelt ét opgradering domæne ad gangen](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært det grundlæggende omkring af Azure automatisering, og hvordan det kan bruges til at administrere Azure-skytjenester, skal du følge disse links for at få flere oplysninger om Azure automatisering.

- [Azure automatisering oversigt](../automation/automation-intro.md)
- [Min første runbook](../automation/automation-first-runbook-graphical.md)
- [Azure automatisering learning kort](https://azure.microsoft.com/documentation/learning-paths/automation/)
 
