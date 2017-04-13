<properties
    pageTitle="Hvad du skal gøre, hvis en Azure tjenesteforstyrrelse påvirker Azure virtuelle maskiner | Microsoft Azure"
    description="Få mere at vide, hvad du skal gøre, hvis en Azure tjenesteforstyrrelse påvirker Azure virtuelle computere."
    services="virtual-machines"
    documentationCenter=""
    authors="kmouss"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines"
    ms.workload="virtual-machines"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="kmouss;aglick"/>

#<a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-virtual-machines"></a>Hvad du skal gøre, hvis en Azure tjenesteforstyrrelse påvirker Azure virtuelle maskiner

Hos Microsoft store vi anstrengelser for at sikre, at vores tjenester altid er tilgængelige for dig, når du har brug for dem. Styrker uden for vores kontrol påvirke os nogle gange på måder, der kan medføre ikke-planlagt tjeneste afbrydelser.

Microsoft yder en serviceaftale (SLA) for sine tjenester som en forpligtelse til oppetid og forbindelse til. SLA for enkelte Azure tjenester kan findes på [Azure serviceaftaler niveau](https://azure.microsoft.com/support/legal/sla/).

Azure indeholder allerede mange indbyggede platformfunktioner, der understøtter meget tilgængelige programmer. Få mere at vide om disse tjenester, du kan finde [nedbrud og høj tilgængelighed til Azure-programmer](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Denne artikel omhandler et SAND nedbrud, når et hele område oplever en afbrydelse på grund af overordnede neutralt nedbrud eller udbredte tjenesten blev afbrudt. Dette er sjældne forekomster, men du skal forberede muligheden for, at der er en afbrydelse af en hel område. Hvis et hele område har en tjenesteforstyrrelse, vil de lokalt overflødige kopier af dine data midlertidigt ikke tilgængelig. Hvis du har aktiveret geografisk gentagelse, gemmes tre flere kopier af dine Azure-lager BLOB og tabeller i et andet område. I tilfælde af en komplet internationale afbrydelse eller nedbrud, ikke er det primære område gendannes, knytter Azure alle DNS-poster til de replikerede geografisk område.

>[AZURE.NOTE]Vær opmærksom på, at du ikke har en hvilken som helst kontrol over denne proces, og det forekommer kun til tjeneste i område hele afbrydelser. På grund af dette, skal du også stole på andre programmer-specifikke sikkerhedskopiering strategier til at opnå det højeste niveau af tilgængelighed. Yderligere oplysninger finder du i afsnittet på [Data strategier for nedbrud](../resiliency/resiliency-disaster-recovery-azure-applications.md#data-strategies-for-disaster-recovery).

For at hjælpe dig med at håndtere disse sjældne forekomster, giver vi de følgende retningslinjer for Azure virtuelle maskiner tale om en tjenesteforstyrrelse af det hele område, hvor din Azure virtuelt programmet installeres.

##<a name="option-1-wait-for-recovery"></a>Mulighed 1: Vent gendannelse
I dette tilfælde er ingen handling fra din side påkrævet. Ved, at vi arbejder fremføre for at gendanne tjenesten kører. Du kan se den aktuelle Tjenestestatus på vores [Azure Dashboard over tjenestetilstand](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Dette er den bedste løsning, hvis du ikke har konfigureret Azure gendannelse af websteder, virtuelt sikkerhedskopi, læseadgang geografisk overflødige lagerplads eller geografisk overflødige lagerplads før afbrydelser. Hvis du har konfigureret geografisk overflødige lagerplads eller læseadgang geografisk overflødige lagerplads for kontoen lagerplads hvor dine VM virtuelle harddiske (virtuelle harddiske) er gemt, kan du se for at gendanne den grundlæggende afbildning Virtuelle og prøver at angive en ny VM fra den. Dette er ikke en indstilling for Foretrukne, fordi der er ingen garantier med synkronisering af data, medmindre der anvendes Azure VM sikkerhedskopiering eller Azure gendannelse af websteder. Denne indstilling er derfor ikke sikkert at arbejde.

For kunder, der vil have øjeblikkelig adgang til virtuelle maskiner, findes følgende to muligheder.  

>[AZURE.NOTE]Vær opmærksom på, at begge af følgende indstillinger har mulighed for at forekomme tab af data.     

##<a name="option-2-restore-a-vm-from-a-backup"></a>Mulighed 2: Gendanne en VM fra en sikkerhedskopi
For kunder, der har konfigureret en VM sikkerhedskopi, kan du gendanne VM fra dens sikkerhedskopiering og gendannelse punkt.

Hvis du vil gendanne en ny VM fra Azure sikkerhedskopi, skal du se [gendanne virtuelle maskiner i Azure](../backup/backup-azure-restore-vms.md).

For at hjælpe dig med at planlægge infrastrukturen Azure virtuelle maskiner sikkerhedskopiering, kan du se [Plan infrastrukturen VM sikkerhedskopiering i Azure](../backup/backup-azure-vms-introduction.md).

##<a name="option-3-initiate-a-failover-by-using-azure-site-recovery"></a>Mulighed 3: Starte en failover ved hjælp af Azure gendannelse af websteder
Hvis du har konfigureret Azure gendannelse af websteder til at fungere sammen med din påvirkede Azure virtuelle maskiner, kan du gendanne din FOS fra deres replikaer. Disse replikaer kan være placeret på Azure eller lokalt. I dette tilfælde kan du oprette en ny VM fra dens eksisterende replika. Hvis du vil gendanne din FOS fra en kopi af Azure gendannelse af websteder, skal du se [overføre Azure IaaS virtuelle maskiner mellem Azure områder med Azure gendannelse af websteder](../site-recovery/site-recovery-migrate-azure-to-azure.md).

>[AZURE.NOTE]Selvom Azure virtuelt operativsystem og datadisce kan replikeres til en sekundær virtuel harddisk, hvis de er i en geografisk overflødige lagerplads eller læseadgang geografisk overflødige lagerplads konto, replikeres hver Virtuelle uafhængigt af hinanden. Dette niveau af replikering garanterer ikke konsistens på tværs af replikerede virtuelle harddiske. Hvis dit program og/eller databaser, der bruger disse datadisce har afhængigheder af hinanden, er det ikke sikkert, at alle virtuelle harddiske replikeres som et øjebliksbillede. Det er også ikke sikkert, at den Virtuelle replika fra geografisk overflødige lagerplads eller læseadgang geografisk overflødige lagerplads medfører en ensartet øjebliksbillede af programmet til at starte VM.

##<a name="next-steps"></a>Næste trin
Få mere at vide om at implementere en nedbrud og høj tilgængelighed strategi, under [nedbrud og høj tilgængelighed til Azure-programmer](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Se [Azure fleksibilitet tekniske retningslinjer](../resiliency/resiliency-technical-guidance.md)for at udvikle en detaljerede tekniske forståelse af en sky platform funktioner.

For at lære at sikkerhedskopiere FOS, skal du se [sikkerhedskopiere Azure virtuelle computere](../backup/backup-azure-vms.md).

Lær, hvordan du bruger Azure gendannelse af websteder at dirigerer og automatisere beskyttelse af dine fysiske (og virtuelle) Windows og Linux-computere, der kører på VMWare og Hyper-V FOS, skal du se [Azure gendannelse af websteder](https://azure.microsoft.com/documentation/learning-paths/site-recovery/).

Hvis vejledningen er ikke fjerne markeringen i, eller hvis du vil have Microsoft for at udføre handlinger på dine vegne, skal du kontakte [Kundesupport](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
