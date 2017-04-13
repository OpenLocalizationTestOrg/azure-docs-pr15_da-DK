<properties
    pageTitle="Overføre Windows virtuelle maskiner fra Amazon webtjenester til Azure med gendannelse af websteder | Microsoft Azure"
    description="I denne artikel beskrives, hvordan du kan overføre Windows virtuelle maskiner, der kører i Amazon Web Services (AWA) til Azure ved hjælp af Azure gendannelse af websteder."
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
    ms.workload="backup-recovery"
    ms.date="08/22/2016"
    ms.author="raynew"/>

#  <a name="migrate-windows-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Overføre Windows virtuelle maskiner i Amazon Web Services (AWS) til Azure med Azure gendannelse af websteder

## <a name="overview"></a>Oversigt

Velkommen til gendannelse af Azure websteder. Brug denne artikel til at overføre Windows forekomster, der kører i AWS til Azure med gendannelse af websteder. Inden du starter, Bemærk, at:

- Azure har to forskellige installation modeller til oprettelse og arbejde med ressourcer: Azure ressourcestyring og klassisk. Azure har også to portaler – Azure klassisk portalen, der understøtter den klassiske implementeringsmodel og Azure portalen med understøttelse af begge installation-modeller. De grundlæggende trin til overførsel er den samme, uanset om du er ved at konfigurere gendannelse af websteder i ressourcestyring eller klassisk. Men Brugergrænsefladen instruktioner og skærmbilleder i denne artikel er relevante for portalen Azure.
- **I øjeblikket kan du kun overføre fra AWS til Azure. Du kan ikke over FOS fra AWS til Azure, men du kan ikke dem tilbage igen. Der er ingen igangværende gentagelse.**
- Overførsel vejledningen i denne artikel er baseret på instruktionerne til replikering en fysisk maskine til Azure. Den indeholder links til trinnene i [gentage VMware FOS eller fysiske servere til Azure](site-recovery-vmware-to-azure.md), som beskriver, hvordan du overfører en fysisk server Azure-portalen.
- Hvis du konfigurerer gendannelse af websteder i portalen klassisk, skal du følge vejledningerne i [denne artikel](site-recovery-vmware-to-azure-classic.md). **Du ikke længere skal bruge** instruktionerne i denne [artikel ældre](site-recovery-vmware-to-azure-classic-legacy.md).

Sende en hvilken som helst kommentarer eller spørgsmål nederst i denne artikel eller på [Azure gendannelse Services-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)


## <a name="prerequisites"></a>Forudsætninger

Her er, hvad du skal bruge til denne installation

- **Konfiguration af server**: en lokal VM, der kører Windows Server 2012 R2, der fungerer som server configuration. Du installerer (herunder proces server og master target server) komponenter for gendannelse af websteder på denne VM for. Få mere at vide i [scenarie arkitektur](site-recovery-vmware-to-azure.md#scenario-architecture) og [konfiguration af server forudsætninger](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).
- **EC2 VM forekomster**: de forekomster, der kører Windows, du vil overføre.

## <a name="deployment-steps"></a>Installation trin

I dette afsnit beskrives installation trinnene i den nye Azure portal. Hvis du skal bruge følgende installation til gendannelse af websteder i portalen klassisk, se i [denne artikel](site-recovery-vmware-to-azure-classic.md).

1. [Opret en samling af legitimationsoplysninger](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Implementer en server configuration](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. Når du har installeret server configuration, bekræfte, at det kan kommunikere med de FOS, du vil overføre.
4. [Konfigurere indstillinger for gentagelse](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Opret en politik for gentagelse, og Tildel til af konfigurationsserveren.
5. [Installere tjenesten mobilitet](site-recovery-vmware-to-azure.md#step-6-replication-application). Hver VM, du vil beskytte skal tjenesten mobilitet installeret. Denne tjeneste sender data til processerveren. Tjenesten mobilitet kan installeres manuelt eller overføres og installeres automatisk af processen serveren, når beskyttelse af VM er aktiveret. Firewallregler på EC2 forekomster, du vil overføre skal konfigureres til at tillade opslagsnål installation af denne tjeneste. Sikkerhedsgruppe for EC2 forekomster skal have følgende regler:

    ![firewallregler](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [Aktivere gentagelse](site-recovery-vmware-to-azure.md#enable-replication). Aktiver replikering for de FOS, du vil overføre. Du kan finde EC2 forekomster ved hjælp af de private IP-adresser, som kan hentes fra konsollen EC2.
7. [Køre en ikke-planlagt failover](site-recovery-failover.md#run-an-unplanned-failover). Når første replikering er fuldført, kan du køre en ikke-planlagt failover fra AWS til Azure for hver VM. Du kan også kan du oprette en plan for gendannelse og køre en ikke-planlagt failover, hvis du vil overføre flere virtuelle maskiner fra AWS til Azure. Du kan [Få mere at vide](site-recovery-create-recovery-plans.md) om gendannelse planer.

## <a name="next-steps"></a>Næste trin

Få flere oplysninger om andre gentagelse scenarier i [Hvad er Azure gendannelse af websteder?](site-recovery-overview.md)
