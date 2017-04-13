<properties
    pageTitle="Få mere at vide: Administration af Azure AD-adgangskoder | Microsoft Azure"
    description="Avancerede emner om administration af Azure AD adgangskoder, herunder hvordan adgangskode tilbageførsel virker, adgangskode tilbageførsel sikkerhed, hvordan communityportalen fungerer nulstilling af adgangskode, og hvilke data der bruges af nulstilling af adgangskode."
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
    ms.date="07/12/2016"
    ms.author="asteen"/>

# <a name="learn-more-about-password-management"></a>Få flere oplysninger om administration af adgangskoder

> [AZURE.IMPORTANT] **Er du her, fordi du har problemer med at logge på?** Hvis det er tilfældet, [her er hvordan du kan ændre og nulstille din egen adgangskode](active-directory-passwords-update-your-own-password.md).

Hvis du allerede har installeret administration af adgangskoder, eller er blot vil du få mere at vide om de tekniske detaljer med af, hvordan det fungerer, før du anvender i dette afsnit kan give dig en god oversigt over de tekniske begreber bag tjenesten. Vi vil gennemgå følgende:

* [**Oversigt over adgangskode tilbageførsel**](#password-writeback-overview)
  - [Hvordan fungerer adgangskode tilbageførsel](#how-password-writeback-works)
  - [Scenarier, der understøttes i forbindelse med adgangskode tilbageførsel](#scenarios-supported-for-password-writeback)
  - [Adgangskode tilbageførsel sikkerhedsmodel](#password-writeback-security-model)
* [**Hvordan nulstille adgangskoden portalen arbejde?**](#how-does-the-password-reset-portal-work)
  - [Hvilke data der bruges af nulstilling af adgangskode?](#what-data-is-used-by-password-reset)
  - [Sådan får du adgang adgangskode nulstille data for dine brugere](#how-to-access-password-reset-data-for-your-users)

## <a name="password-writeback-overview"></a>Oversigt over adgangskode tilbageførsel
Adgangskode tilbageførsel er en [Azure Active Directory forbinde](active-directory-aadconnect.md) komponent, der kan aktiveres og bruges af de aktuelle abonnenter af Azure Active Directory Premium. Du kan finde yderligere oplysninger finder [Azure Active Directory-versioner](active-directory-editions.md).

Adgangskode tilbageførsel kan du konfigurere din skyen lejer for at skrive adgangskoder tilbage til dig lokale Active Directory.  Den ikke opstår du ikke behøvede at konfigurere og administrere en komplicerede lokalt selvbetjening Nulstil løsning, og det er en praktisk metode skybaseret for brugerne at nulstille adgangskoder deres lokale, uanset hvor de befinder dig.  Læs videre for nogle af de vigtigste funktioner i adgangskode tilbageførsel:

- **Nul forsinkelse feedback.**  Adgangskode tilbageførsel er en synkron handling.  Dine brugere får besked med det samme, hvis deres adgangskode ikke opfylder politik eller kunne ikke nulstille eller ændres af andre årsager.
- **Understøtter nulstilling af adgangskoder for brugere ved hjælp af AD FS eller andre teknologier til sammenslutning.**  Med adgangskode tilbageførsel, så længe samlet brugerkontiene synkroniseres på din Azure AD-lejer, de vil kunne administrere deres lokale AD adgangskoder fra skyen.
- **Understøtter nulstilling af adgangskoder for brugere ved hjælp af synkronisering af adgangskoder hash.** Når tjenesten adgangskode Nulstil registrerer, at en synkroniseret brugerkonto er aktiveret til synkronisering af adgangskoder hash, Nulstil vi både denne konto i det lokale miljø og skyen adgangskode samtidigt.
- **Understøttelse af ændring adgangskoder fra access panel og Office 365.**  Når organisationsnetværk eller synkronisering af adgangskoder ville brugere kommer til at ændre deres udløbet eller ikke udløbet adgangskoder, vi vil skrive disse adgangskoder tilbage til dit lokale AD-miljø.
- **Understøtter skrivning adgangskoder tilbage, når en administrator nulstiller dem fra den** [**Azure Management Portal**](https://manage.windowsazure.com).  Når en administrator nulstiller, ville en brugers adgangskode i [Azure Management Portal](https://manage.windowsazure.com), hvis brugeren er knyttet eller synkronisering af adgangskoder, skal vi konfigurere adgangskoden administratoren vælger på din lokale AD, samt.  Dette understøttes ikke i øjeblikket i administrationsportalen til Office.
- **Gennemtvinger dine lokale AD adgangskodepolitikker.**  Når en bruger nulstiller sit adgangskode, vi skal du kontrollere, at det opfylder dine lokale AD-politik før du gemmer den til den pågældende mappe.  Dette omfatter historik, kompleksitet, alder, adgangskodefiltre og andre adgangskodebegrænsninger, du har defineret i din lokale AD.
- **Kræver ikke en hvilken som helst indgående firewallregler.**  Adgangskode tilbageførsel bruger en Azure Service Bus relay som en underliggende kommunikationskanal, hvilket betyder, at du ikke behøver at åbne en hvilken som helst indgående porte i firewallen for denne funktion til at arbejde.
- **Understøttes ikke af brugerkonti, der findes i beskyttet grupper i din lokale Active Directory.** Du kan finde flere oplysninger om beskyttet grupper se [beskyttede konti og grupper i Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

### <a name="how-password-writeback-works"></a>Hvordan fungerer adgangskode tilbageførsel
Adgangskode tilbageførsel består af tre primære komponenter:

- Adgangskode Nulstil skybaseret tjeneste (dette er også integreret i Azure AD adgangskode ændre sider)
- Lejer-specifikke Azure Service Bus relay
- Nulstilling af adgangskode til lokalt slutpunkt

De passer sammen, som beskrevet i de under diagram:

  ![][001]

Når en samlet eller adgangskode hash synkroniserer ville bruger drejer sig om at nulstille eller ændre sin adgangskode i skyen, sker der følgende:

1.  Vi se for at se, hvilken type adgangskode brugeren har.  Hvis vi se adgangskoden administreres lokalt, derefter sikre vi tjenesten tilbageførsel er oppe at køre.  Hvis det er, vi lade brugeren fortsætter, hvis det ikke er, vi fortæller brugeren, der kan ikke nulstille adgangskoden for her.
2.  Derefter overfører relevante godkendelse gates brugeren, og når skærmbilledet Nulstil adgangskode.
3.  Brugeren vælger en ny adgangskode og bekræfter den.
4.  Hvis du klikker på Send, kryptere vi adgangskoden almindelig tekst med en symmetriske nøgle, der blev oprettet under konfigurationen af tilbageførsel.
5.  Når du krypterer adgangskoden, medtage vi dem i en data, der bliver sendt over en HTTPS-kanal til din lejer bestemt service bus relay (som vi også konfigurere for dig under konfigurationen af tilbageførsel).  Denne relay er beskyttet af en tilfældigt adgangskode, du kender din lokale installation.
6.  Når meddelelsen når tjenesten bus, aktiveres adgangskode Nulstil slutpunktet automatisk, og ser, at det har en nulstillingsanmodning afventer.
7.  Tjenesten søger derefter efter brugeren pågældende ved hjælp af attributten skyen anker.  For dette opslag kan fuldføres, brugerobjektet skal findes i AD connector-plads, den skal være knyttet til det tilsvarende MV objekt og det skal være knyttet til det tilsvarende AAD forbindelse objekt. Endelig, hvis synkronisering for at finde denne brugerkonto, skal linket fra AD connector-objektet til MV have Synkroniser reglen `Microsoft.InfromADUserAccountEnabled.xxx` på linket.  Dette da når der opkaldet kommer fra skyen, Synkroniser program bruger attributten cloudAnchor til at finde objektet AAD forbindelse mellemrum og derefter følger linket tilbage til objektet MV og derefter følger linket tilbage til objektet AD. Da der kan være flere AD objekter (med flere områder) for samme bruger, Synkroniser program afhængig af den `Microsoft.InfromADUserAccountEnabled.xxx` link til at vælge det rigtige arbejdsområde.
8.  Når kontoen er fundet, forsøger vi at nulstille adgangskoden direkte i den relevante AD skov.
9.  Hvis handlingen adgangskode sæt er gået igennem, fortæller vi bruger adgangskoden er blevet ændret, og at de kan gå på deres Glædelig måde.
10. Hvis adgangskoden angive handlingen mislykkes, skal vi returnere fejlen til brugeren og lade dem om at prøve igen.  Handlingen kan mislykkes, fordi tjenesten blev ned, fordi de markeret adgangskoden ikke opfylder organisation politikker, fordi vi ikke kunne finde brugeren i den lokale AD eller en række årsager.  Vi har en bestemt meddelelse for mange af disse tilfælde og fortælle brugeren, hvad brugeren kan gøre for at løse problemet.

### <a name="scenarios-supported-for-password-writeback"></a>Scenarier, der understøttes i forbindelse med adgangskode tilbageførsel
Tabellen nedenfor beskrives hvilke scenarier understøttes for hvilke versioner af vores synkroniseringsmuligheder.  Det anbefales generelt, at du installerer den nyeste version af [Azure AD-forbindelse](active-directory-aadconnect.md#install-azure-ad-connect) , hvis du vil bruge adgangskode tilbageførsel.

  ![][002]

### <a name="password-writeback-security-model"></a>Adgangskode tilbageførsel sikkerhedsmodel
Adgangskode tilbageførsel er en meget sikker og effektiv tjeneste.  For at sikre, at dine oplysninger er beskyttet, kan vi en 4-lagdelt sikkerhedsmodel, der er beskrevet nedenfor.

- **Lejer bestemte service-bus relay** – når du konfigurerer tjenesten, vi oprette en lejer-specifikke service bus relay, der er beskyttet af en tilfældigt stærk adgangskode, som Microsoft aldrig har adgang til.
- **Låst ned kryptografisk stærk adgangskode krypteringsnøgle** – når tjenesten bus relay er blevet oprettet, vi oprette en stærk symmetriske nøgle, som vi bruger til at kryptere adgangskoden, når den dukker over en netværksforbindelse.  Denne tast findes kun i din virksomhed hemmeligt store i skyen, som er meget låst og overvåges, ligesom enhver adgangskode i kataloget.
- **Branche standard TLS** – når en adgangskode nulstille eller ændre handlingen forekommer i skyen, kan vi tage almindelig tekst adgangskoden kryptere den med dine offentlige nøgle.  Vi derefter plop, i en HTTPS-meddelelse, sendes via en krypteret kanal ved hjælp af Microsofts SSL-certifikater til din tjeneste bus relay.  Når meddelelsen ankommer til tjenesten Bus, din lokalt agent aktiveres, godkendes af Service Bus ved hjælp af stærk adgangskode, der har oprettet tidligere, opfanger den krypterede meddelelse, dekrypteres den med den private nøgle, vi oprettede, og forsøg for at angive adgangskoden gennem AD DS SetPassword API.  Dette trin er, hvad giver os mulighed at gennemtvinge din AD lokalt adgangskodepolitik (kompleksitet, alder, historik, filtre osv.) i skyen.
- **Meddelelse udløbspolitikker for** – endelig, hvis eller anden grund meddelelsen er placeret i Service Bus fordi din lokalt tjeneste er nede, den fik timeout og fjernet efter nogle minutter for at øge sikkerheden yderligere.

## <a name="how-does-the-password-reset-portal-work"></a>Hvordan nulstille adgangskoden portalen arbejde?
Når en bruger navigerer til portalen til nulstilling af adgangskode, er startet en arbejdsproces til at afgøre, om denne brugerkonto er gyldige, hvilke organisation, som brugerne tilhører, hvor denne brugers adgangskode administreres, og om brugeren har licens til at bruge funktionen eller ej.  Læs gennem trinnene nedenfor for at få mere at vide om logik bag siden til nulstilling af adgangskoden.

1.  Brugeren klikker på den kan ikke få adgang til din konto eller går direkte til [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com).
2.  Brugeren skriver et bruger-id og sender en captcha.
3.  Azure AD kontrollerer, om brugeren er kan du bruge denne funktion ved at gøre følgende:
    - Kontrollerer, at brugeren har denne funktion er aktiveret og en tildelt Azure AD-licens.
        - Hvis brugeren ikke har denne funktion er aktiveret eller tildelt en licens, er brugeren bedt om at kontakte sin administrator for at nulstille sin adgangskode.
    - Kontroller, at brugeren har højre udfordring data, der er defineret i hans eller hendes konto i overensstemmelse med administratorpolitik.
        - Hvis politikken kræver, at kun én udfordring, derefter sikres det, at brugeren har de korrekte data, der er defineret for mindst én af de udfordringer, der er aktiveret af politikken administrator.
          - Hvis brugeren ikke er konfigureret, er brugeren anbefales at kontakte sin administrator for at nulstille sin adgangskode.
        - Hvis politikken kræver to udfordringer, derefter sikres det, at brugeren har de korrekte data, der er defineret for mindst to af de udfordringer, der er aktiveret af politikken administrator.
          - Hvis brugeren ikke er konfigureret, er vi brugeren anbefales at kontakte sin administrator for at nulstille sin adgangskode.
    - Kontrollerer, om brugerens adgangskode administreres lokalt (samlet eller synkronisering af adgangskoder hash havde).
       - Hvis der ikke er gemt installeres og brugerens adgangskode administreres lokalt, har brugeren tilladelse til at fortsætte med at godkende og nulstille sin adgangskode.
       - Hvis der ikke er gemt ikke er installeret og brugerens adgangskode administreres lokalt, er brugeren stillede kontakte sin administrator for at nulstille sin adgangskode.
4.  Hvis det bestemmes, at brugeren er kunne nulstillet sin adgangskode, er brugeren vejledt igennem nulstillingsprocessen.

Få mere at vide mere om, hvordan du installerer adgangskode tilbageførsel på [Introduktion: Administration af Azure AD adgangskoder](active-directory-passwords-getting-started.md).

### <a name="what-data-is-used-by-password-reset"></a>Hvilke data der bruges af nulstilling af adgangskode?
De følgende tabel kan du dispositioner, hvor og hvordan disse data og bruges under nulstilling af adgangskode og er udviklet til at hjælpe dig med at beslutte, hvilke indstillinger for godkendelse er relevante for din organisation. I denne tabel vises også en hvilken som helst formatering krav i de tilfælde hvor du har angivet data på vegne af brugere fra input stier, der ikke validerer disse data.

> [AZURE.NOTE] Arbejdstelefon vises ikke i portalen registrering, fordi brugere ikke er i øjeblikket kan redigere denne egenskab i kataloget.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Kontaktmetode navn</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Azure Active Directory dataelement</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Anvendte / angives hvor?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Formatér krav</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Arbejdstelefon</p>
            </td>
            <td>
              <p>PhoneNumber</p>
              <p>FX sæt-MsolUser - UserPrincipalName JWarner@contoso.com - PhoneNumber "+ 1 1234567890 x 1234"</p>
            </td>
            <td>
              <p>Bruges i:</p>
              <p>Portal til nulstilling af adgangskode</p>
              <p>Angives fra:</p>
              <p>PhoneNumber er angives fra PowerShell, DirSync, Azure Management Portal og Office-administrationsportalen</p>
            </td>
            <td>
              <p>+ ccc xxxyyyzzzz (fx + 1 1234567890)</p>
              <ul>
                <li class="unordered">
Skal angive en landekode<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Skal angive et områdenummer (hvis relevant)<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Skal har give en + foran i landet kode<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Skal have et mellemrum mellem landekode og resten af antallet<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Filtypenavne understøttes ikke, hvis du har en hvilken som helst angivne filtypenavne, vi vil derpå fjerne det fra antallet før afsendelse af telefonopkald.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Mobiltelefon</p>
            </td>
            <td>
              <p>AuthenticationPhone</p>
              <p>ELLER</p>
              <p>MobilePhone</p>
              <p>(Godkendelse telefon bruges, hvis der er data findes, ellers dette går tilbage til feltet mobiltelefon).</p>
              <p>FX sæt-MsolUser - UserPrincipalName JWarner@contoso.com - MobilePhone "+ 1 1234567890 x 1234"</p>
            </td>
            <td>
              <p>Bruges i:</p>
              <p>Portal til nulstilling af adgangskode</p>
              <p>Registrering Portal</p>
              <p>Angives fra: </p>
              <p>AuthenticationPhone er angives fra adgangskode Nulstil registrering portal eller MFA registrering portal.</p>
              <p>MobilePhone er angives fra PowerShell, DirSync, Azure Management Portal og Office-administrationsportalen</p>
            </td>
            <td>
              <p>+ ccc xxxyyyzzzz (fx + 1 1234567890)</p>
              <ul>
                <li class="unordered">
Skal angive en landekode.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Skal angive et områdenummer, (hvis relevant).<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Skal have giver en + foran i landet kode.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Skal have et mellemrum mellem landekode og resten af antallet.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Filtypenavne understøttes ikke, hvis du har en hvilken som helst angivne filtypenavne, vi ignorere den ved afsendelse opkaldet.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Alternative mail</p>
            </td>
            <td>
              <p>AuthenticationEmail</p>
              <p>ELLER</p>
              <p>AlternateEmailAddresses [0] </p>
              <p>(Godkendelse mail bruges, hvis der er data findes, ellers dette går tilbage til feltet alternativ mail).</p>
              <p>Bemærk: feltet alternativ mail er angivet som en matrix af strenge i kataloget.  Vi bruger den første post i denne matrix.</p>
              <p>FX sæt-MsolUser - UserPrincipalName JWarner@contoso.com - AlternateEmailAddresses"email@live.com"</p>
            </td>
            <td>
              <p>Bruges i:</p>
              <p>Portal til nulstilling af adgangskode</p>
              <p>Registrering Portal</p>
              <p>Angives fra: </p>
              <p>AuthenticationEmail er angives fra adgangskode Nulstil registrering portal eller MFA registrering portal.</p>
              <p>AlternateEmail er angives fra PowerShell, portalen Azure administration og Office-administrationsportalen</p>
            </td>
            <td>
              <p>
                <a href="mailto:user@domain.com">user@domain.com</a>eller甲斐@黒川.日本</p>
              <ul>
                <li class="unordered">
Mails skal følge standardformatering som per.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Unicode mails understøttes.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Sikkerhedsspørgsmål og svar</p>
            </td>
            <td>
              <p>Ikke tilgængelig til at ændre direkte i kataloget.</p>
            </td>
            <td>
              <p>Bruges i:</p>
              <p>Portal til nulstilling af adgangskode</p>
              <p>Registrering Portal </p>
              <p>Angives fra: </p>
              <p>Den eneste måde til at angive spørgsmål om sikkerhed er via Azure Management-portalen.</p>
              <p>Den eneste måde til at angive svar på spørgsmål om sikkerhed for en given bruger er via portalen registrering.</p>
            </td>
            <td>
              <p>Spørgsmål om sikkerhed har Maks 200 tegn og en min 3 tegn</p>
              <p>Svar har Maks 40 tegn og en min 3 tegn</p>
            </td>
          </tr>
        </tbody></table>

###<a name="how-to-access-password-reset-data-for-your-users"></a>Sådan får du adgang adgangskode nulstille data for dine brugere
####<a name="data-settable-via-synchronization"></a>Data, der angives via synkronisering
Følgende felter kan synkroniseres fra det lokale:

* Mobiltelefon
* Arbejdstelefon

####<a name="data-settable-with-azure-ad-powershell"></a>Data, der angives med Azure AD PowerShell
Følgende felter er tilgængeligt med Azure AD PowerShell og API'EN Graph:

* Alternative mail
* Mobiltelefon
* Arbejdstelefon
* Godkendelse telefon
* Godkendelse af mail

####<a name="data-settable-with-registration-ui-only"></a>Data, der angives med registrering af Brugergrænsefladen kun
Følgende felter er kun tilgængelig via SSPR registrering Brugergrænsefladen (https://aka.ms/ssprsetup):

* Sikkerhedsspørgsmål og svar

####<a name="what-happens-when-a-user-registers"></a>Hvad sker der, når en bruger registrerer?
Når en bruger registrerer, vil registreringssiden **altid** angive følgende felter:

* Godkendelse telefon
* Godkendelse af mail
* Sikkerhedsspørgsmål og svar

Hvis du har angivet en værdi for **mobiltelefon** eller **Alternative mail**, kan brugere straks bruge dem til at nulstille deres adgangskoder, selvom de ikke har registreret til tjenesten.  Brugere kan desuden se disse værdier, når registrering for første gang, og ændre dem, hvis de ønsker.  Når de er blevet registreret, bevares disse værdier i felterne **Godkendelse telefon** og **Godkendelse mail** henholdsvis.

Det kan være en god måde at fjerne blokeringen af stort antal brugere at bruge SSPR samtidig med at brugere til at validere disse oplysninger via registreringsprocessen.

####<a name="setting-password-reset-data-with-powershell"></a>Angivelse af adgangskode nulstille data med PowerShell
Du kan angive værdier for følgende felter med Azure AD PowerShell.

* Alternative mail
* Mobiltelefon
* Arbejdstelefon

For at komme i gang skal skal du først [du hente og installere Azure AD PowerShell-modulet](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule).  Når du har installeret den, kan du følge nedenstående trin for at konfigurere hvert felt.

#####<a name="alternate-email"></a>Alternative mail
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
```

#####<a name="mobile-phone"></a>Mobiltelefon
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
```

#####<a name="office-phone"></a>Arbejdstelefon
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"
```

####<a name="reading-password-reset-data-with-powershell"></a>Data med PowerShell til nulstilling af adgangskode for læsning
Du kan læse værdier for følgende felter med Azure AD PowerShell.

* Alternative mail
* Mobiltelefon
* Arbejdstelefon
* Godkendelse telefon
* Godkendelse af mail

For at komme i gang skal skal du først [du hente og installere Azure AD PowerShell-modulet](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule).  Når du har installeret den, kan du følge nedenstående trin for at konfigurere hvert felt.

#####<a name="alternate-email"></a>Alternative mail
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
```

#####<a name="mobile-phone"></a>Mobiltelefon
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
```

#####<a name="office-phone"></a>Arbejdstelefon
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber
```

#####<a name="authentication-phone"></a>Godkendelse telefon
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
```

#####<a name="authentication-email"></a>Godkendelse af mail
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

## <a name="links-to-password-reset-documentation"></a>Links til adgangskode nulstille dokumentation
Nedenfor finder du links til alle siderne nulstilling af adgangskode til Azure AD dokumentation:

* **Er du her, fordi du har problemer med at logge på?** Hvis det er tilfældet, [her er hvordan du kan ændre og nulstille din egen adgangskode](active-directory-passwords-update-your-own-password.md).
* [**Sådan fungerer det**](active-directory-passwords-how-it-works.md) – få mere at vide om de seks forskellige komponenter i den tjeneste, og hvad hver betyder
* [**Kom godt i gang**](active-directory-passwords-getting-started.md) - Lær, hvordan du giver dig mulighed for brugerne at nulstille og ændre deres skyen eller en lokal adgangskoder
* [**Tilpas**](active-directory-passwords-customize.md) - Lær at tilpasse udseende og funktionalitet og funktionsmåde for tjenesten til din organisations behov
* [**Bedste fremgangsmåder**](active-directory-passwords-best-practices.md) - se, hvordan du installerer hurtigt og effektivt administrere adgangskoder i din organisation
* [**Få indsigt**](active-directory-passwords-get-insights.md) - få mere at vide om vores integrerede rapporteringsfunktioner
* [**Ofte stillede spørgsmål**](active-directory-passwords-faq.md) – få svar på ofte stillede spørgsmål
* [**Fejlfinding i forbindelse med**](active-directory-passwords-troubleshoot.md) – Lær, hvordan du hurtigt foretage fejlfinding af problemer med tjenesten



[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"
