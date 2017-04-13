<properties
   pageTitle="Gæst OS familie 1 lukning af Bemærk | Microsoft Azure"
   description="Indeholder oplysninger om når Azure gæst OS familie 1 pension er der sket og hvordan du kan finde ud af, om du berøres"
   services="cloud-services"
   documentationCenter="na"
   authors="raiye"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/24/2016"
   ms.author="raiye"/>



# <a name="guest-os-family-1-retirement-notice"></a>Gæst OS familie 1 lukning af meddelelse

Lukning af OS familie 1 blev først offentliggjort 1 juni 2013.

**2 Sept 2014** Operativsystemet Azure gæst (gæst OS) familie 1.x, som er baseret på Windows Server 2008-operativsystemet, officielt er udgået. Alle forsøg på at installere nye tjenester eller opgradere eksisterende-tjenester med familie 1 mislykkes med en fejlmeddelelse, der informerer dig om, at gæst OS familie 1 findes længere.

**3 november 2014** Udvidet support for gæst OS familie 1 afsluttet, og det er fuldt går på pension. Alle tjenester stadig på familie 1 vil blive påvirket. Vi holder disse tjenester på en hvilken som helst tidspunkt. Der er ingen garanti dine tjenester fortsætter med at køre, medmindre du manuelt opgradere dem dig selv.

Hvis du har yderligere spørgsmål, kan du besøge [Cloud Services fora](http://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) eller [kontakte support til Azure](https://azure.microsoft.com/support/options/).




## <a name="are-you-affected"></a>Du påvirkes?

Cloud Services påvirkes, hvis et af følgende gælder:

1. Du har en værdi på "osFamily ="1"eksplicit er angivet i filen ServiceConfiguration.cscfg for skybaseret tjeneste.
2. Du har ikke en værdi for osFamily eksplicit er angivet i filen ServiceConfiguration.cscfg for skybaseret tjeneste. I øjeblikket bruger systemet standardværdien for "1" i dette tilfælde.
3. Portalen Azure klassisk vises dit operativsystem, som gæst family værdi som "Windows Server 2008".

Hvis du vil finde, hvilke af dine skytjenester kører hvilke OS familie, kan du køre scriptet under i Azure PowerShell, selvom du først [konfigurere Azure PowerShell](../powershell-install-configure.md) . Yderligere oplysninger om scriptet, se [Azure gæst OS familie 1 slutningen af liv: juni 2014](http://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx). 

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Din skytjenester der påvirkes af lukning af OS familie 1, hvis kolonnen osFamily i script output er tom eller indeholder værdien "1".

## <a name="recommendations-if-you-are-affected"></a>Anbefalinger, hvis du påvirkes

Vi anbefaler, at du overføre dine skybaseret tjeneste roller til en af de understøttede gæst OS familier:

**Gæst OS family 4.x** -Windows Server 2012 R2 *(anbefales)*

1. Sørg for, at dit program bruger SDK 2.1 eller nyere med .NET framework 4.0, 4.5 eller 4.5.1.
2. Angiv egenskaben osFamily til "4" i filen ServiceConfiguration.cscfg, og geninstaller skybaseret tjeneste.


**Gæst OS family 3.x** -Windows Server 2012

1. Sørg for, at dit program bruger SDK 1,8 eller nyere med .NET framework 4.0 eller 4.5.
2. Angiv egenskaben osFamily til "3" i filen ServiceConfiguration.cscfg, og geninstaller skybaseret tjeneste.


**Gæst OS family 2.x** -Windows Server 2008 R2

1. Sørg for, at dit program bruger SDK 1.3 og derover med .NET framework 3.5 eller 4.0.
2. Angiv egenskaben osFamily til "2" i filen ServiceConfiguration.cscfg, og geninstaller skybaseret tjeneste.


## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Udvidet Support for gæst OS familie 1 afsluttet 3 november 2014
Skytjenester på gæst OS familie 1 understøttes ikke længere. Sørg for at overflytte fra familie 1 så tidligt som muligt for at undgå tjenesteforstyrrelse.  

## <a name="next-steps"></a>Næste trin
Gennemse de nyeste [Gæst OS versioner](cloud-services-guestos-update-matrix.md).
