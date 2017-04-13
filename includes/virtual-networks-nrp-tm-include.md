## <a name="traffic-manager-profile"></a>Trafik Manager profil

Aktivere DNS-distribution til slutpunkter i Azure og uden for Azure, trafik overordnet og dets underordnede slutpunkt ressource. Sådan trafik fordeling er underlagt routing politik metoder. Trafik manager kan også slutpunkt sundhed overvåges, og trafik omdirigeres korrekt baseret på tilstanden for et slutpunkt. 

| Egenskaben | Beskrivelse |
|---|---|
|**trafficRoutingMethod**| mulige værdier er *ydeevne*, *vægtet*og *prioritet* | 
| **DNS** | Fulde Domænenavn til profilen | 
| **Protokol** | overvågning protokol, er mulige værdier *HTTP* og *HTTPS*|
| **Port** | overvågning port |  
| **Sti** | overvågning sti |
| **Slutpunkter** |  objektbeholder til slutpunkt ressourcer | 

### <a name="endpoint"></a>Første eller sidste ark 

Et slutpunkt er en underordnet ressource i en trafik Manager profil. Den repræsenterer en tjeneste eller web slutpunkt til hvilke bruger trafik distribueres baseret på den konfigurerede politik i trafik Manager profil ressourcen. 

| Egenskaben | Beskrivelse | 
|---|---| 
| **Type** |  typen af slutpunktet, mulige værdier er *Azure slutningen Peg*, *Eksterne slutpunkt*og *Indlejrede slutpunkt* | 
| **targetResourceId** |  offentlige IP-adressen på en tjeneste eller web slutpunkt. Det kan være et Azure eller eksterne slutpunkt. | 
| **Vægt** | slutpunkt vægt, der bruges i trafik administration. | 
| **Prioritet** | prioriteten af slutpunktet, bruges til at definere en failover handling |

Eksempel på trafik Manager i Json-format: 


        {
            "apiVersion": "[variables('tmApiVersion')]",
            "type": "Microsoft.Network/trafficManagerProfiles",
            "name": "VMEndpointExample",
            "location": "global",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '0')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '1')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '2')]",
            ],
            "properties": {
                "profileStatus": "Enabled",
                "trafficRoutingMethod": "Weighted",
                "dnsConfig": {
                    "relativeName": "[parameters('dnsname')]",
                    "ttl": 30
                },
                "monitorConfig": {
                    "protocol": "http",
                    "port": 80,
                    "path": "/"
                },
                "endpoints": [
                    {
                        "name": "endpoint0",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 0))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint1",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 1))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint2",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 2))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    }
                ]
            }
        }

 
## <a name="additional-resources"></a>Yderligere ressourcer

Læs [REST-API dokumentation til trafik Manager](https://msdn.microsoft.com/library/azure/mt163664.aspx) kan finde flere oplysninger.
