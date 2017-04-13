<properties
   pageTitle="Azure ressourcestyring support til justering af belastning | Microsoft Azure "
   description="Bruge powershell til justering af belastning med Azure ressourcestyring. Brug af skabeloner til justering af belastning"
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


# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Brug af Azure ressourcestyring Support med Azure justering af belastning

Azure ressourcestyring er foretrukne management framework for tjenester i Azure. Azure justering af belastning kan administreres ved hjælp af Ressourcestyring Azure-baserede API'er og værktøjer.

## <a name="concepts"></a>Begreber

Med ressourcestyring Azure justering af belastning indeholder følgende underordnede ressourcer:

- Front end IP-konfiguration – belastningsjustering kan indeholde et eller flere frontend IP-adresser, også kendt som et virtuelt IP'er (VIPs). Disse IP-adresser fungere som vandindtrængen for trafikken.

- Adresse back end-puljen – dette er IP-adresser, der er forbundet med virtuelt Network Interface kortet (NIC), der skal distribueres Indlæs.

- Indlæse justering af regler – en regel egenskab knyttes en given front-end IP- og port kombination til et sæt af back-end IP-adresser og port kombination. En enkelt belastningsjustering kan have flere justering af belastning regler. Hver regel er en kombination af en front end IP-port og back end-IP- og port, der er knyttet til FOS.

- Sonder – sonder gør det muligt at holde styr på tilstanden for VM forekomster. Hvis en tilstand efterprøvning af af mislykkes, skal være forekomsten VM oprettet automatisk af rotation.

- Indgående NAT regler – NAT regler, der definerer den indgående trafik, der flyder gennem forsiden afslutte IP-adresse og distribueret til back-end IP-adresse.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Hurtig start skabeloner

Azure ressourcestyring kan du klargør programmerne ved hjælp af en deklarativ skabelon. Du kan installere flere tjenester sammen med deres afhængigheder i en enkelt skabelon. Du bruger den samme skabelon til at udrulle flere gange dit program under hvert enkelt trin i programmet livscyklussen.

Skabeloner kan medtage definitioner for virtuelle maskiner, virtuelle netværk, tilgængelighed sæt, netværksgrænseflader (NIC), lagerplads konti, Indlæs balancere, netværk sikkerhedsgrupper og offentlige IP-adresser. Du kan oprette alt, hvad du har brug for et komplekst program med skabeloner. Skabelonfilen kan kontrolleres til styring af webindhold system til versionsstyring og samarbejde.

[Få flere oplysninger om skabeloner](http://go.microsoft.com/fwlink/?LinkId=544798)

[Få flere oplysninger om netværksressourcer](../virtual-network/resource-groups-networking.md)

Hurtig start skabeloner ved hjælp af Azure justering af belastning kan findes i et [GitHub lager](https://github.com/Azure/azure-quickstart-templates) vært for en række genereres gruppeskabeloner.

Eksempler på skabeloner:

- [2 FOS i et belastning og regler for justering af belastning](http://go.microsoft.com/fwlink/?LinkId=544799)
- [2 FOS i en VNET med en intern justering af belastning og justering af belastning regler](http://go.microsoft.com/fwlink/?LinkId=544800)
- [2 FOS i belastningsjustering og konfigurere NAT regler på kg](http://go.microsoft.com/fwlink/?LinkId=544801)


## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Konfiguration af Azure justering af belastning, et PowerShell eller CLI

Introduktion til Azure ressourcestyring cmdletter, kommandolinjeværktøjer og REST API'er

- [Azure netværk cmdletter](https://msdn.microsoft.com/library/azure/mt163510.aspx) kan bruges til at oprette en justering af belastning.
- [Sådan oprettes en justering af belastning ved hjælp af Azure ressourcestyring](load-balancer-get-started-ilb-arm-ps.md)
- [Brug af Azure CLI med Azure ressourcestyring](../xplat-cli-azure-resource-manager.md)
- [Justering af belastning REST API'er](https://msdn.microsoft.com/library/azure/mt163651.aspx)


## <a name="next-steps"></a>Næste trin

Du kan også [få en introduktion til en via justering af belastning internettet](load-balancer-get-started-internet-arm-ps.md) og konfigurere, hvilken type [fordeling tilstand](load-balancer-distribution-mode.md) til en bestemt Indlæs belastningsjusteringstjenesten netværk trafik funktionsmåde.

Lær at administrere [inaktive TCP timeout indstillingerne for en justering af belastning](load-balancer-tcp-idle-timeout.md). Dette er vigtigt, når dit program har brug at holde forbindelser Live for servere bag en justering af belastning.
