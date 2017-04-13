<properties
   pageTitle="Nulstille en lokal Windows-adgangskode, når Azure gæst agent ikke er installeret | Microsoft Azure"
   description="Sådan nulstilles adgangskoden til en lokal Windows-brugerkonto, når Azure gæst agent ikke er installeret eller fungerer på en VM"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="iainfou"/>

# <a name="how-to-reset-local-windows-password-for-azure-vm"></a>Hvordan du nulstiller lokale adgangskode til Windows Azure VM
Du kan nulstille den lokale Windows-adgangskode for en VM i Azure ved hjælp af [Azure portal eller Azure PowerShell](virtual-machines-windows-reset-rdp.md) Azure gæst agent er installeret. Denne metode er den primære metode til at nulstille en adgangskode til en Azure VM. Hvis du støder på problemer med tjenesten Azure gæst agent reagerer ikke eller ikke blev installeret, når du har overført et brugerdefineret billede, du kan manuelt nulstille en adgangskode til Windows. I denne artikel indeholder oplysninger om hvordan du nulstiller en lokal kontoadgangskode ved at knytte den kilde OS virtuelle disk til en anden VM. 

> [AZURE.WARNING] Brug kun denne proces som endelig. Altid forsøge at nulstille en adgangskode ved hjælp af [Azure portal eller Azure PowerShell](virtual-machines-windows-reset-rdp.md) først.


## <a name="overview-of-the-process"></a>Oversigt over processen
De grundlæggende trin for at udføre en lokal nulstilling af adgangskode for en Windows-VM i Azure når der er ingen adgang til Azure gæst agent er som følger:

- Slette kilden VM. De virtuelle disk bevares.
- Vedhæft den kilde VM OS disk til en anden VM i abonnementet Azure. Denne VM kaldes fejlfinding VM.
- Med fejlfinding VM kan du oprette nogle config filerne på den kilde VM OS disk.
- Fjerne den VM OS disk fra fejlfinding VM.
- Bruge en ressourcestyring skabelon til at oprette en VM, ved hjælp af den oprindelige virtuelle disk.
- Når den nye VM startes, opdatere adgangskoden for den bruger, der kræves config filerne, du opretter.


## <a name="detailed-steps"></a>Detaljeret vejledning
Altid forsøge at nulstille en adgangskode ved hjælp af [Azure portal eller Azure PowerShell](virtual-machines-windows-reset-rdp.md) før du prøver at gøre følgende. Sørg for, at du har en sikkerhedskopi af din VM, før du går i gang. 

1. Slette den pågældende VM Azure-portalen. Slette VM kun sletter metadata, referencen til VM i Azure. De virtuelle disk bevares, når VM slettes:

    - Vælg VM i Azure-portalen, klik på *Slet*:

    ![Slette eksisterende VM](./media/virtual-machines-windows-reset-local-password-without-guest-agent/delete_vm.png)

2. Vedhæft den kilde VM OS disk til fejlfinding i forbindelse med VM. Fejlfinding i forbindelse med VM skal være i samme område som den kilde VM OS disk (såsom `West US`):

    - Vælg den fejlfinding VM i portalen Azure. Klik på *diske* | *Vedhæft eksisterende*:

    ![Vedhæfte eksisterende disk](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_attach_existing.png)

    Vælg *Virtuelle fil* , og vælg derefter kontoen lagerplads, der indeholder din kilde VM:

    ![Vælg lagerplads konto](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_storageaccount.PNG)

    Vælg objektbeholderen kilde. Objektbeholderen kilde er typisk *virtuelle harddiske*:

    ![Vælg objektbeholder til lagring](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_container.png)

    Vælg den virtuelle harddisk OS vedhæfte. Klik på *Vælg* for at fuldføre processen:

    ![Vælg kilde Virtuel disk](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_source_vhd.png)

3. Oprette forbindelse til fejlfinding i forbindelse med VM ved hjælp af Fjernskrivebord, og sikre, at den kilde VM OS disken er synlige:

    - Vælg den fejlfinding VM i portalen Azure, og klik på *Opret forbindelse*.
    - Åbn den RDP-fil, der er hentet. Angiv brugernavnet og adgangskoden til fejlfinding i forbindelse med VM.
    - I Stifinder skal du se efter cd'en data, du har vedhæftet. Hvis kilden VMS Virtuelle er knyttet til fejlfinding i forbindelse med VM kun data disken, skal det være F: drev:

    ![Få vist vedhæftede datadisk](./media/virtual-machines-windows-reset-local-password-without-guest-agent/troubleshooting_vm_fileexplorer.png)

