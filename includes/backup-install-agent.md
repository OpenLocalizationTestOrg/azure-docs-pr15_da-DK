## <a name="download-install-and-register-the-azure-backup-agent"></a>Hente, installere og registrere Azure Backup agent

Når du har oprettet samling af legitimationsoplysninger Azure sikkerhedskopi, skal være installeret en agent på hver af dine Windows-computere (Windows Server, Windows-klienten, System Center Data Protection Manager server eller servercomputeren Azure sikkerhedskopi), der gør det muligt for sikkerhedskopiering af data og programmer til Azure.

1. Log på [administrationsportalen til](https://manage.windowsazure.com/)

2. Klik på **Gendannelsestjenester**og derefter vælge den ekstra samling af legitimationsoplysninger, som du vil registrere med en server. Siden Hurtig Start for den ekstra samling vises.

    ![Hurtig start](./media/backup-install-agent/quickstart.png)

3. Klik på indstillingen **For Windows Server eller System Center Data Protection Manager eller Windows-klienten** under **Hente Agent**på siden Hurtig Start. Klik på **Gem** for at kopiere den til den lokale computer.

    ![Gemme agent](./media/backup-install-agent/agent.png)

4. Når agenten er installeret, skal du dobbeltklikke på MARSAgentInstaller.exe for at starte installationen af Azure Backup agent. Vælg mappen og arbejdsområ mappe, der kræves til agenten. Den angivne cacheplacering skal have ledig plads, som er mindst 5% af sikkerhedskopidataene.

5.  Hvis du bruger en proxyserver til at oprette forbindelse til internettet, på skærmbilledet **konfiguration af Proxy** skal du angive proxy server oplysninger. Hvis du bruger en godkendt proxy, angive brugernavn og en adgangskode oplysninger i dette skærmbillede.

6.  Azure Backup agent installeres .NET Framework 4.5 og Windows PowerShell (hvis det ikke er tilgængelige allerede) for at fuldføre installationen.

7.  Når agenten er installeret, skal du klikke på knappen **Fortsæt til registrering** for at fortsætte med arbejdsprocessen.

    ![Registrer](./media/backup-install-agent/register.png)

8. Gå til på skærmbilledet samling legitimationsoplysninger, og vælg filen samling legitimationsoplysninger som tidligere er blevet hentet.

    ![Samling af legitimationsoplysninger legitimationsoplysninger](./media/backup-install-agent/vc.png)

    Filen samling legitimationsoplysninger gælder kun for 48 timer (efter at det er hentet fra portalen). Hvis du støder på en hvilken som helst fejl i dette skærmbillede (f.eks. "samling legitimationsoplysninger filen angivet er udløbet")-logon til portalen Azure og hente filen samling legitimationsoplysninger igen.

    Kontrollér, at filen samling legitimationsoplysninger er tilgængelig på en placering, som kan åbnes ved at installationsprogrammet. Hvis du støder på få adgang til relaterede fejl, kopiere samling legitimationsoplysninger fil til en midlertidig placering på denne computer, og prøv igen.

    Hvis du støder på en ugyldig samling legitimationsoplysninger fejl (f.eks. "angivne ugyldige samling legitimationsoplysninger") filen er beskadiget, eller der ikke har de seneste legitimationsoplysninger er knyttet til tjenesten gendannelse. Prøv derefter igen efter overførslen af en ny samling legitimationsoplysninger fil fra portalen. Denne fejl kan typisk ses, hvis brugeren klikker på indstillingen **Hent samling legitimationsoplysninger** i portalen Azure hurtigt efter hinanden. I dette tilfælde er kun den anden samling legitimationsoplysninger fil gyldig.

9. På skærmbilledet **krypteringsindstilling** kan du oprette en adgangskode eller angive en adgangskode (mindst 16 tegn). Husk at gemme adgangskoden på et sikkert sted.

    ![Kryptering](./media/backup-install-agent/encryption.png)

    > [AZURE.WARNING] Hvis adgangskoden er mistet eller glemt; Microsoft kan hjælpe med at gendanne sikkerhedskopidataene. Slutbrugerens ejer kryptering adgangskoden, og Microsoft har ikke indsigt i den adgangskode, der bruges af slutbrugeren. Gem filen i et sikkert sted, som det er nødvendigt under genoprettelse.

10. Når du klikker på knappen **Udfør** , maskinen er registreret til samling af legitimationsoplysninger, og du er nu klar til at starte Sikkerhedskopiering op til Microsoft Azure.

11. Når du bruger Microsoft Azure Backup enkeltstående kan du ændre de indstillinger, der er angivet under registrering arbejdsprocessen ved at klikke på indstillingen **Skift egenskaber** i Azure sikkerhedskopi mmc snap i.

    ![Ændre egenskaber](./media/backup-install-agent/change.png)

    Når du bruger Data Protection Manager, kan du også ændre de indstillinger, der er angivet under registrering arbejdsprocessen ved at klikke på indstillingen **Konfigurer** ved at vælge **Online** under **fanen** .

    ![Konfigurere Azure sikkerhedskopi](./media/backup-install-agent/configure.png)
