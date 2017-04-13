<properties
   pageTitle="Tilpasse en tjeneste-strukturen klynge ind eller ud | Microsoft Azure"
   description="Tilpasse en tjeneste-strukturen klynge ind eller ud til at matche behov ved at angive automatisk skalering regler for hvert node type/VM skala sæt. Tilføje eller fjerne noder til en tjeneste-strukturen klynge"
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


# <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules"></a>Tilpasse en tjeneste-strukturen klynge ind eller ud ved hjælp af regler for automatisk skalering

Virtuelt skala sæt er en Azure Beregn ressource, som du kan bruge til at installere og administrere en samling af virtuelle maskiner som et sæt. Hver node af type, der er defineret i en tjeneste-strukturen klynge er konfigureret som en separat VM skala sæt. Hver nodetype kan derefter skaleres i eller ud af, uafhængigt af hinanden, har forskellige typer åbne porte og kan have forskellige kapacitet målepunkter. Læs mere om det i [tjenesten strukturen nodetypes](service-fabric-cluster-nodetypes.md) dokumentet. Da den Service-strukturen node-typer i din klynge er foretaget af VM skala angiver på backend-version, skal du konfigurere automatisk skalering regler for hvert node type/VM skala sæt.

>[AZURE.NOTE] Dit abonnement skal have nok kerner for at tilføje de nye VM'er, der udgør denne klynge. Der findes ingen model datavalidering i øjeblikket, så du får en installation tid fejl, hvis nogen af kvotebegrænsningerne ramme.

## <a name="choose-the-node-typevm-scale-set-to-scale"></a>Vælg node type/VM skalaen angivet til at skalere

Du er på nuværende tidspunkt ikke kunne angive automatisk skalering reglerne for VM skala sæt ved hjælp af portalen, så lad os bruge Azure PowerShell (1.0 +) til listen node-typer og derefter føje automatisk skalering regler til dem.

For at få på listen over VM skala sæt, der udgør din klynge skal du køre følgende cmdletter:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <VM Scale Set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevm-scale-set"></a>Angive automatisk skalering regler for sættet node type/VM skala

Hvis din klynge har flere node-typer, derefter Gentag dette for hver node typer/VM skala angiver, at du vil skalere (ind eller ud). Tage hensyn til antallet af knuder, du skal have, før du konfigurerer automatisk skalering. Det mindste antal noder, du har brug for den primære nodetype styres af niveauet pålidelighed, du har valgt. Læs mere om [pålidelighed](service-fabric-cluster-capacity.md).

>[AZURE.NOTE]  Skalering ned den primære node Skriv til mindre end den mindste tal gøre den ustabil klynge eller Flyt. Dette kan medføre tab af data for dine programmer og systemets tjenester.

Funktionen automatisk skalering styres i øjeblikket ikke af de belastning, som dine programmer kan rapporterer til tjenesten struktur. På nuværende tidspunkt automatisk-skalaen du får kun drives af ydeevnen skalere tællere, der udledes ved hver af VM så sæt forekomster.  

