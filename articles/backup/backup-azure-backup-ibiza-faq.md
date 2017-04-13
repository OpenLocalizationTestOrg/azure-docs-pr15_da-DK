<properties
   pageTitle="Gendannelse Services samling ofte stillede spørgsmål om | Microsoft Azure"
   description="Denne version af ofte stillede spørgsmål understøtter offentlige Preview-versionen af tjenesten Azure sikkerhedskopi. Svar på ofte stillede spørgsmål om sikkerhedskopiering agent, sikkerhedskopiering og opbevaring, gendannelse, sikkerhed og andre almindelige spørgsmål om Azure sikkerhedskopiering løsningen."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="løsning til sikkerhedskopiering; Sikkerhedskopiering service"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="10/21/2016"
     ms.author="trinadhk; markgal; jimpark;"/>

# <a name="recovery-services-vault---faq"></a>Gendannelse Services samling – ofte stillede spørgsmål


Denne artikel indeholder oplysninger, der er specifikke for gendannelse Services samling og er et det tillæg [Azure sikkerhedskopi ofte stillede spørgsmål](backup-azure-backup-faq.md). Ofte stillede spørgsmål med Azure sikkerhedskopi giver det samlede sæt af spørgsmål og svar om tjenesten Azure sikkerhedskopi.  

Du kan stille spørgsmål om Azure sikkerhedskopi i afsnittet Disqus i denne artikel eller en relateret artikel. Du kan også sende spørgsmål om tjenesten Azure sikkerhedskopi i [diskussionsforum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>Gendannelse Services vaults er ressourcestyring baseret. Understøttes sikkerhedskopi vaults (tilstanden Klassisk) stadig? <br/>
Ja, sikkerhedskopi vaults understøttes stadig. Oprette sikkerhedskopi vaults i [Klassisk portal](https://manage.windowsazure.com). Oprette gendannelse Services vaults i [Azure-portalen](https://portal.azure.com). Men vi anbefaler muligt at oprette gendannelse services samling som alle fremtidige forbedringer fås kun i gendannelse Services samling.

## <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Kan jeg overføre en sikkerhedskopi samling til en samling af legitimationsoplysninger gendannelsestjenester? <br/>
Desværre ikke, kan ikke på nuværende tidspunkt du overføre indholdet af en sikkerhedskopi samling til en samling af legitimationsoplysninger gendannelsestjenester. Vi arbejder på at tilføje denne funktionalitet, men den er ikke tilgængelig som en del af Public Preview.

## <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Understøtter gendannelse Services vaults klassisk FOS eller Ressourcestyring baseret FOS? <br/>
Gendannelse Services vaults understøtter begge modeller.  Du kan sikkerhedskopiere en VM, der er oprettet i portalen klassisk (som er tilstanden Klassisk FOS) eller en VM, der er oprettet i portalen Azure (som er ressourcestyring baseret) til en samling af legitimationsoplysninger gendannelsestjenester.

## <a name="i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode--how-can-i-backup-them-in-recovery-services-vault"></a>Jeg har sikkerhedskopieret min klassisk FOS i sikkerhedskopiering samling. Nu vil jeg overføre mit FOS fra tilstanden Klassisk til Ressourcestyring tilstand.  Hvordan kan jeg Sikkerhedskopiér dem i gendannelse services samling?
Sikkerhedskopier af klassisk FOS i sikkerhedskopiering samling ikke kan overføres automatisk til gendannelse services samling når du overfører FOS fra klassisk til Ressourcestyring tilstand. Følg disse trin for overførsel af VM sikkerhedskopier:

1. Gå til fanen **Beskyttede elementer** i sikkerhedskopiering samling, og vælg VM. Klik på [Stop beskyttelse](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines). Lad *slette tilknyttede sikkerhedskopidata* indstilling **ikke er markeret**.
2. Overføre den virtuelle maskine fra tilstanden Klassisk til Ressourcestyring tilstand. Sørg for, lager og netværk, der svarer til virtuelt også er overført til Ressourcestyring tilstand.
3. Oprette en gendannelse services samling af legitimationsoplysninger og konfigurere sikkerhedskopi på en overført virtuel maskine ved hjælp af **Sikkerhedskopiering** handlingen oven på samling dashboard. Få mere at vide mere om, hvordan du [aktiverer sikkerhedskopiering i gendannelse services samling](backup-azure-vms-first-look-arm.md)
