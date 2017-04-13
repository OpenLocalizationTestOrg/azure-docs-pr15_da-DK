<properties
    pageTitle="Overføre Azure IaaS virtuelle maskiner fra én Azure område til en anden med gendannelse af websteder | Microsoft Azure"
    description="Brug Azure gendannelse af websteder til at overføre Azure IaaS virtuelle maskiner fra én Azure område til en anden."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/21/2016"
    ms.author="raynew"/>

#  <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Overføre Azure IaaS virtuelle maskiner mellem Azure områder med Azure gendannelse af websteder

## <a name="overview"></a>Oversigt

Velkommen til gendannelse af Azure websteder! Brug denne artikel, hvis du vil overføre Azure FOS mellem Azure områder. Inden du starter, Bemærk, at:

- Azure har to forskellige installation modeller til oprettelse og arbejde med ressourcer: Azure ressourcestyring og klassisk. Azure har også to portaler – Azure klassisk portalen, der understøtter den klassiske implementeringsmodel og Azure portalen med understøttelse af begge installation-modeller. De grundlæggende trin til overførsel er den samme, uanset om du er ved at konfigurere gendannelse af websteder i ressourcestyring eller klassisk. Men Brugergrænsefladen instruktioner og skærmbilleder i denne artikel er relevante for portalen Azure.
- **I øjeblikket kan du kun overføre fra et område til en anden. Du kan ikke over FOS fra én Azure område til en anden, men du kan ikke dem tilbage igen.**
- Overførsel vejledningen i denne artikel er baseret på instruktionerne til replikering en fysisk maskine til Azure. Den indeholder links til trinnene i [gentage VMware FOS eller fysiske servere til Azure](site-recovery-vmware-to-azure.md), som beskriver, hvordan du overfører en fysisk server Azure-portalen.
- Hvis du konfigurerer gendannelse af websteder i portalen klassisk, skal du følge vejledningerne i [denne artikel](site-recovery-vmware-to-azure-classic.md). **Du ikke længere skal bruge** instruktionerne i denne [artikel ældre](site-recovery-vmware-to-azure-classic-legacy.md).

Sende en hvilken som helst kommentarer eller spørgsmål nederst i denne artikel eller på [Azure gendannelse Services-forummet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prerequisites"></a>Forudsætninger

Her er, hvad du skal bruge til denne installation:

- **Konfiguration af server**: en lokal VM, der kører Windows Server 2012 R2, der fungerer som server configuration. Du installerer (herunder proces server og master target server) komponenter for gendannelse af websteder på denne VM for. Få mere at vide i [scenarie arkitektur](site-recovery-vmware-to-azure.md#scenario-architecture) og [konfiguration af server forudsætninger](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).
- **IaaS virtuelle maskiner**: feltet FOS, du vil overføre. Du kan overføre disse FOS ved at behandle dem som fysiske computere.

## <a name="deployment-steps"></a>Installation trin

I dette afsnit beskrives installation trinnene i den nye Azure portal. Hvis du skal bruge følgende installation til gendannelse af websteder i portalen klassisk, se i [denne artikel](site-recovery-vmware-to-azure-classic.md).

1. [Opret en samling af legitimationsoplysninger](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Implementer en server configuration](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. Når du har installeret server configuration, kan du se, at det kan kommunikere med de FOS, du vil overføre.
4. [Konfigurere indstillinger for gentagelse](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Opret en politik for gentagelse, og Tildel til af konfigurationsserveren.
5. [Installere tjenesten mobilitet](site-recovery-vmware-to-azure.md#step-6-replication-application). Hver VM, du vil beskytte skal tjenesten mobilitet installeret. Denne tjeneste sender data til processerveren. Tjenesten mobilitet kan installeres manuelt eller overføres og installeres automatisk af processen serveren, når beskyttelse af VM er aktiveret. Firewallregler på FOS, du vil overføre skal konfigureres til at tillade opslagsnål installation af denne tjeneste.
6. [Aktivere gentagelse](site-recovery-vmware-to-azure.md#enable-replication). Aktiver replikering for de FOS, du vil overføre. Du kan finde de IaaS virtuelle maskiner, du vil overføre til Azure via privat IP-adressen for de virtuelle computere. Du kan finde denne adresse på dashboardet til virtuelt i Azure. Når du aktiverer gentagelse, kan du angive computertypen for FOS som fysiske computere.
7. [Køre en ikke-planlagt failover](site-recovery-failover.md#run-an-unplanned-failover). Når første replikering er fuldført, kan du køre en ikke-planlagt failover fra én Azure område til en anden. Du kan også kan du oprette en plan for gendannelse og køre en ikke-planlagt failover, hvis du vil overføre flere virtuelle maskiner mellem områder. Du kan [Få mere at vide](site-recovery-create-recovery-plans.md) om gendannelse planer.

## <a name="next-steps"></a>Næste trin

Få flere oplysninger om andre replikering scenarier i [Hvad er Azure gendannelse af websteder?](site-recovery-overview.md)
