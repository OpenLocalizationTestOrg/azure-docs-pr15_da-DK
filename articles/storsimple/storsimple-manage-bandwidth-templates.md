<properties
   pageTitle="Administrer dine StorSimple båndbredde skabeloner | Microsoft Azure"
   description="Beskriver, hvordan du administrerer StorSimple båndbredde skabeloner, som gør det muligt at styre forbrug af båndbredde."
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
   ms.date="08/16/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-storsimple-bandwidth-templates"></a>Bruge tjenesten StorSimple Manager til at administrere StorSimple båndbredde skabeloner

## <a name="overview"></a>Oversigt

Båndbredde skabeloner gør det muligt at konfigurere netværk båndbredden på tværs af flere af tidspunktet tidsplaner til klasse data fra enheden StorSimple til skyen.

Med båndbredde, throttling tidsplaner kan du:

- Angiv brugerdefinerede båndbredde tidsplaner afhængigt af arbejdsbelastningen netværk anvendelser.

- Samle administration og genbruge tidsplanerne på tværs af flere enheder i en nemt og problemfrit måde.

> [AZURE.NOTE] Denne funktion findes kun til StorSimple fysiske enheder og ikke til virtuelle enheder.

Alle skabelonerne båndbredde til din tjeneste vises i et tabelformat, og indeholder følgende oplysninger:

- **Navn** – et entydigt navn, der er tildelt til skabelonen båndbredde, da det blev oprettet.

- **Tidsplan** – antallet af tidsplaner, der er indeholdt i en given båndbredde skabelon.

- **Bruges ved** – antallet enheder ved hjælp af båndbredde skabeloner.

Du bruger siden StorSimple Manager **Konfigurer** i portalen Azure klassisk til at administrere båndbredde skabeloner.

Du kan også finde yderligere oplysninger til at konfigurere båndbredde skabeloner i:

- Spørgsmål og svar om båndbredde skabeloner
- Bedste fremgangsmåder til båndbredde skabeloner

## <a name="add-a-bandwidth-template"></a>Tilføje en båndbredde skabelon

Udfør følgende trin for at oprette en ny båndbredde skabelon.

#### <a name="to-add-a-bandwidth-template"></a>Tilføje en båndbredde skabelon

1. Klik på **Tilføj/Rediger båndbredde skabelon**på siden StorSimple Manager tjenesten **konfigurere** .

2. I dialogboksen **Add/Edit båndbredde skabelon** :

   1. Vælg **Opret ny** for at tilføje en ny skabelon båndbredde fra rullelisten **skabelon** .
   2. Angiv et entydigt navn til skabelonen båndbredde.

3. Definere en **båndbredde tidsplan**. Oprette en tidsplan:

   1. Vælg dagene i ugen planen er konfigureret til på rullelisten. Du kan markere flere dage ved at markere de afkrydsningsfelter, der er placeret før de respektive dage på listen.
   2. Vælg indstillingen **Hele dagen** , hvis planen gennemtvinges hele dagen. Hvis denne indstilling er markeret, kan du ikke længere angive et **Starttidspunkt** og et **Sluttidspunkt**. Planen kører fra 12:00 til 11:59 PM.
   3. Vælg et **Starttidspunkt**på rullelisten. Dette er når tidsplanen begynder.
   4. På rullelisten Vælg et **Sluttidspunkt**. Dette er når tidsplanen stopper.

         > [AZURE.NOTE] Overlappende tidsplaner er ikke tilladt. Hvis start- og sluttidspunkter medfører en overlappende tidsplan, vises en fejlmeddelelse om herom.

   5. Angiv den **båndbredde rente**. Dette er båndbredden i høj båndbredde sekundet (Mbps) bruges af enheden StorSimple i handlinger, der involverer skyen (både uploads og downloads). Angiv et tal mellem 1 og 1.000 for dette felt.

   6. Klik på ikonet Kontrollér ![ikonet](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). Den skabelon, du har oprettet, føjes til listen over båndbredde skabeloner på siden **Konfigurer** .

    ![Oprette nye båndbredde skabelon](./media/storsimple-manage-bandwidth-templates/HCS_CreateNewBT1.png)

