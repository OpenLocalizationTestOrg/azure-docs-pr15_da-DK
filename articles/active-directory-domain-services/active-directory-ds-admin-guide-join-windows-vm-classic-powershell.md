<properties
    pageTitle="Azure Active Directory-domænetjenester: Vejledning til Administration | Microsoft Azure"
    description="Deltage i en Windows virtuel maskine til en administreret domæne ved hjælp af Azure PowerShell og klassisk implementeringsmodel."
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="maheshu"/>


# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-powershell"></a>Deltage i en Windows Server virtuel maskine til en administreret domæne ved hjælp af PowerShell

> [AZURE.SELECTOR]
- [Azure klassisk portal - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

> [AZURE.IMPORTANT] Azure har to forskellige installation modeller til oprettelse og arbejde med ressourcer: [ressourcestyring og klassisk](../resource-manager-deployment-model.md). Denne artikel omhandler ved hjælp af den klassiske implementeringsmodel. Azure AD-domænetjenester understøtter i øjeblikket ikke ressourcestyring modellen.

Disse trin viser dig, hvordan du tilpasser et sæt Azure PowerShell-kommandoer, som opretter og konfigurerer på forhånd en Windows-baseret Azure virtuel maskine ved hjælp af en dokumentkomponent fremgangsmåde. Disse trin hjælpe dig med at opbygge en Windows-baseret Azure virtuel maskine og føje den til en administreret Azure AD Domain Services-domæne.

Disse trin skal du følge en Udfyld-i-feltet-tomme fremgangsmåde til oprettelse af Azure PowerShell kommandosæt. Denne metode kan være nyttigt, hvis du er ny bruger af PowerShell, eller du gerne vil vide, hvilke værdier du skal angive for vellykket konfiguration. Avanceret PowerShell-brugere kan få kommandoerne og erstatte deres egne værdier til variablerne (de linjer, der begynder med "$").

Hvis du allerede har gjort det, kan du bruge instruktionerne i [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) til at installere Azure PowerShell på din lokale computer. Åbn en kommandoprompt med Windows PowerShell.

## <a name="step-1-add-your-account"></a>Trin 1: Tilføje din konto

1. Skriv **Tilføj AzureAccount** på PowerShell-prompt, og klik på **Enter**.
2. Skriv den mailadresse, der er knyttet til abonnementet Azure, og klik på **Fortsæt**.
3. Skriv i feltet adgangskode for kontoen.
4. Klik på **Log på**.

## <a name="step-2-set-your-subscription-and-storage-account"></a>Trin 2: Konfigurere dit abonnement og lagerplads konto

Angiv dine Azure-abonnement og lagerplads konto ved at køre disse kommandoer i kommandoprompten Windows PowerShell. Erstatte alt i anførselstegn, herunder den < og > tegn med de korrekte navne.

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Du kan få det korrekte Abonnementsnavnet fra egenskaben SubscriptionName for output af kommandoen **Get-AzureSubscription** . Du kan få kontonavn korrekte lager fra egenskaben etiket for output af kommandoen **Get-AzureStorageAccount** , når du har kørt kommandoen **Vælg AzureSubscription** .


## <a name="step-3-step-by-step-walkthrough---provision-the-virtual-machine-and-join-it-to-the-managed-domain"></a>Trin 3: Trinvise gennemgang - Klargør den virtuelle maskine og deltage i den administrerede domænet
Her er den tilsvarende Azure PowerShell-kommando, der er indstillet til at oprette denne virtuelt med tomme linjer mellem hver blok for læsbarhed.

Angive oplysninger om Windows virtuelt skal klargøres.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

Se [virtuelt og skyen Service størrelser til Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx)i InstanceSize værdier for D-, DS- eller G-serien virtuelle maskiner.

Indeholder oplysninger om administrerede domænet.

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

Angiv navnet på skytjenesten.

    $svcname="Contoso100-test"

Angiv navnet på det virtuelle netværk, som skal være tilsluttet VM. Sørg for, at AAD DS administrerede domænet er tilgængelig i denne virtuelle netværk.

    $vnetname="MyPreviewVnet"

Vælg VM billedet skal bruges til at klargøre VM.

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

Konfigurere VM - VM sætnavn, forekomst størrelse og billede, der skal bruges.

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Hente lokale administratorrettigheder VM. Vælg en stærk lokale administratoradgangskode.

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

Få legitimationsoplysninger for en brugerkonto, der hører til ' AAD DC' administratorgruppen for at kunne tilslutte VM til administrerede domænet. Angiv ikke domænenavnet – for eksempel i vores eksempel, vi angive "bob" som brugernavnet.

    $domainadmincred=Get-Credential –Message "Now type the name (DO NOT INCLUDE THE DOMAIN) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

Konfigurere VM – Angiv domæne joinforbindelse kravet & nødvendige legitimationsoplysninger.

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

Angive et undernet for VM.

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

Valgfrit: Peg på IP-adressen på domænet. Hvis du indstiller Azure AD-domænetjenester administrerede domæne skal være DNS-servere for det virtuelle netværk IP-adresser, er dette trin er ikke påkrævet.

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

Nu klargøre Windows VM medlem af et domæne.

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="script-to-provision-a-windows-vm-and-automatically-join-it-to-an-aad-domain-services-managed-domain"></a>Script til at klargøre en Windows-VM og automatisk slutte den til en administreret AAD Domain Services-domæne
Dette PowerShell kommandosæt opretter en virtuel maskine til en line of business-server, som:

- Bruger Windows Server 2012 R2 Datacenter billede.
- Er en ekstra small virtuel maskine.
- Har navnet contoso-test.
- Automatisk er domæne tilsluttet contoso100 administrerede domænet.
- Føjes til det samme virtuelle netværk som administrerede domænet.

Her er den fulde eksempel på et script til at oprette den Windows virtuelle maskine og tilslutte den automatisk til Azure AD-domænetjenester administrerede domænet.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

    $svcname="Contoso100-test"
    $vnetname="MyPreviewVnet"

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

    $domainadmincred=Get-Credential –Message "Now type the name (not including the domain) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="related-content"></a>Relateret indhold
- [Azure AD-domænetjenester - Introduktion](./active-directory-ds-getting-started.md)

- [Administrere en administreret Azure AD Domain Services-domæne](./active-directory-ds-admin-guide-administer-domain.md)
