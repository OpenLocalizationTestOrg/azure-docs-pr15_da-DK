Du kan kontrollere, at forbindelsen blev bekræftet ved hjælp af den `Get-AzureRmVirtualNetworkGatewayConnection` cmdlet, med eller uden `-Debug`. 

1. Brug følgende cmdlet eksempel konfiguration af værdierne, der svarer til din egen. Hvis du bliver bedt om det, kan du vælge 'A' for at køre 'All'. I eksemplet `-Name` refererer til navnet på den forbindelse, du har oprettet og vil teste.

        Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG

2. Når cmdlet er færdig, kan du få vist værdierne. I eksemplet herunder viser forbindelsesstatus som 'Tilsluttet', og du kan se indgangs- og udgangspunkt byte.

        Body:
        {
          "name": "MyGWConnection",
          "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/connections/MyGWConnection",
          "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
            "virtualNetworkGateway1": {
              "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/virtualNetworkGa
        teways/vnetgw1"
            },
            "localNetworkGateway2": {
              "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/localNetworkGate
        ways/LocalSite"
            },
            "connectionType": "IPsec",
            "routingWeight": 10,
            "sharedKey": "abc123",
            "connectionStatus": "Connected",
            "ingressBytesTransferred": 33509044,
            "egressBytesTransferred": 4142431
          }