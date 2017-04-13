<properties
    pageTitle="Udvid lokalt altid på tilgængelighed grupper til Azure | Microsoft Azure"
    description="Dette selvstudium bruger ressourcer, der er oprettet med den klassiske implementeringsmodel og beskriver, hvordan du bruger guiden Tilføj replika i SQL Server Management Studio (SSMS) til at tilføje en altid på tilgængelighed-gruppen kopi i Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/12/2016"
    ms.author="MikeRayMSFT" />

# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Udvid lokalt altid på tilgængelighed grupper til Azure

Altid på tilgængelighed-grupper giver høj tilgængelighed for grupper af database ved at tilføje sekundær replikaer. Disse replikaer Tillad ned over databaser i tilfælde af en mislykket. De kan desuden bruges til at offload arbejdsbelastninger, som læst eller sikkerhedskopiering opgaver.

Du kan udvide lokale tilgængelighed grupper til Microsoft Azure ved klargøring af en eller flere Azure FOS med SQL Server og derefter tilføje dem som replikaer til din lokale tilgængelighed grupper.

Dette selvstudium forudsætter, at du har følgende:

- Et aktivt Azure abonnement. Du kan [tilmelde dig en gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

- En eksisterende altid på tilgængelighed gruppe i det lokale miljø. Du kan finde flere oplysninger om tilgængelighed grupper, [Altid på tilgængelighed-grupper](https://msdn.microsoft.com/library/hh510230.aspx).

- Forbindelsen mellem det lokale netværk og netværket Azure virtuelle. Se [konfigurere et websted til websted VPN i portalen Azure klassisk](../vpn-gateway/vpn-gateway-site-to-site-create.md)kan finde flere oplysninger om oprettelse af denne virtuelle netværk.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="add-azure-replica-wizard"></a>Azure replika guiden Tilføj

Dette afsnit viser, hvordan du kan bruge **Tilføje Azure replika guiden** til at udvide din altid på tilgængelighed-gruppen løsning for at medtage Azure replikaer.

1. Udvid **Altid på høj tilgængelighed**i SQL Server Management Studio fra > **Tilgængelighed grupper** > **[navn på din tilgængelighed gruppe]**.

1. Højreklik på **Tilgængelighed replikaer**, og klik derefter på **Tilføj replika**.

1. **Tilføje replika til tilgængelighed gruppe guiden** vises som standard. Klik på **Næste**.  Hvis du har valgt **Vis ikke på denne side igen** indstillingen nederst på siden under en tidligere start af denne guide, vises dette skærmbillede ikke.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)

1. Du bliver bedt om at oprette forbindelse til alle eksisterende sekundær kopier. Du kan klikke på **Opret forbindelse...** ud for hver replika, eller du kan klikke på **Opret forbindelse alle...** nederst på skærmen. Efter godkendelse, skal du klikke på **Næste** til at gå videre til næste skærmbillede.

1. Flere faner er vises øverst på siden **Angiv replikaer** : **replikaer**, **slutpunkter**, **Indstillinger for sikkerhedskopiering**og **lytteren**. Klik på **Tilføj... Azure replika** fra fanen **replikaer** Sådan startes guiden Tilføj Azure replika.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)

1. Vælg et eksisterende Azure Management certifikat fra det lokale lager til Windows-certifikat, hvis du har installeret en før. Vælg eller Angiv id'et for et Azure-abonnement, hvis du har brugt en før. Du kan klikke på Hent for at hente og installere et certifikat til administration af Azure og hente listen over de abonnementer, der bruger en Azure-konto.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)

1. Du kan udfylde hvert felt på siden med værdier, der bruges til at oprette Azure VM (Virtual Machine), der er vært replikaen.

  	|Indstilling|Beskrivelse|
