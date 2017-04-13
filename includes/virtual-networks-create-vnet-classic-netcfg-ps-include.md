## <a name="how-to-create-a-vnet-using-a-network-config-file-from-powershell"></a>Sådan oprettes en VNet ved hjælp af et netværk config fil fra PowerShell

Azure bruger en XML-fil til at definere alle tilgængelige VNets til et abonnement. Du kan hente denne fil, og rediger den for at ændre eller slette eksisterende VNets og oprette nye. I dette dokument, kan du se, hvordan du hente denne fil, som kaldes netværk konfiguration (eller netcgf)-fil, og rediger den for at oprette en ny VNet. Markér [Azure virtuelt netværk konfiguration skema](https://msdn.microsoft.com/library/azure/jj157100.aspx) for at få flere oplysninger om konfigurationsfil netværk.

Følg nedenstående trin for at oprette en VNet ved hjælp af en netcfg fil ved hjælp af PowerShell.

1. Hvis du aldrig har brugt Azure PowerShell, se, [hvordan du installere og konfigurere Azure PowerShell](../articles/powershell-install-configure.md) , og følg instruktionerne helt til end for at logge på Azure, og vælg dit abonnement.
2. Brug cmdlet'en **Get-AzureVnetConfig** til at hente konfigurationsfil netværk ved at køre kommandoen nedenfor fra konsollen Azure PowerShell. 

        Get-AzureVNetConfig -ExportToFile c:\NetworkConfig.xml

    Forventet afgang:

        XMLConfiguration                                                                                                     
        ----------------                                                                                                     
        <?xml version="1.0" encoding="utf-8"?>...  

3. Åbn den fil, du gemte i trin 2 ovenfor ved hjælp af en XML- eller tekst editor program, og se efter den **<VirtualNetworkSites>** element. Hvis du har nogen netværk, der allerede har oprettet, hver netværk vises som sin egen **<VirtualNetworkSite>** element.
4. For at oprette det virtuelle netværk, der er beskrevet i dette scenarie skal du tilføje følgende XML lige under det **<VirtualNetworkSites>** element:

        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>

9.  Gemme konfigurationsfil netværk.
10. Brug cmdlet'en **Set-AzureVnetConfig** til at overføre konfigurationsfil netværk ved at køre kommandoen nedenfor fra konsollen Azure PowerShell. Bemærk output under kommandoen, skal du se afsnittet **lykkedes** under **OperationStatus**. Hvis det vil sige ikke er tilfældet, kontrollere XML-filen for fejl.

        Set-AzureVNetConfig -ConfigurationPath c:\NetworkConfig.xml

    Her er den forventede afgang for kommandoen ovenfor:

        OperationDescription OperationId                          OperationStatus
        -------------------- -----------                          ---------------
        Set-AzureVNetConfig  49579cb9-3f49-07c3-ada2-7abd0e28c4e4 Succeeded 
    
11. Brug cmdlet'en **Get-AzureVnetSite** til at kontrollere, at det nye netværk blev tilføjet ved at køre kommandoen nedenfor fra konsollen Azure PowerShell. 

        Get-AzureVNetSite -VNetName TestVNet

    Her er den forventede afgang for kommandoen ovenfor:

        AddressSpacePrefixes : {192.168.0.0/16}
        Location             : Central US
        AffinityGroup        : 
        DnsServers           : {}
        GatewayProfile       : 
        GatewaySites         : 
        Id                   : b953f47b-fad9-4075-8cfe-73ff9c98278f
        InUse                : False
        Label                : 
        Name                 : TestVNet
        State                : Created
        Subnets              : {FrontEnd, BackEnd}
        OperationDescription : Get-AzureVNetSite
        OperationId          : 3f35d533-1f38-09c0-b286-3d07cd0904d8
        OperationStatus      : Succeeded