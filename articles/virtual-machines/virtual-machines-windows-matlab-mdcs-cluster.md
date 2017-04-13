<properties
   pageTitle="MATLAB klynger på virtuelle maskiner | Microsoft Azure"
   description="Brug virtuelle Microsoft Azure-computere til at oprette MATLAB Distributed Computing-serverklynger for at køre din Beregn-intensivt parallelle MATLAB arbejdsmængder"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mscurrell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Windows"
   ms.workload="infrastructure-services"
   ms.date="05/09/2016"
   ms.author="markscu"/>

# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Oprette MATLAB Distributed Computing serverklynger på Azure FOS 

Bruge virtuelle Microsoft Azure-computere for at oprette en eller flere MATLAB Distributed Computing serverklynger for at køre din Beregn-intensivt parallelle MATLAB arbejdsmængder. Installere softwaren MATLAB Distributed Computing Server på en VM bruge som en grundlæggende afbildning og bruge en skabelon Azure Hurtig start eller Azure PowerShell-script (findes på [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) til at installere og administrere klyngen. Oprette forbindelse til klynge til at køre din arbejdsbelastninger efter installation. 

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Om MATLAB og MATLAB Distributed Computing Server 

[MATLAB](http://www.mathworks.com/products/matlab/) platform er udviklet til at løse problemer med teknisk og videnskabelig. MATLAB brugere med store simulering og databehandling opgaver kan bruge MathWorks parallel databehandling produkter at øge hastigheden af deres Beregn-intensivt arbejdsmængder ved at drage fordel af computerklynger og gitter tjenester. [Parallelle Computing værktøjskassen](http://www.mathworks.com/products/parallel-computing/) giver MATLAB brugere parallelize programmer og drage fordel af flere processorer, GPU'er, og beregne klynger. [MATLAB Distributed Computing Server](http://www.mathworks.com/products/distriben/) giver MATLAB brugere at udnytte mange computere i en computerklynge. 


Ved hjælp af Azure virtuelle maskiner, kan du oprette MATLAB Distributed Computing serverklynger, der har alle de samme rådighed til at sende parallelle arbejde som lokale klynger, som interaktive job, batchjob, uafhængige opgaver og udkommunikeret opgaver. Brug af Azure sammen med MATLAB-platformen har mange fordele sammenlignet med klargøring og bruge traditionelle on-premises hardware: et område af virtuelt størrelse, oprettelse af klynger efter behov, så du betaler kun for ressourcerne, Beregn du anvendelse og muligheden for at teste modeller skaleres til brug.  

## <a name="prerequisites"></a>Forudsætninger

* **Klientcomputeren** – du skal bruge en Windows-baseret klientcomputer til at kommunikere med Azure og MATLAB Distributed Computing Server-klynge efter installation. 

* **Azure PowerShell** - se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) til at installere den på klientcomputeren. 

* **Azure abonnement** - Hvis du ikke har et abonnement, kan du oprette en [gratis konto](https://azure.microsoft.com/free/) på blot et par minutter. Større klynger, kan du overveje et overkommelige abonnement eller andre Købsmuligheder. 

* **Kerner kvote** - det være nødvendigt at øge kvoten core for at installere en stor klynge eller mere end én MATLAB Distributed Computing Server-klynge. At øge en kvote, [Åbn en anmodning om online kundesupport](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratis. 

* **MATLAB, parallelle Computing værktøjskasse og MATLAB Distributed Computing Server licenser** - scripts forudsætter, at [MathWorks hostet licens Manager](http://www.mathworks.com/products/parallel-computing/mathworks-hosted-license-manager/) bruges til alle licenser.  

* **MATLAB Distributed Computing serversoftware** - installeres på en VM, der skal bruges som den grundlæggende VM afbildning for klynge FOS. 


## <a name="high-level-steps"></a>Høj niveau trin

De følgende overordnede trin er påkrævet for at bruge Azure virtuelle maskiner til dine MATLAB Distributed Computing serverklynger. Der er detaljerede oplysninger i dokumentationen til tilhørende Hurtig start skabelon og scripts på [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Oprette en grundlæggende VM afbildning**  
    * Hent og Installer MATLAB Distributed Computing Server software på denne VM. 

    >[AZURE.NOTE]Denne proces kan tage et par timer, men du kun behøver at gøre det, når du bruger til hver version af MATLAB.   
    
2. **Oprette en eller flere klynger**  
    * Brug de medfølgende PowerShell-script eller bruge skabelonen Hurtig start til at oprette en klynge fra den grundlæggende VM afbildning.   
    * Administrere klynger ved hjælp af medfølgende PowerShell-script, som gør det muligt at liste, midlertidigt afbryde, genoptage, og Slet klynger. 
 
## <a name="cluster-configurations"></a>Klyngekonfigurationer 

I øjeblikket gør klynge oprettelse af script og skabelon det muligt at oprette en enkelt MATLAB Distributed Computing Server topologi. Hvis du vil oprette en eller flere yderligere klynger med hver klynge, har et forskelligt antal arbejder FOS, ved hjælp af forskellige VM størrelser, og så videre. 

### <a name="matlab-client-and-cluster-in-azure"></a>MATLAB klient og klynge i Azure 

MATLAB klient node, MATLAB Opgavestyring node og MATLAB Distributed Computing Server "arbejder" noder er alle konfigureret som Azure FOS i et virtuelt netværk, som vist i følgende figur. 

![Klynge topologi](./media/virtual-machines-windows-matlab-mdcs-cluster/mdcs_cluster.png)

* Hvis du vil bruge-klyngen, forbinde ved Remote Desktop til klientnoden. Klientnoden kører MATLAB klienten. 

* Klientnoden har et filshare, der kan åbnes af alle medarbejdere.

* MathWorks hostet licens Manager bruges til licens Kontroller til alle MATLAB software. 

* Én MATLAB Distributed Computing Server arbejder per core er oprettet på arbejder FOS som standard, men du kan angive et andet tal. 


## <a name="use-an-azure-based-cluster"></a>Bruge en Azure-baserede klynge 

Som med andre typer MATLAB Distributed Computing serverklynger, skal du bruge klynge Profile Manager i MATLAB-klienten (på klienten VM) til at oprette en MATLAB Opgavestyring klynge profil.

![Klynge Profile Manager](./media/virtual-machines-windows-matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Næste trin

* Du kan finde detaljerede oplysninger til at installere og administrere MATLAB Distributed Computing serverklynger i Azure, den [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) lager, der indeholder skabeloner og scripts. 

* Gå til det [MathWorks websted](http://www.mathworks.com/) for detaljeret dokumentation til MATLAB og MATLAB Distributed Computing Server.
