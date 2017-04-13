<properties
    pageTitle="Azure AD Connect: Brugeren logge på | Microsoft Azure"
    description="Azure AD-forbindelse bruger-logon til brugerdefinerede indstillinger."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/16/2016"
    ms.author="billmath"/>



# <a name="azure-ad-connect-user-sign-on-options"></a>Azure AD forbinde bruger Sign-on indstillinger

Azure AD-forbindelse gør det muligt for dine brugere til at logge både skyen og lokale ressourcer ved hjælp af de samme adgangskoder. I denne artikel beskrives vigtige koncepter for hver identitetsmodel kan hjælpe dig med at vælge identitet, du vil bruge til dit logon til Azure AD.

Hvis du allerede kender Azure AD identitetsmodel og vil have mere at vide om en bestemt metode, klik på under på relevante emne.

* [Synkronisering af adgangskoder](#password-synchronization)
* [Samlet SSO (med ADFS)](#federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm)


## <a name="choosing-a-user-sign-in-method"></a>Vælge en bruger-logon metode
For de fleste organisationer der blot ønsker at give brugerne har logget Office 365, anbefales SaaS programmer og andre Azure AD baseret ressourcer adgangskode til synkronisering af standardindstillingen.
Nogle organisationer har dog bestemt grunde til at bruge en samlet logge på indstillingen såsom AD FS.  Dette omfatter:

- Din organisation allerede har AD FS eller en tredjepart sammenslutning udbyder installeret 3
- Din sikkerhedspolitik forhindrer synkronisering af adgangskode hashes til skyen
- Du kræver, at brugerne opleve problemfri SSO (uden yderligere adgangskode beder), når adgang til skyen ressourcer fra domæne joinforbundne maskiner på virksomhedens netværk
- Du har brug for nogle bestemte funktioner, der har AD FS
    - Lokalt Multi-Factor authentication ved hjælp af en tredjeparts provider eller chipkort (få mere at vide om fra tredjepart MFA udbydere for AD FS i Windows Server 2012 R2)
    - Active Directory-integrationsfunktioner som bløde konto låsning eller AD adgangskode og arbejde timer-politik
    - Betinget adgang til både i det lokale miljø og skyen ressourcer ved hjælp af enhed registrering, Azure AD join eller Intune MDM politikker

### <a name="password-synchronization"></a>Synkronisering af adgangskoder
Med synkronisering af adgangskoder synkroniseres hashværdier for brugeradgangskoder fra din lokale Active Directory til Azure AD.  Når adgangskoder ændres eller nulstille lokalt, synkroniseres de nye adgangskoder umiddelbart til Azure AD, så brugerne kan altid bruge den samme adgangskode til skyen ressourcer, som de gør i det lokale miljø.  Adgangskoderne er aldrig har sendt til Azure AD eller gemt i Azure AD i klar tekst.
Synkronisering af adgangskoder kan bruges sammen med tilbageskrivning adgangskode til at aktivere selv nulstilling af Tjenesteadgangskode i Azure AD.

<center>![Skyen](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[Du kan finde flere oplysninger om synkronisering af adgangskoder](active-directory-aadconnectsync-implement-password-synchronization.md)


### <a name="federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm"></a>Sammenslutning ved hjælp af en ny eller eksisterende AD FS i Windows Server 2012 R2 farm
Med organisationsnetværket sign-on, kan brugerne logge Azure AD baseret services med deres lokale adgangskoder og, mens på virksomhedens netværk, uden at skulle angive sin adgangskode igen.  Indstillingen sammenslutning med AD FS kan du installere en ny eller angive en eksisterende AD FS i Windows Server 2012 R2 farm.  Hvis du vil angive en eksisterende farm, konfigurere Azure AD-forbindelse tillid mellem dine farm og Azure AD, så brugerne kan logge.

<center>![Skyen](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="to-deploy-federation-with-ad-fs-in-windows-server-2012-r2-you-will-need-the-following"></a>Du skal installere sammenslutning med AD FS i Windows Server 2012 R2, skal du bruge følgende

Hvis du installerer en ny farm:

- En server, Windows Server 2012 R2 for sammenslutningsserveren.
- En server, Windows Server 2012 R2 for proxyen Web.
- en .pfx-fil med et SSL-certifikat til din tilsigtede sammenslutning tjenestenavne, som fs.contoso.com.

Hvis du anvender en ny farm eller bruger en eksisterende farm:

- Lokal administratorlegitimationsoplysninger på serverne sammenslutning.
- Lokal administratorlegitimationsoplysninger på en hvilken som helst arbejdsgruppe (ikke-domæne, tilsluttet) servere, som du vil installere rollen Web proxyen.
- Den computer, hvorpå du udføre guiden skal kunne oprette forbindelse til andre computere, der hvor du vil installere AD FS eller Web proxyen via Windows Fjernadministration.

[Konfiguration af SSO-Teknologier med AD FS](./aad-connect/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) 

#### <a name="sign-on-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Log med en tidligere version af AD FS eller en tredjepart løsning

Hvis du allerede har konfigureret skyen Log på ved hjælp af en tidligere version af AD FS (såsom AD FS 2.0) eller en tredjepart sammenslutning udbyder, kan du vælge at springe brugerens logonadresse konfiguration via Azure AD-forbindelse.  Dette gør det muligt at få den seneste synkronisering og andre funktioner i Azure AD-forbindelse, mens du stadig bruger dit eksisterende løsning til logge på.

[Azure AD fra tredjepart sammenslutning kompatibilitet listen](./active-directory-aadconnect-federation-compatibility.md)

## <a name="user-sign-in-and-user-principal-name-upn"></a>Bruger-logon og brugerens hovednavn (UPN)

### <a name="understanding-user-principal-name"></a>Forstå brugerens hovednavn

I Active Directory er standard UPN-suffikset DNS-navnet på det domæne, som brugerkontoen oprettet. I de fleste tilfælde er dette det domænenavn, der er registreret som enterprise domænet på internettet. Du kan dog tilføje flere UPN-suffikser ved hjælp af Active Directory-domæner og -tillidsforhold.

UPN for brugeren, er af formatet username@domai. For eksempel et active directory contoso.com brugeren John kan have UPN john@contoso.com. UPN for brugeren, er baseret på RFC 822. Selvom UPN- og maildomæner dele de samme formater, værdien af UPN for en bruger kan eller er ikke lig med mailadressen på brugeren.

### <a name="user-principal-name-in-azure-ad"></a>Brugerens hovednavn i Azure AD

Azure AD-forbindelse guiden Brug userPrincipalName-attributten, eller kan du angive egenskaben (i brugerdefineret installation) for at blive brugt fra lokale som brugerens hovednavn i Azure AD. Dette er den værdi, der skal bruges til at logge på Azure AD. Hvis værdien for attributten bruger hovednavn ikke svarer til et bekræftet domæne i Azure AD, derefter Azure AD erstatter det med en standard-. onmicrosoft.com værdi.

Alle mapper i Azure Active Directory leveres med et indbygget domænenavn i den formular contoso.onmicrosoft.com, hvor du kan komme i gang med Azure eller andre Microsoft-tjenester. Du kan forbedre og forenkle logonoplevelse ved hjælp af brugerdefinerede domæner. Oplysninger om brugerdefinerede domæne læst navne i Azure AD og hvordan du kan bekræfte et domæne, skal du [føje dit eget domænenavn til Azure Active Directory](active-directory-add-domain.md#add-your-custom-domain-name-to-azure-active-directory)

## <a name="azure-ad-sign-in-configuration"></a>Azure AD-logon konfiguration

### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Azure AD-logon konfiguration med Azure AD-forbindelse
Azure AD logonoplevelse er afhængig af om Azure AD er i stand til at matche bruger hovednavn suffiks for en bruger, der synkroniseres til en af de brugerdefinerede domæner, der er bekræftet i mappen Azure AD. Azure AD-forbindelse får du hjælp under konfigurationen af Azure AD-logon indstillinger, så der svarer til den lokale oplevelse brugeroplevelsen logon i skyen. Azure AD-forbindelse viser de UPN-suffikser, der er defineret for domæne(r) og forsøger at finde dem med et brugerdefineret domæne i Azure AD og hjælper dig med at med den relevante handling, der skal tages.
Azure AD-logonside viser ud UPN Suffikserne defineret for lokale Active Directory og viser den tilsvarende status mod hvert suffiks. Statusværdierne kan være en af de nedenfor:

| Stat | Beskrivelse | Handling påkrævet |
|:----|:----|:----|
|Bekræftet| Azure AD-forbindelse har fundet en overensstemmende bekræftet domæne i Azure AD. Alle brugere for dette domæne kan logge på ved hjælp af deres lokale legitimationsoplysninger| Ingen handling skal bruges |
|Ikke godkendt| Azure AD-forbindelse blev fundet et tilsvarende brugerdefinerede domæne i Azure AD, men det er ikke godkendt. UPN-suffiks for brugerne, for dette domæne, ændres til standard. onmicrosoft.com suffiks efter synkronisering, hvis domænet ikke er godkendt. | Bekræfte dit eget domæne i Azure AD. [Lær mere](./active-directory-add-domain.md#verify-the-domain-name-with-azure-ad) |  
|Ikke tilføjet| Azure AD-forbindelse blev ikke fundet et brugerdefineret domæne, der svarer til UPN-suffikset. UPN-suffiks brugere fra dette domæne, ændres til standard. onmicrosoft.com, hvis domænet ikke er tilføjet og verifeid i Azure.| Tilføje og bekræfte et brugerdefineret domæne, der svarer til UPN-suffikset [Få mere at vide](./active-directory-add-domain.md)|

Azure AD-logon side viser den UPN suffix(s), der er defineret for lokale Active Directory og det tilsvarende brugerdefinerede domæne i Azure AD med den aktuelle bekræftelsesstatus. I brugerdefineret installation, kan du nu vælge attributten for brugerens hovednavn på siden **Azure AD - logon** .

![Azure AD-logon side](./media/active-directory-aadconnect-user-signin/custom_azure_sign_in.png)

Du kan klikke på opdateringsknappen til at hente den seneste status for de brugerdefinerede domæner fra Azure AD igen.

###<a name="selecting-attribute-for-user-principal-name-in-azure-ad"></a>Valg af attribut for brugerens hovednavn i Azure AD

UserPrincipalName - attribut userPrincipalName er attributten brugere anvender, når de logon på Azure AD og Office 365. Domænerne brugt, også kaldet UPN-suffikset, skal være godkendt i Azure AD, før brugerne, der synkroniseres. Det anbefales at holde standard attribut userPrincipalName. Hvis denne egenskab er ikke-distribueret og ikke kan bekræftes, så det er muligt at vælge en anden attribut, for eksempel mail, som den attribut, holde det logon-ID. Dette kaldes alternative-ID. Alternative id-attributværdien skal følge RFC822 standard. Et alternativt ID kan bruges med både adgangskode enkelt enkeltlogon (SSO) og sammenslutning SSO som logon-løsning.

>[AZURE.NOTE] Ved hjælp af et alternativt ID er ikke kompatibel med alle arbejdsbelastninger, som Office 365. Se [Konfigurere alternative logon-ID](https://technet.microsoft.com/library/dn659436.aspx)kan finde flere oplysninger.

#### <a name="different-custom-domain-states-and-effect-on-azure-sign-in-experience"></a>Forskellige brugerdefinerede domæne stater og indvirkning på Azure logonoplevelse
Det er meget vigtigt at forstå relationen mellem de brugerdefinerede domæ stater i Azure AD-biblioteket og UPN suffikser defineret lokalt. Lad os gå gennem de forskellige mulige Azure-logon oplevelser, når du indstiller sycnhronization ved hjælp af Azure AD Connnect.

Oplysningerne nedenfor, Lad os antage, at vi har pågældende med UPN-suffiks contoso.com, der bruges i det lokale bibliotek som en del af UPN, for eksempel user@contoso.com.

###### <a name="express-settings--password-synchronization"></a>Hurtig konfiguration / synkronisering af adgangskoder
| Stat         | Indvirkning på brugeroplevelsen Azure-logon |
|:-------------:|:----------------------------------------|
| Ikke tilføjet | I dette tilfælde er blevet tilføjet ingen brugerdefineret domæne til contoso.com i Azure AD-mappe. Brugere, der har UPN lokalt med suffiks @contoso.com, vil ikke kunne bruge deres lokale UPN til at logge på på Azure. De får i stedet bruge et nyt UPN har modtaget fra Azure AD ved at føje suffiks til standardmappen Azure AD. Eksempelvis hvis du synkroniserer brugere til Azure AD directory azurecontoso.onmicrosoft.com derefter den lokale bruger user@contoso.com får et UPN afuser@azurecontoso.onmicrosoft.com|
| Ikke godkendt | I dette tilfælde har vi et brugerdefineret domæne contoso.com tilføjet i Azure AD directory, men det ikke er endnu har bekræftet. Hvis du går videre med synkronisering af brugere uden ved at bekræfte domænet, skal derefter brugerne tildeles en ny UPN ved Azure AD samme måde, som den gjorde i scenariet ikke tilføjet.|
| Bekræftet | I dette tilfælde har vi et brugerdefineret domæne contoso.com allerede har tilføjet og bekræftes i Azure AD for UPN-suffikset. Brugerne kan bruge deres lokale brugerens hovednavn, f.eks. user@contoso.com, til logon på Azure når de er synkroniseret til Azure AD|

###### <a name="ad-fs-federation"></a>AD FS sammenslutning
Du kan ikke oprette en sammenslutning med standardindstillingerne. onmicrosoft.com-domæne i Azure AD eller en ikke-bekræftede brugerdefinerede domæne i Azure AD. Når du kører guiden Azure AD-forbindelse, hvis du vælger en ikke-bekræftede domæne til at oprette får en sammenslutning med derefter Azure AD-forbindelse du nødvendige poster, der skal oprettes, hvor din DNS hostet for domænet. Finde flere oplysninger under [her](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Hvis du har valgt brugerindstilling Log på som "Sammenslutning med AD FS", skal du have et brugerdefineret domæne for at fortsætte med at oprette en sammenslutning i Azure AD. Det betyder, bør vi har et brugerdefineret domæne contoso.com tilføjet i Azure AD directory til vores diskussion.

| Stat         | Indvirkning på brugeroplevelsen Azure-logon |
|:-------------:|:----------------------------------------|
| Ikke tilføjet | I dette tilfælde blev Azure AD-forbindelse for ikke fundet et tilsvarende brugerdefineret domæne UPN-suffiks contoso.com i Azure AD-mappe. Skal du tilføje et brugerdefineret domæne contoso.com, hvis du har brug for brugere skal logge på ved hjælp af AD FS med deres lokale UPN som user@contoso.com.|
| Ikke godkendt | I dette tilfælde får Azure AD-forbindelse du relevante oplysninger om, hvordan du kan bekræfte dit domæne på et senere tidspunkt|
| Bekræftet | I dette tilfælde kan du gå videre med konfiguration uden en yderligere handling|  

## <a name="changing-user-sign-in-method"></a>Ændre bruger-logon metode

Du kan ændre metoden bruger-logon fra sammenslutning til synkronisering af adgangskoder ved hjælp af opgaver avaialble i Azure AD-forbindelse efter den indledende konfiguration af Azure AD-forbindelse ved hjælp af guiden. Køre guiden Azure AD-forbindelse igen, og du vil få vist en liste over opgaver, som du kan udføre. Vælg **Skift bruger logon** på listen over opgaver. 

![Ændre brugerlogon"](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

På den næste side, bliver du bedt om at angive legitimationsoplysninger for Azure AD.

![Oprette forbindelse til Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

Vælg **Synkronisering af adgangskoder**, på siden **bruger - logon** . Dette ændrer mappen fra knyttet til en administreret.

![Oprette forbindelse til Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin3.png)

>[AZURE.NOTE] Hvis du laver en midlertidig parameter til synkronisering af adgangskoder, skal du kontrollere, **skal du ikke konvertere brugerkonti**. Ikke markere indstillingen vil føre til konvertering af hver enkelt bruger til del af et organisationsnetværk, og det kan tage flere timer.
  
## <a name="next-steps"></a>Næste trin
Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).

Få mere at vide om [Azure AD-forbindelse: designe begreber](active-directory-aadconnect-design-concepts.md)


