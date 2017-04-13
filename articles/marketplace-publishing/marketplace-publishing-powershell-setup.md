<properties
   pageTitle="Konfigurere PowerShell til at oprette en VM på Marketplace | Microsoft Azure"
   description="Vejledning til konfiguration af Azure PowerShell og bruge den som en valgfri proces dataflow for at oprette VM billeder skal installeres til, og sælge på Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/04/2016"
   ms.author="hascipio"/>

# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Konfigurere Azure PowerShell til at oprette et tilbud til Azure Marketplace
Du kan finde detaljerede oplysninger om, hvordan du konfigurerer PowerShell i Azure, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md). En enkelt metode er at bruge metoden certifikat, som er hentet og importerer et certifikat, der bruges til godkendelse. Brug cmdlet'en **Get-AzurePublishSettingsFile** for at få det nødvendige certifikat skal. Gem filen, når du bliver bedt om. Brug cmdlet'en **Importér AzurePublishSettingsFile** for at importere certifikatet til en PowerShell-session.

Bruge **Sæt AzureSubscription** og **Vælg AzureSubscription** cmdletter for at konfigurere og gemme de almindelige Microsoft Azure abonnementsindstillinger PowerShell-session:

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

Den første kommando knytter en standardkonto lager til abonnementet (bruges til visse VM klargøring handlinger).  Andet gør abonnementet (genkendes af andre cmdletter) for det aktuelle sprog.

## <a name="see-also"></a>Se også
- [Introduktion: hvordan du publicerer et tilbud til Azure Marketplace](marketplace-publishing-getting-started.md)
- [Oprette et virtuelt billede på Marketplace](marketplace-publishing-vm-image-creation.md)
