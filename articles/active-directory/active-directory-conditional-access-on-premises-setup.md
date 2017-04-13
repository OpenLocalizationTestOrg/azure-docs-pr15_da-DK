<properties
    pageTitle="Konfiguration af lokale betinget access ved hjælp af Azure Active Directory enhed registrering | Microsoft Azure"
    description="En trinvis vejledning til at aktivere betinget adgang til lokale programmer, der bruger Active Directory Federation Service (AD FS) i Windows Server 2012 R2."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>


# <a name="setting-up-on-premises-conditional-access-using-azure-active-directory-device-registration"></a>Konfiguration af lokale betinget access ved hjælp af Azure Active Directory enhed registrering

Personligt kan ejet enheder af dine brugere markeres konstateret, at din organisation ved at kræve brugere til arbejdsstedet joinforbindelse deres enheder til tjenesten Azure Active Directory enhed registrering. Nedenfor finder du en trinvis vejledning til at aktivere betinget adgang til lokale programmer, der bruger Active Directory Federation Service (AD FS) i Windows Server 2012 R2.

> [AZURE.NOTE]
> Office 365-licens eller den licens, Azure AD Premium er påkrævet, når ved hjælp af enheder, der er registreret i Azure Active Directory enhed registrering service betinget access politikker. Dette omfatter politikker hos Active Directory Federation Services (AD FS) til lokale ressourcer.

