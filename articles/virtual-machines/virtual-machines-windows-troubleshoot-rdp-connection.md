<properties
    pageTitle="Kan ikke RDP til en Azure VM | Microsoft Azure"
    description="Foretage fejlfinding af problemer, når du ikke oprette forbindelse til din Windows virtuelle maskine i Azure ved hjælp af Fjernskrivebord"
    keywords="Remote desktop fejl, forbindelse til Fjernskrivebord fejl, kan ikke oprette forbindelse til VM, fejlfinding af computeren"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="10/26/2016"
    ms.author="iainfou"/>

# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Foretage fejlfinding af Fjernskrivebord forbindelser til en Azure virtuelt

RDP Remote Desktop Protocol ()-forbindelse til din Windows-baseret Azure VM (virtual machine) kan mislykkes af forskellige årsager, så du ikke få adgang til din VM. Problemet kan være med tjenesten Fjernskrivebord på VM, netværksforbindelsen eller Fjernskrivebord-klienten på computeren host. I denne artikel fører dig gennem nogle af de mest almindelige metoder til at løse problemer med RDP forbindelser. 

Hvis du har brug for mere hjælp på en hvilken som helst sted i denne artikel, kan du kontakte de Azure eksperter på [MSDN Azure og stakoverløb fora](https://azure.microsoft.com/support/forums/). Du kan også sende en Azure support-hændelse. Gå til den [Azure supportwebsted](https://azure.microsoft.com/support/options/) , og vælg **Få Support**.

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Hurtige trin til fejlfinding
Efter hvert trin i fejlfindingen, kan du prøve at oprette forbindelse til VM:

1. Nulstil Fjernskrivebord konfiguration.
2. Kontrollere netværk sikkerhedsgruppe regler / Cloud Services slutpunkter.
3. Gennemse VM console logfiler.
4. Tjekke VM ressource tilstand.
5. Nulstille adgangskoden VM.
6. Genstart din VM.
7. Geninstaller din VM.

Fortsætte med at læse Hvis du har brug for mere detaljeret vejledning og forklaringer.

> [AZURE.TIP] Hvis knappen **Opret forbindelse** til din VM er nedtonet i portalen, og du ikke har forbindelse til Azure via en [Express distribuere](../expressroute/expressroute-introduction.md) eller [websted til websted VPN-](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) forbindelse, skal du oprette og tildele din VM en offentlig IP-adresse, før du kan bruge RDP. Du kan læse mere om [offentlige IP-adresser i Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Måder at foretage fejlfinding af problemer med RDP
Du kan foretage fejlfinding af FOS, der er oprettet ved hjælp af Ressourcestyring implementeringsmodel ved hjælp af en af følgende fremgangsmåder:

- [Azure portal](#using-the-azure-portal) - godt, hvis du har brug at nulstille hurtigt RDP konfiguration eller bruger legitimationsoplysningerne, og du har ikke installeret Azure værktøjer.
- [Azure PowerShell](#using-azure-powershell) - nulstille Hvis du er tryg ved en PowerShell-prompt hurtigt de RDP konfiguration eller bruger legitimationsoplysninger ved hjælp af Azure PowerShell-cmdlet'er.

Du kan også finde trin til fejlfinding i forbindelse med VM'er, der er oprettet ved hjælp af [Klassisk implementeringsmodel](#troubleshoot-vms-created-using-the-classic-deployment-model).


<a id="fix-common-remote-desktop-errors"></a>
## <a name="troubleshoot-using-the-azure-portal"></a>Ved hjælp af portalen Azure
Efter hvert trin i fejlfindingen, kan du prøve at oprette forbindelse til din VM igen. Hvis du stadig ikke oprette forbindelse, kan du prøve næste trin.

1. **Nulstille din RDP-forbindelse**. Dette trin i fejlfindingen nulstiller RDP-konfiguration, når Remote forbindelser er deaktiveret eller Windows Firewall-regler der blokerer RDP, f.eks.

    Vælg din VM i portalen Azure. Rul ned ruden indstillinger til sektionen **Support + fejlfinding** nær bunden af listen. Klik på knappen **Nulstil adgangskode** . Angive **tilstand** nulstille **kun konfiguration** , og klik derefter på knappen **Opdater** :

    ![Nulstil RDP konfigurationen i portalen Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/reset-rdp.png)

2. **Bekræfte netværk sikkerhedsgruppe regler**. Dette trin i fejlfindingen kontrollerer, at du har en regel i dit netværk sikkerhedsgruppe til at tillade RDP trafik. Standardporten for RDP er TCP-port 3389. En regel til at tillade RDP trafik kan ikke oprettes automatisk, når du opretter din VM.

    Vælg din VM i portalen Azure. Klik på **netværksgrænseflader** fra ruden indstillinger.

    ![Vis netværksgrænseflader til en VM Azure-portalen](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-network-interfaces.png)

    Vælg grænsefladen dit netværk på listen (der er typisk kun én):

    ![Vælge netværksgrænseflade i portalen Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-interface.png)

    Vælg **netværk sikkerhedsgruppe** til at få vist sikkerhedsgruppen netværk, der er knyttet til dit netværk interface:

    ![Vælg netværk af sikkerhedsgruppen i portalen Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-nsg.png)

    Kontrollér, at der findes en indgående regel, der tillader RDP-trafik på TCP-port 3389. I følgende eksempel viser en gyldig sikkerhedsregel, der tillader RDP trafik. Du kan se `Service` og `Action` er konfigureret korrekt:

    ![Bekræfte RDP NSG regel i portalen Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/verify-nsg-rules.png)

    Hvis du ikke har en regel tillader, der RDP trafik, [oprette en regel til netværk sikkerhedsgruppe](virtual-machines-windows-nsg-quickstart-portal.md). Tillad TCP-port 3389.

3. **Gennemse VM Start diagnosticering**. Dette trin i fejlfindingen gennemser loggene VM console for at afgøre, hvis VM er rapportering om et problem. Ikke alle FOS har Start diagnosticering aktiveret, så denne fejlfindingstrin kan være valgfrit.
    
    Bestemte fejlfindingstrin er ikke medtaget i denne artikel, men kan angive et bredere problem, der påvirker RDP connectivity. Du kan finde flere oplysninger om Gennemse console logfiler og VM skærmbillede, [Start diagnosticering for FOS](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

4. **Tjekke VM ressource tilstand**. Dette trin i fejlfindingen bekræfter, der er ingen kendte problemer med Azure platformen, der kan påvirke connectivity til VM.

    Vælg din VM i portalen Azure. Rul ned ruden indstillinger til sektionen **Support + fejlfinding** nær bunden af listen. Klik på knappen **ressource tilstand** . En sund VM rapporterer som værende **tilgængelige**:

    ![Tjekke VM ressource tilstand i portalen Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/check-resource-health.png)

5. **Nulstil brugerens legitimationsoplysninger**. Dette trin i fejlfindingen nulstiller adgangskoden til en lokal administratorkonto, når du er sikker på, eller har glemt legitimationsoplysningerne.

    Vælg din VM i portalen Azure. Rul ned ruden indstillinger til sektionen **Support + fejlfinding** nær bunden af listen. Klik på knappen **Nulstil adgangskode** . Sørg for, at **tilstanden** er indstillet til at **nulstille adgangskoden** , og angiv derefter dit brugernavn og en ny adgangskode. Til sidst skal du klikke på knappen **Opdater** :

    ![Nulstille brugerlegitimationsoplysninger i portalen Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/reset-password.png)

6. **Genstart din VM**. Dette trin i fejlfindingen kan rette de underliggende problemer, hvor VM selve er problemer.

    Vælg din VM i Azure-portalen, og klik på fanen **Oversigt** . Klik på knappen **Genstart** :

    ![Genstart VM i portalen Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/restart-vm.png)

7. **Geninstaller din VM**. Dette trin i fejlfindingen redeploys din VM til en anden vært i Azure at rette en hvilken som helst underliggende platform eller problemer.

    Vælg din VM i portalen Azure. Rul ned ruden indstillinger til sektionen **Support + fejlfinding** nær bunden af listen. Klik på knappen **Geninstaller** , og klik derefter på **Geninstaller**:

    ![Geninstaller VM i portalen Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/redeploy-vm.png)

    Når handlingen afsluttes, skal du kortvarige diskdata går tabt og dynamiske IP-adresser, der er knyttet til VM er opdateret.

Hvis du oplever stadig RDP problemer, du kan [åbne en supportanmodning](https://azure.microsoft.com/support/options/) eller Læs [mere detaljeret RDP fejlfinding begreberne og trinnene](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-using-azure-powershell"></a>Ved hjælp af Azure PowerShell
Hvis du ikke allerede har gjort, [installere og konfigurere den seneste Azure PowerShell](../powershell-install-configure.md).

I følgende eksempler bruges variabler såsom `myResourceGroup`, `myVM`, og `myVMAccessExtension`. Erstat disse variabelnavne og placeringer med dine egne værdier.

> [AZURE.NOTE] Du nulstille brugerlegitimationsoplysningerne og RDP konfigurationen ved hjælp af [Sæt AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) PowerShell-cmdlet. I eksemplerne nedenfor `myVMAccessExtension` er et navn, du angiver som en del af processen. Hvis du tidligere har arbejdet med VMAccessAgent, kan du få navnet på det eksisterende filtypenavn ved hjælp af `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` til at kontrollere egenskaberne for VM. For at få vist navnet, kan du se under afsnittet 'Filtypenavne' i output.

Efter hvert trin i fejlfindingen, kan du prøve at oprette forbindelse til din VM igen. Hvis du stadig ikke oprette forbindelse, kan du prøve næste trin.

1. **Nulstille din RDP-forbindelse**. Dette trin i fejlfindingen nulstiller RDP-konfiguration, når Remote forbindelser er deaktiveret eller Windows Firewall-regler der blokerer RDP, f.eks.

    I følgende eksempel nulstiller RDP-forbindelse ved brug af en VM med navnet `myVM` i den `WestUS` placering i ressourcegruppen med navnet `myResourceGroup`:

    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```

2. **Bekræfte netværk sikkerhedsgruppe regler**. Dette trin i fejlfindingen kontrollerer, at du har en regel i dit netværk sikkerhedsgruppe til at tillade RDP trafik. Standardporten for RDP er TCP-port 3389. En regel til at tillade RDP trafik kan ikke oprettes automatisk, når du opretter din VM.

    Først skal tildele alle konfigurationsdataene for dit netværk sikkerhedsgruppe til den `$rules` variabel. I følgende eksempel henter oplysninger om sikkerhedsgruppen netværk med navnet `myNetworkSecurityGroup` i ressourcegruppen med navnet `myResourceGroup`:

    ```powershell
    $rules = Get-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```

    Du kan nu se de regler, der er konfigureret for dette netværk sikkerhedsgruppe. Kontrollere, at der findes en regel for at tillade TCP-port 3389 for indgående forbindelser på følgende måde:

    ```powershell
    $rules.SecurityRules
    ```

    I følgende eksempel viser en gyldig sikkerhedsregel, der tillader RDP trafik. Du kan se `Protocol`, `DestinationPortRange`, `Access`, og `Direction` er konfigureret korrekt:

    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```

    Hvis du ikke har en regel tillader, der RDP trafik, [oprette en regel til netværk sikkerhedsgruppe](virtual-machines-windows-nsg-quickstart-powershell.md). Tillad TCP-port 3389.

3. **Nulstil brugerens legitimationsoplysninger**. Dette trin i fejlfindingen nulstiller adgangskoden til den lokale administratorkonto, du angiver, når du er usikker på, eller har glemt legitimationsoplysningerne.

    Først skal du angive brugernavnet og en ny adgangskode ved at tildele legitimationsoplysninger til den `$cred` variabel på følgende måde:

    ```powershell
    $cred=Get-Credential
    ```

    Opdater nu legitimationsoplysningerne på din VM. I følgende eksempel opdaterer legitimationsoplysninger på en VM med navnet `myVM` i den `WestUS` placering i ressourcegruppen med navnet `myResourceGroup`:

    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```

4. **Genstart din VM**. Dette trin i fejlfindingen kan rette de underliggende problemer, hvor VM selve er problemer.

    I følgende eksempel genstarter VM med navnet `myVM` i ressourcegruppen med navnet `myResourceGroup`:

    ```powershell
    Restart-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

5. **Geninstaller din VM**. Dette trin i fejlfindingen redeploys din VM til en anden vært i Azure at rette en hvilken som helst underliggende platform eller problemer.

    I følgende eksempel redeploys VM med navnet `myVM` i den `WestUS` placering i ressourcegruppen med navnet `myResourceGroup`:

    ```powershell
    Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

Hvis du oplever stadig RDP problemer, du kan [åbne en supportanmodning](https://azure.microsoft.com/support/options/) eller Læs [mere detaljeret RDP fejlfinding begreber og trin](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Fejlfinding i forbindelse med VM'er, der er oprettet ved hjælp af implementeringsmodel klassisk

Efter hvert trin i fejlfindingen, kan du prøve at oprette forbindelse til VM.

1. **Nulstille din RDP-forbindelse**. Dette trin i fejlfindingen nulstiller RDP-konfiguration, når Remote forbindelser er deaktiveret eller Windows Firewall-regler der blokerer RDP, f.eks.

    Vælg din VM i portalen Azure. Klik på **... Flere** knappen, og klik derefter på **Nulstil fjernadgang**:

    ![Nulstil RDP konfigurationen i portalen Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-reset-rdp.png)

2.  **Bekræfte Cloud Services slutpunkter**. Dette trin i fejlfindingen kontrollerer, at du har slutpunkter i din Cloud Services til at tillade RDP trafik. Standardporten for RDP er TCP-port 3389. En regel til at tillade RDP trafik kan ikke oprettes automatisk, når du opretter din VM.

    Vælg din VM i portalen Azure. Klik på knappen **slutpunkter** for at få vist slutpunkterne er konfigureret for din VM. Kontroller, slutpunkter findes, der tillader RDP trafik på TCP-port 3389.
    
    I følgende eksempel viser gyldige slutpunkter, som tillader RDP trafik:

    ![Bekræfte Cloud Services slutpunkter i portalen Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)

    Hvis du ikke har et slutpunkt tillader, der RDP trafik, [oprette et Skytjenester slutpunkt](virtual-machines-windows-classic-setup-endpoints.md). Tillad, at TCP private port 3389.

3. **Gennemse VM Start diagnosticering**. Dette trin i fejlfindingen gennemser loggene VM console for at afgøre, hvis VM er rapportering om et problem. Ikke alle FOS har Start diagnosticering aktiveret, så denne fejlfindingstrin kan være valgfrit.
    
    Bestemte fejlfindingstrin er ikke medtaget i denne artikel, men kan angive et bredere problem, der påvirker RDP connectivity. Du kan finde flere oplysninger om Gennemse console logfiler og VM skærmbillede, [Start diagnosticering for FOS](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

4. **Tjekke VM ressource tilstand**. Dette trin i fejlfindingen bekræfter, der er ingen kendte problemer med Azure platformen, der kan påvirke connectivity til VM.

    Vælg din VM i portalen Azure. Rul ned ruden indstillinger til sektionen **Support + fejlfinding** nær bunden af listen. Klik på knappen **Ressource tilstand** . En sund VM rapporterer som værende **tilgængelige**:

    ![Tjekke VM ressource tilstand i portalen Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-check-resource-health.png)

5. **Nulstil brugerens legitimationsoplysninger**. Dette trin i fejlfindingen nulstiller på den lokale administratorkonto, du angiver, når du er sikker på, eller legitimationsoplysningerne, der har glemt adgangskoden.

    Vælg din VM i portalen Azure. Rul ned ruden indstillinger til sektionen **Support + fejlfinding** nær bunden af listen. Klik på knappen **Nulstil adgangskode** . Angiv dit brugernavn og en ny adgangskode. Til sidst skal du klikke på knappen **Gem** :

    ![Nulstille brugerlegitimationsoplysninger i portalen Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-reset-password.png)

6. **Genstart din VM**. Dette trin i fejlfindingen kan rette de underliggende problemer, hvor VM selve er problemer.

    Vælg din VM i Azure-portalen, og klik på fanen **Oversigt** . Klik på knappen **Genstart** :

    ![Genstart VM i portalen Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-restart-vm.png)
    
Hvis du oplever stadig RDP problemer, du kan [åbne en supportanmodning](https://azure.microsoft.com/support/options/) eller Læs [mere detaljeret RDP fejlfinding begreber og trin](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-specific-rdp-errors"></a>Fejlfinding i forbindelse med specifikke RDP-fejl
Du kan støde på en bestemt fejlmeddelelse, når du forsøger at oprette forbindelse til din VM via RDP. Følgende er de mest almindelige fejlmeddelelser:

- [Den fjernsession blev afbrudt, fordi der er ingen Remote Desktop licensservere til rådighed med en licens](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdplicense).
- [Fjernskrivebord kan ikke finde computeren "navn"](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpname).
- Der opstod fejl i [en godkendelse. Lokale sikkerhed myndigheder kan ikke kontaktes](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpauth).
- [Windows sikkerhed fejl: dine legitimationsoplysninger ikke fungerer](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#wincred).
- [Denne computer kan ikke oprette forbindelse til fjerncomputeren](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpconnect).


## <a name="additional-resources"></a>Yderligere ressourcer
Hvis ingen af disse fejl opstod, og du stadig ikke oprette forbindelse til VM via Fjernskrivebord, skal du læse detaljerede [fejlfindingsvejledningen til Fjernskrivebord](virtual-machines-windows-detailed-troubleshoot-rdp.md).

- [Azure IaaS (Windows) diagnostics-pakken](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)
- Trin til fejlfinding i at få adgang til programmer, der kører på en VM, under [fejlfinding i forbindelse med adgang til et program, der kører på en Azure VM](virtual-machines-linux-troubleshoot-app-connection.md).
- Hvis du har problemer med brug af Secure Shell (SSH) til at oprette forbindelse til en Linux VM i Azure, se [fejlfinding i forbindelse med SSH forbindelser til en Linux VM i Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).
