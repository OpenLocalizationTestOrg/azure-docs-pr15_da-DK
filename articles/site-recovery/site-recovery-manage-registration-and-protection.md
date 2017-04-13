<properties
    pageTitle="Fjerne servere og deaktivere beskyttelse | Microsoft Azure" 
    description="I denne artikel beskrives, hvordan registreringen servere fra en samling af legitimationsoplysninger gendannelse af websteder, og du vil deaktivere beskyttelse til virtuelle computere og fysiske servere." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016" 
    ms.author="raynew"/>

# <a name="remove-servers-and-disable-protection"></a>Fjerne servere og deaktivere beskyttelse

Gendannelse af websteder Azure service bidrager til din business løbende og genoprettelse efter genoprettelse (BCDR) strategi ved orchestrating gentagelse, failover og gendannelse af virtuelle maskiner og fysiske servere. Computere kan replikeres til Azure eller til et sekundært lokalt datacenter. En hurtig oversigt over læse [Hvad er Azure gendannelse af websteder?](site-recovery-overview.md)

## <a name="overview"></a>Oversigt

I denne artikel beskrives, hvordan du unregister servere fra samling af legitimationsoplysninger gendannelse af websteder og hvordan du deaktiverer beskyttelse af virtuelle maskiner, der er beskyttet af gendannelse af websteder. 

Sende en hvilken som helst kommentarer eller spørgsmål nederst i denne artikel eller på [Azure gendannelse Services-forummet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="unregister-a-vmm-server"></a>Unregister en VMM server

Du kan fjerne registreringen af en VMM server fra en samling ved at slette server under fanen **servere** på portalen Azure gendannelse af websteder. Bemærk, at:

-  **Forbindelse VMM server**: Vi anbefaler, at du unregister VMM serveren, når den er tilsluttet til Azure. Dette sikrer, at indstillingerne for lokalt VMM serveren, og de VMM servere, der er knyttet til den (VMM servere, der indeholder skyer, som er knyttet til skyer på den server, du vil slette) ryddes korrekt. Vi anbefaler, at du kun fjerne en afbrudt forbindelsen server, hvis der er et permanent problem med connectivity.
- **Afbrudt forbindelsen VMM server**: Hvis VMM serveren ikke er tilsluttet, når du sletter den skal du køre et script manuelt for at udføre oprydningen. Scriptet er tilgængelig i [Microsoft-galleriet](http://aka.ms/asr-cleanup-script-vmm). Bemærk VMM ID serveren for at fuldføre processen manuel oprydning.
- **VMM server i klynge**: Hvis du har brug at fjerne registreringen af en VMM server, der er implementeret i en klynge du gøre følgende:

    - Hvis serveren er forbundet, skal du slette den tilsluttede VMM server under fanen **servere** . Log på på hver klyngenode for at fjerne udbyderen på serveren, og fjerne dem via Kontrolpanel. Kør scriptet oprydning, der refereres til i det forrige afsnit på alle passive noder i klynge for at slette registreringsposter.
    - Hvis serveren ikke er tilsluttet skal du køre scriptet oprydning på alle klyngenoder.

### <a name="unregister-an-unconnected-vmm-server"></a>Unregister en afbrudt forbindelsen VMM-server

Du vil fjerne på VMM serveren:

1. Unregister VMM serveren fra Azure-portalen.
2. Download oprydning scriptet på serveren VMM.
3. Åbn PowerShell med Kør som Administrator muligheden for at ændre udførelse af politikken for standardområdet (lokale computer).
4. Følg vejledningen i scriptet. 

På VMM servere, der har sky, der er parret med skyer på den server, du fjerner:

1. Kør scriptet Oprydning og følge trin 2 til 4.
2. Angiv VMM ID for den VMM server, der er blevet fjernet. 
3. Dette script, fjernes registreringsoplysninger for VMM Server og skyen parring oplysninger.


## <a name="unregister-a-hyper-v-server-in-a-hyper-v-site"></a>Unregister en Hyper-V server i et Hyper-V-websted

Når Azure gendannelse af websteder er installeret til at beskytte virtuelle maskiner, der er placeret på en Hyper-V server i et Hyper-V-websted (med nogen VMM server) kan du fjerne registreringen af en Hyper-V server fra en samling på følgende måde:

1. Deaktivere beskyttelse for virtuelle maskiner, der er placeret på Hyper-V-server.
2. Vælg serveren, under fanen **servere** på portalen Azure gendannelse af websteder > Slet. Serveren skal ikke være forbundet med Azure, når du gør dette.
3. Kør følgende script for at rydde op i indstillinger på serveren og unregister fra samling af legitimationsoplysninger. 

        pushd .
        try
        {
             $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
             $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
             $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
             $isAdmin=$principal.IsInRole($administrators)
             if (!$isAdmin)
             {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;       
             }
    
            $error.Clear()    
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host
        
            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }
        
            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }
        
            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
        
            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."  
                    Remove-Item -Recurse -Path $registrationPath
                }
    
                if (Test-Path $proxySettingsPath)
            {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }
    
                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {            
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                "Registry keys removed."
            }
    
            # First retrive all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {   
            [system.exception]
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0] 
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd


