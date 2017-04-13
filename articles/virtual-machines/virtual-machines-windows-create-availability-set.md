<properties
    pageTitle="Oprette en VM tilgængelighed sæt | Microsoft Azure"
    description="Få mere at vide, hvordan du opretter en tilgængelighed, der er angivet for din virtuelle maskiner via Azure portal eller PowerShell ved hjælp af Ressourcestyring implementeringsmodel."
    keywords="tilgængelighed sæt"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>


# <a name="create-an-availability-set"></a>Oprette et sæt tilgængelighed 

Når ved hjælp af portalen, hvis du vil have din VM skal være en del af en tilgængelighed indstiller, skal du oprette tilgængeligheden angive først.

Se [administrere tilgængeligheden af virtuelle maskiner](virtual-machines-windows-manage-availability.md)kan finde flere oplysninger om oprettelse og brug af tilgængelighed sæt.


## <a name="use-the-portal-to-create-an-availability-set-before-creating-your-vms"></a>Brug portalen til at oprette en angive før du opretter din FOS tilgængelighed

1. Klik på **Gennemse** , og vælg **tilgængelighed angiver**i menuen hub.

2. Den **tilgængelighed angiver blade**, klik på **Tilføj**.

    ![Skærmbillede, der viser knappen Tilføj for at oprette en ny tilgængelighed angive.](./media/virtual-machines-windows-create-availability-set/add-availability-set.png)

3. Udfyld oplysningerne til dit sæt på bladet **Opret tilgængelighed angive** .

    ![Skærmbillede, der viser oplysninger, du skal angive for at oprette sættet tilgængelighed.](./media/virtual-machines-windows-create-availability-set/create-availability-set.png)

    - **Navn** - navnet skal være 1-80 tegn, der udgøres af tal, bogstaver, perioder, understregningstegn og stiplet. Det første tegn skal være et bogstav eller et tal. Det sidste tegn skal være et bogstav, tal eller understregningstegn.
    - **Fejl domæner** - fejl domæner definere gruppen af virtuelle maskiner, der deler en fælles power kilde- og netværk parameter. Som standard FOS adskilles på tværs af op til tre fejl domæner og kan ændres til mellem 1 og 3.
    - **Opdatere domæner** - fem opdatering domæner tildeles som standard, og dette kan angives mellem 1 og 20. Opdater domæner angiver grupper af virtuelle maskiner og underliggende fysisk hardware, der kan genstartes på samme tid. Eksempelvis hvis vi angiver fem opdatere domæner, når mere end fem virtuelle maskiner er konfigureret i en enkelt tilgængelighed angive, den sjette virtuelle maskine skal placeres i det samme opdatering domæne som den første virtuelle maskine, syvende i den samme UD som den anden virtuelle maskine, og så videre. Rækkefølgen af genstart måske ikke er sekventielle, men kun én opdatering domæne genstartes ad gangen.
    - **Abonnement** - Vælg abonnementet, der skal bruges, hvis du har mere end én.
    - **Ressourcegruppe** - Vælg et eksisterende ressourcegruppe ved at klikke på pilen og vælge en ressourcegruppe på rullelisten ned. Du kan også oprette en ny ressourcegruppe ved at skrive et navn. Navnet på kan indeholde følgende tegn: bogstaver, tal, perioder, stiplet, understregningstegn og åbning eller højreparentes. Navnet kan ikke slutte i en periode. Alle FOS i gruppen tilgængelighed skal oprettes i samme ressourcegruppe som sættet tilgængelighed.
    - **Placering** – Vælg en placering på rullelisten.

4. Når du er færdig med at angive oplysningerne, skal du klikke på **Opret**. Når gruppen tilgængelighed er blevet oprettet, kan du se den på listen, når du opdaterer på portalen.

## <a name="use-the-portal-to-create-a-virtual-machine-and-an-availability-set-at-the-same-time"></a>Brug portalen til at oprette en virtuel maskine og en tilgængelighed, der er angivet på samme tid

Hvis du opretter en ny VM ved hjælp af portalen, kan du også oprette en ny tilgængelighed, angive for VM, mens du opretter den første VM i dialogboksen Angiv.

![Skærmbillede, der viser processen for at oprette en ny tilgængelighed, indstille, mens du opretter VM.](./media/virtual-machines-windows-create-availability-set/new-vm-avail-set.png)


## <a name="add-a-new-vm-to-an-existing-availability-set"></a>Tilføje en ny VM til et eksisterende tilgængelighed

Sørg for, at du opretter den i den samme **ressourcegruppe** , og vælg derefter den eksisterende tilgængelighed, der er angivet i trin 3 for hver ekstra VM, som du opretter, der skal hører i dialogboksen Angiv. 

![Skærmbillede af, hvordan du vælger en eksisterende tilgængelighed Definer skal bruges til din VM.](./media/virtual-machines-windows-create-availability-set/add-vm-to-set.png)



## <a name="use-powershell-to-create-an-availability-set"></a>Bruge PowerShell til at oprette et sæt tilgængelighed

I dette eksempel opretter en tilgængelighed, der er angivet i gruppen **RMResGroup** ressource **Vest USA** placering. Dette skal udføres, før du opretter den første VM, der skal være i dialogboksen Angiv.

    New-AzureRmAvailabilitySet -ResourceGroupName "RMResGroup" -Name "AvailabilitySet03" -Location "West US"
    
Du kan finde flere oplysninger [Ny AzureRmAvailabilitySet](https://msdn.microsoft.com/library/mt619453.aspx).


## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

- Når du opretter en VM, hvis det tilgængelighed sæt, du vil ikke findes på rullelisten i portalen har du oprettet den i en anden ressourcegruppe. Hvis du ikke kender ressourcegruppen for din tilgængelighed angive, gå til menuen hub og klikke på Gennemse > tilgængelighed angiver til at se en liste over din tilgængelighed sæt, og som ressourcegrupper de tilhører.


## <a name="next-steps"></a>Næste trin

Tilføje yderligere lagerplads til dit VM ved at tilføje en ekstra [datadisk](virtual-machines-windows-attach-disk-portal.md).