|---|---|
|**Billede af**|Vælg den ønskede kombination af OS og SQL Server|
|**VM størrelse**|Vælg størrelsen på VM, der passer bedst til dine forretningsbehov|
|**VM navn**|Angiv et entydigt navn til den nye VM. Navnet skal indeholde mellem 3 og 15 tegn, kan indeholde kun bogstaver, tal og bindestreger, og skal starte med et bogstav og slutter med et bogstav eller tal.|
|**VM brugernavn**|Angive et brugernavn, bliver administratorkontoen på VM|
|**VM administratoradgangskode**|Angive en adgangskode til den nye konto|
|**Bekræft adgangskode**|Bekræft adgangskoden for den nye konto|
|**Virtuelt netværk**|Angiv det Azure virtuelle netværk, der skal bruge den nye VM. Se [Oversigt over virtuelle netværk](../virtual-network/virtual-networks-overview.md)kan finde flere oplysninger om virtuelle netværk.|
|**Virtuelt netværksundernet**|Angiv det virtuelle netværksundernet, som den nye VM skal bruge|
|**Domæne**|Bekræfte udfyldt på forhånd værdien for domænenavn, er korrekt|
|**Bruger domænenavn**|Angive en konto, der er i den lokale administratorgruppe på de lokale klyngenoder|
|**Adgangskode**|Angiv adgangskoden for domænebrugernavnet|

1. Klik på **OK** for at validere indstillingerne for installation.

1. Juridiske vilkår vises næste. Læs, og klik på **OK** , hvis du accepterer vilkårene.

1. Siden **Angiv replikaer** vises igen. Bekræfte indstillinger for den nye Azure replika under fanerne **replikaer**, **slutpunkter**og **Sikkerhedskopiering præferencer** . Ændre indstillinger for at virksomhedens behov.  Se [Angive replikaer side (nye tilgængelighed gruppe guiden/Tilføj guiden replika)](https://msdn.microsoft.com/library/hh213088.aspx)kan finde flere oplysninger om de parametre, der er indeholdt under følgende faner. Bemærk, at lyttere ikke kan oprettes ved hjælp af fanen lytteren for tilgængelighed grupper, der indeholder Azure replikaer. Desuden, hvis en lytter er allerede blevet oprettet før du starter guiden, modtager du en meddelelse om, at den ikke understøttes i Azure. Vi ser på, hvordan man opretter lyttere i afsnittet **oprette en tilgængelighed gruppe lytteren** .

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)

1. Klik på **Næste**.

1. Vælg den metode til synkronisering af data, du vil bruge på siden **Vælg indledende synkronisering af Data** , og klik på **Næste**. Vælg **Komplet synkronisering af Data**for de fleste scenarier. Du kan finde flere oplysninger om data synkronisering metoder, [Vælg indledende synkronisering dataadgangsside (altid på tilgængelighed gruppe guider)](https://msdn.microsoft.com/library/hh231021.aspx).

1. Gennemse resultaterne på siden **Validering** . Rette udestående spørgsmål, og Kør validering igen, hvis det er nødvendigt. Klik på **Næste**.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)

1. Gennemse indstillingerne på siden **Oversigt** , og derefter klikke på **Udfør**.

1. Klargøring processen starter. Når guiden er fuldført, skal du klikke på **Luk** afslutter guiden.

>[AZURE.NOTE] Guiden Tilføj Azure replika opretter en logfil i Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Denne logfil kan bruges til at foretage fejlfinding af mislykkede Azure replika installationer. Hvis guiden ikke udførelse af handlinger, føres alle tidligere handlinger tilbage, herunder at slette den klargjorte VM.

## <a name="create-an-availability-group-listener"></a>Oprette en tilgængelighed gruppe lytteren

Når gruppen tilgængelighed er blevet oprettet, skal du oprette en lytter for klienter mulighed for at oprette forbindelse til replikaerne. Lyttere dirigere indgående forbindelser til primært eller en sekundær replika skrivebeskyttet tilstand. Du kan finde flere oplysninger om lyttere, [konfigurere en ILB lytteren for altid på tilgængelighed grupper i Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

## <a name="next-steps"></a>Næste trin

Ud over bruger **Tilføje Azure replika guiden** til at udvide din altid på tilgængelighed gruppe til Azure, kan du også flytte nogle SQL Server-arbejdsbelastninger helt til Azure. For at komme i gang skal du se [klargøring af en SQL Server virtuel maskine på Azure](virtual-machines-windows-portal-sql-server-provision.md).

Du kan finde andre emner, der er relateret til kører SQL Server i Azure FOS, [SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-server-iaas-overview.md).