Følg disse instruktioner [til konfiguration af automatisk skalering for hvert VM skala sæt](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

>[AZURE.NOTE] Medmindre din node af type har en holdbarhed niveau af guld eller Silver i en skala ned scenarie skal du ringe [Fjern ServiceFabricNodeState cmdlet](https://msdn.microsoft.com/library/azure/mt125993.aspx) med nodenavnet relevante.

## <a name="manually-add-vms-to-a-node-typevm-scale-set"></a>Tilføje FOS manuelt til et node type/VM skala

Følg eksempel/vejledningen i [skabelongalleriet Hurtig start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) for at ændre antallet af FOS i hver Nodetype. 

>[AZURE.NOTE] Tilføjelse af FOS tager tid, så du ikke forventer tilføjelser skal omgående. Så plan om at tilføje kapacitet godt i tid til at tillade i mere end 10 minutter før VM kapaciteten er tilgængelig for Kopier / service forekomster, der skal placeres.

## <a name="manually-remove-vms-from-the-primary-node-typevm-scale-set"></a>Manuelt fjerne FOS fra det primære node type/VM skala sæt

>[AZURE.NOTE] Tjenesten strukturerede system services kører i den primære nodetype i din klynge. Så bør aldrig lukke eller indskrænke antallet af forekomster i pågældende node-typer garanterer mindre end hvad pålidelighed niveauet. Se [oplysningerne om pålidelighed lag her](service-fabric-cluster-capacity.md). 

Du skal udføre følgende trin én VM forekomst ad gangen. Dette giver mulighed for systemets-tjenester (og dine tjenester uden problemer med høj sikkerhed) kan lukkes problemfrit VM forekomst, du vil fjerne og nye replikaer, der er oprettet på andre noder.

1. Køre [Deaktiver ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx) med hensigt 'RemoveNode' til at deaktivere noden du skal fjerne (den højeste forekomst i den pågældende nodetype).

2. Kør [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) at sikre dig, at noden har faktisk transitioned til deaktiveret. Hvis ikke, skal du vente, indtil noden er deaktiveret. Du kan ikke hurry dette trin.

2. Følg eksempel/vejledningen i [skabelongalleriet Hurtig start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) for at ændre antallet FOS efter en i pågældende Nodetype. Forekomsten af fjernet er den højeste VM forekomst. 

3. Gentag trin 1 til 3 efter behov, men aldrig indskrænke antallet af forekomster i de primære nodetyper mindre end hvad pålidelighed niveau garanterer. Se [oplysningerne om pålidelighed lag her](service-fabric-cluster-capacity.md). 

## <a name="manually-remove-vms-from-the-non-primary-node-typevm-scale-set"></a>Manuelt fjerne FOS fra ikke-primær node type/VM skala sæt

>[AZURE.NOTE] For en med høj sikkerhed-tjeneste, du har brug for et bestemt antal noder til at være altid op til vedligeholde tilgængelighed og bevare dine tjenestens tilstand. På den meget minimum skal du antallet af knuder lig med target replika angive antallet af partition/tjenesten. 

Du skal udføre følgende trin én VM forekomst ad gangen. Dette giver mulighed for systemets-tjenester (og dine tjenester uden problemer med høj sikkerhed) kan lukkes problemfrit på den VM-forekomst, du vil fjerne, og nye replikaer oprettet mere hvor.

1. Køre [Deaktiver ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx) med hensigt 'RemoveNode' til at deaktivere noden du skal fjerne (den højeste forekomst i den pågældende nodetype).

2. Kør [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) at sikre dig, at noden har faktisk transitioned til deaktiveret. Hvis du ikke vente, indtil noden er deaktiveret. Du kan ikke hurry dette trin.

2. Følg eksempel/vejledningen i [skabelongalleriet Hurtig start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) for at ændre antallet FOS efter en i pågældende Nodetype. Dette fjerner nu den højeste VM forekomst. 

3. Gentag trin 1 til 3 efter behov, men aldrig indskrænke antallet af forekomster i de primære nodetyper mindre end hvad pålidelighed niveau garanterer. Se [oplysningerne om pålidelighed lag her](service-fabric-cluster-capacity.md).

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Du kan se i tjenesten strukturen Explorer funktionsmåder

Når du skalere op en klynge afspejles Service-strukturen Explorer antallet af knuder (VM skala angivet forekomster), der er en del af klyngen.  Når du skalere kan en klynge ned du får vist den fjernede node/VM-forekomst, der vises i en beskadiget tilstand, medmindre du kontakter [Fjern ServiceFabricNodeState cmd](https://msdn.microsoft.com/library/mt125993.aspx) med nodenavnet relevante.   

Her er forklaring til dette problem.

Noderne i tjenesten strukturen Stifinder er en refleksion af hvilke Service-strukturen system tjenesterne (FM specifikt) ved, om antallet af knuder på klynge havde/har. Når du skalere VM skalaen angive, hvor VM er blevet slettet, men FM-systemtjenesten stadig mener, at noden (som blev knyttet til VM, der er blevet slettet) kommer tilbage. Så stadig Service-strukturen Explorer få vist denne node (selvom tilstanden kan være fejl eller ukendt).

For at sikre dig, at en node fjernes, når en VM er fjernet, har du to muligheder:

1) Vælg en holdbarhed niveau af guld eller Silver (tilgængelige snart) for Nodetyperne i din klynge, der giver infrastruktur integrationen. Som derefter fjernes automatisk noderne fra vores tjenester (FM) systemtilstand når du skalere.
Referere til [oplysningerne om holdbarhed niveauer her](service-fabric-cluster-capacity.md)

2) Når VM forekomsten er blevet skaleret, skal du ringe til [Fjern ServiceFabricNodeState cmdlet](https://msdn.microsoft.com/library/mt125993.aspx).

>[AZURE.NOTE] Tjenesten strukturen klynger kræver et bestemt antal noder skal op på hele tiden for at bevare tilgængelighed og bevare stat - kaldes "vedligeholdelse af quorum." Det er tilfældet, typisk usikre at lukke alle maskiner i klyngen, medmindre du først har udført en [komplet sikkerhedskopiering af din tilstand](service-fabric-reliable-services-backup-restore.md).

## <a name="next-steps"></a>Næste trin
Læse følgende for at se også om planlægning af klynge kapacitet, opgradering af en klynge og partitionering tjenester:

- [Planlægge din klynge kapacitet](service-fabric-cluster-capacity.md)
- [Klynge opgraderinger](service-fabric-cluster-upgrade.md)
- [Partition med høj sikkerhed tjenester til maksimale skala](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png
