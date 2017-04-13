### <a name="app-service-plan"></a>App-serviceaftale

Opretter serviceaftale som vært for WebApp. Du angiver navnet på planen gennem parameteren **hostingPlanName** . Placeringen af planen, er den samme placering, der bruges til ressourcegruppen. Priser niveau og arbejder størrelsen er angivet i parametrene **sku** og **workerSize**

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "[parameters('sku')]",
        "capacity": "[parameters('workerSize')]"
      },
      "properties": {
        "name": "[parameters('hostingPlanName')]"
      }
    },