Se [oprette forbindelse til arbejdsområde fra enhver enhed til SSO og problemfri anden faktor godkendelse på tværs af virksomhedens programmer](https://technet.microsoft.com/library/dn280945.aspx)kan finde flere oplysninger om betinget access scenarierne for lokale.

Disse funktioner er tilgængelige for kunder, køber en licens til Azure Active Directory Premium.

<a name="supported-devices"></a>Understøttede enheder
-------------------------------------------------------------------------
* Windows 7 domæne sammenføjet enheder.
* Windows 8.1 personlig og domæne sammenføjet enheder.
* iOS 6 eller nyere til Safari-browseren
* Android 4.0 eller nyere, Samsung GS3 eller over telefoner, Samsung Note2 eller over tablets.


<a name="scenario-prerequisites"></a>Scenarie forudsætninger
------------------------------------------------------------------------
* Office 365-abonnement eller Azure Active Directory Premium
* En Azure Active Directory-lejer
* Windows Server Active Directory (Windows Server 2008 eller nyere)
* Opdaterede skema i Windows Server 2012 R2
* Licens til Azure Active Directory Premium
* Windows Server 2012 R2 Federation Services, som er konfigureret til SSO til at Azure AD
* Windows Server 2012 R2 Web Application Proxy Microsoft Azure Active Directory forbinde (Azure AD Connect). [Hente Azure AD-forbindelse her](http://www.microsoft.com/en-us/download/details.aspx?id=47594).
* Bekræftet domæne.

<a name="known-issues-in-this-release"></a>Kendte problemer i denne version
-------------------------------------------------------------------------------
* Enheden ud betinget access politikker kræver enhed objekt tilbageskrivning til Active Directory fra Azure Active Directory. Det kan tage op til 3 timer for enhedsobjekter, der skal skrives tilbage til Active Directory
* iOS 7 enheder spørger altid brugeren til at vælge et certifikat under klientcertifikat.
* Nogle versioner af iOS8, før iOS 8.3 ikke virker.

## <a name="scenario-assumptions"></a>Scenarie antagelser
Dette scenarie antager, at du har et hybridmiljø bestående af en Azure AD-lejer og et lokalt Active Directory. Disse lejere skal være forbundet med Azure AD-forbindelse og med en bekræftet domæne og AD FS for SSO. Den nedenstående tjekliste hjælper dig med at konfigurere dit miljø til fase er beskrevet ovenfor.

<a name="checklist-prerequisites-for-conditional-access-scenario"></a>Tjekliste: Forudsætninger for betinget Access scenarie
--------------------------------------------------------------
Tilslut din Azure AD-lejer med din lokale Active Directory.

## <a name="configure-azure-active-directory-device-registration-service"></a>Konfigurere Azure Active Directory enhed registrering Service
Brug denne vejledning til installation og konfiguration af Azure Active Directory enhed registrering Service for organisationen.

Denne vejledning forudsætter, at du har konfigureret Windows Server Active Directory og har et abonnement på Microsoft Azure Active Directory. Se forudsætninger ovenfor.

Hvis du vil installere Azure Active Directory enhed registrering Service med Azure Active Directory-lejer, ved at udføre opgaver i den følgende tjekliste i rækkefølge. Når en referencekæde tager dig til et emne, skal du gå tilbage til denne tjekliste, når du har gennemgået de grundlæggende emne, så du kan fortsætte med de resterende opgaver i denne tjekliste. Nogle opgaver, der indeholder et scenarie validering trin, der kan hjælpe dig med at bekræfte trinnet er fuldført.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>Del 1: Aktivér Azure Active Directory enhed registrering

Følg den tjekliste nedenfor for at aktivere og konfigurere Azure Active Directory enhed registreringstjenesten.

| Opgave                                                                                                                                                                                                                                                                                                                                                                                             | Reference                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Aktivere registrering af enhed i din Azure Active Directory-lejer for enheder til at deltage i arbejdsområdet. Multi-Factor authentication er som standard ikke aktiveret for tjenesten. Multi-Factor authentication kan anbefales, når du registrerer en enhed. Før du aktiverer Multi-Factor authentication i ADRS, sikre, at AD FS er konfigureret til en udbyder af godkendelse i flere niveauer. | [Aktivere registrering af Azure Active Directory-enhed](active-directory-conditional-access-device-registration-overview.md)               |
| Enheder finde din Azure Active Directory enhed registrering Service ved at søge efter kendte DNS-poster. Du skal konfigurere dit firma DNS, således at enheder kan finde dit Azure Active Directory enhed registrering Service.                                                                                                                                                   | [Konfigurere Azure Active Directory enhed registrering registrering.](active-directory-conditional-access-device-registration-overview.md) |

##<a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>Del 2: Installere og konfigurere Windows Server 2012 R2 Active Directory Federation Services og konfigurere en sammenslutning relation med Azure AD

| Opgave                                                                                                                                                                                                                                                                                                                                                                                             | Reference                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Installere Active Directory Domain Services-domæne med Windows Server 2012 R2 skema filtypenavne. Du behøver ikke at opgradere nogen af dit domæne enheder til Windows Server 2012 R2. Skema opgraderingen er det eneste krav. | [Opgradere dit Active Directory Domain Services-skema](#upgrade-your-active-directory-domain-services-schema)               |
| Enheder finde din Azure Active Directory enhed registrering Service ved at søge efter kendte DNS-poster. Du skal konfigurere dit firma DNS, således at enheder kan finde dit Azure Active Directory enhed registrering Service.                                                                                                                                                   | [Forberede dine Active Directory understøttelse af enheder](#prepare-your-active-directory-to-support-devices) |


##<a name="part-3-enable-device-writeback-in-azure-ad"></a>Del 3: Aktivér enhed tilbageførsel i Azure AD

| Opgave                                                                                                                                                                                                                                                                                                                                                                                             | Reference                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Fuldfør del 2 af aktivering enhed tilbageførsel i Azure AD-forbindelse. Når er afsluttet, gå tilbage dette denne vejledning. | [Aktivering af enhed tilbageførsel i Azure AD-forbindelse](#upgrade-your-active-directory-domain-services-schema)               |


##<a name="optional-part-4-enable-multi-factor-authentication"></a>[Valgfrit] Del 4: Aktivér Multi-Factor authentication

Det anbefales, at du konfigurerer en af flere indstillinger til multi-Factor authentication. Hvis du vil kræve MFA, skal du se [vælge Multi-Factor sikkerhedsløsning for dig](../multi-factor-authentication/multi-factor-authentication-get-started.md). Den indeholder en beskrivelse af hver løsning, og links til hjælp til at konfigurere løsningen efter eget valg.

## <a name="part-5-verification"></a>Del 5: bekræftelse

Installationen er nu færdig. Du kan nu prøve nogle scenarier. Følge linkene nedenfor for at eksperimentere med tjenesten, og Bliv fortrolig med funktionerne


| Opgave                                                                                                                                                                                                                         | Reference                                                                       |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| Deltage i visse enheder til din arbejdsplads via Azure Active Directory enhed registrering. Du kan deltage i iOS, Windows og Android-enheder                                                                                         | [Deltage i enheder til din arbejdsplads via Azure Active Directory enhed registrering](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| Du kan få vist og aktivere/deaktivere registrerede enheder ved hjælp af portalen Administrator. I denne opgave skal du se nogle registrerede enheder ved hjælp af portalen Administrator.                                                        | [Oversigt over registrering af Azure Active Directory-enhed](active-directory-conditional-access-device-registration-overview.md)                             |
| Kontrollér, at enhedsobjekter skrives tilbage fra Azure Active Directory på Windows Server Active Directory.                                                                                                                  | [Kontrollér registrerede enheder er skrevet tilbage til Active Directory](#verify-registered-devices-are-written-back-to-active-directory)                  |
| Nu, hvor brugerne kan registrere deres enheder, du kan oprette program access politikker i AD FS, der tillader kun registrerede enheder. I denne opgave, du vil oprette adgang en regel for programmet access og en brugerdefineret nægtet meddelelse | [Oprette en access-politik for programmet på computeren og brugerdefineret adgang nægtet meddelelse](#create-an-application-access-policy-and-custom-access-denied-message)            |



## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Integrere Azure Active Directory med lokale Active Directory
Dette hjælper dig med at integrere din Azure AD-lejer med lokale active directory, ved hjælp af Azure AD-forbindelse. Selvom trinnene er tilgængelige i portalen Azure klassisk, noter med en speciel instruktioner, der nævnes i dette afsnit.

1.  Log på ved hjælp af en konto, der er en Global Administrator i Azure AD Azure klassisk portalen.
2.  Vælg **Active Directory**i den venstre rude.
3.  Vælg mappen under fanen **mappe** .
4.  Vælg fanen **Katalogintegration** .
5.  Følg trin 1 til 3 for at integrere Azure Active Directory med din lokale mappe under **installere og administrere** sektion.
  1.    Tilføje domæner.
  2.    Installere og køre Azure AD-forbindelse: installere Azure AD-forbindelse ved hjælp af følgende vejledningen, [brugerdefineret installation af Azure AD-forbindelse](./connect/active-directory-aadconnect-get-started-custom.md).
  3. Kontrollere og administrere katalogsynkronisering. Enkelt sign-on – vejledning findes i dette trin.

  > [AZURE.NOTE]
  > Konfigurere sammenslutning med AD FS, som beskrevet i det dokument, der er sammenkædet ovenfor. Du behøver ikke at konfigurere en af funktionerne, preview.


## <a name="upgrade-your-active-directory-domain-services-schema"></a>Opgradere dit Active Directory Domain Services-skema

> [AZURE.NOTE]
> Opgradering af din Active Directory-skemaet kan ikke fortrydes. Det anbefales, at du først udfører dette i et testmiljø.

1. Log på domænecontrolleren med en konto, der indeholder både virksomhedens administrator og skema administrator rettigheder.
2. Kopiér den **[medier] \support\adprep** mappe og undermapper til en af Active Directory-domænecontrollere.
3. Hvor er [medier] stien til Windows Server 2012 R2 installationsmedier.
4. Fra en kommandoprompt skal du gå til mappen adprep og udføre: **adprep.exe/ForestPrep**. Følg vejledningen på skærmen for at fuldføre skema opgraderingen.

## <a name="prepare-your-active-directory-to-support-devices"></a>Forberedelse af din Active Directory for at understøtte enheder

>[AZURE.NOTE] Dette er en enkeltstående handling, der skal du køre for at forberede din Active Directory-område for at understøtte enheder. Du skal være logget på med enterprise administratortilladelser og Active Directory-område skal have Windows Server 2012 R2 skemaet at udføre denne procedure.


##<a name="prepare-your-active-directory-forest-to-support-devices"></a>Forberede din Active Directory-område for at understøtte enheder

> [AZURE.NOTE]
>Dette er en enkeltstående handling, der skal du køre for at forberede din Active Directory-område for at understøtte enheder. Du skal være logget på med enterprise administratortilladelser og Active Directory-område skal have Windows Server 2012 R2 skemaet at udføre denne procedure.

### <a name="prepare-your-active-directory-forest"></a>Forberede din Active Directory-område

1.  Åbn en Windows PowerShell-kommandovindue, og skriv på din sammenslutningsserver: initialisering ADDeviceRegistration
2.  Når du bliver bedt om **ServiceAccountName**, Skriv navnet på den servicekonto, du har valgt som servicekonto for AD FS. Hvis det er en gMSA-konto, kan du angive kontoen i formatet **domain\accountname$** . Brug format **domain\accountname**for en domænekonto.



### <a name="enable-device-authentication-in-ad-fs"></a>Aktivere enhed godkendelse i AD FS

1. Åbne AD FS management console på din sammenslutningsserver, og Naviger til **AD FS** > **Godkendelsespolitikker**.
2. Klik på **Rediger globale primære godkendelse...** fra ruden **Handlinger** .
3. Markér **Aktivér enhed godkendelse** , og klik derefter på**OK**.
4. Som standard fjerne AD FS med jævne mellemrum ubrugte enheder fra Active Directory. Når du bruger Azure Active Directory enhed registrering, så enheder kan administreres i Azure, skal du deaktivere denne opgave.


### <a name="disable-unused-device-cleanup"></a>Deaktivere ubrugte enhed oprydning
1. Åbn en Windows PowerShell-kommandovindue, og skriv på din sammenslutningsserver: Set-AdfsDeviceRegistration - MaximumInactiveDays 0

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Forberede enhed tilbageførsel Azure AD-forbindelse

1.  Fuldfør del 1: Forberede Azure AD forbindelse.


## <a name="join-devices-to-your-workplace-using-azure-active-directory-device-registration"></a>Deltage i enheder til din arbejdsplads via Azure Active Directory enhed registrering

### <a name="join-an-ios-device-using-azure-active-directory-device-registration"></a>Deltage i en iOS-enhed ved hjælp af Azure Active Directory enhed registrering

Azure Active Directory enhed registrering bruger tilmeldingsprocessen Over-the-Air profil for iOS-enheder. Denne proces begynder med at oprette forbindelse til profil tilmelding URL-adressen via webbrowseren Safari-brugeren. URL-formatet er som følger:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

Hvor `yourdomainname` er navnet på det domæne, du har konfigureret med Azure Active Directory. Hvis dit domænenavn er contoso.com, bliver URL-adressen f.eks.:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Der er mange forskellige måder til at kommunikere denne URL-adresse til brugerne. En anbefalede måde er at publicere denne URL-adresse i en brugerdefineret programadgang nægtet meddelelse i AD FS. Dette er dækket i sektionen kommende: [Opret en politik for programmet access og brugerdefineret adgang nægtet meddelelse](#create-an-application-access-policy-and-custom-access-denied-message).

###<a name="join-a-windows-81-device-using-azure-active-directory-device-registration"></a>Deltage i en Windows 8.1-enhed med Azure Active Directory enhed registrering

1. Gå til **PC-indstillinger**på enheden Windows 8.1 > **netværk** > **på arbejdspladsen**.
2. Angiv dit brugernavn i UPN-format. For eksempel dan@contoso.com..
3. Vælg **Deltag**.
4. Når du bliver bedt om det, logge på med dine legitimationsoplysninger. Enheden er nu sammenføjet.

###<a name="join-a-windows-7-device-using-azure-active-directory-device-registration"></a>Deltage i en Windows 7-enhed med Azure Active Directory enhed registrering
Hvis du vil registrere Windows 7 domæne sammenføjet enheder, skal du installere enhed registrering programpakke. Programpakke kaldes arbejdsplads deltage for Windows 7 og kan hentes på [Microsoft Connect websted](https://connect.microsoft.com/site1164). Vide, hvordan du bruger pakken findes på [Konfigurer automatisk enhed registrering for Windows 7 domæne joinforbundne enheder](active-directory-conditional-access-automatic-device-registration-windows7.md).

### <a name="join-an-android-device-using-azure-active-directory-device-registration"></a>Deltage i en Android-enhed ved hjælp af Azure Active Directory enhed registrering

[Azure godkenderen for Android emne](active-directory-conditional-access-azure-authenticator-app.md) indeholder instruktioner til, hvordan du installerer Azure godkenderen app på din Android-enhed og tilføjer en arbejds-konto. Når en arbejdskonto, der oprettes på en Android-enhed, er enheden arbejdsplads tilsluttet organisationen.

## <a name="verify-registered-devices-are-written-back-to-active-directory"></a>Kontrollér registrerede enheder er skrevet tilbage til Active Directory
Du kan få vist og bekræfte, at din enhedsobjekter er skrevet tilbage til din Active Directory ved hjælp af LDP.exe eller ADSI Edit. Begge er tilgængelige med værktøjerne Active Directory-Administrator.

Som standard placeres enhedsobjekter, der er skrevet tilbage fra Azure Active Directory i det samme domæne som din AD FS-farm.

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>Oprette en access-politik for programmet på computeren og brugerdefineret adgang nægtet meddelelse
Forestil dig følgende: du opretter et Relying part sikkerhed og rettighedsadministration i AD FS-program og konfigurere en udstedelse godkendelsesregel, der tillader kun registrerede enheder. Nu er kun de enheder, der er registreret tilladelse til at få adgang til programmet. For at gøre det nemt for dine brugere kan få adgang til programmet, skal konfigurere du et brugerdefineret adgang nægtet meddelelse, der indeholder oplysninger om, hvordan du deltager i deres enhed. Dine brugere har nu en problemfri metode til at registrere deres enheder for at få adgang til et program.

Vejledningen nedenfor viser dig, hvordan at implementere dette scenario.

>[AZURE.NOTE]
Dette afsnit antages det, at du allerede har konfigureret en stole part har tillid til for dit program i AD FS.

1. Åbn værktøjet AD FS MMC og gå til AD FS > sikkerhed og rettighedsadministration relationer > stole part har tillid til.
2. Find det program, som denne nye access-regel skal gælde. Højreklik på programmet, og vælg Rediger Kræv regler...
3. Vælg fanen **Reglerne for Udsendelsesgodkendelse** og vælg derefter **Tilføj regel**
4. **Kræv regel** skabelon rullelisten, Vælg **tillade eller nægte brugere, der er baseret på et indgående krav**. Klik på **Næste**.
5. I Kravbrugeren Regelnavnet: felttype: **tillade adgang fra registrerede enheder**
6. Gøre krav type fra indgående: Rul ned på listen, skal du markere **Er registreret bruger**.
7. Gøre krav værdi i indgående: skal du skrive: **Sand**
8. Vælg alternativknappen **tillade adgang til brugere med denne indgående krav** .
9. Klik på **Udfør** , og vælg derefter **Anvend**.
10. Fjerne alle regler, der er mere lempelige end den regel, du lige har oprettet. For eksempel fjerne standardreglen **Tillade adgang til alle brugere** .

Dit program er nu konfigureret til at tillade adgang, når brugeren kommer fra en enhed, som de er registreret og tilsluttet arbejdspladsen. Du kan finde mere avancerede access politikker, under [Administrere risiko med Multi-Factor adgangskontrol](https://technet.microsoft.com/library/dn280949.aspx).

Derefter skal konfigurere du en brugerdefineret fejlmeddelelse for dit program. Fejlmeddelelsen vil Giv brugerne besked om, at de skal deltager deres enhed arbejdspladsen, før de tillader dig adgang til programmet. Du kan oprette et brugerdefineret programadgang nægtet meddelelse ved hjælp af brugerdefinerede HTML- og Windows PowerShell.

Åbn et Windows PowerShell-kommandovindue på din sammenslutningsserver, og Skriv følgende kommando. Erstatte dele af kommandoen med elementer, der er specifikke for dit system:

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Før du kan få adgang til dette program, skal du registrere din enhed.

**Hvis du bruger en iOS-enhed, Vælg dette link til at deltage i din enhed**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Deltage i denne iOS-enhed til din arbejdsplads.


**Hvis du bruger en enhed med Windows 8.1**, kan du deltage i din enhed ved at gå til **PC-indstillinger ** >  **netværk** > **på arbejdspladsen**.


Hvor "**stole part sikkerhed og rettighedsadministration navn**" er navnet på din programmer stole part sikkerhed og rettighedsadministration objekt i AD FS.
Hvor **yourdomain.com** er navnet på det domæne, du har konfigureret med Azure Active Directory. For eksempel contoso.com.
Sørg for at fjerne en hvilken som helst linjeskift (hvis relevant) fra html-indhold, du sender til cmdlet'en **Set-AdfsRelyingPartyWebContent** til.


Nu når brugere får adgang til dit program fra en enhed, der ikke er registreret med Azure Active Directory enhed registrering-tjenesten, modtager de en side, der ligner skærmbilledet nedenfor.

![Screeshot af en fejl, når brugerne ikke har registreret deres enhed med Azure AD](./media/active-directory-conditional-access/error-azureDRS-device-not-registered.gif)

##<a name="related-articles"></a>Relaterede artikler

- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
