## <a name="defining-a-backup-policy"></a>Definere en politik for sikkerhedskopiering

En ekstra politik definerer et matrix af når data snapshots er taget, og hvor længe disse snapshots beholdes. Når du definerer en politik for sikkerhedskopiering af en VM, kan du udløse en sikkerhedskopieringsjob *en gang om dagen*. Når du opretter en ny politik, anvendes samling af legitimationsoplysninger. Grænsefladen sikkerhedskopiering politik ser sådan ud:

![Politik for sikkerhedskopiering](./media/backup-create-policy-for-vms/backup-policy.png)

Sådan oprettes en politik:

1. Angiv et navn for **politikkens navn**.

2. Øjebliksbillede af dine data kan hentes på dagligt eller ugentligt intervaller. Brug rullemenuen **Sikkerhedskopi frekvens** til at vælge, om data snapshots er taget dagligt eller ugentligt.

    - Hvis du vælger en daglig interval, kan du bruge kontrolelementet fremhævede til at vælge tid på dagen til snapshot. Hvis du vil ændre timen, fjern markeringen timen, og vælg den nye time.

    ![Daglig sikkerhedskopiering politik](./media/backup-create-policy-for-vms/backup-policy-daily.png) <br/>

    - Hvis du vælger et ugentlig interval, kan du bruge de markerede kontrolelementer til at vælge den eller dage i ugen og klokkeslættet for at tage et øjebliksbillede. Vælg en eller flere dage i dag-menuen. Vælg en time i menuen time. Hvis du vil ændre timen, fjern markeringen af den valgte time, og vælg den nye time.

    ![Ugentlig sikkerhedskopiering politik](./media/backup-create-policy-for-vms/backup-policy-weekly.png)

3. Som standard er alle **Opbevaring område** indstillinger markeret. Fjern markeringen i en opbevaring område grænse, du ikke vil bruge. Angiv derefter interval(s) til brug.

    Månedlig og årligt opbevaring områder gør det muligt at angive øjebliksbilleder baseret på en ugentlig eller daglig stigende.

    >[AZURE.NOTE] Når du beskytter en VM, kører en sikkerhedskopieringsjob en gang om dagen. Den tid, når sikkerhedskopieringen kører er det samme for hvert opbevaring område.

4. Klik på **Gem**øverst på bladet efter alle indstillinger for politikken.

    Den nye politik er øjeblikkeligt bliver anvendt i samling af legitimationsoplysninger.
