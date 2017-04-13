<properties
   pageTitle="Azure AD-forbindelse: Opgraderer fra en tidligere version | Microsoft Azure"
   description="Beskriver de forskellige metoder til at opgradere til den nyeste version af Azure Active Directory Connect, herunder opgradering og udslag overførsel."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="10/12/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD-forbindelse: Opgradering fra en tidligere version til senest
Dette emne beskrives de forskellige metoder, du kan bruge til at opgradere installationen af Azure AD-forbindelse til den nyeste version. Vi anbefaler, at du holder dig opdateret med versioner af Azure AD-forbindelse. De trin, der er beskrevet i [komme overførsel](#swing-migration) bruges også, når du foretager væsentlige konfiguration.

Hvis du vil opgradere fra DirSync, skal du se [opgradere fra Azure AD-Synkroniseringsværktøj (DirSync)](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md) i stedet.

Der findes et par forskellige metoder til at opgradere Azure AD-forbindelse.

Metode | Beskrivelse
--- | ---
[Automatisk opgradering](active-directory-aadconnect-feature-automatic-upgrade.md) | Dette er den nemmeste metode til kunder med en hurtig installation.
[Opgradering](#in-place-upgrade) | Hvis du har en enkelt server, Opgrader installation lokalt på den samme server.
[Komme overførsel](#swing-migration) | Du kan forberede dig på en af serverne med den nye version eller konfiguration og ændre active server, når du er klar med to-servere.

Se [tilladelser, der kræves til opgradering](./connect/active-directory-aadconnect-accounts-permissions.md#upgrade)til nødvendige tilladelser.

## <a name="in-place-upgrade"></a>Opgradering
En lokal opgradering fungerer til at flytte fra Azure Active Directory-synkronisering eller Azure AD-forbindelse. Det fungerer ikke for DirSync eller for en løsning med FIM + Azure AD-forbindelse.

Denne metode er foretrukne, når du har en enkelt server og mindre end cirka 100.000 objekter. Hvis der er ændringer til out of box Synkroniser regler, sker en fuld import og fuld synkronisering efter opgraderingen. Dette sikrer, at den nye konfiguration anvendes på alle eksisterende objekter i systemet. Det kan tage et par timer afhængigt af antallet af objekter i omfanget af programmet til synkronisering. Normal delta synkronisering scheduler, som standard hver 30 minutter afbrydes midlertidigt, men fortsætter med synkronisering af adgangskoder. Du kan overveje for at gøre opgradering direkte i en weekend. Hvis der er ingen ændringer af out of box konfigurationen med den nye version af Azure AD-forbindelse, starter en normal delta import/synkronisering, i stedet.  
![Opgradering](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Hvis du har foretaget ændringer til out of box synkronisering regler, vil disse angives tilbage til standardkonfiguration på opgraderingen. Kontrollér, at ændringerne, der foretages som beskrevet i [bedste fremgangsmåder til at ændre standardkonfigurationen](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)for at sikre, at din konfiguration der bevares mellem opgraderinger.

## <a name="swing-migration"></a>Komme overførsel
Hvis du har en kompleks installation eller meget mange objekter, kan det være upraktisk at gøre en opgradering på live systemet. Dette kan for nogle kunder tage flere dage, og i dette tidsrum nogen delta ændringer behandles. Denne metode bruges også, når du planlægger at foretage væsentlige ændringer af konfigurationen, og du vil afprøve dem, før disse publiceres til skyen.

Den anbefalede metode til disse scenarier er at bruge en udslag migrering. Du har brug for (mindst) to servere, én aktiv og en midlertidig server. Den aktive server (blå streger i billedet nedenfor) er ansvarlig for den aktive fremstilling belastning. Midlertidige serveren (stiplede lilla linjer i billedet nedenfor) er forberedt med den nye version eller konfiguration, og når du er fuldt klar denne server foretages aktive. Den forrige aktive server nu med den gamle version eller konfiguration, der er installeret, udføres midlertidige serveren og opgraderet.

De to servere kan bruge forskellige versioner. For eksempel den aktive server, du planlægger at afvikle kan bruge Azure Active Directory-synkronisering og den nye midlertidige server kan bruge Azure AD-forbindelse. Hvis du bruger udslag overførsel til at udvikle en ny konfiguration er det en god ide at har de samme versioner på de to servere.  
![Midlertidige server](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

Bemærk: Det er konstateret, at nogle kunder foretrækker at har tre eller fire servere til dette scenario. Når den midlertidige server er opgraderet, skal har du ikke en server til sikkerhedskopiering i tilfælde af [nedbrud](active-directory-aadconnectsync-operations.md#disaster-recovery). Med tre eller fire servere, kan være forberedt ét sæt af primær/standby servere med den nye version, sikre, at der er altid en midlertidig server, der er klar til at tage over.

Disse trin virker også, hvis du vil flytte fra Azure Active Directory-synkronisering eller en løsning med FIM + Azure AD-forbindelse. Disse trin fungerer ikke for DirSync, men den samme overførsel (også kaldet parallelle installation) slagteknik med trin til DirSync kan findes i [Opgradere Azure Active Directory synkroniserer (DirSync)](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md).

### <a name="swing-migration-steps"></a>Komme overførselstrin

1. Hvis du bruger Azure AD-forbindelse på begge servere og vil kun gøre en konfigurationsændring af, Sørg for, at din active server og arrangere server begge bruger den samme version. Det gør det nemmere at sammenligne forskelle senere. Hvis du opgraderer fra Azure Active Directory-synkronisering, har følgende servere forskellige versioner. Hvis du opgraderer fra en ældre version af Azure AD-forbindelse, er det en god ide at starte med de to servere, der bruger den samme version, men det er ikke påkrævet.
2. Hvis du har foretaget nogle brugerdefineret konfiguration og midlertidige serveren ikke har det, skal du følge trinnene under [Flyt brugerdefineret konfiguration fra aktiv til arrangering server](#move-custom-configuration-from-active-to-staging-server).
3. Hvis du opgraderer fra en tidligere version af Azure AD-forbindelse, kan du opgradere midlertidige serveren til den nyeste version. Hvis du vil flytte fra Azure Active Directory-synkronisering, skal du installere Azure AD-forbindelse på midlertidige-serveren.
4. Lade Synkroniser programmet Kør fuldstændig import og fuld synkronisering af dine midlertidige server.
5. Bekræft, at den nye konfiguration ikke medføre uventede ændringer ved hjælp af trinnene under **Bekræft** i [Bekræft konfigurationen af en server](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Hvis noget er ikke som forventet, korrekt, skal du køre importere og synkronisere, og Bekræft, indtil data, der ser pæn ud. Disse trin kan findes i emnet med den sammenkædede.
6. Skift midlertidige serveren for at være aktive server. Dette er det sidste trin **skiftes active server** i [Bekræft konfigurationen af en server](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Hvis du opgraderer Azure AD-forbindelse, skal du opgradere serveren nu i arrangere tilstand til den nyeste version. Følg de samme trin som før for at komme dataene og konfigurationen opgraderet. Hvis du har opgraderet fra Azure Active Directory-synkronisering, kan du nu deaktivere og afvikler din gamle server.

### <a name="move-custom-configuration-from-active-to-staging-server"></a>Flytte brugerdefineret konfiguration fra aktiv til arrangering server
Hvis du har foretaget ændringer i konfigurationen til den aktive server, skal du kontrollere, at de samme ændringer er anvendt til arrangering serveren.

De brugerdefinerede Synkroniser regler, du har oprettet, kan flyttes med PowerShell. Andre ændringer skal anvendes på samme måde på begge systemer og kan ikke overføres.

Ting, skal du sikre dig, er konfigureret på samme måde på begge servere:

- Forbindelse til samme områder.
- Et domæne og OU filtrering.
- De samme valgfrie funktioner, som synkronisering af adgangskoder og adgangskode der ikke er gemt.

**Flyt synkronisering regler**  
Hvis du vil flytte en regel for brugerdefineret synkronisering, skal du gøre følgende:

1. Åbn **Synkronisering regler Editor** på din active server.
2. Vælg Brugerdefineret reglen. Klik på **Eksporter**. Dette viser et Notesblok-vindue. Gemme den midlertidige fil med filtypenavnet PS1. Det gør det et PowerShell-script. Kopier filen ps1 til arrangering-serveren.  
![Synkroniser regel Eksportér](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. Forbindelse GUID er anderledes ud på den server, der er midlertidige og skal ændres. Starte **Synkronisering regler Editor**for at få GUID, Vælg en af de out of box regler, der repræsenterer det samme tilsluttede system, og klik på **Eksporter**. Erstat GUID i PS1 filen med GUID fra midlertidige serveren.
4. Kør filen PS1 i en PowerShell-prompt. Dette vil oprette reglen for brugerdefineret synkronisering på midlertidige serveren.
5. Hvis du har flere brugerdefinerede regler, Gentag for alle brugerdefinerede regler.

## <a name="next-steps"></a>Næste trin
Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
