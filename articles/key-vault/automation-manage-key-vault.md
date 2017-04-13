<properties
    pageTitle="Administrere Azure-tasten samling ved hjælp af Azure automatisering | Microsoft Azure"
    description="Få mere at vide om, hvordan Azure Automation service kan bruges til at administrere Azure-tasten samling."
    services="Key-Vault, automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="magoedte;csand"/>

#<a name="managing-azure-key-vault-using-azure-automation"></a>Administrere Azure-tasten samling ved hjælp af Azure automatisering

Denne vejledning introducerer dig til Azure Automation service, og hvordan det kan bruges til at forenkle administration af dine nøgler og hemmeligheder i Azure-tasten samling.

## <a name="what-is-azure-automation"></a>Hvad er Azure automatisering?

[Azure automatisering](../automation/automation-intro.md) er en Azure service for at forenkle skyen administration ved hjælp af automatisering af områdeprocesser og ønskede tilstand konfiguration. Ved hjælp af Azure automatisering, manuel, kan gentages, køres lange og fejlbehæftede opgaver automatisere henblik på øget pålidelighed, effektivitet og klokkeslæt til værdi for din organisation.

Azure automatisering giver en høj grad af pålidelighed, let tilgængelige arbejdsproces udførelse af program, der skaleres til at opfylde dine behov. I Azure Automation kan processer sættes manuelt ved at 3 tredjepart systemer eller med planlagte intervaller så opgaver sker nøjagtigt, når det er nødvendigt.

Reducere funktionsdygtige omkostninger og frigøre IT og DevOps personale til at fokusere på arbejde, der tilføjer forretningsværdi ved at flytte dine skyen administrationsopgaver skal køres automatisk med Automation Azure.


## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Hvordan kan Azure automatisering med at administrere Azure-tasten samling?

Tasten samling kan administreres i Azure Automation ved hjælp af [AzureRM nøgle samling cmdletter] (https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) og [-cmdletter Azure klassisk nøgle samling](https://msdn.microsoft.com/library/azure/dn868052.aspx). Azure-modul til administration af klassiske nøgle samling findes automatisk i Azure automatisering, og du kan importere [AzureRM KeyVault modul](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) til Azure automatisering, så du kan udføre mange af dine nøgle samling administrationsopgaver i tjenesten. Du kan også par disse cmdlet'er i Azure Automation med cmdletter for andre Azure tjenester til at automatisere komplekse opgaver på tværs af Azure tjenester og -3 part-systemer.

Du kan udføre disse opgaver blandt andet med Azure-tasten samling cmdletter: 

- Oprette og konfigurere en vigtige samling
- Oprette eller importere en nøgle
- Oprette eller opdatere et hemmeligt
- Opdatere attributterne for en nøgle
- Få en nøgle eller hemmeligt
- Slette en nøgle eller hemmeligt

Her er nogle eksempler på bruge PowerShell til at administrere nøgle samling:  

* [Azure vigtige samling - Step by Step](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Opsætte og konfigurere en Azure vigtige samling](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)


## <a name="next-steps"></a>Næste trin

Nu hvor du har lært det grundlæggende omkring af Azure automatisering, og hvordan det kan bruges til at administrere Azure-tasten samling, skal du følge disse links for at få flere oplysninger om Azure automatisering.

* Se Azure automatisering [Introduktion selvstudium](../automation/automation-first-runbook-graphical.md).
* Se de [Azure nøgle samling PowerShell-scripts](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).