## <a name="stop-protecting-a-hyper-v-virtual-machine"></a>Stoppe med at beskytte en Hyper-V virtuel maskine

Hvis du vil fjerne beskyttelsen af en Hyper-V virtuel maskine skal du fjerne beskyttelse for den. Afhængigt af hvordan du fjerner beskyttelsen være det nødvendigt at rydde op i indstillingerne for dokumentbeskyttelse manuelt på computeren. 

### <a name="remove-protection"></a>Fjerne beskyttelsen

1. Vælg den virtuelle maskine i skyen egenskaber under fanen **virtuelle maskiner** > **Fjern**.
2. På siden **Bekræft fjernelse af Virtual Machine** har du et par indstillinger:

    - **Deaktivere beskyttelse**– Hvis du aktiverer og gemme denne indstilling, hvis den virtuelle maskine ikke længere være beskyttet af gendannelse af websteder. Indstillingerne for dokumentbeskyttelse til den virtuelle maskine der ryddes automatisk op.
    - **Fjerne fra samling af legitimationsoplysninger**– Hvis du vælger denne indstilling, hvis den virtuelle maskine, fjernes kun fra samling af legitimationsoplysninger gendannelse af websteder. Indstillingerne for dokumentbeskyttelse i det lokale miljø til den virtuelle maskine påvirkes ikke. Du skal rydde op i indstillinger for manuelt at fjerne indstillingerne for dokumentbeskyttelse og fjerne den virtuelle maskine fra Azure abonnement og fjerne indstillingerne for dokumentbeskyttelse skal du rydde dem op manuelt ved hjælp af vejledningen nedenfor.

Hvis du vælger for at slette den virtuelle maskine og dens harddiske fjernes de fra målplaceringen.

### <a name="clean-up-protection-settings-manually-between-vmm-sites"></a>Rydde op i indstillingerne for dokumentbeskyttelse manuelt (mellem VMM websteder)

Hvis du har valgt **stoppe med at administrere den virtuelle maskine**, manuelt rydde op i indstillinger:

1. Køre scriptet fra VMM console for at rydde op i indstillingerne for den primære virtuelle maskine på den primære server. Klik på knappen PowerShell for at åbne VMM PowerShell console i konsollen VMM. Erstat SQLVM1 med navnet på din virtuelle maskine.

         $vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. På den sekundære VMM server, kører denne script for at rydde op i indstillingerne for den sekundære virtuelle maskine:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force

3. På den sekundære VMM-server, når du har kørt scriptet opdatere virtuelle maskiner på Hyper-V host serveren så den sekundære virtuelle maskine igen bliver fundet i konsollen VMM.
4. Ovenstående trin, fjernes markeringen gentagelse indstillinger kun VMM server. Hvis du vil fjerne virtuelt gentagelse til den virtuelle maskine. Du skal gøre de nedenstående trin på både primært og sekundære virtuelle computere. Køre den under script til at fjerne gentagelse og erstatte SQLVM1 med navnet på din virtuelle maskine.

        Remove-VMReplication –VMName “SQLVM1”


