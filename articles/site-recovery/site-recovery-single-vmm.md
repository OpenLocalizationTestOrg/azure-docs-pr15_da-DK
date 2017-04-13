
<properties
    pageTitle="Azure gendannelse af websteder: Kontraprøverne Hyper-V virtuelle maskiner på en enkelt VMM server | Microsoft Azure"
    description="I denne artikel beskrives, hvordan du overfører Hyper-V virtuelle maskiner, hvis du kun har en enkelt VMM-server."
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
    ms.date="08/24/2016"
    ms.author="raynew"/>

#  <a name="replicate-hyper-v-virtual-machines-on-a-single-vmm-server"></a>Gentage Hyper-V virtuelle maskiner på en enkelt VMM-server

Læs denne artikel for at lære at gentage Hyper-V virtuelle maskiner, der er placeret på en Hyper-V host server i en VMM sky, hvis du kun har en enkelt VMM server i din installation.

Azure har to forskellige [installation modeller](../resource-manager-deployment-model.md) til oprettelse og arbejde med ressourcer: Azure ressourcestyring og klassisk. Azure har også to portaler – Azure klassisk portalen, der understøtter den klassiske implementeringsmodel og Azure portalen med understøttelse af begge installation-modeller. I denne artikel indeholder vejledning til konfiguration af gentagelse i portalen Azure.


