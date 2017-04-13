<properties
    pageTitle="Installere IIS på din første Windows VM | Microsoft Azure"
    description="Eksperimentere med din første Windows virtuelle maskine ved at installere IIS og åbne port 80 ved hjælp af portalen Azure."
    keywords=""
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="cynthn"/>

# <a name="experiment-with-installing-a-role-on-your-windows-vm"></a>Eksperimentere med installation af en rolle på din Windows-VM
    
Når du har din første virtuelle maskine (VM) op at køre, kan du flytte til installation af software og -tjenester. Dette selvstudium skal vi bruge Server Manager på Windows Server VM til at installere IIS. Derefter skal du oprette en netværk sikkerhed gruppe (NSG) ved hjælp af portalen Azure til at åbne port 80 til IIS trafik. 

Hvis du ikke allerede har oprettet din første VM, skal du gå tilbage til at [oprette din første Windows virtuelle maskine i portalen Azure](virtual-machines-windows-hero-tutorial.md) før du fortsætter med dette selvstudium.

## <a name="make-sure-the-vm-is-running"></a>Sørg for, at VM kører

1. Åbn [Azure-portalen](https://portal.azure.com).
2. Klik på menuen hub **virtuelle maskiner**. Vælg den virtuelle maskine på listen.
3. Hvis status er **stoppet (Deallocated)**, skal du klikke på knappen **Start** på bladet **Essentials** VM. Hvis status er **kører**, kan du flytte til næste trin.

## <a name="connect-to-the-virtual-machine-and-sign-in"></a>Oprette forbindelse til den virtuelle maskine og logge på

1.  Klik på menuen hub **virtuelle maskiner**. Vælg den virtuelle maskine på listen.

3. Klik på bladet til den virtuelle maskine, **Opret forbindelse**. Dette opretter og henter en Remote Desktop Protocol-fil (RDP-fil), der er som en genvej til at oprette forbindelse til din computer. Du vil gemme filen på skrivebordet for nem adgang. **Åbne** denne fil til at oprette forbindelse til din VM.

    ![Skærmbillede af portalen Azure, der viser, hvordan du opretter forbindelse til din VM](./media/virtual-machines-windows-hero-tutorial/connect.png)

4. Du får en advarsel, som RDP er fra en ukendt udgiver. Dette er normalt. Klik på **Opret forbindelse** fortsat i vinduet Fjernskrivebord.

    ![Skærmbillede af en advarsel om en ukendt udgiver](./media/virtual-machines-windows-hero-tutorial/rdp-warn.png)

5. Skriv det brugernavn og adgangskoden til den lokale konto, du har oprettet, da du oprettede VM i vinduet Windows-sikkerhed. Brugernavnet, der er angivet som *vmname*& #92; *brugernavn*, klik derefter på **OK**.

    ![Skærmbillede af at angive VM navn, brugernavn og adgangskode](./media/virtual-machines-windows-hero-tutorial/credentials.png)
    
6.  Du får en advarsel, at certifikatet, der ikke kan bekræftes. Dette er normalt. Klik på **Ja** for at bekræfte identiteten af den virtuelle maskine og afslutte logger på.

    ![Skærmbillede, der viser en meddelelse du vil bekræfte VM identitet](./media/virtual-machines-windows-hero-tutorial/cert-warning.png)


Hvis du støder på problemer, når du forsøger at oprette forbindelse, kan du se [fejlfinding af Fjernskrivebord forbindelser til en Windows-baseret Azure virtuel maskine](virtual-machines-windows-troubleshoot-rdp-connection.md).


## <a name="install-iis-on-your-vm"></a>Installere IIS på din VM

Nu hvor du er logget på til VM, installeres vi en serverrolle, så du kan eksperimentere mere.

1. Åbn **Server Manager** , hvis den ikke allerede er åben. Klik på menuen **Start** , og klik derefter på **Server Manager**.
2. Vælg **Lokalserver** i venstre rude i **Server Manager**. 
3. I menuen Vælg **Administrer** > **tilføje roller og funktioner**.
4. Vælg **rollebaseret eller funktion-baseret installation**i funktioner guiden, på siden **Installationstype** og tilføje roller, og klik derefter på **Næste**.

    ![Skærmbillede, der viser fanen funktioner guiden og tilføje roller for installationstype](./media/virtual-machines-windows-hero-tutorial/role-wizard.png)

5. Vælg VM server puljen, og klik på **Næste**.
6. Vælg **Web Server (IIS)**på siden **Serverroller** .

    ![Skærmbillede, der viser fanen funktioner guiden og tilføje roller for serverroller](./media/virtual-machines-windows-hero-tutorial/add-iis.png)

7. Sørg for, at **medtage administrationsværktøjer** er markeret i pop op-vinduet om tilføjelse af funktioner, der kræves for IIS, og klik derefter på **Tilføj funktioner**. Når pop op-vinduet lukkes, skal du klikke på **Næste** i guiden.

    ![Skærmbillede, der viser genvejsmenuen for at bekræfte, at tilføje rollen IIS](./media/virtual-machines-windows-hero-tutorial/confirm-add-feature.png)

8. Klik på **Næste**på siden funktioner på.
9. Klik på **Næste**på siden **Web Server rolle (IIS)** . 
10. Klik på **Næste**på siden **Rolle Services** . 
11. Klik på **Installer**på siden **Confirmation** . 
12. Når installationen er fuldført, kan du klikke på **Luk** i guiden.



## <a name="open-port-80"></a>Åbn port 80 

Hvis din VM at acceptere indgående trafik via port 80, skal du føje en indgående regel til sikkerhedsgruppen netværk. 

1. Åbn [Azure-portalen](https://portal.azure.com).
2. Vælg den VM, du har oprettet i **virtuelle computere** .
3. Vælg **netværksgrænseflader** i indstillingerne for virtuelle maskiner, og vælg derefter grænsefladen for det eksisterende netværk.

    ![Skærmbillede, der viser indstillingen virtuelt for netværksgrænseflader](./media/virtual-machines-windows-hero-tutorial/network-interface.png)

4. Klik på den **netværk sikkerhedsgruppe** **Essentials** for netværksgrænsefladen.

    ![Skærmbillede, der viser afsnittet Essentials for netværksgrænsefladen](./media/virtual-machines-windows-hero-tutorial/select-nsg.png)

5. I bladet **Essentials** for NSG, bør du have en eksisterende standard indgående regel for **standard tillade rdp** som gør det muligt at logge på til VM. Du vil tilføje en anden indgående regel for at tillade IIS trafik. Klik på **indgående sikkerhedsregel**.

    ![Skærmbillede, der viser afsnittet Essentials for NSG](./media/virtual-machines-windows-hero-tutorial/inbound.png)

6. Klik på **Tilføj**i **indgående sikkerhedsregler for**.

    ![Skærmbillede, der viser knappen til at tilføje en sikkerhedsregel](./media/virtual-machines-windows-hero-tutorial/add-rule.png)

7. Klik på **Tilføj**i **indgående sikkerhedsregler for**. Skriv **80** i portområdet, og Sørg for, at **Tillad** er markeret. Når du er færdig, skal du klikke på **OK**.

    ![Skærmbillede, der viser knappen til at tilføje en sikkerhedsregel](./media/virtual-machines-windows-hero-tutorial/port-80.png)
 
Finde flere oplysninger om NSGs, indgående og udgående regler under [Tillad ekstern adgang til din VM ved hjælp af portalen Azure](virtual-machines-windows-nsg-quickstart-portal.md)
 
## <a name="connect-to-the-default-iis-website"></a>Oprette forbindelse til standard IIS-websted

1. Klik på **virtuelle maskiner** i portalen Azure, og vælg derefter din VM.
2. Kopiere din **offentlige IP-adresse**i bladet **Essentials** .

    ![Skærmbillede af, hvor du kan finde den offentlige IP-adresse på din VM](./media/virtual-machines-windows-hero-tutorial/ipaddress.png)

2. Åbn en browser, og skriv din offentlige IP-adresse således i adresselinjen skal: http://<publicIPaddress> , og klik på **Enter** for at gå til denne adresse.
3. Din browser skal åbne standard IIS-webside. Det ser nogenlunde således ud:

    ![Skærmbillede, der viser, hvordan IIS standardsiden ser ud i en browser](./media/virtual-machines-windows-hero-tutorial/iis-default.png)

    

## <a name="next-steps"></a>Næste trin

- Du kan også eksperimentere med [vedhæfte en datadisk](virtual-machines-windows-attach-disk-portal.md) til din computer og virtuel. Datadisce giver mere lagerplads til din virtuelle maskine.