### <a name="clean-up-protection-settings-manually-between-on-premises-vmm-sites-and-azure"></a>Rydde op i indstillingerne for dokumentbeskyttelse manuelt (mellem lokale VMM websteder og Azure)

1. Kører denne script for at rydde op i indstillingerne for den primære virtuelle maskine på VMM kildeserveren:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. Ovenstående trin, fjernes markeringen replikering indstillinger kun VMM server. Når du har fjernet sikre gentagelse fra VMM server for at fjerne gentagelse til den virtuelle maskine, der kører på serveren host Hyper-V med dette script. Erstat SQLVM1 med navnet på din virtuelle maskine og host01.contoso.com med navnet på serveren, host Hyper-V.

        $vmName = "SQLVM1"
        $hostName  = "host01.contoso.com"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

### <a name="clean-up-protection-settings-manually-between-hyper-v-sites-and-azure"></a>Rydde op i indstillingerne for dokumentbeskyttelse manuelt (mellem Hyper-V websteder og Azure)

1. Hyper-V host kildeserveren, hvis du vil fjerne gentagelse til den virtuelle maskine brug på dette script. Erstat SQLVM1 med navnet på din virtuelle maskine.

        $vmName = "SQLVM1"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

## <a name="stop-protecting-a-vmware-virtual-machine-or-a-physical-server"></a>Stoppe med at beskytte en VMware virtuel computer eller en fysisk server

Hvis du vil stoppe med at beskytte en VMware virtuel computer eller en fysisk server skal du fjerne beskyttelse for den. Afhængigt af hvordan du fjerner beskyttelsen være det nødvendigt at rydde op i indstillingerne for dokumentbeskyttelse manuelt på computeren. 

### <a name="remove-protection"></a>Fjerne beskyttelsen

1. Vælg den virtuelle maskine i skyen egenskaber under fanen **virtuelle maskiner** > **Fjern**.
2. Vælg en af indstillingerne på **fjerne virtuelt** :

    - **Deaktivere beskyttelse (brug for gendannelse analysér og lydstyrken tilpasning)**– der kun vises og aktivere denne indstilling, hvis du har:
        - **Resized virtuelt lydstyrken**– når du ændrer størrelsen på et drev, den virtuelle maskine er i en kritisk. Vælg denne indstilling, hvis dette sker. Det deaktiverer beskyttelse samtidig bevare gendannelse punkter i Azure. Når du genaktiverer beskyttelse til maskinen overføres dataene til størrelse er tilpasset lydstyrken til Azure.
        - **Køre en failover**– når du har kontrolleret dit miljø ved at køre en failover fra lokale VMware virtuelle maskiner eller fysiske servere til Azure, Vælg denne indstilling for at begynde at beskytte dine lokale virtuelle maskiner igen. Denne indstilling deaktiverer hver virtuelt, og derefter skal du genaktivere beskyttelse af dem. Bemærk, at:
            - Deaktivere den virtuelle maskine med denne indstilling påvirker ikke replika virtuel maskine i Azure.
            - Du skal fjerne tjenesten mobilitet fra den virtuelle maskine.
    
    - **Deaktivere beskyttelse**– Hvis du aktiverer og gemme denne indstilling, hvis computeren ikke længere være beskyttet af gendannelse af websteder. Indstillingerne for dokumentbeskyttelse for computeren der ryddes automatisk op.
    - **Fjerne fra samling af legitimationsoplysninger**– Hvis du vælger denne indstilling, hvis maskinen, fjernes kun fra samling af legitimationsoplysninger gendannelse af websteder. Lokalt beskyttelsesindstillinger for maskinen påvirkes ikke. Fjerne indstillinger på computeren og for at fjerne den virtuelle maskine fra Azure skal abonnement, og du rydde indstillingerne op ved at fjerne tjenesten mobilitet.
    
        ![Fjerne indstillinger](./media/site-recovery-manage-registration-and-protection/remove-vm.png)

















