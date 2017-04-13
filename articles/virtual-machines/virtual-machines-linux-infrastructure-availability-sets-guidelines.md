<properties
    pageTitle="Tilgængelighed angive retningslinjer | Microsoft Azure"
    description="Få mere at vide om de vigtigste design og implementering retningslinjer for installation af tilgængelighed sæt i Azure infrastrukturtjenester."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="availability-sets-guidelines"></a>Tilgængelighed angiver retningslinjer

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

I denne artikel fokuserer på forstå de nødvendige planlægning trin for tilgængelighed sæt at sikre, at dine programmer forbliver, der er tilgængelige under planlagt eller uventet begivenheder.

## <a name="implementation-guidelines-for-availability-sets"></a>Implementering retningslinjer for tilgængelighed sæt

Beslutninger:

- Hvor mange tilgængelighed sæt har du brug for for de forskellige roller og niveauer i infrastrukturen programmet?

Opgaver:

- Definere antallet af FOS i hvert program niveau, du har brug for.
- Find ud af, hvis du vil tilpasse antallet af fejl eller opdatere domæner skal bruges til dit program.
- Definer de nødvendige tilgængelighed sæt ved hjælp af din navnekonventionen, og hvad FOS er placeret dem. En VM kan kun være placeret i en tilgængelighed sæt. 

## <a name="availability-sets"></a>Tilgængelighed sæt

I Azure, kan virtuelle maskiner (VM'er) placeres i til en logisk gruppering kaldet et sæt tilgængelighed. Når du opretter FOS inden for en tilgængelighed sæt, distribuerer Azure-platformen placeringen af disse FOS på tværs af den underliggende struktur. Skal der være planlagt vedligeholdelse til Azure-platformen eller en underliggende hardware / infrastruktur fejl, brug af tilgængelighed sæt sikrer, at mindst én VM forbliver, der kører.

Som en bedste fremgangsmåde programmer skal ikke er placeret på en enkelt VM. Et tilgængelighed sæt, der indeholder en enkelt VM få ikke en hvilken som helst beskyttelse fra planlagt eller uventet hændelser i den Azure platform. [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines) kræver to eller flere FOS inden for en tilgængelighed, der er indstillet til at tillade fordelingen af FOS på tværs af den underliggende struktur.

Den underliggende infrastruktur i Azure er inddelt i at opdatere domæner og fejl domæner. Disse domæner er defineret af hvilke værter dele et almindelige opdateringsprocessen eller del lignende fysiske infrastruktur som power og netværk. Azure distribuerer automatisk din FOS inden for en angivet på tværs af domæner til at vedligeholde tilgængelighed og fejltolerance tilgængelighed. Afhængigt af størrelsen på dit program og antallet af FOS inden for en tilgængelighed sæt, kan du tilpasse antallet af domæner, du vil bruge. Du kan læse mere om [Administration af tilgængelighed og brug af domæner opdatering og fejl](virtual-machines-linux-manage-availability.md).

Når du udformer infrastrukturen programmet på computeren, skal du også planlægge ud af programmet niveauer til at bruge. Gruppen VM'er, der har samme formål i til at være tilgængelig angiver som en tilgængelighed, der er angivet for din front end VM'er, der kører nginx eller Apache. Oprette en separat tilgængelighed, der er angivet for din back end-VM'er, der kører MongoDB eller MySQL. Målet er at sikre, at hver komponent i dit program er beskyttet af et sæt tilgængelighed og mindst én gang forekomst forbliver altid, der kører.

Indlæse balancere kan anvendes foran hver programmet lag til at arbejde sammen med et sæt tilgængelighed og sikre trafik kan altid sendes til en aktiv forekomst. Din FOS kan fortsætte med at køre i hele planlagte og ikke-planlagt vedligeholdelse af begivenheder uden belastningsjustering, men dine slutbrugere muligvis ikke kunne løse dem, hvis den primære VM er ikke tilgængelig.


## <a name="next-steps"></a>Næste trin
[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 