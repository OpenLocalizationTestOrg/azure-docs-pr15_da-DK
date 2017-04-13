<properties
    pageTitle="Fejlfinding: Administration af Azure AD-adgangskoder | Microsoft Azure"
    description="Fejlfinding af almindelige trin til administration af Azure AD adgangskoder, herunder Nulstil, ændring, der ikke er gemt, registrering, og hvilke oplysninger der skal medtages i forbindelse med leder du efter hjælp."
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="asteen"/>

# <a name="how-to-troubleshoot-password-management"></a>Fejlfinding i forbindelse med administration af adgangskoder

> [AZURE.IMPORTANT] **Er du her, fordi du har problemer med at logge på?** Hvis det er tilfældet, [her er hvordan du kan ændre og nulstille din egen adgangskode](active-directory-passwords-update-your-own-password.md).

Hvis du har problemer med administration af adgangskoder, klar vi til at hjælpe. De fleste problemer, du kan støde på, kan løses med et par enkle fejlfindingstrin, du kan læse om nedenfor til at foretage fejlfinding af installationen af:

* [**Oplysninger, der skal medtages, når du har brug for hjælp**](#information-to-include-when-you-need-help)
* [**Problemer med konfiguration af administration af adgangskoder i portalen Azure administration**](#troubleshoot-password-reset-configuration-in-the-azure-management-portal)
* [**Problemer med adgangskode Management rapporter i portalen Azure administration**](#troubleshoot-password-management-reports-in-the-azure-management-portal)
* [**Problemer med adgangskoden nulstille registrering Portal**](#troubleshoot-the-password-reset-registration-portal)
* [**Problemer med adgangskoden nulstille Portal**](#troubleshoot-the-password-reset-portal)
* [**Problemer med adgangskode tilbageførsel**](#troubleshoot-password-writeback)
  - [Adgangskode tilbageførsel hændelseslog fejlkoder](#password-writeback-event-log-error-codes)
  - [Problemer med adgangskode tilbageførsel connectivity](#troubleshoot-password-writeback-connectivity)

Hvis du har prøvet fejlfinding i forbindelse med følgende og er stadig støder på problemer, du kan stille et spørgsmål i [Azure AD-fora](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) eller kontakte support og vi vil se nærmere på dit problem, som vi kan.

## <a name="information-to-include-when-you-need-help"></a>Oplysninger, der skal medtages, når du har brug for hjælp

Hvis du ikke kan løse dit problem med vejledningen nedenfor, kan du kontakte vores supportmedarbejdere. Når du kontakte vedkommende, anbefales det at medtage følgende oplysninger:

 - **Generel beskrivelse af fejlen over** – hvad præcise fejlmeddelelse har brugeren se?  Hvis der var ingen fejlmeddelelse, skal du beskrive de uventede funktionsmåder du bemærket, detaljeret.
 - **Siden** – var hvilken side du på når du fik vist fejlen (omfatte URL-adressen)?
 - **Dato / klokkeslæt / tidszonen** – Hvad var nøjagtige dato og klokkeslæt, du fik vist fejlen (omfatte tidszonen)?
 - **Understøtter kode** –, hvad var support koden genereres, når brugeren fik vist fejlen (for at finde dette, reproducere fejlen, og derefter klikke på linket understøtter kode i bunden af skærmen og sende support engineering GUID, der er resultatet).
   - Hvis du er på en side uden en support kode i bunden, skal du trykke på F12, og Søg efter SID og CID og sende disse to resultater til support engineering.

    ![][001]

 - **Bruger-ID** – Hvad var ID'ET for den bruger, der fik vist fejlen (f.eks.user@contoso.com)?
 - **Oplysninger om brugeren** – var bruger, der er del af et organisationsnetværk, adgangskode hash synkroniserede, kun skyen?  Brugeren har en tildelt AAD Premium eller AAD grundlæggende licens?
 - **Programmets hændelseslog** – Hvis du bruger adgangskode tilbageførsel og fejlen er i infrastrukturen i det lokale miljø, skal du zip en sikkerhedskopi af din programmets hændelseslog fra serveren Azure AD-forbindelse og sende sammen med din anmodning.

Med denne oplysninger om Hjælp os med at løse dit problem så hurtigt som muligt.


## <a name="troubleshoot-password-reset-configuration-in-the-azure-management-portal"></a>Fejlfinding i forbindelse med konfiguration af Nulstil adgangskode i portalen Azure administration
Hvis du støder på en fejl, når nulstilling af adgangskode til konfiguration af, kan du muligvis løse problemet ved at følge trinnene til fejlfinding nedenfor:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Fejl store og små bogstaver</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Hvilke fejl en bruger se?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Løsning</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Jeg kan ikke se sektionen <strong>Bruger adgangskodepolitik Nulstil </strong>under fanen <strong>Konfigurer</strong> i administrationsportalen Azure</p>
            </td>
            <td>
              <p>Sektionen <strong>Bruger adgangskodepolitik nulstille </strong>er ikke synlig på fanen <strong>Konfigurer</strong> i portalen Azure administration.</p>
            </td>
            <td>
              <p>Dette kan ske, hvis du ikke har en AAD Premium eller AAD grundlæggende licens, der er tildelt til administratoren udfører denne handling. </p>
              <p>Tildele en AAD Premium eller AAD grundlæggende licens til den pågældende administratorkonto ved at gå til fanen <strong>licenser</strong> for at løse dette, og prøv igen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Jeg kan ikke se en af de konfigurationsindstillinger under afsnittet <strong>Bruger adgangskodepolitik Nulstil</strong> , som er beskrevet i dokumentationen til.</p>
            </td>
            <td>
              <p>Sektionen <strong>Bruger adgangskodepolitik nulstille </strong>er synlig, men kun flag, der vises under det er flaget <strong>Brugere aktiveret til nulstilling af adgangskode</strong> .</p>
            </td>
            <td>
              <p>Resten af Brugergrænsefladen vises, når du skifter flaget <strong>Brugere aktiveret til nulstilling af adgangskode</strong> til <strong>Ja.</strong></p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Jeg kan ikke se indstillingen en bestemt konfiguration.</p>
            </td>
            <td>
              <p>Jeg kan for eksempel ikke se indstillingen <strong>antallet af dage, før en bruger skal bekræfte deres kontaktoplysninger data</strong> , når jeg ruller gennem sektionen <strong>Bruger adgangskodepolitik nulstille</strong> (eller andre eksempler på det samme problem).</p>
            </td>
            <td>
              <p>Mange elementer i Brugergrænsefladen er skjult, indtil de skal bruges. Forsøg at aktivere alle indstillingerne på siden, hvis du vil have vist.</p>
              <p>Du kan finde flere oplysninger om alle de kontrolelementer, der er tilgængelige for dig i <a href="active-directory-passwords-customize.md#password-management-behavior">funktionsmåde for administration af adgangskoder</a> .</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Jeg kan ikke se indstillingen <strong>Skrive tilbage adgangskoder til en lokal installation</strong> konfiguration</p>
            </td>
            <td>
              <p>Indstillingen <strong>Skrive tilbage adgangskoder til en lokal installation</strong> er ikke synlig under fanen <strong>Konfigurer</strong> i portalen Azure administration</p>
            </td>
            <td>
              <p>Denne indstilling er kun synligt, hvis du har hentet Azure AD-forbindelse og konfigureret adgangskode tilbageførsel. Når du har gjort det, vises indstillingen, og gør det muligt at aktivere eller deaktivere tilbageførsel fra skyen.</p>
              <p>Se <a href="active-directory-passwords-getting-started.md#step-2-enable-password-writeback-in-azure-ad-connect">Aktivere adgangskode tilbageførsel i Azure AD-forbindelse</a> til flere oplysninger om, hvordan du gør dette.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-management-reports-in-the-azure-management-portal"></a>Fejlfinding i forbindelse med adgangskode management rapporter i portalen Azure administration
Hvis du støder på en fejl, når du bruger adgangskode management rapporter, kan du muligvis løse problemet ved at følge trinnene til fejlfinding nedenfor:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Fejl store og små bogstaver</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Hvilke fejl en bruger se?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Løsning</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Jeg kan ikke se rapporter om administration af adgangskode</p>
            </td>
            <td>
              <p>Rapporterne <strong>aktivitet til nulstilling af adgangskode</strong> og <strong>registrering aktivitet til nulstilling af adgangskode</strong> er ikke synlige under <strong>Aktivitetslog</strong> rapporterne i fanen <strong>rapporter</strong> .</p>
            </td>
            <td>
              <p>Dette kan ske, hvis du ikke har en AAD Premium eller AAD grundlæggende licens, der er tildelt til administratoren udfører denne handling. </p>
              <p>Tildele en AAD Premium eller AAD grundlæggende licens til den pågældende administratorkonto ved at gå til fanen <strong>licenser</strong> for at løse dette, og prøv igen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Bruger registreringer vises flere gange</p>
            </td>
            <td>
              <p>Når en bruger registrerer alternative mail, mobiltelefon og spørgsmål om sikkerhed, vises de som separate linjer i stedet for en enkelt linje.</p>
            </td>
            <td>
              <p>I øjeblikket, når en bruger registrerer, kan ikke vi forudsætter, at de vil registrere alt findes på registreringssiden. Vi log som et resultat i øjeblikket enkelte individuelle data, der er registreret som en separat begivenhed.</p>
              <p>Hvis du vil sammenlægge disse data, kan du hente rapporten og åbne dataene, som en pivottabel i excel for at få større fleksibilitet.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Fejlfinding i forbindelse med portalen adgangskode Nulstil registrering
Hvis du støder på en fejl, når du registrerer en bruger til nulstilling af adgangskode, kan du muligvis løse problemet ved at følge trinnene til fejlfinding nedenfor:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Fejl store og små bogstaver</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Hvilke fejl en bruger se?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Løsning</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directory er ikke aktiveret for nulstilling af adgangskode</p>
            </td>
            <td>
              <p>Administratoren har ikke aktiveret du kan bruge denne funktion.</p>
            </td>
            <td>
              <p>Skifte flaget <strong>Brugere aktiveret til nulstilling af adgangskode</strong> til <strong>Ja</strong> , og trykke på <strong>Gem</strong> i fanen Azure Management Portal directory konfiguration. Du skal have en Azure AD Premium eller grundlæggende licens, der er tildelt til administratoren udfører denne handling.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Brugeren har ikke en tildelt licens af AAD Premium eller AAD grundlæggende</p>
            </td>
            <td>
              <p>Administratoren har ikke aktiveret du kan bruge denne funktion.</p>
            </td>
            <td>
              <p>Tildele en Azure AD Premium eller Azure AD grundlæggende licens til brugeren under fanen <strong>licenser</strong> i portalen Azure administration. Du skal have en Azure AD Premium eller grundlæggende licens, der er tildelt til administratoren udfører denne handling.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Anmodning om behandling af fejl</p>
            </td>
            <td>
              <p>Bruger får vist en fejlmeddelelse om:</p>
              <p>

              </p>
              <p>Anmodning om behandling af fejl </p>
              <p>Når du forsøger at nulstille en adgangskode.</p>
            </td>
            <td>
              <p>Det kan skyldes mange af de problemer, men generelt denne fejl skyldes enten en strømafbrydelse eller konfiguration problemet tjeneste, der blev ikke fundet. </p>
              <p>Hvis du får denne fejlmeddelelse, og det, som påvirker beskyttelsen din virksomhed, skal du kontakte support, og vi kan hjælpe dig ASAP. Se <a href="#information-to-include-when-you-need-help">oplysninger der skal medtages, når du har brug for hjælp</a> til at se, hvad du kan give til support engineering som hjælp til en hurtig opløsning.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-the-password-reset-portal"></a>Fejlfinding i forbindelse med portalen adgangskode Nulstil
Hvis du støder på en fejl, når nulstille en adgangskode for en bruger, kan du muligvis løse problemet ved at følge trinnene til fejlfinding nedenfor:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Fejl store og små bogstaver</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Hvilke fejl en bruger se?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Løsning</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directory er ikke aktiveret for nulstilling af adgangskode</p>
            </td>
            <td>
              <p>Din konto er ikke aktiveret for nulstilling af adgangskode</p>
              <p>Vi beklager, men din administrator ikke har konfigureret kontoen til brug sammen med denne tjeneste. </p>
              <p>

              </p>
              <p>Hvis du vil, kan vi kontakte en administrator i organisationen at nulstille din adgangskode for dig.</p>
            </td>
            <td>
              <p>Skifte flaget <strong>Brugere aktiveret til nulstilling af adgangskode</strong> til <strong>Ja</strong> , og trykke på <strong>Gem</strong> i fanen Azure Management Portal directory konfiguration. Du skal have en Azure AD Premium eller grundlæggende licens, der er tildelt til administratoren udfører denne handling.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Brugeren har ikke en tildelt licens af AAD Premium eller AAD grundlæggende</p>
            </td>
            <td>
              <p>Mens vi ikke kan nulstille adgangskoder ikke administrator automatisk, kan vi kontakte organisationens administrator for at gøre det for dig.</p>
            </td>
            <td>
              <p>Tildele en Azure AD Premium eller Azure AD grundlæggende licens til brugeren under fanen <strong>licenser</strong> i portalen Azure administration. Du skal have en Azure AD Premium eller grundlæggende licens, der er tildelt til administratoren udfører denne handling.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directory er aktiveret til nulstilling af adgangskode, men brugeren har manglende eller misdannede godkendelsesoplysninger</p>
            </td>
            <td>
              <p>Din konto er ikke aktiveret for nulstilling af adgangskode</p>
              <p>Vi beklager, men din administrator ikke har konfigureret kontoen til brug sammen med denne tjeneste. </p>
              <p>

              </p>
              <p>Hvis du vil, kan vi kontakte en administrator i organisationen at nulstille din adgangskode for dig.</p>
            </td>
            <td>
              <p>Kontrollér, at brugeren har udformet korrekt kontaktdata på en fil i mappen, før du fortsætter. Se, <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">hvilke data der bruges af nulstilling af adgangskode</a> for at få oplysninger om, hvordan du konfigurerer godkendelsesoplysninger i kataloget, så brugerne ikke kan se denne fejl.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directory er aktiveret til nulstilling af adgangskode, men en bruger har kun én del af en kontaktdata på filen, når politik er indstillet til kræver to bekræftelse</p>
            </td>
            <td>
              <p>Din konto er ikke aktiveret for nulstilling af adgangskode</p>
              <p>Vi beklager, men din administrator ikke har konfigureret kontoen til brug sammen med denne tjeneste. </p>
              <p>

              </p>
              <p>Hvis du vil, kan vi kontakte en administrator i organisationen at nulstille din adgangskode for dig.</p>
            </td>
            <td>
              <p>Sørg for, der bruger har mindst to korrekt konfigurerede kontakter metoder (f.eks., både mobiltelefon og arbejdstelefon), før du fortsætter. Se, <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">hvilke data der bruges af nulstilling af adgangskode</a> for at få oplysninger om, hvordan du konfigurerer godkendelsesoplysninger i kataloget, så brugerne ikke kan se denne fejl.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directory er aktiveret til nulstilling af adgangskode, og brugeren er konfigureret korrekt, men brugeren kan ikke kontaktes </p>
            </td>
            <td>
              <p>Noget gik galt.  Vi der opstod en uventet fejl under kontakte dig.</p>
            </td>
            <td>
              <p>Det kan være resultatet af en midlertidig tjenestefejl eller forkert kontaktdata, som vi ikke kunne registrere korrekt. Hvis brugeren skal vente ti sekunder, en prøve igen, og linket "Kontakt administratoren" vises. Klik på Prøv igen sender igen opkaldet, mens at klikke på "Kontakt administratoren" vil sende en mail med formular til brugeren, adgangskode eller globale administratorer (i prioritetsrækkefølge) anmoder om en skal udføres for den pågældende brugerkonto til nulstilling af adgangskode.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Modtager brugeren aldrig nulstilling af adgangskode SMS eller telefonopkald</p>
            </td>
            <td>
              <p>Brugeren klikker på "tekst mig" eller "Ring til mig" og modtager noget aldrig.</p>
            </td>
            <td>
              <p>Det kan være resultatet af et svigagtig udformet telefonnummer i kataloget. Sørg for, at telefonnummeret, der er i formatet "+ ccc xxxyyyzzzzXeeee". Hvis du vil vide mere om formatering telefon se tal til brug med nulstilling af adgangskoder, <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">hvilke data der bruges af nulstilling af adgangskode</a>.</p>
              <p>Hvis du kræver et filtypenavn skal dirigeres til den pågældende bruger, du bemærke, nulstilling af adgangskode ikke understøtter udvidelser, selvom du angiver en i kataloget (de er fjernet før opkaldet er afsendt). Prøv at bruge et tal uden et filtypenavn, eller integrere filtypenavnet i telefonnummer i din PBX.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Modtager brugeren aldrig mail til nulstilling af adgangskode</p>
            </td>
            <td>
              <p>Brugeren klikker på "sende mig" og modtager noget aldrig.</p>
            </td>
            <td>
              <p>De mest almindelige årsager til dette problem er, at meddelelsen er afvist af et spamfilter. Kontrollere din spam, mappen uønsket mail eller slettede post for mail.</p>
              <p>Også sørge for, at du tjekker den rigtige mail for meddelelse... masser af mennesker har minder meget om e-mail-adresser og ender kontrollere den forkerte Indbakke for meddelelsen. Hvis ingen af disse indstillinger fungerer, det er også muligt, er forkert udformet mailadresse i mappen, skal du kontrollere for at kontrollere e-mail-adressen er den rigtige og prøve igen. Hvis du vil vide mere om formatering mail se adresser til brug med nulstilling af adgangskoder, <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">hvilke data der bruges af nulstilling af adgangskode</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Jeg har angivet en politik til nulstilling af adgangskoder, men denne politik anvendes ikke, når en administratorkonto benytter nulstilling af adgangskode,</p>
            </td>
            <td>
              <p>Administratorkonti nulstilling af deres adgangskoder se de samme indstillinger, der er aktiveret til nulstilling af adgangskode, mail og mobiltelefon, uanset hvilken politik er indstillet i afsnittet <strong>Bruger adgangskodepolitik nulstille</strong> <strong>Konfigurer</strong> under fanen.</p>
            </td>
            <td>
              <p>De indstillinger, der er konfigureret under sektionen <strong>Bruger adgangskodepolitik Nulstil</strong> på fanen <strong>Konfigurer</strong> gælder kun for brugere i organisationen.</p>
              <p>Microsoft administrerer og kontrolelementer Admin til nulstilling af adgangskode politik for at sikre, at det højeste niveau af sikkerhed</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Brugeren forhindres forsøger for mange gange i en dag til nulstilling af adgangskode</p>
            </td>
            <td>
              <p>Bruger får vist en fejlmeddelelse om:</p>
              <p>

              </p>
              <p>Brug en anden indstilling.</p>
              <p>Du har forsøgt at bekræfte din konto for mange gange i den sidste 1 time(r). Af sikkerhedsmæssige årsager, kan det være nødvendigt at vente 24 time(r), før du kan prøve igen. </p>
              <p>Hvis du vil, kan vi kontakte en administrator i organisationen at nulstille din adgangskode for dig.</p>
            </td>
            <td>
              <p>Vi implementere en automatisk variere den benyttede metode til at blokere brugere i at forsøge at nulstille sin adgangskode for mange gange i et kort tidsrum. Dette sker, når:</p>
              <ol class="ordered">
                <li>
Brugeren forsøger at validere et telefonnummer 5 klokkeslæt i en time.<br\><br\></li>
                <li>
Brugeren forsøger at bruge sikkerhed spørgsmål port 5 gange i en time.<br\><br\></li>
                <li>
Brugeren forsøger at nulstille en adgangskode til den samme brugerkonto 5 gange i en time.<br\><br\></li>
              </ol>
              <p>Bed brugeren om at vente 24 timer efter det seneste forsøg på for at løse dette problem, og brugeren vil derefter kunne nulstille sin adgangskode.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Brugeren kan se en fejl, når validering af vedkommendes telefonnummer</p>
            </td>
            <td>
              <p>Når du forsøger at bekræfte en telefon for at bruge som en godkendelsesmetode, kan brugeren se en fejlmeddelelse om:</p>
              <p>

              </p>
              <p>Forkert angivet telefonnummer.</p>
            </td>
            <td>
              <p>Denne fejl opstår, når det telefonnummer, der er angivet ikke svarer til telefonnummeret på en fil.</p>
              <p>Kontrollér, at brugeren er indtastning af hele det telefonnummer, herunder område og land kode, når du forsøger at bruge en telefonbaseret metode til nulstilling af adgangskode.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Anmodning om behandling af fejl</p>
            </td>
            <td>
              <p>Bruger får vist en fejlmeddelelse om:</p>
              <p>

              </p>
              <p>Anmodning om behandling af fejl </p>
              <p>Når du forsøger at nulstille en adgangskode.</p>
            </td>
            <td>
              <p>Det kan skyldes mange af de problemer, men generelt denne fejl skyldes enten en strømafbrydelse eller konfiguration problemet tjeneste, der blev ikke fundet. </p>
              <p>Hvis du får denne fejlmeddelelse, og det, som påvirker beskyttelsen din virksomhed, skal du kontakte support, og vi kan hjælpe dig ASAP. Se <a href="#information-to-include-when-you-need-help">oplysninger der skal medtages, når du har brug for hjælp</a> til at se, hvad du kan give til support engineering som hjælp til en hurtig opløsning.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-writeback"></a>Fejlfinding i forbindelse med adgangskode tilbageførsel
Hvis du støder på en fejl, når aktivere, deaktivere eller bruge adgangskode der ikke er gemt, kan du muligvis løse problemet ved at følge trinnene til fejlfinding nedenfor:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Fejl store og små bogstaver</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Hvilke fejl en bruger se?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Løsning</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Generelt onboarding og start fejl</p>
            </td>
            <td>
              <p>Adgangskode Nulstil tjenesten starter ikke lokalt med fejl 6800 i Azure AD-forbindelse computerens programmets hændelseslog.</p>
              <p>

              </p>
              <p>Efter onboarding, samlet eller adgangskode hash kan ikke synkroniserede brugere nulstille sin adgangskode.</p>
            </td>
            <td>
              <p>Når adgangskode tilbageførsel er aktiveret, ringer Synkroniser program biblioteket tilbageførsel for at udføre konfigurationen (onboarding) ved at ringe til skyen onboarding-tjenesten. Eventuelle fejl under onboarding eller under start WCF-slutpunkt for adgangskode tilbageførsel medfører fejl i hændelseslog i computeren Azure AD-forbindelse hændelseslog.</p>
              <p>Under genstart ADSync-tjenesten, hvis der ikke er gemt blev konfigureret, startes WCF slutpunktet. Men hvis start af slutpunktet mislykkes, skal vi vil blot logge begivenhed 6800 og lade Synkroniser tjenesten blev startet. Tilstedeværelse af begivenheden betyder, at den adgangskode tilbageførsel slutpunkt ikke blev startet op. Hændelseslog oplysninger om denne begivenhed (6800) sammen med poster i hændelsesloggen generere ved PasswordResetService komponent indikerer, hvorfor slutpunktet ikke kunne startes. Gennemse disse hændelseslog fejl og forsøger at starte Azure AD-forbindelse igen, hvis adgangskode tilbageførsel stadig ikke virker. Hvis problemet fortsætter, kan du prøve at deaktivere og genaktivere adgangskode tilbageførsel.</p>
            </td>
          </tr>
                    <tr>
            <td>
              <p>Når en bruger forsøger at nulstille en adgangskode eller låse op for en konto med adgangskode tilbageførsel aktiveret, mislykkes handlingen. Derudover kan du se en begivenhed i den Azure AD-forbindelse hændelseslog, der indeholder: "synkronisering program returneres en fejl hr = 800700CE, meddelelse = filnavnet eller filtypenavn er for lang" efter handlingen unlock opstår.
                            </p>
            </td>
            <td>
              <p>Dette kan ske, hvis du har opgraderet fra ældre versioner af Azure AD-forbindelse eller DirSync. Opgradere til ældre versioner af Azure AD-forbindelse, angive en 254 tegn adgangskode for kontoen Azure AD Management Agent (nyere versioner kan angive en adgangskode til længden af 127 tegn). Sådan lange adgangskoder arbejde for AD forbindelse Import og eksport af handlinger, men de understøttes ikke af handlingen Lås op.
                            </p>
            </td>
            <td>
              <p>[Finde Active Directory-kontoen](active-directory-aadconnect-accounts-permissions.md#active-directory-account) til Azure AD-forbindelse og nulstille adgangskoden til at indeholde højst 127 tegn. Åbn derefter **Tjenesten Brugerprofilsynkronisering** fra menuen Start. Gå til **forbindelser** , og find **Active Directory Connector**. Markere det og klikke på **Egenskaber**. Gå til siden **legitimationsoplysninger** og angive den nye adgangskode. Vælg **OK** for at lukke siden.
                            </p>
            </td>
          </tr>
                    <tr>
            <td>
              <p>Fejl under konfiguration af tilbageførsel under installationen af Azure AD-forbindelse.</p>
            </td>
            <td>
              <p>På det sidste trin i installationsprocessen Azure AD-forbindelse vist du en fejl, der angiver, at adgangskoden tilbageførsel ikke kunne konfigureres.</p>
              <p>

              </p>
              <p>Azure AD forbinde programmets hændelseslog indeholder fejl 32009 med teksten "Fejl ved hentning auth token".</p>
            </td>
            <td>
              <p>Denne fejl opstår i følgende to tilfælde:</p>
              <ul>
                <li class="unordered">
Du har angivet en forkert adgangskode for kontoen global administrator, der er angivet i starten af installationsprocessen Azure AD-forbindelse.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Du har forsøgt at bruge en samlet bruger til den globale administratorkonto, der er angivet i starten af installationsprocessen Azure AD-forbindelse.<br\><br\></li>
              </ul>
              <p>For at rette denne fejl, skal du sikre, at du ikke bruger en samlet konto til den globale administrator, du har angivet i starten af installationsprocessen Azure AD-forbindelse, og, at den angivne adgangskode er korrekt.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Fejl under konfiguration af tilbageførsel under installationen af Azure AD-forbindelse.</p>
            </td>
            <td>
              <p>Azure AD-forbindelse maskine hændelseslog indeholder fejl 32002 udløst af PasswordResetService.</p>
              <p>

              </p>
              <p>Fejlen læser: "fejl forbindelse til ServiceBus, provideren sikkerhedstoken kunne ikke angive et sikkerhedstoken..."</p>
              <p>

              </p>
            </td>
            <td>
              <p>Den egentlige årsag til denne fejl er, at adgangskoden Nulstil tjenesten kører i din lokale miljø ikke er kunne oprette forbindelse til tjenesten bus slutpunkt i skyen. Denne fejl skyldes normalt normalt en firewallregel for blokerer en udgående forbindelse til en bestemt port eller web-adresse.</p>
              <p>

              </p>
              <p>Sørg for, at din firewall tillader udgående forbindelser til følgende:</p>
              <ul>
                <li class="unordered">
Al trafik via TCP 443 (HTTPS)<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Udgående forbindelser til <br\><br\></li>
              </ul>
              <p>

              </p>
              <p>Når du har opdateret disse regler, statisk Azure AD-forbindelse og adgangskode tilbageførsel skal begynde at arbejde igen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Adgangskode tilbageførsel slutpunkt lokalt ikke nås</p>
            </td>
            <td>
              <p>Efter at have prøvet for nogle klokkeslæt, samlet eller adgangskode hash kan ikke synkroniserede brugere nulstille sin adgangskode.</p>
            </td>
            <td>
              <p>I visse tilfælde kan tjenesten adgangskode tilbageførsel undlade at starte igen når Azure AD-forbindelse er startet igen. I disse tilfælde først skal du kontrollere om adgangskode tilbageførsel vises skal være aktiveret på prem. Dette kan gøres ved hjælp af guiden Azure AD-forbindelse eller powershell (se HowTos ovenfor). Hvis funktionen ser ud til at være aktiveret, prøv at aktivere eller deaktivere funktionen igen enten via Brugergrænsefladen eller PowerShell. Se "trin 2: Aktivér adgangskode der ikke er gemt på computeren katalogsynkronisering &amp; konfigureres firewallregler" i <a href="active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords">hvordan du kan aktivere/deaktivere adgangskode tilbageførsel</a> kan finde flere oplysninger om, hvordan du gør dette.</p>
              <p>

              </p>
              <p>Hvis det ikke virker, kan du prøve helt fjerne og geninstallere Azure AD-forbindelse.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Tilladelser fejl</p>
            </td>
            <td>
              <p>Del af et organisationsnetværk eller synkronisering af adgangskoder hash'd brugere, der forsøger at nulstille deres adgangskoder se en fejl, når du har sendt den adgangskode, der angiver der opstod et problem med tjenesten.</p>
              <p>

              </p>
              <p>Ud over dette skal under adgangskode Nulstil handlinger, vises der muligvis en fejl om management agent blev nægtet adgang i din til lokale hændelseslogfiler.</p>
            </td>
            <td>
              <p>Hvis du får vist disse fejl i din hændelseslog, Bekræft, at kontoen AD glidende gennemsnit (der er angivet i guiden på tidspunktet for konfiguration) har de nødvendige tilladelser til adgangskode tilbageførsel.</p>
              <p>

              </p>
              <p>Bemærk, at når der gives denne tilladelse kan det tage op til 1 time for tilladelserne til at kommer lidt efter lidt ned via sdprop baggrund opgave på DC. </p>
              <p>Hvis du vil arbejde til nulstilling af adgangskode, have tilladelse til at være tidsstemplet på sikkerhedsbeskrivelsen af brugerobjektet, hvis adgangskode nulstilles. Indtil denne tilladelse vises på brugerobjektet, fortsætter nulstilling af adgangskode mislykkes med adgang nægtet.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Fejl, når du konfigurerer adgangskode tilbageskrivning fra konfigurationsguiden af Azure AD-forbindelse </p>
            </td>
            <td>
              <p>"Kan ikke finde glidende gennemsnit" fejl i guiden under aktivering/deaktivering af adgangskode tilbageførsel</p>
            </td>
            <td>
              <p>Der er et kendt fejl i den endelige version af Azure AD-forbindelse, der viser i følgende situationer:</p>
              <ol class="ordered">
                <li>
Du konfigurerer Azure AD-forbindelse til lejer abc.com (domæne bekræftet) ved hjælp af creds. Dette resulterer i AAD forbindelse med navnet "abc.com – AAD" der oprettes.<br\><br\></li>
                <li>
Du derefter ændre AAD creds for forbindelsen (ved hjælp af gamle Synkroniser brugergrænseflade) for at (Bemærk det er den samme lejer men andet domænenavn). <br\><br\></li>
                <li>
Nu prøver du at aktivere/deaktivere adgangskode tilbageførsel. Guiden oprette navnet på den forbindelse, ved hjælp af creds, som "abc.onmicrosoft.com – AAD" og overføre til Cmdletten adgangskode tilbageførsel. Dette mislykkes, fordi der ikke er nogen forbindelse, der er oprettet med dette navn.<br\><br\></li>
              </ol>
              <p>Problemet er løst i vores nyeste builds. Hvis du har en ældre build, er en løsning at bruge powershell-cmdlet til at aktivere/deaktivere funktionen. Se "trin 2: Aktivér adgangskode der ikke er gemt på computeren katalogsynkronisering &amp; konfigureres firewallregler" i <a href="active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords">hvordan du kan aktivere/deaktivere adgangskode tilbageførsel</a> kan finde flere oplysninger om, hvordan du gør dette.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Kan ikke nulstille adgangskoden for brugere i særlige grupper som domæneadministrator / Enterprise administratorer osv.</p>
            </td>
            <td>
              <p>Del af et organisationsnetværk eller synkronisering af adgangskoder hash'd brugere, der er en del af beskyttet grupper og forsøger at nulstille deres adgangskoder se en fejl, når du har sendt den adgangskode, der angiver der opstod et problem med tjenesten.</p>
            </td>
            <td>
              <p>Privilegerede brugere i Active Directory er beskyttet med AdminSDHolder. Se <a href="https://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx">http://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx</a> få mere at vide. </p>
              <p>

              </p>
              <p>Det betyder sikkerhedsbeskrivelser på disse objekter kontrolleres med jævne mellemrum, så det svarer til et angivet i AdminSDHolder og er nulstillet, hvis de er forskellige. De ekstra tilladelser, som er nødvendige for adgangskode tilbageførsel derfor ikke kommer lidt efter lidt til disse brugere. Dette kan medføre adgangskode tilbageførsel ikke arbejder for disse brugere. Som et resultat, understøtter vi ikke administrere adgangskoder for brugere i disse grupper fordi det sideskift sikkerhedsmodel AD.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Nulstil handlinger mislykkes med objekt blev ikke fundet</p>
            </td>
            <td>
              <p>Del af et organisationsnetværk eller synkronisering af adgangskoder hash'd brugere, der forsøger at nulstille deres adgangskoder se en fejl, når du har sendt den adgangskode, der angiver der opstod et problem med tjenesten.</p>
              <p>

              </p>
              <p>Ud over dette skal under adgangskode Nulstil handlinger, vises der muligvis en fejl i dine hændelseslogfiler fra tjenesten Azure AD-forbindelse, der angiver fejlen "Objekt blev ikke fundet".</p>
            </td>
            <td>
              <p>Denne fejl angiver som regel, Synkroniser programmet kan ikke finde den brugerobjektet i AAD connector-plads eller den sammenkædede MV eller AD forbindelse mellemrum objekt. </p>
              <p>

              </p>
              <p>Sørg for, at brugeren faktisk synkroniseret fra lokalt til AAD via den aktuelle forekomst af Azure AD-forbindelse for at løse dette problem, og Undersøg tilstanden for objekterne i connector mellemrum og MV. Bekræft, at AD CS objektet er forbindelse til objektet MV via "Microsoft.InfromADUserAccountEnabled.xxx" reglen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Nulstil handlinger mislykkes med flere forekomster fundet eror</p>
            </td>
            <td>
              <p>Del af et organisationsnetværk eller synkronisering af adgangskoder hash'd brugere, der forsøger at nulstille deres adgangskoder se en fejl, når du har sendt den adgangskode, der angiver der opstod et problem med tjenesten.</p>
              <p>

              </p>
              <p>Ud over dette skal under adgangskode Nulstil handlinger, vises der muligvis en fejl i dine hændelseslogfiler fra tjenesten Azure AD-forbindelse, der angiver fejlen "Flere maches fundet".</p>
            </td>
            <td>
              <p>Dette betyder, Synkroniser program fundet, at MV objektet er forbundet med mere end én AD CS objekter via "Microsoft.InfromADUserAccountEnabled.xxx". Det betyder, at brugeren har en aktiveret konto i mere end ét område. </p>
              <p>

              </p>
              <p>Dette scenarie understøttes i øjeblikket ikke for adgangskode tilbageførsel.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Adgangskode handlinger mislykkes med en konfigurationsfejl.</p>
            </td>
            <td>
              <p>Adgangskode handlinger mislykkes med en konfigurationsfejl. Programmets hændelseslog indeholder Azure AD-forbindelse fejl 6329 med tekst: 0x8023061f (handlingen mislykkedes, fordi synkronisering af adgangskoder ikke er aktiveret på denne Management Agent.)</p>
            </td>
            <td>
              <p>Dette sker, hvis Azure AD-forbindelse konfigurationen ændres for at tilføje&nbsp;et nyt AD område (eller for at fjerne og derefter tilføje en eksisterende skov) <strong>efter</strong> funktionen adgangskode tilbageførsel er allerede blevet aktiveret. Adgangskode handlinger for brugere i disse områder, der er tilføjet for nylig mislykkes. Du kan løse problemet ved at deaktivere og genaktivere funktionen adgangskode tilbageførsel, når ændringerne i skov konfigurationen er fuldført.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Skrive tilbage adgangskoder, der er blevet nulstillet ved brugere fungerer korrekt, men skrive tilbage adgangskoder ændret af en bruger eller Nulstil for en bruger af en administrator mislykkes.</p>
            </td>
            <td>
              <p>Når du forsøger at nulstille en adgangskode på vegne af en bruger fra Azure Management-portalen, du ser en meddelelse om: "tjenesten adgangskode Nulstil kører i din lokale miljø understøtter ikke administratorer nulstille brugeradgangskoder. Du kan opgradere til den nyeste version af Azure AD-forbindelse til at løse dette problem."</p>
            </td>
            <td>
              <p>Dette sker, når versionen af synkronisering program ikke understøtter handlingen bestemt adgangskode tilbageførsel, der blev brugt. Versioner af Azure AD-forbindelse senere end 1.0.0419.0911 understøtter alle adgangskode management handlinger, herunder adgangskode nulstille tilbageførsel, adgangskode Skift tilbageførsel og administrator-definerede adgangskode Nulstil tilbageførsel fra Azure Management-portalen.&nbsp; DirSync versioner understøtter senere end 1.0.6862 kun adgangskode Nulstil tilbageførsel. Du kan løse dette problem ved det anbefales, at du installerer den nyeste version af Azure AD-forbindelse eller Azure Active Directory forbinde (få flere oplysninger under <a href="active-directory-aadconnect">Katalogintegrationsværktøjer</a>) til at løse dette problem, og du får mest muligt ud af adgangskode tilbageførsel i din organisation.</p>
            </td>
          </tr>
        </tbody></table>


## <a name="password-writeback-event-log-error-codes"></a>Adgangskode tilbageførsel hændelseslog fejlkoder
En god fremgangsmåde, når fejlfinding af problemer med adgangskode tilbageførsel er at undersøge dette program hændelseslog på computeren Azure AD-forbindelse. Denne hændelseslog indeholder begivenheder fra to kilder af interesse for adgangskode tilbageførsel. PasswordResetService kilden beskriver problemer, der er relateret til driften af adgangskode tilbageførsel og handlinger. ADSync kilden beskriver problemer, der er relateret til angivelse af adgangskoder i dit miljø, AD og handlinger.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Kode</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Navngive / meddelelse</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Kilde</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Beskrivelse</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>6329</p>
            </td>
            <td>
              <p>KAUTION: MMS(4924) 0x80230619 – "en begrænsning forhindrer, at adgangskoden, der ændres til den aktuelle, der er angivet."</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Denne hændelse opstår, når tjenesten adgangskode tilbageførsel forsøger at angive en adgangskode til din lokale mappe, som ikke opfylder de adgangskode alder, historik, kompleksitet eller spamfiltrering krav på domænet.</p>
              <ul>
                <li class="unordered">
Hvis du har en mindste adgangskode alder og senest har ændret adgangskode i vinduet tid, kan du ikke kan ændre adgangskoden igen, indtil den når den angivne alder på dit domæne. Til testformål, skal du vælge Minimumalderen til 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Hvis du har aktiveret kravene til adgangskoden oversigt og derefter skal du vælge en adgangskode, der ikke er blevet brugt i de sidste N tidspunkter, hvor N er indstillingen adgangskode oversigt. Hvis du markerer en adgangskode, som er blevet brugt i de seneste tidspunkter, hvor N, får derefter du vist en fejl i dette tilfælde. Til testformål, skal du vælge oversigt til 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Hvis du har kravene til adgangskoden kompleksitet, gennemtvinges alle dem, når brugeren forsøger at ændre eller nulstille en adgangskode.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Hvis du har adgangskodefiltre, der er aktiveret, og en bruger vælger en adgangskode, som ikke opfylder filterkriterierne, klik derefter på Nulstil eller ændre mislykkes handlingen.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>HR 8023042</p>
            </td>
            <td>
              <p>Synkronisering program returneres en fejl hr = 80230402, meddelelse = et forsøg på at få et objekt mislykkedes, fordi der er dublerede poster med det samme anker</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Denne hændelse indtræffer, når det samme bruger-id er aktiveret i flere domæner.  Eksempelvis hvis du synkronisering af konto/ressource områder og har det samme bruger-id Præsenter og aktiveret i hver, denne fejl kan opstå.  </p>
              <p>Denne fejl kan også opstå, hvis du bruger en ikke-unik anker attribut (som alias eller UPN) og to brugere deler samme anker attributten.</p>
              <p>For at løse dette problem ved at sikre, at du ikke har nogen dublerede brugere i dine domæner og, at du bruger en entydig anker attribut for hver bruger.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31001</p>
            </td>
            <td>
              <p>PasswordResetStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Begivenheden angiver, at tjenesten, lokalt registreret en nulstilling af adgangskode anmodning om en samlet eller synkronisering af adgangskoder hash'd bruger, der kommer fra skyen. Denne hændelse er den første hændelse i hver adgangskode nulstille tilbageførsel handling.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31002</p>
            </td>
            <td>
              <p>PasswordResetSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver, at en bruger har valgt en ny adgangskode under en handling til nulstilling af adgangskode, og vi konstateret, at denne adgangskode opfylder kravene til virksomhedens adgangskoden, og denne adgangskode er blevet skrevet tilbage til det lokale miljø AD.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31003</p>
            </td>
            <td>
              <p>PasswordResetFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver, at en bruger har valgt en adgangskode, og, at adgangskoden er modtaget korrekt til det lokale miljø, men når vi har forsøgt at angive adgangskoden i det lokale miljø AD, der opstod en fejl. Dette kan ske af forskellige årsager:</p>
              <ul>
                <li class="unordered">
Brugerens adgangskode opfylder alder, historik, kompleksitet ikke, eller filtrere krav til domænet. Prøv en helt ny adgangskode for at løse dette problem.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Tjenestekonto glidende gennemsnit har ikke de nødvendige tilladelser til at angive den nye adgangskode på den pågældende brugerkonto.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Brugerens konto er i en beskyttet gruppe, som domæne eller enterprise-administratorer, som ikke tillader adgangskode sæt handlinger.<br\><br\></li>
              </ul>
              <p>Se <a href="#troubleshoot-password-writeback">Fejlfinding i forbindelse med adgangskode tilbageførsel</a> for at lære mere om, hvilke andre situtions kan forårsage fejlen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31004</p>
            </td>
            <td>
              <p>OnboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse opstår, hvis du aktiverer adgangskode tilbageførsel med Azure AD-forbindelse og angiver, at vi startet onboarding din organisation til webtjenesten adgangskode tilbageførsel.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31005</p>
            </td>
            <td>
              <p>OnboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Begivenheden angiver onboarding processen lykkedes og, adgangskode tilbageførsel ikke er klar til brug.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31006</p>
            </td>
            <td>
              <p>ChangePasswordStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Begivenheden angiver, at tjenesten, lokalt registreret anmodning om en adgangskode ændring af et organisationsnetværk eller synkronisering af adgangskoder hash'd bruger, der kommer fra skyen. Denne hændelse er den første hændelse i enhver adgangskode Skift tilbageførsel operation.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31007</p>
            </td>
            <td>
              <p>ChangePasswordSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver, at en bruger har valgt en ny adgangskode under en handling til ændring af adgangskoden, vi konstateret, at denne adgangskode opfylder kravene til virksomhedens adgangskoden, og denne adgangskode er blevet skrevet tilbage til det lokale miljø AD.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31008</p>
            </td>
            <td>
              <p>ChangePasswordFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver, at en bruger har valgt en adgangskode, og, at adgangskoden er modtaget korrekt til det lokale miljø, men når vi har forsøgt at angive adgangskoden i det lokale miljø AD, der opstod en fejl. Dette kan ske af forskellige årsager:</p>
              <ul>
                <li class="unordered">
Brugerens adgangskode opfylder alder, historik, kompleksitet ikke, eller filtrere krav til domænet. Prøv en helt ny adgangskode for at løse dette problem.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Tjenestekonto glidende gennemsnit har ikke de nødvendige tilladelser til at angive den nye adgangskode på den pågældende brugerkonto.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Brugerens konto er i en beskyttet gruppe, som domæne eller enterprise-administratorer, som ikke tillader adgangskode sæt handlinger.<br\><br\></li>
              </ul>
              <p>Se <a href="#troubleshoot-password-writeback">Fejlfinding i forbindelse med adgangskode tilbageførsel</a> for at lære mere om, hvilke andre situationer kan forårsage fejlen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31009</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Tjenesten lokale registreret en nulstilling af adgangskode anmodning om en samlet eller synkronisering af adgangskoder hash'd bruger, der kommer fra administratoren på vegne af en bruger. Denne hændelse er den første hændelse i hver admin-definerede adgangskode Nulstil tilbageførsel handling.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31010</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Administratoren har valgt en ny adgangskode under handlingen admin-definerede adgangskode Nulstil, vi konstateret, at denne adgangskode opfylder kravene til virksomhedens adgangskoden, og denne adgangskode er blevet skrevet tilbage til det lokale miljø AD.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31011</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Administratoren har valgt en adgangskode på vegne af en bruger, og denne adgangskode er modtaget korrekt til det lokale miljø, men når vi har forsøgt at angive adgangskoden i det lokale miljø AD, der opstod en fejl. Dette kan ske af forskellige årsager:</p>
              <ul>
                <li class="unordered">
Brugerens adgangskode opfylder alder, historik, kompleksitet ikke, eller filtrere krav til domænet. Prøv en helt ny adgangskode for at løse dette problem.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Tjenestekonto glidende gennemsnit har ikke de nødvendige tilladelser til at angive den nye adgangskode på den pågældende brugerkonto.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Brugerens konto er i en beskyttet gruppe, som domæne eller enterprise-administratorer, som ikke tillader adgangskode sæt handlinger.<br\><br\></li>
              </ul>
              <p>Se <a href="#troubleshoot-password-writeback">Fejlfinding i forbindelse med adgangskode tilbageførsel</a> for at lære mere om, hvilke andre situtions kan forårsage fejlen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31012</p>
            </td>
            <td>
              <p>OffboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse opstår, hvis du deaktiverer adgangskode tilbageførsel med Azure AD-forbindelse og angiver, at vi startet offboarding din organisation til webtjenesten adgangskode tilbageførsel.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31013</p>
            </td>
            <td>
              <p>OffboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver offboarding processen lykkedes og, adgangskode tilbageførsel egenskab er blevet deaktiveret korrekt.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31014</p>
            </td>
            <td>
              <p>OffboardingEventFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver offboarding processen ikke blev fuldført. Det kan skyldes fejlen tilladelser på den sky eller lokale administratorkonto, der er angivet under konfigurationen, eller fordi du forsøger at bruge organisationsnetværket skyen global administrator, når du deaktiverer adgangskode tilbageførsel. Du kan løse problemet, kontrollere din administrative tilladelser og, at du ikke bruger en samlet konto under konfigurationen af muligheden for adgangskode tilbageførsel.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31015</p>
            </td>
            <td>
              <p>WriteBackServiceStarted </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver, at adgangskoden tilbageførsel tjenesten blev startet og er klar til at acceptere adgangskode management anmodninger fra skyen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31016</p>
            </td>
            <td>
              <p>WriteBackServiceStopped </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Begivenheden angiver, at adgangskoden tilbageførsel tjenesten er standset og, adgangskode management anmodninger fra skyen ikke bliver lykkes.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31017</p>
            </td>
            <td>
              <p>AuthTokenSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver, at vi er blevet hentet et godkendelse token til den globale administrator, der er angivet under installationen af Azure AD-forbindelse for at starte processen offboarding eller onboarding.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31018</p>
            </td>
            <td>
              <p>KeyPairCreationSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver, at vi blev oprettet krypteringsnøglen adgangskode, der skal bruges til at kryptere adgangskoder fra skyen skal sendes til dit lokale miljø.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32000</p>
            </td>
            <td>
              <p>UnknownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver en ukendt fejl under en handling til administration af adgangskode. Se på undtagelse teksten i begivenheden for flere oplysninger. Hvis du oplever problemer, prøve at deaktivere og genaktivere adgangskode tilbageførsel. Hvis det ikke hjælper, omfatter en kopi af din hændelseslog sammen med det angivne registrering-id insider til din support engineering.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32001</p>
            </td>
            <td>
              <p>ServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver der opstod en fejl, oprette forbindelse til skyen adgangskoden nulstille tjeneste, og sker normalt, når tjenesten lokale kunne ikke oprette forbindelse til webtjenesten adgangskode Nulstil. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32002</p>
            </td>
            <td>
              <p>ServiceBusError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver, at der opstod en fejl, oprette forbindelse til din lejer service bus forekomst. Det kan ske, fordi du blokerer udgående forbindelser i dit lokale miljø. Se din firewall til at sikre, at du Tillad forbindelser til <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>og over TCP 443, og prøv igen. Hvis du stadig har problemer, prøve at deaktivere og genaktivere adgangskode tilbageførsel.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32003</p>
            </td>
            <td>
              <p>InPutValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver, at det input, overføres til vores web service API var ugyldige. Prøv igen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32004</p>
            </td>
            <td>
              <p>DecryptionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver, at der opstod en fejl, dekryptere den adgangskode, der er modtaget fra skyen. Det kan skyldes en dekryptering vigtige uoverensstemmelse mellem skytjenesten og dit lokale miljø. For at løse dette problem, deaktivere eller genaktivere adgangskode der ikke er gemt i dit lokale miljø.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32005</p>
            </td>
            <td>
              <p>ConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Under onboarding gemme vi lejer-specifikke oplysninger i en konfigurationsfil i dit lokale miljø. Denne hændelse angiver der opstod en fejl, gemme denne fil eller, når tjenesten er blevet startet der en fejl læste filen. For at løse dette problem skal du prøve at deaktivere og igen aktivering adgangskode tilbageførsel for at tvinge en igen skrive af denne konfigurationsfil. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32006</p>
            </td>
            <td>
              <p>EndPointConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>FORÆLDET – denne hændelse er ikke til stede i Azure AD-forbindelse, kun meget tidligt opbygger af DirSync som understøttes der ikke er gemt.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32007</p>
            </td>
            <td>
              <p>OnBoardingConfigUpdateError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Under onboarding sender vi data fra skyen til den lokale adgangskode nulstille tjeneste. Disse data skrives derefter til en fil i hukommelsen før der sendes til tjenesten sync til at gemme oplysningerne sikkert på disken. Denne hændelse angiver et problem med at skrive eller opdatere dataene i hukommelsen. For at løse dette problem skal du prøve at deaktivere og igen aktivering adgangskode tilbageførsel for at tvinge en igen skrive af denne konfiguration.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32008</p>
            </td>
            <td>
              <p>ValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver vi har modtaget et ugyldigt svar fra webtjenesten adgangskode Nulstil. For at løse dette problem skal du prøve at deaktivere og igen aktivering adgangskode tilbageførsel.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32009</p>
            </td>
            <td>
              <p>AuthTokenError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver, at vi ikke kunne hentes tilladelse token til den globale administratorkonto, der er angivet under installationen af Azure AD-forbindelse. Fejlen kan skyldes en forkert brugernavn eller adgangskode angivet for global administrator eller fordi global administrator angivet er knyttet. For at løse dette problem ved at køre igen konfiguration med det rigtige brugernavn og adgangskode og sikre administratoren er en administreret (kun skyen eller synkronisering af adgangskoder ville) konto.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32010</p>
            </td>
            <td>
              <p>CryptoError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver, at der opstod en fejl, når der genereres adgangskoden krypteringsnøglen eller dekryptere en adgangskode, som modtages fra skytjenesten. Denne fejl, der sandsynligvis angiver et problem med dit miljø. Se på oplysningerne om din hændelseslog til at få mere at vide og løse dette problem. Du kan også prøve at deaktivere og genaktivere tjenesten adgangskode tilbageførsel til at løse dette problem.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32011</p>
            </td>
            <td>
              <p>OnBoardingServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver, at lokale tjenesten ikke kunne korrekt kommunikere med adgangskode Nulstil webtjenesten til at starte processen onboarding. Det kan være på grund af en firewallregel eller et problem med at få et auth token for din lejer. Hvis du vil løse dette problem ved at sikre, at du ikke blokerer udgående forbindelser over TCP 443 og TCP-9350 9354 eller <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>, og som ikke er i organisationsnetværk administratorkonto AAD, du bruger til indbyggede. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32012</p>
            </td>
            <td>
              <p>OnBoardingServiceDisableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>FORÆLDET – denne hændelse er ikke til stede i Azure AD-forbindelse, kun meget tidligt opbygger af DirSync som understøttes der ikke er gemt.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32013</p>
            </td>
            <td>
              <p>OffBoardingError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver, at lokale tjenesten ikke kunne korrekt kommunikere med adgangskode Nulstil webtjenesten til at starte processen offboarding. Det kan være på grund af en firewallregel eller et problem med at få en tilladelse token for din lejer. For at løse dette problem ved at sikre, at du ikke blokerer udgående forbindelser over 443 eller <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>, og, administratorkonto AAD du bruger til at Afvikl ikke er i organisationsnetværk. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32014</p>
            </td>
            <td>
              <p>ServiceBusWarning </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver, at der var forsøg igen for at oprette forbindelse til din lejer service bus forekomst. Under normale forhold bør dette ikke være et problem, men hvis du får vist denne hændelse mange gange overveje at tjekke din netværksforbindelse til tjenesten bus, især hvis det er en lang ventetid eller med lav båndbredde.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32015</p>
            </td>
            <td>
              <p>ReportServiceHealthError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>For at overvåge tilstanden for din adgangskode tilbageførsel tjeneste, sender vi godkendelse gyldighed data til vores adgangskode nulstille webtjeneste hver 5 minutter. Denne hændelse angiver, at der opstod en fejl, når du sender oplysningerne sundhed tilbage til skyen webtjeneste. Oplysningerne sundhed inkluderer ikke en OII eller PII data, og er kun en godkendelse gyldighed og statistik for grundlæggende tjenesten så vi kan tilbyde service statusoplysninger i skyen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33001</p>
            </td>
            <td>
              <p>ADUnKnownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver, at der opstod en ukendt fejl, der returneres af AD, skal du kontrollere Azure AD-forbindelse server-hændelsesloggen for hændelser fra ADSync kilden kan finde flere oplysninger om fejlen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33002</p>
            </td>
            <td>
              <p>ADUserNotFoundError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver, at den bruger, der forsøger at nulstille eller ændre en adgangskode ikke blev fundet i den lokale mappe. Dette kan ske, når brugeren er blevet slettet lokalt, men ikke i skyen, eller hvis der er et problem med synkronisering. Markér Synkroniser logfilerne samt sidst par Synkroniser køre detaljer kan finde flere oplysninger.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33003</p>
            </td>
            <td>
              <p>ADMutliMatchError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Når en nulstilling af adgangskode eller anmodning om ændring stammer fra skyen, kan vi bruger skyen anker angivet under installationen af Azure AD-forbindelse til at finde ud af, hvordan du kan sammenkæde anmodningen tilbage til en bruger i dit lokale miljø. Denne hændelse angiver, at vi fandt to brugere i din lokale mappe med samme skyen anker attribut. Markér Synkroniser logfilerne samt sidst par Synkroniser køre detaljer kan finde flere oplysninger.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33004</p>
            </td>
            <td>
              <p>ADPermissionsError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver servicekonto Management Agent ikke har de relevante tilladelser på den pågældende til at angive en ny adgangskode konto. Sørg for, at kontoen glidende gennemsnit i brugerens skov har Nulstil og redigeringstilladelser og adgangskode på alle objekter i området.  Du kan finde flere oplysninger om, hvordan du gøre med dette, ved at se <a href="active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions">trin 4: konfigurere de relevante tilladelser i Active Directory</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33005</p>
            </td>
            <td>
              <p>ADUserAccountDisabled </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver, at vi har forsøgt at nulstille eller ændre en adgangskode til en konto, der blev deaktiveret lokalt. Aktivere kontoen, og prøv igen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33006</p>
            </td>
            <td>
              <p>ADUserAccountLockedOut </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Begivenhed angiver, at vi har forsøgt at nulstille eller ændre en adgangskode til en konto, der er låst ude lokalt. Spærringer kan opstå, når en bruger har prøvet en ændring eller Nulstil adgangskode handlingen for mange gange i kort tid. Låse op for kontoen, og prøv igen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33007</p>
            </td>
            <td>
              <p>ADUserIncorrectPassword </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver, at brugeren har angivet en forkert aktuelle adgangskode når udføre en adgangskode ændres handling. Angiv den korrekte aktuelle adgangskode, og prøv igen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33008</p>
            </td>
            <td>
              <p>ADPasswordPolicyError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse opstår, når tjenesten adgangskode tilbageførsel forsøger at angive en adgangskode til din lokale mappe, som ikke opfylder de adgangskode alder, historik, kompleksitet eller spamfiltrering krav på domænet.</p>
              <ul>
                <li class="unordered">
Hvis du har en mindste adgangskode alder og senest har ændret adgangskode i vinduet tid, kan du ikke kan ændre adgangskoden igen, indtil den når den angivne alder på dit domæne. Til testformål, skal du vælge Minimumalderen til 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Hvis du har aktiveret kravene til adgangskoden oversigt og derefter skal du vælge en adgangskode, der ikke er blevet brugt i de sidste N tidspunkter, hvor N er indstillingen adgangskode oversigt. Hvis du markerer en adgangskode, som er blevet brugt i de seneste tidspunkter, hvor N, får derefter du vist en fejl i dette tilfælde. Til testformål, skal du vælge oversigt til 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Hvis du har kravene til adgangskoden kompleksitet, gennemtvinges alle dem, når brugeren forsøger at ændre eller nulstille en adgangskode.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Hvis du har adgangskodefiltre, der er aktiveret, og en bruger vælger en adgangskode, som ikke opfylder filterkriterierne, klik derefter på Nulstil eller ændre mislykkes handlingen.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>33009</p>
            </td>
            <td>
              <p>ADConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Denne hændelse angiver, at der opstod et problem du skriver en adgangskode tilbage til din lokale mappe på grund af et konfigurationsproblem med Active Directory. Markér den Azure AD-forbindelse maskine programmets hændelseslog for meddelelser fra tjenesten ADSync for flere oplysninger om, hvad der opstod. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34001</p>
            </td>
            <td>
              <p>ADPasswordPolicyOrPermissionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>FORÆLDET – denne hændelse er ikke til stede i Azure AD-forbindelse, kun meget tidligt opbygger af DirSync som understøttes der ikke er gemt.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34002</p>
            </td>
            <td>
              <p>ADNotReachableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>FORÆLDET – denne hændelse er ikke til stede i Azure AD-forbindelse, kun meget tidligt opbygger af DirSync som understøttes der ikke er gemt.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34003</p>
            </td>
            <td>
              <p>ADInvalidAnchorError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>FORÆLDET – denne hændelse er ikke til stede i Azure AD-forbindelse, kun meget tidligt opbygger af DirSync som understøttes der ikke er gemt.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-writeback-connectivity"></a>Fejlfinding i forbindelse med adgangskode tilbageførsel connectivity

Hvis du oplever afbrydelser af tjenester med komponenten adgangskode tilbageførsel af Azure AD-forbindelse, er her nogle hurtige trin, du kan udføre for at løse dette:

 - [Genstart Azure AD forbinde Synkroniser Service](#restart-the-azure-AD-Connect-sync-service)
 - [Deaktivere og genaktivere funktionen adgangskode tilbageførsel](#disable-and-re-enable-the-password-writeback-feature)
 - [Installere den nyeste version af Azure AD-forbindelse](#install-the-latest-azure-ad-connect-release)
 - [Fejlfinding i forbindelse med adgangskode tilbageførsel](#troubleshoot-password-writeback)

Vi anbefaler Generelt, at du udføre disse trin i den rækkefølge, der er ovenfor for at gendanne din tjeneste i den hurtigste måde.

### <a name="restart-the-azure-ad-connect-sync-service"></a>Genstart Azure AD forbinde Synkroniser Service
Genstart Azure AD forbinde synkroniseringstjenesten kan hjælpe med at løse problemer med serverforbindelsen eller andre midlertidige problemer med tjenesten.

 1. Klik på **Start** på den server, der kører **Azure AD-forbindelse**som administrator.
 2. Skriv **"services.msc"** i søgefeltet, og tryk på **Enter**.
 3. Se efter posten **Microsoft Azure AD-forbindelse** .
 4. Højreklik på posten service, klik på **Genstart**, og vent på handlingen er fuldført.

    ![][002]

Denne fremgangsmåde kan genoprette forbindelsen med skytjenesten og løse afbrydelser du oplever muligvis.  Hvis genstart tjenesten Sync ikke løser dit problem, anbefaler vi, at du forsøger at deaktivere og genaktivere funktionen adgangskode der ikke er gemt som en næste trin.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Deaktivere og genaktivere funktionen adgangskode tilbageførsel
Deaktivere og genaktivere funktionen adgangskode tilbageførsel kan hjælpe med at løse problemer med serverforbindelsen.

 1. Åbn **Azure AD-forbindelse konfigurationsguiden**som administrator.
 2. I dialogboksen **Opret forbindelse til Azure AD** skal du angive dine **Azure AD global administrator legitimationsoplysninger**
 3. Angiv dine **legitimationsoplysninger AD Domain Services**i dialogboksen **Opret forbindelse til Active Directory-Domænetjenester** .
 4. Klik på knappen **Næste** i dialogboksen **entydigt identificerer dine brugere** .
 5. Dialogboksen **valgfrie funktioner** , fjern markeringen i afkrydsningsfeltet **adgangskode tilbageskrivning** .

    ![][003]

 6. Klik på **Næste** gennem de resterende sider i dialogboksen uden at ændre noget, indtil du kommer til siden **klar til at konfigurere** .
 7. Sørg for, at Konfigurer siden viser den **adgangskode tilbageskrivning indstilling som deaktiveret** , og klik derefter på den grønne **Konfigurer** knap for at bekræfte dine ændringer.
 8. Fravælg indstillingen **Synkroniser nu** i dialogboksen **færdig** , og klik derefter på **Udfør** for at lukke guiden.
 9. Genåbnet **Azure AD-forbindelse konfigurationsguiden**.
 10.    **Gentag trin 2-8**, bortset fra at sikre, at du **markere indstillingen adgangskode tilbageskrivning** de **valgfrie funktioner** skærmbilledet for at aktivere tjenesten igen.

    ![][004]

Denne fremgangsmåde kan genoprette forbindelsen med vores skytjeneste og løse afbrydelser du oplever muligvis.

Hvis deaktivere og genaktivere funktionen adgangskode tilbageførsel ikke løser dit problem, anbefaler vi, at du forsøger at installere igen Azure AD-forbindelse som en næste trin.

### <a name="install-the-latest-azure-ad-connect-release"></a>Installere den nyeste version af Azure AD-forbindelse
Geninstallere pakken Azure AD-forbindelse vil løse eventuelle konfigurationsproblemer som kan påvirke din mulighed for at enten oprette forbindelse til vores skytjenester eller til at administrere adgangskoder i dit lokale AD-miljø.
Vi anbefaler, du udføre dette trin, når du forsøger de første to trin, der er beskrevet ovenfor.

 1. Hent den nyeste version af Azure AD-forbindelse [her](active-directory-aadconnect.md#install-azure-ad-connect).
 2. Da du allerede har installeret Azure AD-forbindelse, skal du kun til at udføre en opgradering for at opdatere installationen af Azure AD-forbindelse til den nyeste version.
 3. Udføre den hentede pakke og følge den på skærmen instruktionerne for at opdatere din computer og Azure AD-forbindelse.  Der kræves ingen ekstra manuelle trin, medmindre du har tilpasset forældet afkrydsningsfeltet Synkroniser regler, i hvilket tilfælde du skal **sikkerhedskopiere disse, før du fortsætter med opgradere og manuelt installere dem, når du er færdig med igen**.

Denne fremgangsmåde kan genoprette forbindelsen med vores skytjeneste og løse afbrydelser du oplever muligvis.

Hvis du installerer den nyeste version af Azure AD-forbindelse serveren ikke løser dit problem, anbefaler vi, du forsøger deaktivere og igen aktivering adgangskode tilbageførsel som det sidste trin, når du har installeret den seneste synkronisering QFE.

Hvis det ikke løser dit problem, derefter anbefaler vi, at du se nærmere på [Fejlfinding i forbindelse med adgangskode tilbageførsel](#troubleshoot-password-writeback) og [Azure AD adgangskode Management ofte stillede spørgsmål om](active-directory-passwords-faq.md) for at se, hvis dit problem kan skal diskuteres der.


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Links til adgangskode nulstille dokumentation
Nedenfor finder du links til alle siderne nulstilling af adgangskode til Azure AD dokumentation:

* **Er du her, fordi du har problemer med at logge på?** Hvis det er tilfældet, [her er hvordan du kan ændre og nulstille din egen adgangskode](active-directory-passwords-update-your-own-password.md).
* [**Sådan fungerer det**](active-directory-passwords-how-it-works.md) – få mere at vide om de seks forskellige komponenter i den tjeneste, og hvad hver betyder
* [**Kom godt i gang**](active-directory-passwords-getting-started.md) - Lær, hvordan du giver dig mulighed for brugerne at nulstille og ændre deres skyen eller en lokal adgangskoder
* [**Tilpas**](active-directory-passwords-customize.md) - Lær at tilpasse udseende og funktionalitet og funktionsmåde for tjenesten til din organisations behov
* [**Bedste fremgangsmåder**](active-directory-passwords-best-practices.md) - se, hvordan du installerer hurtigt og effektivt administrere adgangskoder i din organisation
* [**Få indsigt**](active-directory-passwords-get-insights.md) - få mere at vide om vores integrerede rapporteringsfunktioner
* [**Ofte stillede spørgsmål**](active-directory-passwords-faq.md) – få svar på ofte stillede spørgsmål
* [**Få mere at vide**](active-directory-passwords-learn-more.md) - gå deep til de tekniske detaljer i Sådan fungerer service



[001]: ./media/active-directory-passwords-troubleshoot/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-troubleshoot/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-troubleshoot/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-troubleshoot/004.jpg "Image_004.jpg"
