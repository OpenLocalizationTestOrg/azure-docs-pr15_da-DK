<properties
 pageTitle="Oprette en HPC Pack hoved node i en Azure VM | Microsoft Azure"
 description="Lær at bruge portalen Azure og implementeringsmodel ressourcestyring til at oprette en Microsoft HPC Pack hoved node i en Azure VM."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/17/2016"
 ms.author="danlep"/>

# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Oprette noden hoved i en HPC Pack klynge i en Azure VM med et Marketplace billede


Bruge et [Microsoft HPC Pack virtuelt billede](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) fra Azure Marketplace og Azure portalen til at oprette noden hoved i en HPC-klynge. Dette HPC Pack VM billede er baseret på Windows Server 2012 R2 Datacenter med HPC Pack 2012 R2 Update 3 forudinstalleret. Brug denne hoved node for et bevis for konceptet installation af HPC Pack i Azure. Du kan derefter tilføje Beregn noder til klynge til at køre HPC arbejdsmængder.



>[AZURE.TIP]Du skal installere en komplet HPC Pack klynge i Azure, der omfatter hoved node og Beregn noder, anbefaler vi, at du bruger en automatisk metode. Indstillingerne for inkluderer [scriptet til implementering af HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) og skabelonen [HPC Pack klynge i Windows arbejdsbelastninger](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/) ressourcestyring. Du kan finde flere skabeloner i [HPC Pack klynge indstillinger i Azure](virtual-machines-windows-hpcpack-cluster-options.md) . 


## <a name="planning-considerations"></a>Overvejelser i forbindelse med

Som vist i følgende figur, installerer du noden HPC Pack hoved i en Active Directory-domæne i en Azure virtuelt netværk.

![HPC Pack hoved node][headnode]

* **Active Directory-domæne** - feltet HPC Pack hoved node skal være medlem af en Active Directory-domæne i Azure, før du starter HPC tjenesterne på VM. Som vist i denne artikel, til et bevis for konceptet installation, kan du hæve den VM, du opretter til noden hoved som et domænenavn fra domænecontrolleren før du starter HPC tjenesterne. En anden mulighed er at installere en separat domænecontrolleren og skov i Azure, du deltager i noden hoved VM.

* **Azure virtuelt netværk** – når du bruger implementeringsmodel ressourcestyring til at udrulle noden hoved skal du angive eller oprette en Azure virtuelt netværk. Du kan bruge det virtuelle netværk, hvis du vil deltage i noden hoved til en eksisterende Active Directory-domæne. Du skal også den senere for at tilføje beregningsnode FOS til klyngen.

    
## <a name="steps-to-create-the-head-node"></a>Trin til at oprette noden hoved

Følgende er overordnede trin til at bruge Azure-portalen til at oprette en Azure VM for noden HPC Pack hoved ved hjælp af Ressourcestyring implementeringsmodel. 


1. Hvis du vil oprette et nyt Active Directory-område i Azure med separat domænenavn fra domænecontrolleren FOS, er en af mulighederne at bruge en [ressourcestyring skabelon](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/). Det er fint til at springe dette trin over og konfigurere noden hoved VM sig selv som et domænenavn fra domænecontrolleren for et enkelt bevis for konceptet installation. Denne indstilling er beskrevet senere i denne artikel.
    
2. Klik på **Opret virtuelt** [HPC Pack 2012 R2 på Windows Server 2012 R2 side](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) i Azure Marketplace. 

3. Vælg implementeringsmodel **Ressourcestyring** i portalen, på siden **HPC Pack 2012 R2 på Windows Server 2012 R2** , og klik derefter på **Opret**.

    ![Billede af HPC Pack][marketplace]

4. Bruge portal til at konfigurere indstillingerne og oprette VM. Hvis du er ny bruger Azure, skal du følge selvstudiet [oprette virtuelt et Windows Azure-portalen](virtual-machines-windows-hero-tutorial.md). Et bevis for konceptet installation, du kan normalt acceptere standardindstillingen eller anbefalede indstillinger.

    >[AZURE.NOTE]Hvis du vil deltage i noden hoved til en eksisterende Active Directory-domæne i Azure, skal du kontrollere du angiver det virtuelle netværk for domænet, når du opretter VM.
       
4. Når du har oprettet VM og VM kører, [oprette forbindelse til VM](virtual-machines-windows-connect-logon.md) ved Fjernskrivebord. 

5. Deltage i VM til en eksisterende domæner, eller Opret en domæner på selve VM.

    * Hvis du har oprettet VM i en Azure virtuelt netværk med en eksisterende domæner, kan du deltage VM til området ved hjælp af standard-Server Manager eller Windows PowerShell-værktøjerne. Genstart derefter.

    * Hvis du har oprettet VM i et nyt virtuelt netværk (uden en eksisterende domæner), derefter hæve VM som et domænenavn fra domænecontrolleren. Bruge almindelige trin til at installere og konfigurere rollen Active Directory Domain Services på noden hoved. Detaljeret vejledning, skal du se [installere en ny Windows Server 2012 Active Directory-område](https://technet.microsoft.com/library/jj574166.aspx).

5. Når VM kører og er tilsluttet et Active Directory-område, skal du starte HPC Pack tjenesterne på følgende måde:

    en. Oprette forbindelse til noden hoved VM ved hjælp af en domænekonto, der er medlem af den lokale administratorgruppe. For eksempel bruge den du konfigurere, da du oprettede noden hoved VM administratorkonto.

    b. Starte Windows PowerShell som administrator for en standard hoved node konfiguration, og skrive følgende:

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    Det kan tage flere minutter, før HPC Pack tjenesterne til at starte.

    For yderligere hoved node konfigurationsindstillinger, Skriv `get-help HPCHNPrepare.ps1`.


## <a name="next-steps"></a>Næste trin

* Nu kan du arbejde med noden hoved i din HPC Pack klynge. For eksempel start HPC klynge Manager, og Fuldfør [Installation opgaveliste](https://technet.microsoft.com/library/jj884141.aspx).
* Hvis du vil øge klyngen beregne kapacitet efter behov, tilføje [Azure burst noder](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md) i en skybaseret tjeneste. 

* Prøv at køre en test arbejdsbyrde på klyngen. Se et eksempel på HPC Pack i [Introduktion til](https://technet.microsoft.com/library/jj884144).

<!--Image references-->
[headnode]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/marketplace.png
