<properties
    pageTitle="Azure Government Services | Microsoft Azure"
    description="Oplysninger om administration af dit abonnement i Azure Government"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="zakramer"
    manager="liki"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/21/2016"
    ms.author="zakramer" />


#  <a name="managing-and-connecting-to-your-subscription-in-azure-government"></a>Administrere og oprette forbindelse til dit abonnement i Azure Government

Azure Government har entydige URL-adresser og slutpunkter til administration af dit miljø. Det er vigtigt at bruge de rette forbindelser til at administrere dit miljø via portalen eller PowerShell. Når du har forbindelse til Azure Government-miljø, fungerer handlingerne normal til administration af en tjeneste, hvis komponenten er blevet installeret.

## <a name="connecting-via-the-portal"></a>Oprette forbindelse via portalen
Portalen er den primære måde, de fleste personer, der oprettes forbindelse til Azure Government.  For at oprette forbindelse, skal du gå til portalen på [https://portal.azure.us](https://portal.azure.us).  Kan få adgang til den ældre version af Azure portalen via [https://manage.windowsazure.us](https://manage.windowsazure.us).

Abonnementer kan oprettes for din konto ved at oprette forbindelse til [https://account.windowsazure.us](https://account.windowsazure.us).

## <a name="connecting-via-powershell"></a>Oprette forbindelse via PowerShell

Om du bruger Azure PowerShell til at administrere et stort abonnement via script eller access-funktioner, der ikke er tilgængelige for øjeblikket i portalen Azure du har brug for at oprette forbindelse til Azure Government i stedet for Azure offentlige.  Hvis du har brugt PowerShell i Azure offentlige, er det hovedsageligt på samme måde.  Forskellene mellem Azure Government er:

+ Oprette forbindelse til din konto
+ Områdenavne

>[AZURE.NOTE] Hvis du ikke har brugt PowerShell endnu, kan du se [Introduktion til Azure PowerShell](../powershell-install-configure.md).

Når du starter PowerShell, har du fortælle Azure PowerShell til at oprette forbindelse til Azure Government ved at angive en parameter miljø.  Parameteren sikrer, at PowerShell forbindelse til de korrekte slutpunkter.  Samling af slutpunkter bestemmes, når du forbinder log på din konto.  Forskellige API'er kræver forskellige versioner af parameteren miljø:

Forbindelsestype | Kommandoen
---|----
[Service Management](https://msdn.microsoft.com/library/dn708504.aspx) kommandoer | `Add-AzureAccount -Environment AzureUSGovernment`
[Ressourcestyring](https://msdn.microsoft.com/library/mt125356.aspx) kommandoer | `Add-AzureRmAccount -EnvironmentName AzureUSGovernment`
[Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) -kommandoer | `Connect-MsolService -AzureEnvironment UsGovernment`
[Azure Active Directory kommandoen v2](https://msdn.microsoft.com/library/azure/mt757189.aspx) | `Connect-AzureAD -AzureEnvironmentName AzureUSGovernment`

Du kan også bruge parameteren miljø, når du opretter forbindelse til en lagerplads konto ved hjælp af ny AzureStorageContext og angive AzureUSGovernment.

### <a name="determining-region"></a>Afgøre område

Når du har forbindelse, er der en ekstra forskel – de områder, der bruges til at målrette en tjeneste.  Hver Azure skyen har forskellige områder.  Du kan se dem på siden tilgængelighed.  Du bruger normalt område i parameteren placering til en kommando.

Der findes en produkter.  Azure Government områder skal være formateret anderledes end deres almindelige navne:

Almindelige navn | Kommandoen
---|----
USA Gov Virginia | USGov Virginia
Krigsskibe i USA Gov Iowa | Krigsskibe i USGov Iowa

>[AZURE.NOTE] Der er ikke plads mellem USA og Gov, når du bruger parameteren placering.

Hvis du vil nogensinde før at validere de tilgængelige områder i Azure Government, kan du køre følgende kommandoer og udskrive den aktuelle liste:

    Get-AzureLocation

Hvis du er vide mere om de tilgængelige miljøer på tværs af Azure, kan du køre:

    Get-AzureEnvironment

## <a name="next-steps"></a>Næste trin

Hvis du leder efter flere oplysninger, kan du se:

+ [PowerShell dokumenter på GitHub](https://github.com/Azure/azure-powershell)
+ [En trinvis instruktion på at oprette forbindelse til Ressourcestyring](https://blogs.msdn.microsoft.com/azuregov/2015/10/08/configuring-arm-on-azure-gc/)
+ [Azure PowerShell-dokumenter på MSDN](https://msdn.microsoft.com/library/mt619274.aspx)

Supplerende oplysninger og opdateringer abonnere på [Microsoft Azure Government Blog] (https://blogs.msdn.microsoft.com/azuregov/)
