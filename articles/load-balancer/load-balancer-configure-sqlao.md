<properties
   pageTitle="Konfigurere justering af belastning for SQL altid på | Microsoft Azure"
   description="Konfigurere justering af belastning til at arbejde med SQL altid på og hvordan du kan udnytte powershell for at oprette justering af belastning for SQL-implementering"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="configure-load-balancer-for-sql-always-on"></a>Konfigurere justering af belastning for SQL altid på

SQL Server AlwaysOn tilgængelighed grupper kan nu køres med ILB. Tilgængelighed gruppe er SQL Server største løsning til høj tilgængelighed og genoprettelse efter nedbrud. Tilgængelighed gruppe lytteren tillader klienten programmer problemfrit oprette forbindelse til den primære replika, uanset hvor mange af replikaerne i konfigurationen.

Navnet på lytteren (DNS) er knyttet til en netværksbelastningen IP-adresse og Azures justering af belastning omdirigerer den indgående trafik til den primære server i replikasættet.

Du kan bruge ILB understøttelse af SQL Server AlwaysOn (lytteren) slutpunkter. Du kan nu har kontrol over Hjælp til handicappede i lytteren og kan vælge Indlæs afstemmes IP-adressen fra en bestemt undernet i netværket virtuel (VNet).

Ved hjælp af ILB på lytteren, SQL Serverslutpunktet (fx Server = tcp:ListenerName, 1433; Database = databasenavn) er kun tilgængelige for:

- Tjenester og FOS i det samme virtuelle netværk
- Tjenester og FOS fra forbundne lokalt netværk
- Tjenester og FOS fra forbundne VNets

![ILB_SQLAO_NewPic](./media/load-balancer-configure-sqlao/sqlao1.png)

Figur 1 - SQL AlwaysOn konfigureret med internettet justering af belastning

## <a name="add-internal-load-balancer-to-the-service"></a>Tilføje interne justering af belastning til tjenesten

1. I eksemplet nedenfor vil vi konfigurere et virtuelle netværk, der indeholder et undernet, kaldet 'Undernet-1':

        Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc

2. Tilføje Indlæs afstemmes slutpunkter for ILB på hver VM

        Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –
        DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

        Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    I eksemplet ovenfor, du har 2 VM kaldet "sqlsvc1" og "sqlsvc2" kører i skyen service "Sqlsvc". Når du har oprettet ILB med "DirectServerReturn" Skift, føjer du Indlæs afstemmes slutpunkter til ILB tillade SQL til konfiguration af lyttere for grupperne tilgængelighed.

Se [konfigurere en intern justering af belastning for en gruppe med AlwaysOn tilgængelighed i Azure](../virtual-machines/virtual-machines-windows-portal-sql-alwayson-int-listener.md)kan finde flere oplysninger om SQL AlwaysOn.

## <a name="see-also"></a>Se også

[Introduktion til konfiguration af en via justering af belastning internettet](load-balancer-get-started-internet-arm-ps.md)

[Introduktion til konfiguration af en intern justering af belastning](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurere en Indlæs belastningsjusteringstjenesten fordeling tilstand](load-balancer-distribution-mode.md)

[Konfigurere inaktive TCP timeoutindstillinger for din justering af belastning](load-balancer-tcp-idle-timeout.md)