4. Klik på **Gem** nederst på siden, og klik derefter på **Ja** når du bliver bedt om at bekræfte. Dette vil gemme ændringerne i konfigurationen, du har foretaget.

## <a name="edit-a-bandwidth-template"></a>Redigere en båndbredde skabelon

Udfør følgende trin for at redigere en båndbredde skabelon.

### <a name="to-edit-a-bandwidth-template"></a>Redigere en båndbredde skabelon

1. Klik på **Tilføj/Rediger båndbredde skabelon**.

2. I dialogboksen **Add/Edit båndbredde skabelon** :

   1. Vælg en eksisterende båndbredde skabelon, du vil ændre, på listen **skabelon** ned.
   2. Udfør ændringerne. (Du kan ændre nogen af de eksisterende indstillinger for.)
   3. Klik på ikonet Kontrollér ![Ikonet](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). Du får vist den ændrede skabelon på listen over båndbredde skabeloner på siden Konfigurer.

3. Klik på **Gem** nederst på siden for at gemme ændringerne. Klik på **Ja** når du bliver bedt om at bekræfte.

> [AZURE.NOTE] Du kan ikke at gemme ændringerne, hvis den redigerede tidsplan overlapper med en eksisterende tidsplan i skabelonen båndbredde, du vil redigere.

## <a name="delete-a-bandwidth-template"></a>Slette en båndbredde skabelon

Udfør følgende trin for at slette en båndbredde skabelon.

#### <a name="to-delete-a-bandwidth-template"></a>Slette en båndbredde skabelon

1. Vælg den skabelon, du vil slette, på listen tabelformat båndbredde skabeloner til din tjeneste. En sletteikonet (**x**) vises til yderste højre for den valgte skabelon. Klik på **x** for at slette skabelonen.

2. Du bliver bedt om at bekræfte. Klik på **OK** for at fortsætte.

Hvis skabelonen er i brug af en hvilken som helst diskenhed(er), kan du ikke slette den. Du får vist en fejlmeddelelse om, at skabelonen, der er i brug. Dialogboksen en fejlmeddelelsen vises rådgivning, alle henvisninger til skabelonen, der skal fjernes.

Du kan slette alle henvisninger til skabelonen ved at få adgang til siden **Lydstyrken beholdere** og ændre lydstyrken beholdere, der bruger denne skabelon, så de kan bruge en anden skabelon eller bruge en brugerdefineret eller ubegrænset båndbredde indstilling. Når alle referencer er blevet fjernet, kan du slette skabelonen.

## <a name="use-a-default-bandwidth-template"></a>Bruge en standardskabelon båndbredde

En standardskabelon båndbredde leveres og bruges af lydstyrken beholdere som standard til at gennemtvinge båndbredde kontrolelementer, når du åbner i skyen. Standardskabelonen fungerer også som en klar reference til brugere oprette deres egne skabeloner. Oplysninger om denne standardskabelonen er:

- **Navn** – ubegrænset overnatninger og weekender

- **Tidsplan** – en enkelt tidsplan fra mandag til fredag, der anvender en båndbredde på 1 Mbps mellem 8 AM og PM 5 enhed tid. Båndbredden, der er angivet til ubegrænset for resten af ugen.

Standardskabelonen kan redigeres. Brug af skabelonen (herunder redigerede versioner) registreres.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Oprette en skabelon til varer hele dagen båndbredde, der starter på et bestemt tidspunkt

Følg denne fremgangsmåde for at oprette en tidsplan, der starter på et bestemt tidspunkt og kører hele dagen. I eksemplet tidsplanen starter ved 9 AM morgen og kører indtil 9 AM næste morgen. Det er vigtigt at være opmærksom på, start- og sluttidspunkter for en given plan skal være begge indeholdt samme tidsplanen, 24-timers og kan ikke strækker sig over flere dage. Hvis du har brug at konfigurere båndbredde skabeloner, der strækker sig over flere dage, skal du bruge flere tidsplaner (som vist i eksemplet).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Oprette en varer hele dagen båndbredde skabelon

