## <a name="using-vault-credentials-to-authenticate-with-the-azure-backup-service"></a>Brug af samling legitimationsoplysninger til at godkende med tjenesten Azure sikkerhedskopi

Den lokale server (Windows-klient eller Windows Server eller Data Protection Manager server), skal godkendes med en sikkerhedskopi samling, før den kan sikkerhedskopiere data til Azure. Godkendelsen opnås ved hjælp af "samling legitimationsoplysninger". Samling af legitimationsoplysninger legitimationsoplysninger begrebet ligner en "Publicer indstillinger" fil, som bruges i Azure PowerShell begrebet.

### <a name="what-is-the-vault-credential-file"></a>Hvad er samling legitimationsoplysninger fil?

Filen samling legitimationsoplysninger er et certifikat, der genereres af portalen for hver ekstra samling. På portalen overfører derefter offentlig nøgle til Access kontrolelement ACS (Service). Privat nøgle for certifikatet, er gjort tilgængelig for brugeren som en del af arbejdsprocessen, som er angivet som input i arbejdsprocessen maskine registrering. Dette godkender computer for at sende backup-data til en identificerede samling i Azure Backup-tjenesten.

Samling af legitimationsoplysninger legitimationsoplysninger bruges kun under registrering arbejdsprocessen. Det er brugerens ansvaret for at sikre, at filen samling legitimationsoplysninger ikke er er blevet kompromitteret. Hvis den falder i hænder af enhver uautoriseret-bruger, kan filen samling legitimationsoplysninger bruges til at registrere andre computere mod den samme samling af legitimationsoplysninger. Blive dog som sikkerhedskopidataene er krypteret med en adgangskode, som hører til kunden, eksisterende sikkerhedskopierede data kan ikke afsløret. For at reducere disse bekymringer er samling legitimationsoplysningerne angivet til at udløbe i 48hrs. Du kan hente samling legitimationsoplysninger for et ekstra samling en hvilken som helst antal gange – men kun seneste samling legitimationsoplysninger filen er gældende under registrering arbejdsprocessen.

### <a name="download-the-vault-credential-file"></a>Hente filen samling legitimationsoplysninger

Filen samling legitimationsoplysninger hentes via en sikker kanal fra Azure-portalen. Tjenesten Azure sikkerhedskopi ikke kendskab til den private nøgle for certifikatet, og den private nøgle bevares ikke i portalen eller tjenesten. Brug følgende trin til at hente filen samling legitimationsoplysninger til en lokal computer.

1.  Log på [administrationsportalen til](https://manage.windowsazure.com/)
2.  Klik på **Tjenester til genoprettelse** i den venstre navigationsrude, og vælg den ekstra samling af legitimationsoplysninger, du har oprettet. Klik på ikonet skyen for at få adgang til visningen Hurtig Start for den ekstra samling af legitimationsoplysninger.

    ![Hurtig visning](./media/backup-download-credentials/quickview.png)

3.  Klik på **Hent samling legitimationsoplysninger**på siden Hurtig Start. På portalen genererer filen samling legitimationsoplysninger, der er gjort tilgængelig til hentning.

    ![Download](./media/backup-download-credentials/downloadvc.png)

4.  På portalen genererer en samling af legitimationsoplysninger legitimationsoplysninger ved hjælp af en kombination af samling navn og den aktuelle dato. Klik på **Gem** for at hente samling legitimationsoplysningerne til den lokale konto overførsler mappe, eller Vælg Gem som i menuen Gem til at angive en placering til samling legitimationsoplysninger.

### <a name="note"></a>Bemærk!
- Sørg for, samling legitimationsoplysninger er gemt på en placering, som kan åbnes fra din computer. Hvis den er gemt i en fil del/små og mellemstore virksomheder, søge efter adgangstilladelserne.
- Filen samling legitimationsoplysninger bruges kun under registrering arbejdsprocessen.
- Filen samling legitimationsoplysninger udløber efter 48hrs og kan hentes fra portalen.
- Referere til Azure sikkerhedskopi [ofte stillede spørgsmål](../articles/backup/backup-azure-backup-faq.md) til et spørgsmål i arbejdsprocessen.
