<properties
   pageTitle="Azure AD-forbindelse: Konti og tilladelser | Microsoft Azure"
   description="Dette emne beskrives de konti, der bruges og oprettet og tilladelser, der kræves."
   services="active-directory"
   documentationCenter=""
   authors="billmath"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/04/2016"
   ms.author="billmath"/>


# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD-forbindelse: Konti og tilladelser
Installationsguiden af Azure AD-forbindelse indeholder to forskellige stier:

- I indstillingerne for Express kræver guiden flere rettigheder, så den kan konfigurere din konfiguration nemt, uden at du skal oprette brugere eller konfigurere tilladelser separat.

- Guiden giver dig flere valgmuligheder og indstillinger i brugerdefinerede indstillinger, men der er nogle situationer, hvor du vil sikre, at du har de rette tilladelser.

## <a name="related-documentation"></a>Relateret dokumentation
Hvis du ikke har læst i dokumentationen til [integrering af dine lokale identiteter med Azure Active Directory](../active-directory-aadconnect.md), indeholder den følgende tabel links til relaterede emner.

Emne |  
--------- | ---------
Installere ved hjælp af indstillinger for Express | [Hurtig installation af Azure AD-forbindelse](active-directory-aadconnect-get-started-express.md)
Installere ved hjælp af brugerdefinerede indstillinger | [Brugerdefineret installation af Azure AD-forbindelse](active-directory-aadconnect-get-started-custom.md)
Opgradering fra DirSync | [Opgradere fra Azure AD-Synkroniseringsværktøj (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)


## <a name="express-settings-installation"></a>Indstillinger for standardinstallation
I indstillingerne for Express beder installationsguiden om AD DS Enterprise Administratoroplysninger, så dit lokale Active Directory kan konfigureres med nødvendige tilladelser til Azure AD-forbindelse. Hvis du opgraderer fra DirSync, bruges AD DS virksomhedsadministratorer legitimationsoplysningerne til at nulstille adgangskoden for den konto, der bruges af DirSync. Du skal også Azure AD Global Administrator legitimationsoplysninger.

Side i guiden  | Legitimationsoplysninger, der indsamles | Tilladelser, der kræves| Bruges til
------------- | ------------- |------------- |-------------
I/T.|Bruger, der kører installationsguiden| Administratoren af den lokale server| <li>Opretter den lokale konto, der bruges som [Synkroniser program tjenestekonto](#azure-ad-connect-sync-service-account).
Oprette forbindelse til Azure AD| Azure AD directory legitimationsoplysninger | Globale administratorrolle i Azure AD | <li>Aktivere synkronisering i mappen Azure AD.</li>  <li>Oprettelse af [Azure AD-konto](#azure-ad-service-account) , der bruges til løbende synkronisering handlinger i Azure AD.</li>
Oprette forbindelse til Active Directory-Domænetjenester | Legitimationsoplysningerne for lokale Active Directory | Medlem af gruppen Enterprise administratorer (EA) i Active Directory| <li>Opretter en [konto](#active-directory-account) i Active Directory og tildeler tilladelser til den. Dette oprettet konto bruges til at læse og skrive mappeoplysninger under synkronisering.</li>

### <a name="enterprise-admin-credentials"></a>Enterprise Administratoroplysninger
Disse legitimationsoplysninger anvendes kun under installationen og anvendes, når installationen er fuldført. Det er virksomhedens administrator og ikke Domain Admin, for at sikre, at tilladelserne i Active Directory kan angives i alle domæner.

### <a name="global-admin-credentials"></a>Global administrator-legitimationsoplysninger
Disse legitimationsoplysninger anvendes kun under installationen og bruges ikke, når installationen er fuldført. Det bruges til at oprette [Azure AD-konto](#azure-ad-service-account) , der bruges til at synkronisere ændringer til Azure AD. Kontoen kan også synkronisere som en funktion i Azure AD.

### <a name="permissions-for-the-created-ad-ds-account-for-express-settings"></a>Tilladelser for den oprettede AD DS højde for udtrykkelig indstillinger
Den [konto](#active-directory-account) , der er oprettet til at læse og skrive til AD DS have følgende tilladelser, når der er oprettet af udtrykkelig indstillinger:

Tilladelse | Bruges til
---- | ----
<li>Gentage Directory ændringer</li><li>Kontraprøverne mappen ændres alle | Synkronisering af adgangskoder
Læse-og skriveadgang alle egenskaber, der bruger | Importere og Exchange hybrid
Læse-og skriveadgang alle egenskaber iNetOrgPerson | Importere og Exchange hybrid
Læse-og skriveadgang alle egenskaber gruppere | Importere og Exchange hybrid
Læse-og skriveadgang alle egenskaber Kontakt | Importere og Exchange hybrid
Nulstil adgangskode | Forberedelse til aktivering af adgangskode tilbageførsel

## <a name="custom-settings-installation"></a>Indstillinger for brugerdefineret installation
Når du bruger brugerdefinerede indstillinger, skal den konto, der bruges til at oprette forbindelse til Active Directory oprettes før installationen. De tilladelser, skal du give denne konto findes i [oprette kontoen AD DS](#create-the-ad-ds-account).

Side i guiden  | Legitimationsoplysninger, der indsamles | Tilladelser, der kræves| Bruges til
------------- | ------------- |------------- |-------------
I/T. | Bruger, der kører installationsguiden|<li>Administratoren af den lokale server</li><li>Hvis du bruger en fuld SQL Server, skal brugeren være System Administrator (Systemadministratorens) i SQL</li>| Som standard opretter den lokale konto, der bruges som [Synkroniser program tjenestekonto](#azure-ad-connect-sync-service-account). Kontoen oprettes kun, når administratoren ikke angiver en bestemt konto.
Installere synkroniseringstjenester, Service-kontoindstilling | AD eller lokale brugerlegitimationsoplysninger for konto | Bruger, der tildeles tilladelser ved installationsguiden | Hvis administratoren angiver en konto, bruges denne konto som servicekonto for synkroniseringstjenesten.
Oprette forbindelse til Azure AD | Azure AD directory legitimationsoplysninger| Globale administratorrolle i Azure AD| <li>Aktivere synkronisering i mappen Azure AD.</li>  <li>Oprettelse af [Azure AD-konto](#azure-ad-service-account) , der bruges til løbende synkronisering handlinger i Azure AD.</li>
Forbinde dine adresselister | Lokale Active Directory legitimationsoplysninger for hvert område, der er forbundet til Azure AD | Tilladelserne, der afhænger af, hvilke funktioner du aktiverer og kan findes i [Opret AD DS konto](#create-the-ad-ds-account) |Denne konto bruges til at læse og skrive mappeoplysninger under synkronisering.
AD FS-servere | For hver server på listen indsamler guiden legitimationsoplysninger, når legitimationsoplysningerne for den bruger, der kører guiden ikke er tilstrækkelige til at oprette forbindelse | Domæneadministrator | Installation og konfiguration af rollen AD FS server.
Web application proxyservere |For hver server på listen indsamler guiden legitimationsoplysninger, når legitimationsoplysningerne for den bruger, der kører guiden ikke er tilstrækkelige til at oprette forbindelse | Lokale administratorer på maskinen mål | Installation og konfiguration af WAP serverrolle.
Legitimationsoplysningerne for proxyen Hav tillid til |Sammenslutning service sikkerhed og rettighedsadministration legitimationsoplysninger (legitimationsoplysningerne proxyen bruger til at tilmelde dig til et Hav tillid til certifikat fra FS |Domænekonto, der er en lokal administrator af AD FS-server | Indledende tilmelding certifikatets FS WAP sikkerhed og rettighedsadministration.
AD FS servicekonto siden "Brug en domæne brugerindstilling konto" | AD brugerlegitimationsoplysninger konto | Domænebruger | Brugerkontoen AD hvis legitimationsoplysninger leveres bruges som logon-kontoen til AD FS-tjenesten.

### <a name="create-the-ad-ds-account"></a>Oprette kontoen AD DS
Når du installerer Azure AD-forbindelse, den konto, du angiver på siden **forbinde dine adresselister** skal findes i Active Directory og tilladelser, der kræves. Installationsguiden kontrollerer ikke tilladelserne, og problemer, findes kun under synkronisering.

Afhænger af, hvilke tilladelser, du har brug for de valgfrie funktioner aktivere du. Hvis du har flere domæner, skal have tildelt tilladelser for alle domæner i området. Hvis du ikke aktiverer en af disse funktioner, er standard **Domæne** brugertilladelser tilstrækkelige.

Funktion | Tilladelser
------ | ------
Synkronisering af adgangskoder | <li>Gentage Directory ændringer</li>  <li>Kontraprøverne mappen ændres alle
Exchange-hybridinstallation | Skrivetilladelse til de attributter, der er beskrevet i [Exchange hybrid tilbageførsel](../active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) for brugere, grupper og kontakter.
Adgangskode tilbageførsel | Skrivetilladelse til de attributter, der er beskrevet i [Introduktion til administration af adgangskoder](../active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions) for brugere.
Enhed tilbageførsel | Tilladelser med en PowerShell-script, som beskrevet i [enhed tilbageførsel](../active-directory-aadconnect-feature-device-writeback.md).
Gruppen tilbageførsel | Læse, oprette, opdatere og slette gruppeobjekter i den OU, hvor grupperne salgsdistributioner skal være placeret.

## <a name="upgrade"></a>Opgradere
Når du opgraderer fra én version af Azure AD-forbindelse til en ny version, skal du bruge følgende tilladelser:

Vigtigste | Tilladelser, der kræves | Bruges til
---- | ---- | ----
Bruger, der kører installationsguiden | Administratoren af den lokale server | Opdater binære filer.
Bruger, der kører installationsguiden | Medlem af ADSyncAdmins | Foretag ændringer til synkronisering af regler og andre konfiguration.
Bruger, der kører installationsguiden | Hvis du bruger en fuld SQL server: DBO (eller lignende) af synkronisering engine-database | Foretag database niveau ændringer, som opdaterer tabeller med nye kolonner.

## <a name="more-about-the-created-accounts"></a>Flere oplysninger om de oprettede konti

### <a name="active-directory-account"></a>Active Directory-konto
Hvis du bruger udtrykkelig indstillinger, oprettes en konto i Active Directory, der bruges til synkronisering. Oprettet firmaet er placeret i skovroddomænet i beholderen brugere og har navnet præfikset **MSOL_**. Kontoen er oprettet med en lang kompleks adgangskode, der ikke udløber. Hvis du har en adgangskodepolitik på dit domæne, foretag sikker lange og komplekse adgangskoder ville være tilladt for denne konto.

![AD-konto](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

### <a name="azure-ad-connect-sync-service-accounts"></a>Azure AD-forbindelse Synkroniser tjenestekonti
En lokal tjenestekonto er oprettet af installationsguiden (medmindre du angiver kontoen, der skal bruges i brugerdefinerede indstillinger). Kontoen er præfikset **AAD_** og bruges til tjenesten faktisk sync til at køre som. Hvis du installerer Azure AD-forbindelse på et domænenavn fra domænecontrolleren, oprettes kontoen i domænet. Hvis du bruger en fjernserver, der kører SQL server, eller hvis du bruger en proxy, der kræver godkendelse, skal være placeret servicekonto **AAD_** i domænet.

![Synkroniser tjenestekonto](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

Kontoen er oprettet med en lang kompleks adgangskode, der ikke udløber.

Denne konto bruges til at gemme adgangskoder til de andre konti på en sikker måde. Disse andre konti adgangskoder gemmes krypteret i databasen. Privat nøglerne for kryptering er beskyttet med cryptographic services hemmeligt nøgle kryptering ved hjælp af Windows Data beskyttelse API (DPAPI). Du bør ikke nulstille adgangskoden på service-kontoen, da Windows derefter sletter tasterne kryptering af sikkerhedsmæssige årsager.

Hvis du bruger en fuld SQL Server, er servicekonto DBO af oprettet databasen til synkronisering af programmet. Tjenesten fungerer ikke efter hensigten sammen med andre tilladelser. En SQL-logon, oprettes der også.

Kontoen er også tilladelse til filer, registreringsdatabasenøgler og andre objekter, der er relateret til synkronisering af programmet.

### <a name="azure-ad-service-account"></a>Azure AD-tjenestekonto
Der oprettes en konto i Azure AD til tjenesten sync brug. Denne konto kan blive identificeret efter dens visningsnavn.

![AD-konto](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

Navnet på den server, kontoen bruges på kan identificeres i den anden del af brugernavnet. På billedet er servernavnet FABRIKAMCON. Hvis du har arrangere servere, har hver server sin egen konto. Er der en grænse på 10 Synkroniser tjenestekonti i Azure AD.

Servicekonto er oprettet med en lang kompleks adgangskode, der ikke udløber. Det er tildelt rollen speciel **Directory-synkronisering konti** , der indeholder kun tilladelser til at udføre directory synkroniseringsopgaver. Denne specielle indbyggede rolle kan ikke tildeles uden for guiden Azure AD-forbindelse og portalen Azure viser denne konto med rollen **bruger**.

![AD-konto rolle](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccountrole.png)

## <a name="next-steps"></a>Næste trin

Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](../active-directory-aadconnect.md).
