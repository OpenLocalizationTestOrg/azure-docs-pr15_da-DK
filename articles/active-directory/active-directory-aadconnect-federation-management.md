<properties
    pageTitle="Active Directory Federation Services Management og tilpasning med Azure AD-forbindelse | Microsoft Azure"
    description="Administration af AD FS med Azure AD-forbindelse og tilpasning af AD FS-logon brugeroplevelsen med Azure AD-forbindelse og PowerShell."
    keywords="AD FS, ADFS, AD FS management, der opretter forbindelse mellem AAD, oprette forbindelse, logon, AD FS opsætning, reparere sikkerhed og rettighedsadministration, O365, sammenslutning, afhængige part"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/01/2016"
    ms.author="anandy"/>

# <a name="active-directory-federation-services-management-and-customization-with-azure-ad-connect"></a>Active Directory Federation Services administration og tilpasning med Azure AD-forbindelse

I denne artikel detaljer opgaver i forbindelse med Active Directory Federation Services (AD FS) og, der kan udføres ved hjælp af Microsoft Azure Active Directory Connect samt andre almindelige AD FS-opgaver, kan det være nødvendigt for en komplet konfiguration af en AD FS-farm.

| Emne | Hvad det dækker |
|:------|:-------------|
|**AD FS administration**|
|[Reparere indstillingen Hav tillid til](#repairthetrust)| Hvis du reparerer sammenslutning tilliden med Office 365 |
|[Tilføje en AD FS-server](#addadfsserver) | Udvide AD FS-farm med en ekstra AD FS-server|
|[Tilføje en proxyserver til AD FS web-program](#addwapserver) | Udvide AD FS-farm med en ekstra WAP-server|
|[Tilføje et medlem af organisationsnetværket domæne](#addfeddomain)| Føje et medlem af organisationsnetværket domæne|
| **AD FS tilpasning**|
|[Tilføje et brugerdefineret firmalogo eller en illustration](#customlogo)| Tilpasse en AD FS-logon side med et firmalogo og en illustration |
|[Tilføje en beskrivelse af logon](#addsignindescription) | Tilføje en beskrivelse af logonside |
|[Ændre AD FS Kræv regler](#modclaims) | Ændring af AD FS krav om forskellige sammenslutning scenarier |

## <a name="ad-fs-management"></a>AD FS administration

Azure AD-forbindelse indeholder forskellige opgaver, der er relateret til AD FS, der kan udføres ved hjælp af guiden Azure AD-forbindelse med minimale brugerinput. Når du er færdig med at installere Azure AD-forbindelse ved at køre guiden, kan du køre guiden igen for at udføre flere opgaver.

### Reparere indstillingen Hav tillid til<a name=repairthetrust></a>

Azure AD-forbindelse kan kontrollere, om den aktuelle tilstand for tilliden AD FS og Azure Active Directory og foretage relevante handlinger til at reparere indstillingen Hav tillid til. Følg disse trin for at reparere din Azure AD og AD FS har tillid til.

1. Vælg **Reparer AAD og ADFS Hav tillid til** listen over flere opgaver.
![Reparere AAD og ADFS Hav tillid til](media\active-directory-aadconnect-federation-management\RepairADTrust1.PNG)

2. Angiv dine legitimationsoplysninger for global administrator for Azure AD på siden **Opret forbindelse til Azure AD** , og klik på **Næste**.
![Oprette forbindelse til Azure AD](media\active-directory-aadconnect-federation-management\RepairADTrust2.PNG)

3. Angiv legitimationsoplysningerne for domæneadministratoren af på siden **fjernadgang til legitimationsoplysninger** .
![Fjernadgang legitimationsoplysninger](media\active-directory-aadconnect-federation-management\RepairADTrust3.PNG)

    Når du klikker på **Næste**, kontrolleres for certifikat sundhed Azure AD-forbindelse, og få vist eventuelle problemer.

    ![Tilstanden for certifikater](media\active-directory-aadconnect-federation-management\RepairADTrust4.PNG)

    Siden **klar til at konfigurere** viser en liste over handlinger, der skal udføres for at reparere indstillingen Hav tillid til.

    ![Klar til at konfigurere](media\active-directory-aadconnect-federation-management\RepairADTrust5.PNG)

4. Klik på **Installer** for at reparere indstillingen Hav tillid til.

>[AZURE.NOTE] Azure AD-forbindelse kan kun reparation eller act på certifikater, der er selvsigneret. Azure AD-forbindelse kan ikke reparere tredjeparts certifikater.

### Tilføje en AD FS-server<a name=addadfsserver></a>

> [AZURE.NOTE] Azure AD-forbindelse kræver certifikatfil PFX Sådan føjes en AD FS-server. Du kan derfor udføre denne handling, kun, hvis du har konfigureret AD FS-farm ved hjælp af Azure AD-forbindelse.

1. Vælg **Implementer en ekstra Sammenslutningsserver** , og klik på **Næste**.
![Yderligere sammenslutningsserveren](media\active-directory-aadconnect-federation-management\AddNewADFSServer1.PNG)

2. Angiv dine legitimationsoplysninger for global administrator for Azure AD, og klik på **Næste**på siden **Opret forbindelse til Azure AD** .
![Oprette forbindelse til Azure AD](media\active-directory-aadconnect-federation-management\AddNewADFSServer2.PNG)

3. Angiv domænet legitimationsoplysninger for administrator.
![Domæneadministratorlegitimationsoplysninger for administrator](media\active-directory-aadconnect-federation-management\AddNewADFSServer3.PNG)

4. Azure AD-forbindelse beder om adgangskoden for den PFX-fil, du har angivet under konfigurationen af din nye AD FS-farm med Azure AD-forbindelse. Klik på **Angiv adgangskode** for at angive adgangskoden for PFX-fil.
![Adgangskode til certifikat](media\active-directory-aadconnect-federation-management\AddNewADFSServer4.PNG)

    ![Angive SSL-certifikat](media\active-directory-aadconnect-federation-management\AddNewADFSServer5.PNG)

5. Angiv servernavnet eller IP-adresse, der skal føjes til AD FS-farm på siden **AD FS-servere** .
![AD FS-servere](media\active-directory-aadconnect-federation-management\AddNewADFSServer6.PNG)

6. Klik på **Næste** , og gå til den sidste side i **Konfigurer** . Når Azure AD-forbindelse er færdig med at tilføje serverne i farmen AD FS, får du mulighed for at bekræfte forbindelsen.
![Klar til at konfigurere](media\active-directory-aadconnect-federation-management\AddNewADFSServer7.PNG)

    ![Installationen er fuldført](media\active-directory-aadconnect-federation-management\AddNewADFSServer8.PNG)

### Tilføje en proxyserver til AD FS web-program<a name=addwapserver></a>

> [AZURE.NOTE] Azure AD-forbindelse kræver certifikatfil PFX at tilføje en web application proxy-server. Derfor vil du kunne udføre denne handling kun, hvis du har konfigureret AD FS-farm ved hjælp af Azure AD-forbindelse.

1. Vælg **Installere Web proxyen** på listen over tilgængelige opgaver.
![Installere programmet webtjenesteproxy](media\active-directory-aadconnect-federation-management\WapServer1.PNG)

2. Angive legitimationsoplysninger for Azure global administrator.
![Oprette forbindelse til Azure AD](media\active-directory-aadconnect-federation-management\wapserver2.PNG)

3. På siden **Angiv SSL-certifikat** skal du angive adgangskoden for den PFX-fil, du angav, da konfiguration af AD FS-farm med Azure AD-forbindelse.
![Adgangskode til certifikat](media\active-directory-aadconnect-federation-management\WapServer3.PNG)

    ![Angive SSL-certifikat](media\active-directory-aadconnect-federation-management\WapServer4.PNG)

4. Tilføj serveren, der skal tilføjes som en web-proxyen. Da web application proxy-server ikke kan føjes til domænet, beder guiden til administratorrettigheder for at blive føjet serveren.
![Administrative server-legitimationsoplysninger](media\active-directory-aadconnect-federation-management\WapServer5.PNG)

5. På siden **Proxy sikkerhed og rettighedsadministration legitimationsoplysninger** med administratorrettigheder for at konfigurere proxy Hav tillid til, og få adgang til den primære server i AD FS-farmen.
![Legitimationsoplysningerne for proxyen Hav tillid til](media\active-directory-aadconnect-federation-management\WapServer6.PNG)

6. På siden **klar til at konfigurere** viser guiden en liste over handlinger, der skal udføres.
![Klar til at konfigurere](media\active-directory-aadconnect-federation-management\WapServer7.PNG)

7. Klik på **Installer** for at afslutte konfigurationen. Når konfigurationen er fuldført, giver guiden mulighed for at bekræfte forbindelse til servere. Klik på **Bekræft** for at kontrollere connectivity.
![Installationen er fuldført](media\active-directory-aadconnect-federation-management\WapServer8.PNG)

### Tilføje et medlem af organisationsnetværket domæne<a name=addfeddomain></a>

Det er nemt at tilføje et domæne for at være knyttet til Azure AD ved hjælp af Azure AD-forbindelse. Azure AD-forbindelse tilføjer domænet til sammenslutning og ændrer Kræv reglerne for at afspejle udstederen korrekt, når du har flere domæner, der er knyttet til Azure AD.

1. For at tilføje et medlem af organisationsnetværket domæne skal du markere opgaven **tilføje en ekstra Azure AD-domæne**.
![Yderligere Azure AD-domæne](media\active-directory-aadconnect-federation-management\AdditionalDomain1.PNG)

2. På den næste side i guiden skal du angive global administrator legitimationsoplysningerne til Azure AD.
![Oprette forbindelse til Azure AD](media\active-directory-aadconnect-federation-management\AdditionalDomain2.PNG)

3. På siden **fjernadgang legitimationsoplysninger** skal du angive domænet legitimationsoplysninger for administrator.
![Fjernadgang legitimationsoplysninger](media\active-directory-aadconnect-federation-management\additionaldomain3.PNG)

4. Guiden giver en liste over Azure AD-domæner, som du kan oprette din lokale mappe på den næste side. Vælg domænet på listen.
![Azure AD-domæne](media\active-directory-aadconnect-federation-management\AdditionalDomain4.PNG)

    Når du har valgt domænenavn, giver guiden dig relevante oplysninger om yderligere handlinger, som fører guiden og påvirkningen af konfigurationen. I nogle tilfælde, hvis du vælger et domæne, der ikke er endnu er godkendt i Azure AD får guiden du oplysninger kan hjælpe dig med at bekræfte domænet. Få mere at vide, skal du se [tilføje dit eget domænenavn til Azure Active Directory](active-directory-add-domain.md) .

5. Klik på **Næste**, og siden **klar til at konfigurere** kan se en liste over handlinger, der skal udføre Azure AD-forbindelse. Klik på **Installer** for at afslutte konfigurationen.
![Klar til at konfigurere](media\active-directory-aadconnect-federation-management\AdditionalDomain5.PNG)

## <a name="ad-fs-customization"></a>AD FS tilpasning

De følgende afsnit indeholder oplysninger om nogle af de almindelige opgaver, som du kan være nødvendigt at udføre, når du tilpasser din AD FS-logonsiden.

### Tilføje et brugerdefineret firmalogo eller en illustration<a name=customlogo></a>

For at ændre logoet på det firma, der vises på siden **Log på** skal du bruge følgende Windows PowerShell-cmdlet og syntaks.

> [AZURE.NOTE] De anbefalede dimensioner for logoet er 260 x 35 @ 96 dpi med en filstørrelse, der ikke er større end 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [AZURE.NOTE] Parameteren *TargetName* er påkrævet. Standardtemaet, der er udgivet med AD FS hedder standard.


### Tilføje en beskrivelse af logon<a name=addsignindescription></a>

For at tilføje en logonside beskrivelse til den **logonside**, skal du bruge følgende Windows PowerShell-cmdlet og syntaks.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

### Ændre AD FS Kræv regler<a name=modclaims></a>

AD FS understøtter et omfattende Kræv sprog, du kan bruge til at oprette brugerdefinerede Kræv regler. Du kan finde yderligere oplysninger finder [Feltet rolle Kræv regel sprog](https://technet.microsoft.com/library/dd807118.aspx).

I nedenstående afsnit beskrives, hvordan du kan skrive brugerdefinerede regler for visse scenarier, der vedrører Azure AD og AD FS sammenslutning.

#### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Fast ID betinget på en værdi, der ikke er til stede i attributten

Azure AD-forbindelse kan du angive en attribut, der skal bruges som en datakilde anker når objekter er synkroniseret til Azure AD. Hvis værdien i den brugerdefinerede attribut ikke er tomme, vil du måske gerne problem med et fast ID-krav. Du kan for eksempel Vælg **ms-ds-consistencyguid** som attributten for kilde anker og vil udstede **ImmutableID** som **ms-ds-consistencyguid** , i tilfælde af attributten har en værdi med den. Hvis der ikke er nogen værdi mod attributten, udstede **objectGuid** som fast-ID.  Du kan oprette brugerdefinerede Kræv regelsættet som beskrevet i følgende afsnit.

**Regel 1: Forespørgsel af attributter**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

I denne regel forespørger du værdierne for **ms-ds-consistencyguid** og **objectGuid** for brugeren, fra Active Directory. Ændre navnet på store til en passende Butiksnavn i din AD FS-installation. Også ændre typen krav til typen stort krav til din sammenslutning som defineret for **objectGuid** og **ms-ds-consistencyguid**.

Desuden ved hjælp af **Tilføj** og ikke **problem**, du undgå at tilføje et udgående problem for objektet og kan bruge værdier som mellemliggende værdier. Du vil udstede kravet i en regel for senere, når du har oprettet hvilken værdi der skal bruges som det fast-ID.

**Regel 2: Kontrollér, om ms-ds-consistencyguid findes til brugeren**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Denne regel definerer et midlertidige flag kaldet **idflag** , der er angivet til **useguid** , hvis der ikke er nogen **ms-ds-concistencyguid** udfyldt til brugeren. Logik bag dette er, at AD FS ikke tillade tomme krav. Så når du tilføjer krav http://contoso.com/ws/2016/02/identity/claims/objectguid og http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid i regel 1, du ender med et **msdsconsistencyguid** gøre krav på kun hvis værdien er udfyldt til brugeren. Hvis det ikke er udfyldt, ser AD FS, at den har en tom værdi og slipper den med det samme. Alle objekter har **objectGuid**, så kravet vil stadig være der, når regel 1 udføres.

**Regel 3: Udstede ms-ds-consistencyguid som fast ID, hvis den findes**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Dette er en implicit **findes** kontrol. Hvis værdien for kravet findes, derefter problem, som det fast-ID. I forrige eksempel bruges **nameidentifier** kravet. Du skal ændre dette til typen relevante krav til fast-ID i dit miljø.

**Regel 4: Udstede objectGuid som fast ID, hvis ms-ds-consistencyGuid ikke er til stede**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

I denne regel skal tjekker du blot midlertidige flaget **idflag**. Du beslutter, om at udstede kravet ud fra dens værdi.

> [AZURE.NOTE] Rækkefølgen af disse regler er vigtigt.

#### <a name="sso-with-a-subdomain-upn"></a>SSO med et underdomæne UPN

Du kan tilføje mere end ét domæne til at være samlet ved hjælp af Azure AD-forbindelse, som beskrevet i [tilføje et nyt medlem af organisationsnetværket domæne](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain). Du skal ændre UPN-kravet, så udsteder ID svarer til roddomænet og ikke underdomænet, fordi organisationsnetværket roddomænet også dækker underordnede.

Kræv reglen for udsteder ID er som standard angivet som:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Standard udsteder ID krav](media\active-directory-aadconnect-federation-management\issuer_id_default.png)

Standardreglen blot tager UPN-suffikset og bruges i udsteder ID kravet. For eksempel John er en bruger i sub.contoso.com, og contoso.com er knyttet til Azure AD. John indsætter john@sub.contoso.com som brugernavnet, mens du logge på Azure AD, og det gør du krav på reglen i AD FS håndterer det på følgende måde standard-udsteder-ID.

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**Gøre krav på værdien:** http://sub.contoso.com/adfs/services/trust/

Hvis du vil have kun roddomænet i værdien udsteder krav, krav reglen så det svarer til følgende ændres.

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>Næste trin

Lær mere om [logon brugerindstillinger](active-directory-aadconnect-user-signin.md).
