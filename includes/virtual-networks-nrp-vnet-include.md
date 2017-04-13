## <a name="virtual-network"></a>Virtuelt netværk
Virtuelle netværk (VNET) og undernet ressourcer hjælpe dig med at definere sikkerhed rammen for arbejdsbelastninger, som kører i Azure. En VNet er er betegnet ved en samling af adresse mellemrum, defineret som CIDR dokumentkomponenter. 

>[AZURE.NOTE] Administratorerne kender CIDR notation. Hvis du ikke kender med CIDR, [vide mere om den](http://whatismyipaddress.com/cidr).

![VNet med flere undernet](./media/resource-groups-networking/Figure4.png)

VNets indeholder følgende egenskaber.

|Egenskaben|Beskrivelse|Eksempelværdier|
|---|---|---|
|**addressSpace**|Samling af adresse præfikser på enheder, der udgør VNet i CIDR notation|192.168.0.0/16|
|**undernet**|Samling af undernet, der udgør VNet|Se [undernet](#Subnets) nedenfor.|
|**IP-adresse**|IP-adresse, der er tildelt til et objekt. Dette er en skrivebeskyttet egenskab.|104.42.233.77|

### <a name="subnets"></a>Undernet
Et undernet er en underordnet ressource af en VNet, og gør det muligt at definere segmenter af adresse mellemrum i en CIDR blok, ved hjælp af IP-adressepræfikser. Netværkskort kan føjes til undernet og tilsluttet FOS, give forbindelse til forskellige arbejdsbelastninger.

Undernet indeholder følgende egenskaber. 

|Egenskaben|Beskrivelse|Eksempelværdier|
|---|---|---|
|**addressPrefix**|Enkelt adressepræfiks, der udgør undernet i CIDR notation|192.168.1.0/24|
|**networkSecurityGroup**|NSG anvendt til undernettet|Se [NSGs](#Network-Security-Group)|
|**routeTable**|Distribuere tabel, der er anvendt til undernettet|Se [UDR](#Route-table)|
|**ipConfigurations**|Samling af IP-configruation-objekter, der bruges af netværkskort tilsluttet til undernettet|Se [UDR](#Route-table)|


Eksempel VNet i JSON-format:

    {
        "name": "TestVNet",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/virtualNetworks",
        "location": "westus",
        "tags": {
            "displayName": "VNet"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "addressSpace": {
                "addressPrefixes": [
                    "192.168.0.0/16"
                ]
            },
            "subnets": [
                {
                    "name": "FrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "networkSecurityGroup": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "routeTable": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
                        },
                        "ipConfigurations": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
                            },
                            ...]
                    }
                },
                ...]
        }
    }

### <a name="additional-resources"></a>Yderligere ressourcer

- Få mere at vide om [VNet](../articles/virtual-network/virtual-networks-overview.md).
- Læs [REST-API referencedokumentation](https://msdn.microsoft.com/library/azure/mt163650.aspx) for VNets.
- Læs [REST-API referencedokumentation](https://msdn.microsoft.com/library/azure/mt163618.aspx) for undernet.