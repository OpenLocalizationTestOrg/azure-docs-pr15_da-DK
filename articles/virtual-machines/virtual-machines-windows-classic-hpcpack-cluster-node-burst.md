<properties
 pageTitle="Føje burst noder til en HPC Pack klynge | Microsoft Azure"
 description="Lær, hvordan du kan udvide en HPC Pack klynge i Azure efter behov ved at tilføje arbejder rolle forekomster, der kører i en skybaseret tjeneste"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="10/14/2016"
 ms.author="danlep"/>

# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>Føje efter behov "burst" noder til en HPC Pack klynge i Azure



Hvis du har konfigureret en [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) klynge i Azure, har du måske en metode til hurtigt at skalere klynge kapaciteten, op eller ned, uden at opretholde et sæt forudkonfigurerede beregningsnode FOS. I denne artikel beskrives, hvordan til at tilføje efter behov "burst" noder (arbejder rolle forekomster kører i en skybaseret tjeneste) som Beregn ressourcer til en hoved node i Azure. 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

![Burst noder][burst]

Trinnene i denne artikel hjælpe dig med at føje Azure noder hurtigt til en skybaseret HPC Pack hoved node VM for en test eller bevis af konceptet installation. De overordnede trin er de samme som trinnene for at "burst til Azure" tilføje skyen beregne kapacitet til en lokal HPC Pack klynge. Se [konfigurere en hybrid beregne klynge med Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)et selvstudium. Se [Burst til Azure med Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)til detaljeret vejledning og overvejelser i forbindelse med fremstilling installationer.


## <a name="prerequisites"></a>Forudsætninger

* **HPC Pack hoved node implementeret i en Azure VM** - du kan bruge en separat hoved node VM eller en, der er en del af en større klynge. For at oprette en enkeltstående hoved node skal du se [Implementer en HPC Pack hoved Node i en Azure VM](virtual-machines-windows-hpcpack-cluster-headnode.md). Se [Indstillinger for at oprette og administrere en Windows HPC klynge i Azure med Microsoft HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md)til automatiseret HPC Pack klynge installationsindstillinger.

    >[AZURE.TIP] Hvis du bruger [scriptet til implementering af HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) til at oprette klyngen i Azure, kan du medtage Azure burst noder i din automatiseret installation. Se eksemplerne i denne artikel.

* **Azure abonnement** - tilføje Azure noder, kan du vælge det samme abonnement, der bruges til at installere noden hoved VM, eller et andet abonnement (eller abonnementer).

* **Kerner kvote** - det være nødvendigt at øge kvoten kerner, især hvis du vælger at installere flere Azure noder med Multifacetteret størrelser. At øge en kvote, [Åbn en anmodning om online kundesupport](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratis.

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>Trin 1: Oprette en skybaseret tjeneste og en lagerplads konto for noderne Azure

Brug Azure klassisk portal eller tilsvarende værktøj til at konfigurere følgende ressourcer, der skal bruges til at udrulle din Azure noder:

* En ny Azure skybaseret tjeneste
* En ny konto Azure-lager

>[AZURE.NOTE] Ikke genbruge en eksisterende skybaseret tjeneste i dit abonnement. 

**Overvejelser i forbindelse med**

* Konfigurere en separat skybaseret tjeneste for hver Azure node skabelon, du planlægger at oprette. Du kan dog bruge den samme konto lagerplads for flere node skabeloner.

* Vi anbefaler, at du placerer skytjenesten og lagerplads kontoen for installationen i det samme Azure område.




## <a name="step-2-configure-an-azure-management-certificate"></a>Trin 2: Konfigurer et Azure management certifikat

Hvis du vil tilføje Azure noder som Beregn ressourcer, skal du et management certifikat hoved node og Overfør en tilsvarende certifikat til Azure abonnementet bruges til installationen.

I dette scenario, kan du vælge **Standard HPC Azure Management certifikat** , der HPC Pack installerer og konfigurerer automatisk på noden hoved. Dette certifikat er nyttig til test formål og test af koncept installationer. For at bruge dette certifikat skal du overføre filer C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer fra noden hoved VM til abonnementet. Klik på **Indstillinger**for at overføre certifikatet i [Azure klassisk portal](https://manage.windowsazure.com), > **Management certifikater**.

Du kan finde yderligere indstillinger til konfiguration af administration certifikatet [scenarier for at konfigurere Azure Management-certifikat til Azure Burst installationer](http://technet.microsoft.com/library/gg481759.aspx).

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>Trin 3: Installere Azure noder til klyngen



Trinnene for at tilføje og starte Azure noder i dette scenario er normalt den samme som trinnene med en lokal hoved node. Se følgende afsnit i [trin til at installere Azure noder med Microsoft HPC Pack](https://technet.microsoft.com/library/gg481758.aspx)kan finde flere oplysninger:

* Oprette en skabelon til Azure node

* Føje Azure noder til Windows HPC-klynge

* Starte (klargøre) på Azure noder

Når du tilføjer og starte noderne, er de klar til at bruge til at køre klynge job.

Hvis du støder på problemer, når du installerer Azure noder, se [Fejlfinding i forbindelse med installationer af Azure noder med Microsoft HPC Pack](http://technet.microsoft.com/library/jj159097.aspx).

## <a name="next-steps"></a>Næste trin

* For at bruge en Beregn-intensivt forekomst størrelse for noderne burst, skal du se overvejelser i [om H-serien og Beregn-intensivt A-serien FOS](virtual-machines-windows-a8-a9-a10-a11-specs.md).

* Hvis du vil automatisk Forøg eller Formindsk Azure dataressourcer ifølge klynge arbejdsbelastningen, skal du se [automatisk forøge og formindske Azure Beregn ressourcer i en HPC Pack klynge](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-cluster-node-burst/burst.png