4. Oprette `gpt.ini` i `\Windows\System32\GroupPolicy` på den kilde VM drev (hvis gpt.ini findes, Omdøb til gpt.ini.bak):

    > [AZURE.WARNING] Sørg for, at du ikke ved et uheld opretter følgende filer i C:\Windows, OS drev til fejlfinding i forbindelse med VM. Oprette følgende filer i OS drevet til kilden VM, som er vedhæftet som en datadisk.

    - Tilføj følgende linjer i den `gpt.ini` fil, du oprettede:

    ```
    [General]
    gPCFunctionalityVersion=2
    gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
    Version=1
    ```

    ![Oprette gpt.ini](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_gpt_ini.png)
 
5. Oprette `scripts.ini` i `\Windows\System32\GroupPolicy\Machine\Scripts`. Kontrollér, at skjulte mapper vises. Hvis det er nødvendigt, kan du oprette den `Machine` eller `Scripts` mapper.

    - Tilføj følgende linjer på `scripts.ini` fil, du oprettede:

    ```
    [Startup]
    0CmdLine=C:\Windows\System32\FixAzureVM.cmd
    0Parameters=
    ```

    ![Oprette scripts.ini](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_scripts_ini.png)
 
6. Oprette `FixAzureVM.cmd` i `\Windows\System32` med følgende indholdet, erstatte `<username>` og `<newpassword>` med dine egne værdier:

    ```
    NET USER <username> <newpassword>
    ```

    ![Oprette FixAzureVM.cmd](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_fixazure_cmd.png)

    Du skal opfylde kravene til kompleksitet konfigureret adgangskoden til din VM, når du definerer den nye adgangskode.

7. Fjerne disken fra fejlfinding VM Azure-portalen:

    - Klik på *diske*Markér fejlfinding VM i portalen Azure.
    - Vælge data disken vedhæftet i trin 2 skal du klikke på *Afbryd*:

    ![Fjerne disk](./media/virtual-machines-windows-reset-local-password-without-guest-agent/detach_disk.png)

8. Før du opretter en VM, kan du hente URI, der harddisken kilde OS:

    - Vælg kontoen, lagerplads på portalen Azure skal du klikke på *BLOB*.
    - Vælg objektbeholderen. Objektbeholderen kilde er typisk *virtuelle harddiske*:

    ![Vælg lagerplads konto blob](./media/virtual-machines-windows-reset-local-password-without-guest-agent/select_storage_details.png)

    Vælg din kilde VM OS Virtuelle, og klik på knappen *Kopiér* ud for *URL-adressen* :

    ![Kopiere disk URI](./media/virtual-machines-windows-reset-local-password-without-guest-agent/copy_source_vhd_uri.png)

9. Oprette en VM fra den kilde VM OS disk:

    - Brug [denne skabelon til Azure ressourcestyring](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) til at oprette en VM fra en særlig virtuel harddisk. Klik på den `Deploy to Azure` knap for at åbne portalen Azure med skabelonbaseret oplysninger udfyldt for dig.
    - Hvis du vil bevare alle de tidligere indstillinger for VM, skal du vælge *Rediger skabelon* for at give dine eksisterende VNet, undernet, netværkskort eller offentlige IP.
    - I den `OSDISKVHDURI` parameter tekstboks, Indsæt URI af dine imidlertid Virtuelle få i det foregående trin:

    ![Oprette en VM fra skabelon](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_new_vm_from_template.png)

10. Når den nye VM kører, kan du oprette forbindelse til VM ved hjælp af Fjernskrivebord med den nye adgangskode, du har angivet i den `FixAzureVM.cmd` script.

11. Fjerne følgende filer til at rydde op miljøet fra din fjernsession til den nye VM:

    - Fra %windir%\System32
        - fjerne FixAzureVM.cmd
    - Fra %windir%\System32\GroupPolicy\Machine\
        - fjerne scripts.ini
    - Fra %windir%\System32\GroupPolicy
        - fjerne gpt.ini (hvis gpt.ini fandtes før, og du omdøbte dem til gpt.ini.bak, Omdøb .bak-filen tilbage til gpt.ini)

## <a name="next-steps"></a>Næste trin
Hvis du stadig ikke oprette forbindelse ved hjælp af Fjernskrivebord, kan du se [RDP fejlfinding vejledning](virtual-machines-windows-troubleshoot-rdp-connection.md). [Detaljeret vejledning til fejlfinding af RDP](virtual-machines-windows-detailed-troubleshoot-rdp.md) ser på metoder i stedet for bestemte trin til fejlfinding. Du kan også [åbne en anmodning om Azure support](https://azure.microsoft.com/support/options/) til praktiske Hjælp.