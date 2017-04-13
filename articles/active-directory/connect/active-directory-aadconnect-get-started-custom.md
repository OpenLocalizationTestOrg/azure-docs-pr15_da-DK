<properties
    pageTitle="Azure AD-forbindelse: Brugerdefineret installation | Microsoft Azure"
    description="Dette dokument indeholder oplysninger om de brugerdefinerede installationsindstillinger til Azure AD-forbindelse. Brug disse instruktioner til at installere Active Directory via Azure AD-forbindelse."
    services="active-directory"
    keywords="Hvad er Azure AD-forbindelse, skal du installere Active Directory, nødvendige komponenter til Azure AD"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/13/2016"
    ms.author="billmath"/>

# <a name="custom-installation-of-azure-ad-connect"></a>Brugerdefineret installation af Azure AD-forbindelse
Azure AD Connect **brugerdefinerede indstillinger for** bruges, når du vil have flere indstillinger for installationen. Det bruges, hvis du har flere områder, eller hvis du vil konfigurere valgfrie funktioner, der ikke er omfattet i udtrykkelig installationen. Det bruges i alle tilfælde, hvor indstillingen [**Hurtig installation**](active-directory-aadconnect-get-started-express.md) ikke opfylder dine installation eller topologi.

Før du går i gang med at installere Azure AD-forbindelse, Sørg for at [hente Azure AD-forbindelse](http://go.microsoft.com/fwlink/?LinkId=615771) , og Fuldfør trinnene i den foreløbige nødvendige [Azure AD-forbindelse: Hardware og forudsætninger, der er](../active-directory-aadconnect-prerequisites.md). Sørg også for konti, der er tilgængelige som beskrevet i [Azure AD-forbindelse konti og tilladelser](active-directory-aadconnect-accounts-permissions.md), der kræves.

Hvis tilpassede indstillinger for stemmer ikke overens topologien for eksempel for at opgradere DirSync, se [relateret dokumentation](#related-documentation) for andre scenarier.

## <a name="custom-settings-installation-of-azure-ad-connect"></a>Indstillinger for brugerdefineret installation af Azure AD-forbindelse

### <a name="express-settings"></a>Hurtig konfiguration
Klik på **Tilpas** for at starte en tilpassede indstillinger for installation på denne side.

### <a name="install-required-components"></a>Installere påkrævede komponenter
Når du installerer synkroniseringstjenester, kan du lade sektionen valgfri konfiguration ikke er markeret, og Azure AD-forbindelse konfigurerer alt automatisk. Det opretter en forekomst af SQL Server 2012 Express LocalDB, oprette de relevante grupper og tildele tilladelser. Hvis du vil ændre standardindstillingerne, kan du bruge tabellen nedenfor til at forstå de valgfrie konfigurationsindstillinger, der er tilgængelige.

![Påkrævede komponenter](./media/active-directory-aadconnect-get-started-custom/requiredcomponents.png)

Valgfri konfiguration  | Beskrivelse
------------- | -------------
Brug en eksisterende SQL Server | Kan du angive navnet på SQL Server og navnet på forekomsten. Vælg denne indstilling, hvis du allerede har en databaseserver, du vil bruge. Angiv navnet på forekomsten, efterfulgt af et komma og port tal i **Navnet på forekomsten** , hvis din SQL Server ikke har gennemsyn er aktiveret.
Brug en eksisterende tjenestekonto | Azure AD-forbindelse opretter en lokal tjenestekonto for synkronisering services til at bruge som standard. Adgangskoden er oprettet automatisk og ukendt til den person, der installerer Azure AD-forbindelse. Hvis du bruger en ekstern SQL server eller bruge en proxy, der kræver godkendelse, du har brug for en tjeneste konto i domænet og kender adgangskoden. Angiv service-kontoen, der skal bruges i disse tilfælde. Kontrollér, at den bruger, der kører installationen er en Systemadministratorens i SQL, så du kan oprette et logon til service-kontoen. Se [Azure AD-forbindelse konti og tilladelser](active-directory-aadconnect-accounts-permissions.md#custom-settings-installation)
Angive brugerdefinerede synkronisering | Som standard opretter Azure AD-forbindelse fire grupper lokale til serveren når synkronisering services er installeret. Disse grupper er: administratorgruppen, operatorer gruppen, gennemse gruppe og gruppen Nulstil adgangskode. Du kan angive dine egne grupper her. Grupperne skal være lokalt på serveren og kan ikke være placeret i domænet.

### <a name="user-sign-in"></a>Bruger-logon
Når du har installeret de påkrævede komponenter, bliver du bedt om at vælge brugere enkelt sign-on – metode. Den følgende tabel indeholder en kort beskrivelse af de tilgængelige indstillinger. Se [bruger - logon](../active-directory-aadconnect-user-signin.md)til en fuldstændig beskrivelse af de logonmetoder.

![Bruger-logon](./media/active-directory-aadconnect-get-started-custom/usersignin.png)

Enkelt Sign-On-indstilling | Beskrivelse
------------- | -------------
Synkronisering af adgangskoder | Brugerne kan logge på Microsoft cloud services, som Office 365, ved hjælp af den samme adgangskode, de bruger i deres lokale netværk. Brugere adgangskoderne synkroniseres til Azure AD som en adgangskode hash og godkendelse forekommer i skyen. Du kan finde flere oplysninger i [synkronisering af adgangskoder](../active-directory-aadconnectsync-implement-password-synchronization.md) .
Sammenslutning med AD FS | Brugerne kan logge på Microsoft cloud services, som Office 365, ved hjælp af den samme adgangskode, de bruger i deres lokale netværk.  Brugerne er omdirigeret til deres lokale AD FS forekomst for at logge på og godkendelse forekommer i det lokale miljø.
Ikke Konfigurer | Hverken funktion er installeret og konfigureret. Vælg denne indstilling, hvis du allerede har en 3 part sammenslutningsserveren eller en anden eksisterende løsning på plads.

### <a name="connect-to-azure-ad"></a>Oprette forbindelse til Azure AD
Angiv en global administrator og en adgangskode på siden Opret forbindelse til Azure AD skærm. Hvis du har valgt **sammenslutning med AD FS** på den forrige side, skal ikke logge på med en konto i et domæne, du vil aktivere til sammenslutning. En anbefaling er at bruge en konto i standard **onmicrosoft.com** -domæne, som følger med din Azure AD-mappe.

Denne konto bruges kun til at oprette en tjenestekonto i Azure AD og bruges ikke, når guiden er fuldført.  
![Bruger-logon](./media/active-directory-aadconnect-get-started-custom/connectaad.png)

Hvis din global administrator har aktiveret MFA, skal du angive adgangskoden igen i pop op-logon til og fuldføre MFA udfordringen. Udfordringen kan være en udbyder en bekræftelseskode eller et telefonopkald.  
![MFA bruger-logon](./media/active-directory-aadconnect-get-started-custom/connectaadmfa.png)

Global administrator kan også have [Privilegerede identitet Management](../active-directory-privileged-identity-management-getting-started.md) aktiveret.

Hvis du modtager en fejl og har problemer med forbindelse, kan du se [fejlfinding af forbindelsesproblemer](../active-directory-aadconnect-troubleshoot-connectivity.md).

## <a name="pages-under-the-section-sync"></a>Sider under afsnittet synkronisering

### <a name="connect-your-directories"></a>Forbinde dine adresselister
Hvis du vil oprette forbindelse til din Active Directory Domain Service, skal Azure AD-forbindelse legitimationsoplysninger for en konto med de nødvendige tilladelser. Du kan angive delen domæne i NetBios eller fulde Domænenavn, det vil sige, FABRIKAM\syncuser eller fabrikam.com\syncuser. Denne konto kan være en almindelig brugerkonto, fordi de skal kun få standardtilladelser. Men afhængigt af scenariet, du skal muligvis flere tilladelser. Du kan finde flere oplysninger, se [Azure AD forbinde konti og tilladelser](../active-directory-aadconnect-accounts-permissions.md#create-the-ad-ds-account)

![Oprette forbindelse Directory](./media/active-directory-aadconnect-get-started-custom/connectdir.png)

### <a name="azure-ad-sign-in-configuration"></a>Azure AD-logon konfiguration
Denne side kan du gennemgå findes i lokale UPN domæner AD DS og som er blevet godkendt i Azure AD. Denne side kan du konfigurere attributten skal bruges til userPrincipalName.

![Ikke-bekræftede domæner](./media/active-directory-aadconnect-get-started-custom/aadsigninconfig.png)  
Gennemgå alle domæner, der er markeret **Ikke tilføjet** og **Ikke godkendt**. Kontrollér, at disse domæner, du bruger er blevet godkendt i Azure AD. Klik på Opdater symbolet, når du har bekræftet dit domæne. Se [tilføje og bekræfte domænet](../active-directory-add-domain.md) kan finde flere oplysninger

**UserPrincipalName** - attribut userPrincipalName er attribut brugerne bruge, når de logger på Azure AD og Office 365. Domænerne brugt, også kaldet UPN-suffikset, skal være godkendt i Azure AD, før brugerne, der synkroniseres. Microsoft anbefaler at beholde standard attribut userPrincipalName. Hvis denne egenskab er ikke-distribueret og ikke kan bekræftes, er det muligt at vælge en anden attribut. Du kan for eksempel vælge mail som attributten holder det logon-ID. Ved hjælp af en anden attribut end userPrincipalName er kendt som **Alternativ-ID**. Alternative id-attributværdien skal følge RFC822 standard. Et alternativt ID kan bruges med både synkronisering af adgangskoder og sammenslutning.

>[AZURE.WARNING]
Ved hjælp af et alternativt ID er ikke kompatibel med alle arbejdsbelastninger, som Office 365. Få mere at vide henvise til [Konfiguration af alternative logon-ID](https://technet.microsoft.com/library/dn659436.aspx).

### <a name="domain-and-ou-filtering"></a>Domæne og OU filtrering
Som standard synkroniseres alle domæner og afdelinger. Hvis der er nogle domæner eller afdelinger, du ikke vil synkronisere med Azure AD, kan du fjerne markeringen af disse domæner og afdelinger.  
![Filtrering af DomainOU](./media/active-directory-aadconnect-get-started-custom/domainoufiltering.png) denne side i guiden konfigurerer domæne-baseret filtrering. Se [domæne-baseret filtrering](../active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering)kan finde flere oplysninger.

Det er også muligt, at alle domæner, ikke er tilgængelig på grund af firewall begrænsninger. Disse domæner er ikke valgt som standard og har en advarsel.  
![Er utilgængelig domæner](./media/active-directory-aadconnect-get-started-custom/unreachable.png)  
Hvis du får vist en advarsel, Sørg for, at disse domæner er faktisk er utilgængelig og advarslen forventes.

### <a name="uniquely-identifying-your-users"></a>Entydigt identificerer dine brugere
Sammenholdelsespolitik på tværs af områder funktion gør det muligt at definere, hvordan brugere fra din Active Directory-Domænetjenester områder er repræsenteret i Azure AD. En bruger enten muligvis repræsenteret kun én gang på tværs af alle områder eller har en kombination af aktiverede og deaktiverede konti. Brugeren kan også repræsenteres som en kontakt i nogle områder.

![Entydige](./media/active-directory-aadconnect-get-started-custom/unique.png)

Indstilling | Beskrivelse
------------- | -------------
[Brugere er kun repræsenteret én gang på tværs af alle områder](../active-directory-aadconnect-topologies.md#multiple-forests-separate-topologies) | Alle brugere er oprettet som individuelle objekter i Azure AD. Objekterne, der er ikke tilsluttet metaverse.
[Mail-attribut](../active-directory-aadconnect-topologies.md#multiple-forests-full-mesh-with-optional-galsync) | Denne indstilling sammenkæder brugere og kontaktpersoner, hvis attributten mail har samme værdi i forskellige områder. Brug denne indstilling, når kontaktpersonerne er blevet oprettet ved hjælp af GALSync.
[ObjectSID og msExchangeMasterAccountSID / msRTCSIP-OriginatorSid](../active-directory-aadconnect-topologies.md#multiple-forests-account-resource-forest) | Denne indstilling sammenkæder en aktiveret bruger i et område, konto med en deaktiveret bruger i en ressourceskov. I Exchange kaldes denne konfiguration en sammenkædet postkasse. Denne indstilling kan også bruges, hvis du kun bruger Lync og Exchange er ikke til stede i området ressource.
sAMAccountName og MailNickName | Denne indstilling sammenkæder på attributter, hvor det er meningen, logon-ID for brugeren, du kan finde.
En bestemt attribut | Denne indstilling kan du vælge din egen attribut. **Begrænsning:** Sørg for at vælge en attribut, der allerede findes i metaverse. Hvis du vælger en brugerdefineret attribut (ikke i metaverse), kan du udføre guiden.

**Kilde anker** - attribut sourceAnchor er en attribut, der er fast i et brugerobjekt levetid. Det er den primære nøgle sammenkædning lokal bruger med bruger i Azure AD. Da attributten ikke kan ændres, skal du planlægge en god attribut, der skal bruge. En velegnet er objectGUID. Denne attribut ændres ikke, medmindre brugerkontoen er flyttet mellem områder/domæner. I et med flere områder-miljø, hvor du flytter konti mellem områder, skal en anden attribut anvendes, som en attribut med medarbejder-id. Undgå attributter, der skal ændres, når en person tjenestemanden eller ændre tildelinger. Du kan ikke bruge attributter med en @-sign, så mail og userPrincipalName ikke kan bruges. Attributten er også store og små bogstaver så når du flytter et objekt mellem områder, skal du sørge for at bevare de små og store bogstaver. Binær attributter er base64-kodet, men andre attributter typer forbliver i den kodet tilstand. Denne attribut er også kendt som immutableID i sammenslutning scenarier og nogle Azure AD-grænseflader. Kan finde flere oplysninger om datakilde anker i [designbegreber](../active-directory-aadconnect-design-concepts.md#sourceAnchor).

### <a name="sync-filtering-based-on-groups"></a>Synkroniser filtrering baseret på grupper
Filtrering på grupper funktion gør det muligt at synkronisere en lille del af objekter til et pilotprojekt. Hvis du vil bruge denne funktion, skal du oprette en gruppe hertil i din lokale Active Directory. Tilføj derefter brugere og grupper, der skal synkroniseres til Azure AD som direkte medlemmer. Du kan senere tilføje og fjerne brugere fra denne gruppe til at bevare på listen over objekter, der skal være til stede i Azure AD. Alle objekter, du vil synkronisere skal være et direkte medlem af gruppen. Brugere, grupper, kontakter og computere/enheder skal alle direkte medlemmer. Indlejrede gruppemedlemskab ikke er blevet løst. Når du føjer en gruppe, der føjes et medlem, kun gruppen selve og ikke dets medlemmer.

![Synkroniser filtrering](./media/active-directory-aadconnect-get-started-custom/filter2.png)

>[AZURE.WARNING]
Denne funktion er kun beregnet til at understøtte en distribution af forsøg. Ikke bruge det i et fuldt udviklede produktionsmiljø.

I et fuldt udviklede produktionsmiljø skal det være svært at bevare en enkelt gruppe med alle objekter til at synkronisere. I stedet skal du bruge en af metoderne i [Konfigurer filtrering](../active-directory-aadconnectsync-configure-filtering.md).

### <a name="optional-features"></a>Valgfrie funktioner
Dette skærmbillede kan du vælge de valgfrie funktioner til din specifikke scenarier.

![Valgfrie funktioner](./media/active-directory-aadconnect-get-started-custom/optional.png)

>[AZURE.WARNING]
Hvis du har i øjeblikket DirSync eller Azure Active Directory-synkronisering aktive, har ikke aktiveret nogen af tilbageførsel funktioner i Azure AD-forbindelse.

Valgfrie funktioner | Beskrivelse
------------------- | -------------
Exchange-Hybridinstallation | Funktionen Exchange-Hybridinstallation, der giver mulighed for samtidig eksistensen af Exchange-postkasser både lokalt og i Office 365. Azure AD-forbindelse synkronisering af en bestemt række [attributter](../active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) fra Azure AD tilbage til din lokale mappe.
Azure AD-app og filtrering af attribut | Ved at aktivere Azure AD app og attributten filtrering, kan sæt synkroniserede attributter tilpasses. Denne indstilling føjer to flere konfiguration sider med guiden. Se [Azure AD app og attributten filtrering](#azure-ad-app-and-attribute-filtering)kan finde flere oplysninger.
Synkronisering af adgangskoder | Hvis du har valgt sammenslutning som løsningen logon, kan du aktivere denne indstilling. Synkronisering af adgangskoder kan derefter bruges som en ekstra indstilling. Yderligere oplysninger finder du under [synkronisering af adgangskoder](../active-directory-aadconnectsync-implement-password-synchronization.md).
Adgangskode tilbageførsel | Ved at aktivere adgangskode tilbageførsel, skrives ændringer af adgangskode, der stammer fra Azure AD tilbage til din lokale mappe. Du kan finde flere oplysninger i [Introduktion til administration af adgangskoder](../active-directory-passwords-getting-started.md).
Gruppen tilbageførsel | Hvis du bruger funktionen **Office 365-grupper** , kan du få disse grupper, der er repræsenteret i din lokale Active Directory. Denne indstilling er kun tilgængelig, hvis du har Exchange findes i din lokale Active Directory. Du kan finde yderligere oplysninger finder [gruppe der ikke er gemt](../active-directory-aadconnect-feature-preview.md#group-writeback).
Enhed tilbageførsel | Gør muligt at tilbageførsel enhedsobjekter i Azure AD til din lokale Active Directory for betinget access scenarier. Du kan finde yderligere oplysninger finder [aktivering enhed tilbageførsel i Azure AD-forbindelse](../active-directory-aadconnect-feature-device-writeback.md).
Attributten katalogsynkronisering lokalnummer | Når du aktiverer udvidelser attribut katalogsynkronisering, synkroniseres attributter, der er angivet til Azure AD. Du kan finde yderligere oplysninger finder [Directory filtypenavne](../active-directory-aadconnectsync-feature-directory-extensions.md).

### <a name="azure-ad-app-and-attribute-filtering"></a>Azure AD-app og filtrering af attribut
Hvis du vil begrænse hvilke attributter til at synkronisere med Azure AD, derefter i gang ved at vælge hvilke tjenester du bruger. Hvis du foretager ændringer i konfigurationen på denne side, har en ny tjeneste skal være markeret eksplicit ved at køre installationsguiden.

![Valgfrie funktioner Apps](./media/active-directory-aadconnect-get-started-custom/azureadapps2.png)

Denne side vises baseret på de tjenester, der er valgt i det forrige trin, alle de attributter, der er synkroniseret. Denne liste er en kombination af alle objekttyper synkroniseres. Hvis der er nogle bestemte attributter, du skal ikke synkronisere, kan du fjerne markeringen af disse attributter.

![Valgfrie funktioner attributter](./media/active-directory-aadconnect-get-started-custom/azureadattributes2.png)

>[AZURE.WARNING]
Fjerne attributter, kan det påvirke funktionaliteten. Bedste fremgangsmåder og anbefalinger, under [attributter synkroniseres](../active-directory-aadconnectsync-attributes-synchronized.md#attributes-to-synchronize).

### <a name="directory-extension-attribute-sync"></a>Attributten katalogsynkronisering lokalnummer
Du kan udvide skemaet i Azure AD med brugerdefinerede attributter, der er tilføjet af din organisation eller andre attributter i Active Directory. Hvis du vil bruge denne funktion, Vælg **Directory lokalnummer attribut Synkroniser** på siden **Valgfrie funktioner** . Du kan vælge flere attributter, der skal synkroniseres på denne side.

![Directory filtypenavne](./media/active-directory-aadconnect-get-started-custom/extension2.png)

Du kan finde yderligere oplysninger finder [Directory filtypenavne](../active-directory-aadconnectsync-feature-directory-extensions.md).

## <a name="configuring-federation-with-ad-fs"></a>Konfigurere sammenslutning med AD FS
Det er nemt med blot nogle få klik at konfigurere AD FS med Azure AD-forbindelse. Følgende er påkrævet før konfigurationen.

- En Windows Server 2012 R2 server til sammenslutningsserveren med fjernadministration aktiveret
- En Windows Server 2012 R2 server til Web Application Proxy-server med fjernadministration aktiveret
- Et SSL-certifikat til navnet på tjenesten sammenslutning, du vil bruge (for eksempel sts.contoso.com)

### <a name="ad-fs-configuration-pre-requisites"></a>AD FS konfiguration forudsætninger
For at konfigurere din AD FS-farm ved hjælp af Azure AD-forbindelse skal du sørge WinRM er aktiveret på de eksterne servere. Desuden gennemfører porte kravet er angivet i [tabel 3 - Azure AD-forbindelse og sammenslutning servere/WAP](../active-directory-aadconnect-ports.md#table-3---azure-ad-connect-and-federation-serverswap).

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Oprette en ny AD FS-farm eller bruge en eksisterende AD FS-farm
Du kan bruge en eksisterende AD FS-farm, eller du kan vælge at oprette en ny AD FS-farm. Hvis du vælger at oprette en ny, skal du angive SSL-certifikat. Hvis SSL-certifikatet er beskyttet med adgangskode, kan du bliver bedt om adgangskoden.

![AD FS Farm](./media/active-directory-aadconnect-get-started-custom/adfs1.png)

Hvis du vælger at bruge en eksisterende AD FS-farm, kommer du direkte til konfiguration af tillidsforhold mellem AD FS og Azure AD skærm.

### <a name="specify-the-ad-fs-servers"></a>Angiv AD FS-serverne
Angiv de servere, du vil installere AD FS på. Du kan føje en eller flere servere, der er baseret på din kapacitet, planlægning behov. Deltage i alle servere til Active Directory, før du udfører denne konfiguration. Microsoft anbefaler at installere en enkelt AD FS-server til test og pilotprojekt installationer. Derefter tilføje og installere flere servere at opfylde dine skalering behov ved at køre Azure AD-forbindelse igen efter den indledende konfiguration.

>[AZURE.NOTE]
Sørg for, at alle serverne er tilsluttet en AD-domæne, før du gør denne konfiguration.

![AD FS-servere](./media/active-directory-aadconnect-get-started-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>Angive Web Application proxyservere
Angiv de servere, der skal være din webprogram proxyservere. Web application proxyserver er installeret i din DMZ (ekstranet ned) og understøtter anmodninger om godkendelse fra den ekstranet. Du kan føje en eller flere servere, der er baseret på din kapacitet, planlægning behov. Microsoft anbefaler at installere en enkelt Web application proxyserver til test og pilotprojekt installationer. Derefter tilføje og installere flere servere at opfylde dine skalering behov ved at køre Azure AD-forbindelse igen efter den indledende konfiguration. Vi anbefaler, at du har en tilsvarende antal proxyservere at tilfredsstille godkendelse fra intranettet.

>[AZURE.NOTE]
<li> Hvis den konto, du bruger ikke er en lokal administrator på AD FS-servere, bliver du bedt om legitimationsoplysninger.</li>
<li> Sørg for, at der er HTTP/HTTPS forbindelsen mellem Azure AD-forbindelse serveren og Web Application Proxy-server, før du udfører dette trin.</li>
<li> Sørg for, at der er HTTP/HTTPS forbindelsen mellem Web Application Server og AD FS-server for at tillade anmodninger om brugergodkendelse passerer gennem.</li>

![WebApp](./media/active-directory-aadconnect-get-started-custom/adfs3.png)

Du bliver bedt om at angive legitimationsoplysninger, så web application server kan oprette en sikker forbindelse til AD FS-serveren. Disse legitimationsoplysninger skal være en lokal administrator på AD FS-serveren.

![Proxy](./media/active-directory-aadconnect-get-started-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>Angiv servicekonto for tjenesten AD FS
Tjenesten AD FS kræver en domæne tjenestekonto til godkendelse af brugere og opslag brugeroplysninger i Active Directory. Det kan understøtte to typer tjenestekonti:

- **Gruppen administrerede tjenestekonto** - introduceret i Active Directory-domænetjenester med Windows Server 2012. Denne type konto indeholder tjenester, såsom AD FS, en enkelt konto uden at behøve at opdatere adgangskoden til kontoen jævnligt. Brug denne indstilling, hvis du allerede har Windows Server 2012 domæne enheder i det domæne, serverne AD FS tilhører.
- **Domænebrugerkonto** – denne type konto, skal du angive en adgangskode og opdateres regelmæssigt adgangskoden, når adgangskoden ændres eller udløber. Brug denne indstilling, kun, når du ikke har Windows Server 2012 domæne enheder i det domæne, serverne AD FS tilhører.

Hvis du har valgt gruppe administrerede tjenestekonto, og denne funktion aldrig er blevet brugt i Active Directory, bliver du bedt om Enterprise Administratoroplysninger. Disse legitimationsoplysninger anvendes til at starte de vigtigste store og aktivere funktionen i Active Directory.

![AD FS-tjenestekonto](./media/active-directory-aadconnect-get-started-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-wish-to-federate"></a>Vælg det Azure AD-domæne, du vil samle
Denne konfiguration bruges til at konfigurere sammenslutning relationen mellem AD FS og Azure AD. Det konfigurerer AD FS til problem sikkerhedstokens til Azure AD og konfigurerer Azure AD for at oprette tillid tokens ud fra denne bestemte forekomst af AD FS. Denne side kan kun muligt at konfigurere et enkelt domæne i den indledende installation. Du kan konfigurere flere domæner senere ved at køre Azure AD-forbindelse igen.

![Azure AD-domæne](./media/active-directory-aadconnect-get-started-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Bekræft det Azure AD-domæne, der er valgt til sammenslutning
Når du vælger domænet til at være del af et organisationsnetværk, giver Azure AD-forbindelse nødvendige oplysninger til at bekræfte en ikke-bekræftede domæne. Se [Tilføj og bekræfte domænet](../active-directory-add-domain.md) til, hvordan du kan bruge disse oplysninger.

![Azure AD-domæne](./media/active-directory-aadconnect-get-started-custom/verifyfeddomain.png)

>[AZURE.NOTE]
AD-forbindelse forsøger at bekræfte domænet under Konfigurer fase. Hvis du fortsætter med at konfigurere uden at føje de nødvendige DNS-poster, er guiden er ikke muligt at fuldføre konfigurationen.

## <a name="configure-and-verify-pages"></a>Konfigurere og kontrollere sider
Konfigurationen sker på denne side.

>[AZURE.NOTE]
Før du fortsætter med installationen, og hvis du har konfigureret sammenslutning, Sørg for, at du har konfigureret [navneoversættelse for sammenslutningsservere](../active-directory-aadconnect-prerequisites.md#name-resolution-for-federation-servers).

![Klar til at konfigurere](./media/active-directory-aadconnect-get-started-custom/readytoconfigure2.png)

### <a name="staging-mode"></a>Midlertidige tilstand
Det er muligt at konfigurere en ny synkronisering server parallelt med arrangere tilstand. Det understøttes kun for at få en synkronisering server eksporterer til en mappe i skyen. Men hvis du vil flytte fra en anden server, for eksempel en igangværende DirSync kan du aktivere Azure AD-forbindelse i arrangere tilstand. Når aktiveret, Synkroniser program importere og synkronisere data som normalt, men det eksporterer ikke noget til Azure AD eller AD. Funktioner adgangskode synkronisering og adgangskode tilbageførsel er deaktiveret, mens i arrangere tilstand.

![Midlertidige tilstand](./media/active-directory-aadconnect-get-started-custom/stagingmode.png)

Det er muligt at foretage nødvendige ændringer i programmet til synkronisering og gennemse, hvad der eksporteres i midlertidige tilstand. Når konfigurationen ser rigtigt ud, kører installationsguiden af igen, og Deaktiver midlertidige tilstand. Data er nu eksporteres til Azure AD fra denne server. Sørg for at deaktivere anden serveren på samme tid, så kun én server aktivt eksport.

Du kan finde yderligere oplysninger finder [midlertidige tilstand](../active-directory-aadconnectsync-operations.md#staging-mode).

### <a name="verify-your-federation-configuration"></a>Bekræfte din sammenslutning konfiguration
Azure AD-forbindelse kontrollerer DNS-indstillingerne for dig, når du klikker på knappen Bekræft.

![Fuldføre](./media/active-directory-aadconnect-get-started-custom/completed.png)

![Bekræfte](./media/active-directory-aadconnect-get-started-custom/adfs7.png)

Desuden kan du gøre følgende bekræftelse:

- Validere, kan du logge på fra en browser fra et domæne joinforbundne maskine på intranettet: oprette forbindelse til https://myapps.microsoft.com og kontrollere logge på med din logget på konto. Den indbyggede AD DS administratorkonto ikke er synkroniseret og kan ikke bruges til bekræftelse.
- Valider, kan du logge på fra en enhed fra den ekstranet. På en privat computer eller en mobilenhed, skal du oprette forbindelse til https://myapps.microsoft.com og angive dine legitimationsoplysninger.
- Valider omfattende klient-logon. Oprette forbindelse til https://testconnectivity.microsoft.com skal du vælge fanen **Office 365** , og vælg en **Office 365 enkelt Sign-On-Test**.

## <a name="next-steps"></a>Næste trin
Når installationen er fuldført, du logger af og log på igen for at Windows før du kan bruge synkronisering Service Manager eller synkronisering regel Editor.

Nu hvor du har installeret Azure AD-forbindelse kan du [kontrollere installationen og tildele licenser](../active-directory-aadconnect-whats-next.md).

Lær mere om disse funktioner, som er blevet aktiveret til installationen: [Undgå utilsigtede sletninger](../active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) og [Azure AD forbinde tilstand](../active-directory-aadconnect-health-sync.md).

Få flere oplysninger om disse almindelige emner: [scheduler og hvordan du kan udløse synkronisering](../active-directory-aadconnectsync-feature-scheduler.md).

Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](../active-directory-aadconnect.md).

## <a name="related-documentation"></a>Relateret dokumentation

Emne |  
--------- | ---------
Oversigt over Azure AD-forbindelse | [Integrere dine lokale identiteter med Azure Active Directory](../active-directory-aadconnect.md)
Installere ved hjælp af indstillinger for Express | [Hurtig installation af Azure AD-forbindelse](active-directory-aadconnect-get-started-express.md)
Opgradering fra DirSync | [Opgradere fra Azure AD-Synkroniseringsværktøj (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)
Konti, der bruges til installation | [Flere oplysninger om Azure AD-forbindelse konti og tilladelser](active-directory-aadconnect-accounts-permissions.md)