Hvis du har spørgsmål efter at have læst i denne artikel, kan du sende dem i Disqus kommentarer nederst i denne artikel eller på [Azure gendannelse Services-forummet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Oversigt

Hvis du vil gentage Hyper-V FOS placeret på Hyper-V værter i VMM, og du kun har en enkelt VMM server, du kan [replikeres til Azure](site-recovery-vmm-to-azure.md), eller mellem skyer på enkelt VMM serveren.

Vi anbefaler, at du replikeres til Azure, fordi failover og gendannelse ikke problemfri ved replikering mellem skyer, og du skal bruge en lang række manuel trin. Hvis du vil gentage ved hjælp af VMM serveren, kan du gøre følgende:

- Gentage med en enkelt enkeltstående VMM server
- Gentage med en enkelt VMM-server, der er implementeret i en strakt klynge i Windows


## <a name="replicate-across-sites-with-a-single-standalone-vmm-server"></a>Gentage på tværs af websteder med en enkelt enkeltstående VMM server

![Enkeltstående virtuelle VMM server](./media/site-recovery-single-vmm/single-vmm-standalone.png)

I dette scenarie du installerer den enkelt VMM server som en virtuel maskine i det primære websted og gentage denne VM en sekundær websted ved hjælp af gendannelse af websteder og Hyper-V replika.

1. Konfigurere VMM på en Hyper-V VM. Vi anbefaler, at du installerer SQL Server-forekomst, der bruges af VMM på den samme VM til at spare tid. Hvis du vil bruge en ekstern forekomst af SQL Server og en afbrydelse opstår, du vil gendanne forekomsten, før du kan gendanne VMM.
2. Sørg for, at serveren VMM har mindst to skyer, der er konfigureret. Én sky indeholder FOS du vil gentage, og andre skyen fungerer som den sekundære placering. I skyen, der indeholder de FOS, du vil beskytte skal have:

    - En eller flere VMM host grupper, der indeholder en eller flere Hyper-V værtsservere i hver host gruppe.
    - Mindst én Hyper-V virtuelt på hver Hyper-V host server.

3. Oprette en samling af legitimationsoplysninger gendannelse Services, oprette og hente en samling af legitimationsoplysninger registreringsnøgle og registrere VMM serveren i samling af legitimationsoplysninger. Under registrering du har installeret Azure websted gendannelse provideren på VMM-serveren.
4. Konfigurere en eller flere skyer på VMM VM, og Tilføj Hyper-V værter til disse skyer.
3. Konfigurere indstillingerne for dokumentbeskyttelse for sky. Du kan angive navnet på den server, enkelt VMM som kilde- og destinationswebsteder placeringer. For at konfigurere tilknytning af netværk skal knytte du VM netværk til skyen med de FOS, du vil beskytte VM netværk for gentagelse skyen.
4. Aktivere første replikering for FOS, du vil beskytte via netværket, fordi begge skyer er placeret på den samme server.
4. Aktivere Hyper-V replika på værten Hyper-V, der indeholder den VMM VM i Hyper-V Manager-konsollen, og Aktivér gentagelse på VM. Kontrollér, at du ikke tilføjer VMM VM til en hvilken som helst sky, der er beskyttet af gendannelse af websteder. Dette sikrer, at Hyper-V replika indstillinger ikke tilsidesættes af gendannelse af websteder.
5. Hvis du vil oprette gendannelse planer, skal angive du den samme VMM server til kilde- og destinationswebsteder.

Følg vejledningen i [denne artikel](site-recovery-vmm-to-vmm.md) for at oprette en samling af legitimationsoplysninger, registrere serveren, og Konfigurer beskyttelse.

### <a name="what-to-do-in-an-outage"></a>Hvad du skal gøre i en afbrydelse

Hvis en komplet afbrydelse opstår, og du har brug for til at betjene fra webstedet sekundær, skal du gøre følgende:

1.  Køre en ikke-planlagt failover mislykkes over VMM VM fra primært til sekundær websted i konsollen Hyper-V Manager i det sekundære websted.
2.  Bekræft, at VMM VM er oppe at køre i det sekundære websted.
3.  Køre en ikke-planlagt failover mislykkes over arbejdsbelastningen FOS fra primært til sekundær skyer i samling gendannelsestjenester. For at fuldføre den ikke-planlagte sekundære af FOS skal du bekræfte sekundære eller Vælg et andet gendannelsespunkt efter behov.
4.  Når den ikke-planlagte sekundære er fuldført, kan brugere adgang til arbejdsbelastningen ressourcer i det sekundære websted.

Når det primære websted fungerer normalt igen, kan du gøre følgende:

1.  Aktiver modsat replikering for VMM VM, til at starte replikering fra sekundær til primære i Hyper-V Manager-konsollen.
2.  Køre en planlagt failover mislykkes tilbage VMM VM til webstedet for primær i Hyper-V Manager-konsollen. Udfør den sekundære for at fuldføre den. Derefter aktivere modsat replikering skal starte replikering VMM fra primære til sekundær.
3.  Aktiver modsat replikering for arbejdsbyrde FOS til at starte replikering dem fra sekundær til primære i samling gendannelsestjenester.
4.  Køre en planlagt failover mislykkes tilbage arbejdsbelastningen FOS til webstedet for primær i samling gendannelsestjenester. Udfør den sekundære for at fuldføre den. Derefter aktivere modsat replikering skal starte replikering arbejdsbelastningen FOS fra primære til sekundær.



## <a name="replicate-across-sites-with-a-single-vmm-server-in-a-stretched-cluster"></a>Gentage på tværs af websteder med en enkelt VMM server i en strakt klynge

![Grupperet virtuelle VMM server](./media/site-recovery-single-vmm/single-vmm-cluster.png)

I stedet for anvender en enkeltstående VMM server som en VM, der kopieres til et sekundært websted, kan du gøre VMM meget tilgængeligt ved at installere det som en VM i en Windows sekundær klynge. Dette giver arbejdsbelastningen spændstighed og beskyttelse mod hardwarefejl. Hvis du vil installere med gendannelse af websteder skal VMM VM installeres i en Stræk klynge på tværs af geografisk separat websteder. Sådan gør du:

1. Installere VMM på en virtuel maskine i en Windows sekundær klynge, og vælg indstillingen for at køre serveren som høj tilgængelighed under installationen.
2. SQL Server-forekomsten, der bruges af VMM skal replikeres med SQL Server AlwaysOn tilgængelighed grupper, så der er en kopi af databasen i det sekundære websted.
3. Følg vejledningen i [denne artikel](site-recovery-vmm-to-vmm.md) for at oprette en samling af legitimationsoplysninger, registrere serveren, og Konfigurer beskyttelse. Du har brug for til at registrere hver VMM server i klynge i samling af legitimationsoplysninger gendannelsestjenester. For at gøre dette, du har installeret provideren på en aktive node og registrere VMM serveren. Derefter skal du installere udbyderen på andre noder.

### <a name="what-to-do-in-an-outage"></a>Hvad du skal gøre i en afbrydelse

Når der opstår en afbrydelse, er VMM serveren og dens tilsvarende SQL Server-database mislykkedes og adgang til dem fra webstedet sekundær.


## <a name="next-steps"></a>Næste trin

Du kan [Få mere at vide](site-recovery-vmm-to-vmm.md) om detaljerede gendannelse af websteder installation for VMM til VMM gentagelse.
