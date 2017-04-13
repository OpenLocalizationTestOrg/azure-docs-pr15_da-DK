<properties
   pageTitle="Slette en Azure klynge og dens ressourcer | Microsoft Azure"
   description="Lær, hvordan helt slette en tjeneste-strukturen klynge enten slette ressourcegruppen, der indeholder klyngen eller ved at slette selektivt ressourcer."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>

# <a name="delete-a-service-fabric-cluster-on-azure-and-the-resources-it-uses"></a>Slette en tjeneste-strukturen klynge på Azure og de ressourcer, der bruges

En tjeneste-strukturen klynge består af mange andre Azure ressourcer ud over klyngeressourcen sig selv. For at slette en tjeneste-strukturen klynge, skal du også slette alle de ressourcer, der er foretaget af fuldstændigt.
Har du to muligheder: enten slette ressourcegruppen, der er klyngen i (som sletter klyngeressourcen og andre ressourcer i ressourcegruppen) eller slette specifikt klyngeressourcen og den tilknyttede ressourcer (men ikke andre ressourcer i ressourcegruppen).

>[AZURE.NOTE] Slette den klynge ressource **ikke** slette alle de andre ressourcer, som din tjeneste strukturen klynge består af.

## <a name="delete-the-entire-resource-group-rg-that-the-service-fabric-cluster-is-in"></a>Slette hele ressourcegruppen (indbyggede RG), der er Service-strukturen klynge i

Dette er den nemmeste måde at sikre, at du sletter alle de ressourcer, der er knyttet til din klynge, herunder ressourcegruppen. Du kan slette ressourcegruppen ved hjælp af PowerShell eller via Azure-portalen. Hvis din ressourcegruppe indeholder ressourcer, der ikke er relateret til tjenesten strukturen klynge, kan du slette bestemte ressourcer.

### <a name="delete-the-resource-group-using-azure-powershell"></a>Slette ressourcegruppen ved hjælp af Azure PowerShell

Du kan også slette ressourcegruppen ved at køre følgende Azure PowerShell-cmdletter. Sørg for, at Azure PowerShell 1.0 eller nyere er installeret på computeren. Hvis du ikke har gjort det tidligere, skal du følge trinnene beskrevet i [hvordan du installerer og konfigurerer Azure PowerShell.](../powershell-install-configure.md)

Åbne en PowerShell-vinduet, og Kør følgende cmdletter PS:

```powershell
Login-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

Du får en meddelelse om at bekræfte sletningen, hvis du ikke har brugt den *-kraft* indstilling. På bekræftelse slettes den indbyggede RG og alle de ressourcer, den indeholder.

### <a name="delete-a-resource-group-in-the-azure-portal"></a>Slette en ressourcegruppe i portalen Azure  

1. Log på [Azure-portalen](https://portal.azure.com).
2. Gå til den tjeneste strukturen klynge, du vil slette.
3. Klik på ressourcen gruppens navn på siden klynge essentials.
4. Dette viser siden **Ressource gruppe Essentials** .
5. Klik på **Slet**.
6. Følg vejledningen på siden for at fuldføre sletningen af ressourcegruppen.

![Ressourcegruppe Slet][ResourceGroupDelete]


## <a name="delete-the-cluster-resource-and-the-resources-it-uses-but-not-other-resources-in-the-resource-group"></a>Slette klyngeressourcen og de ressourcer, den bruger, men ikke andre ressourcer i ressourcegruppen

Hvis din ressourcegruppe indeholder kun de ressourcer, der er relateret til den tjeneste strukturen klynge, du vil slette, er det nemmere at slette hele ressourcegruppen. Hvis du vil slette selektivt ressourcer én ad gangen i din ressourcegruppe, følg derefter disse trin.

Hvis du har installeret din klynge ved hjælp af portalen eller bruge en af Service strukturen ressourcestyring skabelonerne fra skabelongalleriet på, er alle de ressourcer, der bruger klyngen mærket med de følgende to mærker. Du kan bruge dem til at beslutte, hvilke ressourcer, du vil slette.

***Mærke #1:*** Nøgle = clusterName, værdi = 'navnet på klyngen'

***Mærke #2:*** Nøgle = resourceName, værdi = ServiceFabric

### <a name="delete-specific-resources-in-the-azure-portal"></a>Slette bestemte ressourcer i portalen Azure

1. Log på [Azure-portalen](https://portal.azure.com).
2. Gå til den tjeneste strukturen klynge, du vil slette.
3. Gå til **Indstillinger for alle** på bladet essentials.
4. Klik på **mærker** under **Ressourcestyring** i bladet indstillinger.
5. Klik på en af **mærker** i bladet mærker til at få en liste over alle ressourcerne med mærket.

    ![Ressource-mærker][ResourceTags]

6. Når du har på listen over ressourcer, der er mærket, klik på hver af ressourcerne, og slet dem.

    ![Mærket ressourcer][TaggedResources]

### <a name="delete-the-resources-using-azure-powershell"></a>Slette de ressourcer, ved hjælp af Azure PowerShell

Du kan slette ressourcerne, én ad gangen ved at køre følgende Azure PowerShell-cmdletter. Sørg for, at Azure PowerShell 1.0 eller nyere er installeret på computeren. Hvis du ikke har gjort det tidligere, skal du følge trinnene beskrevet i [hvordan du installerer og konfigurerer Azure PowerShell.](../powershell-install-configure.md)

Åbne en PowerShell-vinduet, og Kør følgende cmdletter PS:

```powershell
Login-AzureRmAccount
```
For hver af ressourcerne, vil du slette, skal du køre følgende:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

Hvis du vil slette klyngeressourcen, skal du køre følgende:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## <a name="next-steps"></a>Næste trin
Læse følgende for at se også om opgradering af en klynge og partitionering tjenester:

- [Få mere at vide om klynge opgraderinger](service-fabric-cluster-upgrade.md)
- [Få mere at vide om partitionering med høj sikkerhed tjenester til maksimale skala](service-fabric-concepts-partitioning.md)


<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG
