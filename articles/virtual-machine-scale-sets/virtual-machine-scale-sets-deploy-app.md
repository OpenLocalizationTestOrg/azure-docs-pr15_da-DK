<properties
    pageTitle="Installere en App på virtuelt skala sæt | Microsoft Azure"
    description="Installere en app på virtuelt skala sæt"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="guybo"/>

# <a name="deploy-an-app-on-virtual-machine-scale-sets"></a>Installere en App på virtuelt skala sæt

Et program, der kører på en VM skala angive er typisk installeret på en af tre måder:

- Installeres nye programmer på en platform billede fra det øjeblik. En platform billede i denne kontekst er en afbildning af operativsystemet fra Azure Marketplace, som Ubuntu 16.04, Windows Server 2012 R2 osv.

Du kan installere ny software på en platform billede ved hjælp af en [VM filtypenavn](../virtual-machines/virtual-machines-windows-extensions-features.md). Filtypenavnet VM er software, der afspilles, når en VM er installeret. Du kan køre vilkårlig kode, du kan lide fra det øjeblik ved hjælp af et brugerdefineret script filtypenavn. [Her](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) er et eksempel Azure ressourcestyring skabelon med to VM filtyper: en Linux brugerdefineret Script lokalnummer installere Apache og PHP og en diagnosticering lokalnummer at sende ydelsesdata, der bruges af Azure Autoskalering.

En fordelen ved denne fremgangsmåde er, du har et niveau af Adskillelsen mellem din programkode og Operativsystemet og kan vedligeholde dit program separat. Naturligvis kan, betyder, at der er også mere dynamiske dele og VM installation klokkeslæt være længere tid, hvis der er meget for scriptet til at hente og konfigurere.

**Hvis du sender følsomme oplysninger i brugerdefineret Script lokalnummer kommandoen (som en adgangskode), skal du sørge for at angive den `commandToExecute` i den `protectedSettings` attributten for filtypenavnet brugerdefineret Script i stedet for den `settings` attributter.**

- Oprette en brugerdefineret VM afbildning, der indeholder både Operativsystemet og programmet på computeren i en enkelt virtuel harddisk. Her består sættet skala af et sæt FOS kopieret fra et billede, der er oprettet af dig selv, som du har til at vedligeholde. Denne fremgangsmåde kræver ingen ekstra konfiguration på VM installation tid. I den `2016-03-30` version af VM skala sæt (og tidligere versioner) OS diskene for FOS i sættet skala er begrænset til en enkelt lagerplads konto. Dermed, kan du lade maksimalt 40 FOS i et sæt skala, i modsætning til 100 VM per skala angivet grænse med platform billeder. Du kan finde flere oplysninger i [Skala angive Design oversigt](./virtual-machine-scale-sets-design-overview.md) .

- Installere en platform eller et brugerdefineret billede, som er en objektbeholder vært og installere programmet som en eller flere beholdere, som du administrerer med et værktøj til styring orchestrator eller konfiguration. Dejligt om denne fremgangsmåde er, at du har udtrukket infrastrukturen skyen fra programlag og vedligeholde dem separat.

## <a name="what-happens-when-a-vm-scale-set-scales-out"></a>Hvad sker der, når en VM skala angive skaleres ud?

Programmet installeres automatisk, når du føjer en eller flere FOS til en skala, angive ved at øge kapaciteten – hvad enten manuelt eller via Autoskalering –. For eksempel hvis skalaen angiver har filtypenavne, der er defineret, kører de på en ny VM hver gang den er oprettet. Hvis sættet skala er baseret på et brugerdefineret billede, er en ny VM en kopi af brugerdefinerede kildebilledet. Hvis skalaen angive FOS er objektbeholder værter, og klik derefter du muligvis Start kode at indlæse beholdere i filtypenavnet brugerdefineret Script eller filtypenavnet muligvis installere en agent, der registrerer ved hjælp af en klynge orchestrator (såsom Azure objektbeholder tjenesten).

## <a name="how-do-you-manage-application-updates-in-vm-scale-sets"></a>Hvordan administrerer du programmet opdateringer i VM skala sæt?

For programmet opdateringer i VM skala sæt følge tre primære fremgangsmåder fra de tre metoder til installation af foregående program:

* Opdaterer med VM filtypenavne. En hvilken som helst VM filtypenavne, der er defineret for en VM skala angive udføres hver gang en ny VM er installeret, en eksisterende VM reimaged, eller filtypenavnet VM opdateres. Hvis du vil opdatere dit program, direkte opdatering af et program gennem filtypenavne er en interessant tilgang – du opdatere definitionen filtypenavn. Der er en nem måde at gøre det ved at ændre fileUris til at pege på den nye software.

* Billede af fast brugerdefinerede metode. Når du Kagesalg programmet (eller app komponenter) til et VM billede kan du fokusere på at opbygge en pålidelig pipeline for at automatisere build, test og installation af billederne. Du kan designe dit arkitektur for at lette hurtig udskiftning af en faseinddelt skala sæt til fremstilling. Et godt eksempel på denne metode er den [Azure Spinnaker driver arbejde](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/).

Pakker og Terraform understøtter også Azure Ressourcestyring, så du kan også definere dine billeder "som kode" og opretter dem i Azure, skal du bruge den virtuelle harddisk i dit skala sæt. Imidlertid vil gøre så blive problematisk for Marketplace billeder, hvor filtypenavne/brugerdefinerede scripts bliver vigtigere, da du ikke direkte manipulere bit fra Marketplace.

* Opdater beholdere. Abstrakt livscyklus Programadministration til et niveau over skyinfrastruktur, f.eks ved encapsulating programmer og app-komponenter til beholdere og administrere disse beholdere gennem objektbeholder orchestrators og konfiguration ledere som kok/Puppet.

Skalaen angive FOS, og derefter bliver til en stabil substrat for beholdere og kun kræver lejlighedsvise sikkerhed og OS-relaterede opdateringer. Som nævnt, er Azure objektbeholder tjenesten et godt eksempel ønsker at bruge denne tilgang og opbygning af en tjeneste rundt om den.

## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>Sådan du udrulle en OS opdatering på tværs af opdatering domæner?

Antag, at du vil opdatere OS billedet og holde den VM skala angive kører. En metode til at gøre det er at opdatere Virtual Machine billeder én VM ad gangen. Du kan gøre det med PowerShell eller Azure CLI. Der er separat kommandoer for at opdatere VM skala angive modellen (hvordan konfigurationen er defineret), og at udstede "manuel opgradering" opkald på individuelle FOS.

[Her](https://github.com/gbowerman/vmsstools) er et eksempel Python script, som kan automatisere for at opdatere et VM skala angive én opdatering domæne ad gangen. (Advarsel: det er mere et bevis for konceptet end en hærdet fremstilling klar-løsning – du gerne vil tilføje nogle fejl fejlkontrol osv.).
