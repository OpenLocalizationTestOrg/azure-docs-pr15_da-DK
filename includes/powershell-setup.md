<properties services="virtual-machines" title="Setting up PowerShell" authors="JoeDavies-MSFT" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm=""
   ms.workload="infrastructure"
   ms.date="05/12/2015"
   ms.author="rasquill" />

## <a name="setting-up-powershell"></a>Konfiguration af PowerShell

Før du kan bruge Azure PowerShell, skal du følge disse trin.

### <a name="verify-powershell-versions"></a>Bekræfte PowerShell versioner

Før du kan bruge Windows PowerShell, skal du have Windows PowerShell, Version 3.0 eller 4.0. For at finde versionen af Windows PowerShell, skal du skrive denne kommando kommandoprompt Windows PowerShell.

    $PSVersionTable

Du bør se nogenlunde sådan ud.

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2

Bekræft, at værdien af **PSVersion** er 3.0 eller 4.0. For at installere en kompatibel version skal du se [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) eller [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Du kan også aktivere Azure PowerShell version 0.8.0 eller nyere. Du kan se versionen af Azure PowerShell, som du har installeret med denne kommando i kommandoprompten Azure PowerShell.

    Get-Module azure | format-table version

Du bør se nogenlunde sådan ud.

    Version
    -------
    0.8.16.1

Se, [hvordan du installere og konfigurere Azure PowerShell](powershell-install-configure.md)instruktioner og et link til den nyeste version.


### <a name="set-your-azure-account-and-subscription"></a>Angive Azure-konto og abonnement

Hvis du ikke allerede har et Azure-abonnement, kan du aktivere din [MSDN abonnement fordele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller Log op til en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

Åbn en Azure PowerShell-kommandoprompten, og log på Azure med denne kommando.

    Add-AzureAccount

Hvis du har flere Azure abonnementer, du kan få vist dine Azure abonnementer med denne kommando.

    Get-AzureSubscription

Du får vist følgende type af oplysninger:

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName : 
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Du kan angive det aktuelle Azure abonnement ved at køre disse kommandoer i kommandoprompten Azure PowerShell. Erstatte alt i anførselstegn, herunder den < og > tegn med det rigtige navn.

    $subscr="<SubscriptionName from the display of Get-AzureSubscription>"
    Select-AzureSubscription -SubscriptionName $subscr -Current 

Finde flere oplysninger om Azure abonnementer og -konti, [Sådan: oprette forbindelse til dit abonnement](powershell-install-configure.md#Connect).
