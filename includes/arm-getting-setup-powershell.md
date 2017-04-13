## <a name="setting-up-powershell-for-resource-manager-templates"></a>Konfiguration af PowerShell i ressourcestyring skabeloner

Før du kan bruge Azure PowerShell med Ressourcestyring, skal du have højre Windows PowerShell og Azure PowerShell versioner.

### <a name="verify-powershell-versions"></a>Bekræfte PowerShell versioner

Kontrollér, at du har Windows PowerShell, version 3.0 eller 4.0. For at finde versionen af Windows PowerShell, skal du skrive denne kommando kommandoprompt Windows PowerShell.

    $PSVersionTable

Du får vist følgende type af oplysninger:

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2


Bekræft, at værdien af **PSVersion** er 3.0 eller 4.0. Hvis ikke, skal du se [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) eller [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

### <a name="set-your-azure-account-and-subscription"></a>Angive Azure-konto og abonnement

Hvis du ikke allerede har et Azure-abonnement, kan du aktivere din [MSDN abonnement fordele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller Log op til en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

Åbn en Azure PowerShell-kommandoprompten, og log på Azure med denne kommando.

    Login-AzureRmAccount

Hvis du har flere Azure abonnementer, du kan få vist dine Azure abonnementer med denne kommando.

    Get-AzureRmSubscription

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

    $subscr="<SubscriptionName from the display of Get-AzureRmSubscription>"
    Select-AzureRmSubscription -SubscriptionName $subscr -Current

Finde flere oplysninger om Azure abonnementer og -konti, [Sådan: oprette forbindelse til dit abonnement](powershell-install-configure.md#Connect).
