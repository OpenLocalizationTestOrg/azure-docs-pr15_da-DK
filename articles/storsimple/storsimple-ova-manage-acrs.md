<properties 
   pageTitle="Administrere adgang kontrolelement poster for matrixen StorSimple virtuelle | Microsoft Azure"
   description="Beskriver, hvordan du administrerer access kontrolelement poster (ACRs) for at afgøre, hvilke værter kan oprette forbindelse til en enhed på StorSimple virtuelle matrixen."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/03/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-access-control-records-for-the-storsimple-virtual-array"></a>Bruge tjenesten StorSimple Manager til at administrere adgang kontrolelement poster til den virtuelle StorSimple-matrix 

## <a name="overview"></a>Oversigt

Microsoft Access control-poster (ACRs) gør det muligt at angive, hvilke værter kan oprette forbindelse til en enhed på StorSimple virtuelle matrixen (også kendt som den StorSimple lokale virtuelle enhed). ACRs er angivet til et bestemt drev og indeholder iSCSI kvalificerede navnene (IQNs) på værterne. Når en vært forsøger at oprette forbindelse til en disk, enheden kontrollerer ACR der er knyttet til denne lydstyrken for IQN navn, og hvis der er en match, derefter forbindelsen er oprettet. Sektionen **access kontrolelement records** på siden **Konfigurer** viser alle access kontrolelement posterne med de tilsvarende IQNs på værterne.

Dette selvstudium beskrives følgende almindelige ACR-relaterede opgaver:

- Få IQN
- Tilføj en post til kontrol af adgang 
- Redigere en post til kontrol af adgang 
- Slette en post til kontrol af adgang 

> [AZURE.IMPORTANT] 
> 
> - Når du tildeler en ACR til en disk, tager sig, at lydstyrken ikke er samtidig adgang til af mere end én ikke-grupperede vært da dette kan ødelægge lydstyrken. 
> - Når du sletter en ACR fra en disk, Sørg for, at den tilsvarende vært ikke har adgang til lydstyrken fordi sletningen kan medføre en skrivebeskyttet forstyrrelse.

## <a name="get-the-iqn"></a>Få IQN

Udfør følgende trin for at få IQN på en Windows-vært, der kører Windows Server 2012.

[AZURE.INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Tilføje en ACR

Du kan bruge siden **konfiguration** af StorSimple Manager service til at tilføje ACRs. Typisk, der skal tilknyttes en ACR én lydstyrken.

Finde oplysninger om at knytte en ACR til et drev, gå til [tilføje en disk](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).

>[AZURE.IMPORTANT] 
> 
>Når du tildeler en ACR til en disk, Sørg for, at lydstyrken ikke er samtidig adgang til af mere end én ikke-grupperede vært da dette kan ødelægge lydstyrken.
 
Udfør følgende trin for at tilføje en ACR.

#### <a name="to-add-an-acr"></a>Tilføje en ACR

1. På landingsside service, Vælg din tjeneste, skal du dobbeltklikke på navnet på tjenesten og klik derefter på fanen **konfiguration** .

    ![under fanen konfiguration](./media/storsimple-ova-manage-acrs/acr1.png)

2. Angiv et **navn** til din ACR i listen tabelformat under **Access kontrolelement poster**.

3. Angiv IQN navnet på din Windows-vært under **iSCSI afsenderen navn**. 

4. Klik på **Gem** nederst på siden for at gemme den nyoprettede ACR. Du får vist følgende meddelelse.

    ![bekræftelsesmeddelelsen](./media/storsimple-ova-manage-acrs/acr2.png)

5. Klik på ikonet Kontrollér ![Markér ikonet](./media/storsimple-ova-manage-acrs/check-icon.png). Listen tabelformat opdateres afspejler denne tilføjelse.

## <a name="edit-an-acr"></a>Redigere en ACR

Du kan bruge siden **konfiguration** af i portalen Azure klassisk til at redigere ACRs. 

> [AZURE.NOTE] Du skal ændre kun disse ACRs, ikke der i øjeblikket er i brug. Hvis du vil redigere en ACR, der er knyttet til en enhed, der er i brug, skal du først tage lydstyrken offline.

Udfør følgende trin for at redigere en ACR.

#### <a name="to-edit-an-acr"></a>Redigere en ACR

1. På landingsside service, Vælg din tjeneste, skal du dobbeltklikke på navnet på tjenesten og klik derefter på fanen **konfiguration** .

2. Hold markøren over den ACR, som du vil ændre listen over tabelformat af access control-poster.

3. Angiv et nyt navn og/eller IQN for ACR.

4. Klik på **Gem** nederst på siden for at gemme den ændrede ACR. Du får vist en bekræftelsesmeddelelse. 

5. Klik på ikonet Kontrollér ![Markér ikonet](./media/storsimple-ova-manage-acrs/check-icon.png). Listen tabelformat opdateres afspejler denne ændring.

## <a name="delete-an-access-control-record"></a>Slette en post til kontrol af adgang

Du bruger siden **konfiguration** af i portalen Azure klassisk slette ACRs. 

> [AZURE.NOTE] 
> 
> - Du skal slette kun disse ACRs, ikke der i øjeblikket er i brug. Hvis du vil slette en ACR, der er knyttet til en enhed, der er i brug, skal du først tage lydstyrken offline.
> - Når du sletter en ACR fra en disk, Sørg for, at den tilsvarende vært ikke har adgang til lydstyrken fordi sletningen kan medføre en skrivebeskyttet forstyrrelse.

Udfør følgende trin for at slette en post til kontrol af access.

#### <a name="to-delete-an-access-control-record"></a>Slette en post til kontrol af adgang

1. På landingsside service, Vælg din tjeneste, skal du dobbeltklikke på navnet på tjenesten og klik derefter på fanen **konfiguration** .

2. Hold markøren over den ACR, du vil slette listen over tabelformat access kontrolelement poster (ACRs).

3. En sletteikonet (**x**) vises i kolonnen yderst til højre for den ACR, du vælger. Klik på **x** for at slette ACR. Du får vist følgende meddelelse.

    ![bekræftelsesmeddelelsen](./media/storsimple-ova-manage-acrs/acr3.png)

5. Klik på ikonet Kontrollér ![Markér ikonet](./media/storsimple-ova-manage-acrs/check-icon.png). Listen tabelformat opdateres afspejler sletningen.

## <a name="next-steps"></a>Næste trin

- Få mere at vide om [at tilføje enheder og konfigurere ACRs](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).
