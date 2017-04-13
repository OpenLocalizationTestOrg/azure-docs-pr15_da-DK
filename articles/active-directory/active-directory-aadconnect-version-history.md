<properties
   pageTitle="Azure AD Connect: Versionshistorik for Release | Microsoft Azure"
   description="I dette emne beskrives alle versioner af Azure AD-forbindelse og Azure Active Directory-synkronisering"
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
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Versionshistorik for udgivelse

Azure Active Directory-gruppe opdaterer regelmæssigt Azure AD-forbindelse med nye funktioner og funktionalitet. Ikke alle tilføjelser gælder for alle målgrupper.

I denne artikel henvender sig til at hjælpe dig med at holde styr på de versioner, der er blevet frigivet samt beskrivelse af, om du vil opdatere til den nyeste version eller ej.

Dette er liste over relaterede emner:

Emne |  
--------- | --------- |
Trin til at opgradere fra Azure AD-forbindelse | Forskellige metoder til [opgradering fra en tidligere version til senest](active-directory-aadconnect-upgrade-previous-version.md) Azure AD Connect version.
Nødvendige tilladelser | Tilladelser, der kræves for at anvende en opdatering, skal du se [konti og tilladelser](./connect/active-directory-aadconnect-accounts-permissions.md#upgrade)
Download| [Hent Azure AD-forbindelse](http://go.microsoft.com/fwlink/?LinkId=615771)

## <a name="112810"></a>1.1.281.0
Udgivet: 2016 August

**Fast problemer:**

- Ændringer til at synkronisere interval foregår ikke indtil efter næste synkronisering cyklus er fuldført.
- Azure AD-forbindelse guiden ikke accepterer Azure AD-konto, hvis brugernavn starter med et understregningstegn (\_).
- Azure AD-forbindelse guiden kan ikke godkende Azure AD-konto, hvis kontoadgangskode indeholder for mange specialtegn. Fejlmeddelelsen "kunne ikke valideres legitimationsoplysninger. En uventet fejl." der returneres.
- Fjerne midlertidige server deaktiverer synkronisering af adgangskoder i Azure AD-lejer og får synkronisering af adgangskoder mislykkes med active server.
- Synkronisering af adgangskoder mislykkes i usædvanlige tilfælde, når der ikke er nogen adgangskodehash-værdien, der er gemt på brugeren.
- Når Azure AD-forbindelse server er aktiveret for midlertidige tilstand, er adgangskode tilbageførsel ikke midlertidigt deaktiveret.
- Azure AD-forbindelse guiden viser ikke den aktuelle adgangskodesynkronisering og adgangskode tilbageførsel konfiguration, når serveren er i arrangere tilstand. Den viser altid dem som deaktiveret.
- Ændringer i konfigurationen til synkronisering af adgangskoder og adgangskode tilbageførsel bevares ikke af Azure AD-forbindelse guide, når serveren er i arrangere tilstand.

**Forbedringer af:**

- Opdaterede Start-ADSyncSyncCycle til at angive, om det er muligt for at starte en ny synkronisering cyklus eller ej.
- Tilføjede Stop ADSyncSyncCycle til at afbryde Synkroniser cyklus og handling, som er på nuværende tidspunkt i gang.
- Opdaterede Stop ADSyncScheduler til at afbryde Synkroniser cyklus og handling, som er på nuværende tidspunkt i gang.
- Når du konfigurerer [Directory filtypenavne](active-directory-aadconnectsync-feature-directory-extensions.md) i guiden Azure AD-forbindelse, kan AD-attribut af typen "Teletex streng" nu være markerede.

## <a name="111890"></a>1.1.189.0
Udgivet: 2016 juni

**Fast problemer og forbedringer:**

- Azure AD-forbindelse kan nu installeres på en FIPS kompatibel server.
    - Synkronisering af adgangskoder under [synkronisering af adgangskoder og FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips)
- Fast et problem, hvor et NetBIOS-navn ikke blev fundet til det fulde Domænenavn i Active Directory Connector.

## <a name="111800"></a>1.1.180.0
Udgivet: 2016 maj

**Nye funktioner:**

- Advarer og du ved at bekræfte domæner, hvis du ikke før du kører Azure AD-forbindelse.
- Understøttelse af [Microsoft Cloud Tyskland](active-directory-aadconnect-instances.md#microsoft-cloud-germany)tilføjet.
- Understøttelse af den nyeste [Microsoft Azure Government cloud](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) -infrastruktur med nye URL-adressen krav tilføjet.

**Fast problemer og forbedringer:**

- Tilføjet filtrering til synkronisering regel Editor for at gøre det nemmere at finde Synkroniser regler.
- Forbedret ydeevne, når du sletter et forbindelse mellemrum.
- Fast en problemer, når det samme objekt blev både slettes og tilføjes i den samme køre (kaldet Slet/Tilføj).
- En deaktiveret Synkroniser regel vil ikke længere genaktivere inkluderet objekter og attributter på opgradering eller mappe skema Opdater.

## <a name="111300"></a>1.1.130.0
Udgivet: 2016 April

**Nye funktioner:**

- Understøttelse af flere værdier attributter til [Directory Extensions](active-directory-aadconnectsync-feature-directory-extensions.md)tilføjet.
- Understøttelse af flere konfiguration variationer til [automatisk opgradering](active-directory-aadconnect-feature-automatic-upgrade.md) skal betragtes som berettiget til opgradering tilføjet.
- Tilføjet nogle-cmdlet'er til [brugerdefineret scheduler](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Udgivet: 2016 marts

**Fast problemer:**

- Foretaget understøttes sikker standardinstallation ikke kan bruges på Windows Server 2008 (pre-R2) siden synkronisering af adgangskoder ikke på dette operativsystem.
- Opgradering fra DirSync med et brugerdefineret filter konfiguration fungerer ikke som forventet.
- Når du opgraderer til en nyere version, og der er ingen ændringer af konfigurationen, en fuld import/synkronisering skal ikke planlægges.

## <a name="111100"></a>1.1.110.0
Udgivet: 2016 februar

**Fast problemer:**

- Opgradering fra tidligere versioner fungerer ikke, hvis installationen ikke er i standardmappen **C:\Program Files** .
- Hvis du installerer og fjerne markeringen af **starte synkronisering proces..** i slutningen af installationsguiden skal køre installationsguiden igen, giver ikke planlæggeren.
- Planlæggeren fungerer ikke som forventet på servere, hvor formatet dato/klokkeslæt ikke er dansk. Det vil også blokere `Get-ADSyncScheduler` til at returnere korrekte tidspunkter.
- Hvis du har installeret en tidligere version af Azure AD-forbindelse med ADFS som indstillingen logon og opgradering, kan du køre installationsguiden igen.

## <a name="111050"></a>1.1.105.0
Udgivet: 2016 februar

**Nye funktioner:**

- [Automatisk opgradering](active-directory-aadconnect-feature-automatic-upgrade.md) funktion for Express indstillinger kunder.
- Understøttelse af den globale administrator ved hjælp af MFA og PIM i installationsguiden.
    - Skal du tillade din proxy også tillade trafik til https://secure.aadcdn.microsoftonline-p.com, hvis du bruger MFA.
    - Du skal føje https://secure.aadcdn.microsoftonline-p.com til listen over pålidelige websteder til MFA til at fungere korrekt.
- Tillad, at ændre brugerens logon metode efter indledende installation.
- Tillad [domæne og OU filtrering](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) i installationsguiden. Dette kan også oprette forbindelse til områder, hvor ikke alle domæner er tilgængelige.
- [Opgavestyring](active-directory-aadconnectsync-feature-scheduler.md) er indbygget til synkronisering af programmet.

**Funktioner, der er opgraderet fra preview til GA:**

- [Enhed tilbageførsel](active-directory-aadconnect-feature-device-writeback.md).
- [Directory filtypenavne](active-directory-aadconnectsync-feature-directory-extensions.md).

**Nye funktioner for eksempel:**

- Den nye standard synkronisere cyklus interval er 30 minutter. Bruges til at være 3 timer for alle tidligere versioner. Føjer understøttelse af for at ændre funktionsmåden [scheduler](active-directory-aadconnectsync-feature-scheduler.md) .

**Fast problemer:**

- Siden Bekræft DNS-domæner ikke altid kan genkende domænerne.
- Spørger efter domæne Administratoroplysninger, når du konfigurerer ADFS.
- Lokale AD konti genkendes ikke af installationsguiden, hvis placeret i et domæne med et andet DNS træ end roddomænet.

## <a name="1091310"></a>1.0.9131.0
Udgivet: 2015 December

**Fast problemer:**

- Synkronisering af adgangskoder fungerer muligvis ikke, når du ændrer adgangskoder i Active Directory-Domænetjenester, men fungerer, når du angiver adgangskode.
- Når du har en proxyserver, Azure AD-godkendelse mislykkes under installation eller FN opgradering på konfigurationssiden.
- Opdatering fra en tidligere version af Azure AD-forbindelse med en fuld mislykkes SQL Server, hvis du ikke er Systemadministratorens i SQL.
- Opdatering fra en tidligere version af Azure AD-forbindelse med en fjernbetjening viser SQL Server fejlen "Kan ikke få adgang til ADSync SQL-databasen".

## <a name="1091250"></a>1.0.9125.0
Udgivet: November 2015

**Nye funktioner:**

- ADFS til Azure AD tillid kan omkonfigurere.
- Kan opdatere Active Directory-skemaet og for at gendanne synkronisering regler.
- Kan deaktivere en regel for synkronisering.
- Kan definere "AuthoritativeNull" som en ny konstant i en regel for synkronisering.

**Nye funktioner for eksempel:**

- [Azure AD forbinde tilstand for synkronisering](active-directory-aadconnect-health-sync.md).
- Understøttelse af [Azure AD-domænetjenester](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) synkronisering af adgangskoder.

**Nye understøttede scenarie:**

- Understøtter flere lokale Exchange organisationer. Du kan få flere oplysninger i [hybridinstallationer med flere Active Directory-områder](https://technet.microsoft.com/library/jj873754.aspx) .

**Fast problemer:**

- Problemer med synkronisering af adgangskoder:
    - Et objekt, der flyttes fra ud af omfang i omfang vil ikke have adgangskoden synkroniseret. Denne incudes OU og attributten filtrering.
    - Vælge en ny OU medtage synkroniseret kræver ikke en fuld adgangskode synkronisering.
    - Når en deaktiveret bruger er aktiveret synkronisere adgangskoden ikke.
    - Den adgangskode forsøg kø er uendelig og den tidligere grænse på 5.000 objekter til udgå er blevet fjernet.
    - [Forbedret fejlfinding](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization).
- Kan ikke oprette forbindelse til Active Directory med Windows Server 2016 skov tværfunktionelt niveau.
- Kan ikke ændre den gruppe, der bruges til gruppen filtrering efter indledende installation.
- Vil ikke længere oprette en ny brugerprofil på serveren Azure AD-forbindelse for alle brugere at gøre en ændring af adgangskoden med adgangskode tilbageførsel aktiveret.
- Kan ikke bruge langt heltal værdier synkroniseret regler områder.
- Afkrydsningsfeltet "enhed tilbageførsel" forbliver deaktiveret, hvis der er utilgængeligt domæne enheder.

## <a name="1086670"></a>1.0.8667.0
Udgivet: 2015 August

**Nye funktioner:**

- Installationsguiden af Azure AD-forbindelse er nu oversat til alle sprog, Windows Server.
- Understøttelse af konto tilføjet låse op, når du bruger Azure AD administration af adgangskoder.

**Fast problemer:**

- Azure AD-forbindelse installationsguiden går ned, hvis en anden bruger fortsætter installation i stedet for den person, som installationen for første gang.
- Det er ikke muligt at geninstallere, hvis en tidligere fjernelse af Azure AD-forbindelse mislykkes fjerne Azure AD-forbindelse Synkroniser rent.
- Kan ikke installere Azure AD-forbindelse ved hjælp af standardinstallation, hvis brugeren ikke er i roden af domænet af området, eller hvis en ikke-engelsk version af Active Directory bruges.
- Hvis det fulde Domænenavn på Active Directory-brugerkontoen kan løses, vises en vildledende fejlmeddelelse "Mislykket at acceptere skemaet".
- Hvis den konto, der bruges på Active Directory Connector ændres uden for guiden, mislykkes guiden på efterfølgende indstillinger.
- Azure AD-forbindelse nogle gange kan ikke installere på et domænenavn fra domænecontrolleren.
- Kan ikke aktivere og deaktivere "Midlertidige tilstand", hvis filtypenavn attributter er blevet tilføjet.
- Adgangskode tilbageførsel mislykkes i nogle konfiguration på grund af en forkert adgangskode i Active Directory Connector.
- DirSync kan ikke opgraderes, hvis dn bruges i attributten filtrering.
- For mange CPU'en, når du bruger nulstilling af adgangskode.

**Fjernede preview funktioner:**

- Funktionen Vis [bruger tilbageførsel](active-directory-aadconnect-feature-preview.md#user-writeback) fjernet midlertidigt baseret på feedback fra kunderne preview. Det føjet igen senere når vi har behandlet den medfølgende feedback.

## <a name="1086410"></a>1.0.8641.0
Udgivet: 2015 juni

**Første version af Azure AD-forbindelse.**

Ændrede navnet fra Azure Active Directory-synkronisering til Azure AD-forbindelse.

**Nye funktioner:**

- [Indstillinger for](./connect/active-directory-aadconnect-get-started-express.md) standardinstallation
- Kan [konfigurere ADFS](./connect/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
- Kan [opgradere fra DirSync](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md)
- [Undgå utilsigtede sletninger](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
- Introduceret [arrangere tilstand](active-directory-aadconnectsync-operations.md#staging-mode)

**Nye funktioner for eksempel:**

- [Bruger tilbageførsel](active-directory-aadconnect-feature-preview.md#user-writeback)
- [Gruppen tilbageførsel](active-directory-aadconnect-feature-preview.md#group-writeback)
- [Enhed tilbageførsel](active-directory-aadconnect-feature-device-writeback.md)
- [Directory filtypenavne](active-directory-aadconnect-feature-preview.md#directory-extensions)


## <a name="104940501"></a>1.0.494.0501
Udgivet: 2015 maj

**Ny krav:**

- Azure Active Directory-synkronisering kræver nu .net framework version 4.5.1 installeres.

**Fast problemer:**

- Adgangskode tilbageførsel fra Azure AD går ned med en servicebus forbindelsesfejl.

## <a name="104910413"></a>1.0.491.0413
Udgivet: 2015 April

**Fast problemer og forbedringer:**

- Active Directory Connector behandler ikke sletter korrekt Hvis papirkurven er aktiveret, og der er flere domæner i området.
- Ydeevnen for importhandlinger er blevet forbedret i Azure Active Directory Connector.
- Når en gruppe har overskredet grænsen for medlemskab (som standard grænsen er indstillet til 50k objekter), gruppen er blevet slettet i Azure Active Directory. Den nye funktionsmåde er, at gruppen forbliver, er der opstod en fejl og nogen medlemskab ændringer kan ikke eksporteres.
- Et nyt objekt kan ikke klargøres, hvis en faseinddelt Slet med det samme DN findes allerede i connector-plads.
- Nogle objekter er markeret med henblik på synkroniseres under en delta synkronisering, selvom der er ingen ændring midlertidigt på objektet.
- Listen Foretrukne DC fjerner at tvinge en synkronisering af adgangskoder også.
- CSExportAnalyzer har problemer med nogle objekter stater.

**Nye funktioner:**

- En joinforbindelse kan nu oprette forbindelse til "ANY" objekttype i MV.

## <a name="104850222"></a>1.0.485.0222
Udgivet: februar 2015

**Forbedringer af:**

- Forbedret import ydeevne.

**Fast problemer:**

- Synkronisering af adgangskoder respekterer attributten cloudFiltered bruges af attribut filtrering. Filtrerede objekter er ikke længere i området for synkronisering af adgangskoder.
- I sjældne situationer, hvor topologien havde meget mange domænecontrollere, virker ikke synkronisering af adgangskoder.
- "Holdt op med at-server" Når du importerer fra Azure AD-forbindelse efter enhedsstyring er blevet aktiveret i Azure AD/Intune.
- Deltage i fremmed principper for sikkerhed (FSPs) fra flere domæner i samme skov medfører en tvetydige joinforbindelser fejl.

## <a name="104751202"></a>1.0.475.1202
Udgivet: December 2014

**Nye funktioner:**

- Det understøttes nu for at gøre synkronisering af adgangskoder med attributten baseret filtrering. Du kan finde flere detaljer, [synkronisering af adgangskoder med filtrering](active-directory-aadconnectsync-configure-filtering.md).
- Attributten msDS-ExternalDirectoryObjectID skrives tilbage til AD. Dette tilføjer support til Office 365-programmer, der bruger OAuth2 til at få adgang til begge Online og lokale postkasser i en Hybridinstallation af Exchange.

**Fast problemer med opgradering:**

- En nyere version af det Logonassistent er tilgængelige på serveren.
- En brugerdefineret installationssti blev brugt til at installere Azure Active Directory-synkronisering.
- En ugyldig brugerdefineret joinforbindelse kriterium blokerer opgraderingen.

**Andre løsninger:**

- Fast skabelonerne til Office Pro Plus.
- Fast installationsproblemer skyldes brugernavne, der starter med en bindestreg.
- Fast at miste indstillingen sourceAnchor, når du kører installationsguiden af en gang.
- Fast ETW-sporing af synkronisering af adgangskoder

## <a name="104701023"></a>1.0.470.1023
Udgivet: oktober 2014

**Nye funktioner:**

- Synkronisering af adgangskoder fra flere lokale AD til Azure AD.
- Oversat brugergrænseflade til installation til alle sprog, Windows Server.

**Opgradering fra AADSync 1.0 GA**

Hvis du allerede har Azure Active Directory-synkronisering installeret, er der et ekstra trin, du skal udføre i tilfælde af, at du har ændret én af reglerne for synkronisering out of box. Når du har opgraderet til 1.0.470.1023 frigive, synkroniseringen er dubleret regler, du har ændret. Benyt følgende fremgangsmåde for hver ændret Synkroniser regel:

- Find reglen synkronisering, du har ændret og notere ændringerne.
- Slet Synkroniser reglen.
- Find den nye synkronisering regel, der er oprettet af Azure Active Directory-synkronisering, og Anvend ændringerne igen.

**Tilladelserne for kontoen AD**

AD-kontoen skal tildeles yderligere tilladelser for at kunne læse de adgangskode hashes fra AD. Tilladelser til at give hedder "Replikering Directory ændringer" og "Replikering Directory ændringer alle". Begge tilladelser der kræves for at kunne læse de adgangskode hashes

## <a name="104190911"></a>1.0.419.0911
Udgivet: September 2014

**Første version af Azure Active Directory-synkronisering.**

## <a name="next-steps"></a>Næste trin
Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
