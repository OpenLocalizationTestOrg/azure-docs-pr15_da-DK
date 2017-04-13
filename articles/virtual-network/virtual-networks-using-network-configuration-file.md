<properties 
    pageTitle="Konfigurere et virtuelt netværk ved hjælp af et netværk konfigurationsfil" 
    description="Vejledningen for at eksportere og importere en netværk konfigurationsfil til portalen Azure Management for at oprette eller ændre virtuelle netværk. " 
    services="virtual-network" 
    documentationCenter="" 
    authors="jimdial" 
    manager="carmonm" 
    editor="tysonn"/>

<tags
    ms.service="virtual-network"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services" 
    ms.date="03/15/2016"
    ms.author="jdial"/>

# <a name="configure-a-virtual-network-using-a-network-configuration-file"></a>Konfigurere et virtuelt netværk ved hjælp af et netværk konfigurationsfil

Du kan konfigurere et virtuelt netværk (VNet), ved hjælp af portalen Azure Management eller ved hjælp af et netværk konfigurationsfil.

## <a name="creating-and-modifying-a-network-configuration-file"></a>Oprette og redigere en netværk konfigurationsfil 
Den nemmeste måde at redigere en netværk konfigurationsfil er at eksportere netværksindstillingerne fra en eksisterende virtuelle netværkskonfiguration, derefter redigere filen indeholde de indstillinger, du vil konfigurere til dine virtuelle netværk.

Hvis du vil redigere konfigurationsfil netværk, kan du blot åbne filen, Foretag de ønskede ændringer og derefter gemme filen. Du kan bruge en *XML-* editor til at foretage ændringer i konfigurationsfil netværk. 

Overvåge skal du følge vejledningen til [skemaet til netværk konfigurationsindstillinger](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

Azure finder et undernet, der har noget anvendes på den som **i brug**. Når et undernet er i brug, er det ikke ændres. Før du foretager ændringer, du flytte alt det, du har installeret til undernet til et andet undernet, der ikke bliver ændret.   Se [flytte en VM eller rolle forekomst til et andet undernet](virtual-networks-move-vm-role-to-subnet.md).

## <a name="export-and-import-virtual-network-settings-using-the-management-portal"></a>Eksportere og importere virtuelle netværksindstillinger ved hjælp af portalen administration  
Du kan importere og eksportere netværk konfigurationsindstillinger, der er indeholdt i din konfigurationsfil netværk ved hjælp af PowerShell eller Management-portalen. Vejledningen nedenfor kan du eksportere og importere ved hjælp af portalen administration. 

### <a name="to-export-your-network-settings"></a>Eksportere dine netværksindstillinger
Når du eksporterer, skrives alle indstillingerne for de virtuelle netværk i dit abonnement til en .XML-fil. 

1. Log på **administrationsportalen til**.
2. Klik på **Eksporter**i portalen Management nederst på siden **netværk** . 
3. I vinduet **eksportere netværkskonfiguration** skal du bekræfte, at du har markeret det abonnement, du vil eksportere dine netværksindstillinger. Klik derefter på markeringen nederst til højre. 
4. Når du bliver bedt om det, kan du gemme filen *NetworkConfig.xml* til placeringen af dit valg.


### <a name="to-import-your-network-settings"></a>Importere dine netværksindstillinger

1. Klik på **Ny**i **Management-portalen**, i navigationsruden nederst til venstre.
2. Klik på **netværkstjenester** -> **virtuelt netværk** -> **importkonfiguration**.
3. Gå til dit netværk konfigurationsfil, og klik derefter på pilen **Næste** på siden **Importér konfigurationsfil netværk** .
4. På siden **opbygge dit netværk** skal se du oplysningerne på skærmen, der viser, hvilke sektioner i din netværkskonfiguration ændres eller oprettet. Hvis ændringerne ser rigtige for dig, skal du klikke på markeringen for at fortsætte med at opdatere eller oprette dit virtuelle netværk. 