1. Oprette en tidsplan, der starter 9 AM morgen og kører til midnat.

2. Tilføje en anden tidsplan. Konfigurere den anden tidsplan for at køre fra midnat indtil 9 AM morgen.

3. Gem skabelonen båndbredde.

Sammensat tidsplanen derefter starter på et tidspunkt efter eget valg og køre varer hele dagen.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Spørgsmål og svar om båndbredde skabeloner

**Q**. Hvad sker der med båndbredde kontrolelementer, når du er mellem planerne? (En tidsplan er afsluttet, og en anden er ikke startet endnu.)

**A**. I så fald vil være ansat nogen båndbredde kontrolelementer. Det betyder, at enheden kan bruge ubegrænset båndbredde, når overgang switchmiljø data til skyen.

**Q**. Kan du redigere båndbredde skabeloner på en offline-enhed?

**A**. Du kan ikke ændre båndbredde skabeloner på enheder beholdere, hvis den tilsvarende enhed er offline.

**Q**. Kan du redigere en båndbredde skabelon, der er knyttet til en lydstyrken objektbeholder, når de tilknyttede enheder er offline?

**A**. Du kan redigere en båndbredde skabelon, der er knyttet til en lydstyrken objektbeholder, hvis enheder er offline. Bemærk, at når enheder arbejder offline, ingen data skal være lagdelt fra enheden til skyen.

**Q**. Kan du slette en standardskabelon?

**A**. Selvom du kan slette en standardskabelon, er det ikke en god ide at gøre dette. Brug af en standardskabelon, herunder redigerede versioner registreres. Datatypen registrering er analysere og i løbet af tid, der bruges til at forbedre standardskabelonen.

**Q**. Hvordan finder du ud af, at dine båndbredde skabeloner skal ændres?

**A**. En af de tegn, du vil ændre skabeloner i båndbredde er, når du begynder at se netværket langsommere eller formindsker flere gange på en dag. Hvis dette sker, overvåge lagerplads for og brugen netværket ved at se på de i/o-ydeevne og netværkskapacitet diagrammer.

Identificere tidspunktet for dag og lydstyrken beholdere, hvori netværk flaskehals opstår fra overførselshastighed netværksdata. Hvis dette sker, når dataene er der lagdelt til skyen (få disse oplysninger fra i/o-ydeevne for alle lydstyrken beholdere for enhed til skyen), og derefter skal du redigere skabelonerne båndbredde, der er knyttet til din lydstyrken beholdere.

Når de ændrede skabeloner er i brug, skal du overvåge igen efter betydeligt latenstider på netværket. Hvis disse findes stadig, skal du besøge igen dine båndbredde skabeloner.

**Q**. Hvad sker der, hvis flere lydstyrken beholdere på min enhed har du planlægger, overlap, men forskellige begrænsninger gælder for hver?

**A**. Lad os antage, at du har en enhed med 3 lydstyrken beholdere. De planer, der er knyttet til disse beholdere helt overlapper. For hver af disse beholdere skal er båndbredde begrænsningerne bruges 5, 10 og 15 Mbps. Når i/O'er der foregår på alle disse beholdere på samme tid, minimum på 3 båndbredde begrænsningerne kan anvendes: i dette tilfælde 5 Mbps som disse udgående i/o-anmodninger dele den samme kø.

## <a name="best-practices-for-bandwidth-templates"></a>Bedste fremgangsmåder til båndbredde skabeloner

Følg disse bedste fremgangsmåder for enheden StorSimple:

- Konfigurere båndbredde skabeloner på din enhed for at aktivere variable throttling af netværkets hastighed af enheden på forskellige tidspunkter på dagen. Skabelonerne båndbredde, når den bruges med sikkerhedskopiering tidsplaner kan effektivt udnytte flere netværksbåndbredde for skyen handlinger belastet.

- Beregne den faktiske båndbredde, der kræves til en bestemt installation, der er baseret på størrelsen af installationen og formålet påkrævet gendannelse tid (RTO).

## <a name="next-steps"></a>Næste trin

Lær mere om [ved hjælp af tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).
