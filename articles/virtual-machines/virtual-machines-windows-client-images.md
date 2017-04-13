<properties
   pageTitle="Ved hjælp af Windows-klientbilleder for Udviklingscenter/afprøve scenarier | Microsoft Azure"
   description="Hvordan du bruger Visual Studio abonnement fordele ved for at installere Windows 7/8/10 i Azure for Udviklingscenter/afprøve scenarier"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="iainfou"/>

# <a name="using-windows-client-in-azure-for-devtest-scenarios"></a>Brug af Windows-klient i Azure for Udviklingscenter/afprøve scenarier

Du kan bruge Windows 7, Windows 8 eller Windows 10 i Azure for Udviklingscenter/afprøve scenarier, hvis du har et passende Visual Studio (tidligere MSDN)-abonnement. I denne artikel beskrives krav skal man opfylde for, der kører Windows-klienten i Azure og brug af Azure galleriet billederne.


## <a name="subscription-eligibility"></a>Abonnement berettigelse
Aktive Visual Studio abonnenter (personer, der har købt en abonnementslicens til Visual Studio) kan bruge Windows-klienten til udvikling og testformål. Windows-klient kan bruges på din egen hardware og Azure virtuelle maskiner, der kører i alle typer Azure-abonnement. Windows-klient kan ikke installeres på eller bruges på Azure til normal daglig drift eller bruges af personer, ikke der er aktive Visual Studio abonnenter.

Gøre arbejdet lettere, har vi foretaget bestemte billeder i Windows 10 tilgængelig fra galleriet Azure i [berettiget Udviklingscenter/afprøve tilbyder](#eligible-offers). Visual Studio abonnenter i en hvilken som helst type tilbud kan også [en dækkende måde at forberede og oprette](virtual-machines-windows-prepare-for-upload-vhd-image.md) en 64-bit Windows 7, Windows 8 eller Windows 10 billede og derefter [overføre til Azure](virtual-machines-windows-upload-image.md). Brug forbliver begrænset til Udviklingscenter/test af aktive Visual Studio abonnenter.


## <a name="eligible-offers"></a>Gyldigt tilbud
Den følgende tabel indeholder oplysninger om tilbuddet id'er, der er berettiget til at installere Windows 10-galleriet Azure. Windows 10 billeder er kun synligt for de følgende tilbud. Visual Studio abonnenter, der skal køre Windows-klient i en anden tilbud type kræver, at du [en dækkende måde at forberede og oprette](virtual-machines-windows-prepare-for-upload-vhd-image.md) et 64-bit Windows 7, Windows 8 eller Windows 10 billede og [derefter overføre til Azure](virtual-machines-windows-upload-image.md).

| Tilbud navn | Tilbudsnummer | Tilgængelige klientbilleder |
|:-----------|:------------:|:-----------------------:|
| [Pay-As-You-Go Udviklingscenter/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)                          | 0023P | Windows 10 |
| [Visual Studio Enterprise (MPN) abonnenter](https://azure.microsoft.com/offers/ms-azr-0029p/)      | 0029P | Windows 10 |
| [Visual Studio Professional abonnenter](https://azure.microsoft.com/offers/ms-azr-0059p/)          | 0059P | Windows 10 |
| [Visual Studio Test Professional abonnenter](https://azure.microsoft.com/offers/ms-azr-0060p/)     | 0060P | Windows 10 |
| [Visual Studio Premium med MSDN (benefit)](https://azure.microsoft.com/offers/ms-azr-0061p/)       | 0061P | Windows 10 |
| [Visual Studio Enterprise abonnenter](https://azure.microsoft.com/offers/ms-azr-0063p/)            | 0063P | Windows 10 |
| [Visual Studio Enterprise (BizSpark) abonnenter](https://azure.microsoft.com/offers/ms-azr-0064p/) | 0064P | Windows 10 |
| [Virksomhed-Udviklingscenter/Test](https://azure.microsoft.com/ofers/ms-azr-0148p/)                              | 0148P | Windows 10 |


## <a name="check-your-azure-subscription"></a>Kontrollere abonnementet Azure
Hvis du ikke kender dit tilbud-ID, kan du hente den via portalen Azure eller portalen konto.

Abonnement tilbud-ID er noteret på bladet 'Abonnementer' i portalen Azure:

![Tilbud-ID-oplysninger fra Azure-portalen](./media/virtual-machines-windows-client-images/offer_id_azure_portal.png) 

Du kan også få vist tilbud-ID'ET fra ['Abonnementer' fane](http://account.windowsazure.com/Subscriptions) i portalen Azure-konto:

![Tilbud-ID-oplysninger fra portalen Azure-konto](./media/virtual-machines-windows-client-images/offer_id_azure_account_portal.png) 


## <a name="next-steps"></a>Næste trin
Du kan nu implementere din FOS ved hjælp af [PowerShell](virtual-machines-windows-ps-create.md), [ressourcestyring skabeloner](virtual-machines-windows-ps-template.md)eller [Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
