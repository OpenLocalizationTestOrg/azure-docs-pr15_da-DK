<properties
   pageTitle="Tjenesten strukturen node-typer og VM skala sæt | Microsoft Azure"
   description="Beskriver, hvordan Service-strukturen node-typer er relateret til VM skala sæt, og hvordan opretter forbindelse til en VM skala angive forekomst eller en klyngenode til Fjern."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# <a name="the-relationship-between-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Forholdet mellem Service-strukturen node-typer og virtuelt skala sæt

Virtuelt skala sæt er en Azure beregne ressource, du kan bruge til at installere og administrere en samling af virtuelle maskiner som et sæt. Hver node af type, der er defineret i en tjeneste-strukturen klynge er konfigureret som en separat VM skala angive. Hver nodetype kan derefter skaleres eller ned uafhængigt af hinanden, har forskellige typer åbne porte og kan have forskellige kapacitet målepunkter.

Følgende skærmbillede viser en klynge, der indeholder to nodetyper: front end- og back end.  Hver nodetype har fem noder.

![Skærmbillede af en klynge, der indeholder to typer af Node][NodeTypes]

## <a name="mapping-vm-scale-set-instances-to-nodes"></a>Tilknytte VM skala angive forekomster til noder

Som du kan se ovenfor, starte forekomster VM skala angive fra forekomst, 0 og derefter går op. Nummereringen afspejles i navne. For eksempel er BackEnd_0 forekomst 0 af back end-VM skala angive. Denne bestemt VM skala angive der er fem forekomster, med navnet BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 og BackEnd_4.

Når du skalere op VM skala sæt oprettes en ny forekomst. Ny VM skala angive navnet på forekomsten bliver typisk VM skala angive navnet + tallets næste forekomst. I eksemplet er det BackEnd_5.


## <a name="mapping-vm-scale-set-load-balancers-to-each-node-typevm-scale-set"></a>Indlæse balancere indstillet tilknytning VM skala til hver node type/VM skala sæt

Hvis du har installeret din klynge fra portalen eller har brugt den eksempel ressourcestyring skabelon, som vi tilbyder, derefter når du får en liste over alle de ressourcer under en ressourcegruppe får du vist Indlæs balancere for hver enkelt VM skala angive eller node.

Navnet skal noget i retning af: **kg -&lt;NodeType navn&gt;**. For eksempel kg-sfcluster4doc-0, som vist i dette skærmbillede:


![Ressourcer][Resources]


## <a name="remote-connect-to-a-vm-scale-set-instance-or-a-cluster-node"></a>Remote oprette forbindelse til en VM skala angive forekomst eller en klyngenode
Hver Node af type, der er defineret i en klynge er konfigureret som en separat VM skala angive.  Det betyder, at Nodetyperne kan skaleres op eller ned uafhængigt af hinanden og kan bestå af forskellige VM lagerenheder. I modsætning til enkelt forekomst FOS får forekomster VM skala angive ikke en virtuel IP-adressen på deres egne. Så kan det være lidt udfordring når du søger efter en IP-adresse og port, som du kan bruge til remote opretter forbindelse til en bestemt forekomst.

Her er de trin, du kan følge for at finde dem.

### <a name="step-1-find-out-the-virtual-ip-address-for-the-node-type-and-then-inbound-nat-rules-for-rdp"></a>Trin 1: Find ud af nodetypen virtuelle IP-adresse og derefter indgående NAT regler for RDP

Hvis du vil have, skal du hente indgående NAT regler værdierne, der er defineret som en del af den ressource definition for **Microsoft.Network/loadBalancers**.

Gå til Indlæs belastningsjusteringstjenesten blade og klik derefter på **Indstillinger**på portalen.

![LBBlade][LBBlade]


Klik på **indgående NAT regler**i **Indstillinger**. Denne nu giver dig IP-adresse og port, som du kan bruge til remote oprette forbindelse til den første VM skala angive forekomst. I skærmbilledet nedenfor er det **104.42.106.156** og **3389**

![NATRules][NATRules]

### <a name="step-2-find-out-the-port-that-you-can-use-to-remote-connect-to-the-specific-vm-scale-set-instancenode"></a>Trin 2: Find ud af den port, som du kan bruge til remote oprette forbindelse til den specifikke VM skala angive forekomst/node

Jeg talt om hvordan forekomster VM skala angive knyttes til noderne tidligere i dette dokument. Vi bruger, til at finde ud af den nøjagtige port.

Der tildeles portene i stigende rækkefølge efter den VM skala angive forekomst. så i min eksempel FrontEnd node type er portene for hver af de fem forekomster følgende. Nu skal du gøre det samme tilknytning til din VM skala angive forekomst.

|**VM skala sæt forekomst**|**Port**|
|-----------------------|--------------------------|
|FrontEnd_0|3389|
|FrontEnd_1|3390|
|FrontEnd_2|3391|
|FrontEnd_3|3392|
|FrontEnd_4|3393|
|FrontEnd_5|3394|


### <a name="step-3-remote-connect-to-the-specific-vm-scale-set-instance"></a>Trin 3: Forbind Remote på forekomsten af bestemte VM skala angive

I skærmbilledet nedenfor kan jeg bruge Fjernskrivebord til at oprette forbindelse til FrontEnd_1:

![RDP][RDP]

## <a name="how-to-change-the-rdp-port-range-values"></a>Sådan ændres RDP port Områdeværdier

### <a name="before-cluster-deployment"></a>Før installation af klynge

Når du installerer den klynge ved hjælp af en skabelon til Ressourcestyring, kan du angive området i **inboundNatPools**.

Gå til den ressource definition for **Microsoft.Network/loadBalancers**. Under, kan du finde en beskrivelse af **inboundNatPools**.  Erstatte *frontendPortRangeStart* og *frontendPortRangeEnd* værdierne.

![InboundNatPools][InboundNatPools]


### <a name="after-cluster-deployment"></a>Efter installation af klynge
Dette er lidt mere avanceret, kan det medføre FOS få genbruges. Du har nu til at angive nye værdier ved hjælp af Azure PowerShell. Sørg for, at Azure PowerShell 1.0 eller nyere installeret på computeren. Hvis du ikke har gjort dette før, jeg vil opfordre, at du følger trinnene beskrevet i [hvordan du installerer og konfigurerer Azure PowerShell.](../powershell-install-configure.md)

Log på din Azure-konto. Hvis denne PowerShell-kommando mislykkes eller anden grund, skal du kontrollere, om du har Azure PowerShell installeret korrekt.

```
Login-AzureRmAccount
```

Kør følgende for at få yderligere oplysninger om din justering af belastning, og du kan se værdierne for beskrivelsen af **inboundNatPools**:

```
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
```

Angiv nu *frontendPortRangeEnd* og *frontendPortRangeStart* til de ønskede værdier.

```
$PropertiesObject = @{
    #Property = value;
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <RG name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load Balancer name> -ApiVersion <use the API version that get returned> -Force
```


## <a name="next-steps"></a>Næste trin

- [Oversigt over funktionen "Installere overalt" og en sammenligning med Azure-administreret klynger](service-fabric-deploy-anywhere.md)
- [Klynge sikkerhed](service-fabric-cluster-security.md)
- [Service-strukturen SDK og komme i gang](service-fabric-get-started.md)


<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png
