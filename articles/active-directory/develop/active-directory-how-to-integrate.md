<properties
   pageTitle="Hvordan du integrerer med Azure Active Directory | Microsoft Azure"
   description="En vejledning til fordelene ved og ressourcer til integration med Azure Active Directory."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="integrating-with-azure-active-directory"></a>Integration med Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory giver organisationer med professionel og administration af identitet til skyen programmer.  Azure AD-integration giver brugerne en strømlinet logonoplevelse og hjælper med at dit program er i overensstemmelse med IT politik.

## <a name="how-to-integrate"></a>Hvordan du integrerer

Der er flere måder for dit program du integrere systemet med Azure AD.  Drage fordel af som mange eller få disse scenarier, som er relevante for dit program.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Understøtter Azure AD som en metode til at logge på dit program

**Reducere logon gnidning og reducere omkostninger til support.** Ved hjælp af Azure AD til at logge på dit program, brugerne ikke har flere navne og din adgangskode til at huske.  Som en udvikler har du en mindre adgangskode til at gemme og beskytte.  Ikke at have til at håndtere glemt nulstilling af adgangskoder, kan det være en alene spare.  Azure AD styrer logon til nogle af verdens mest populære skyen programmer, herunder Office 365 og Microsoft Azure.  Med hundredvis af millioner brugere fra millioner af organisationer, sandsynligvis er brugeren allerede er logget på til Azure AD.  Lær mere om [at tilføje understøttelse af Azure AD-logon](../active-directory-authentication-scenarios.md).

**Forenkle log op for dit program.**  Azure AD kan sende vigtige oplysninger om en bruger under Tilmeld dig for dit program, så du kan udfylde udfyldt din tilmelding formular eller fjerne den helt.  Brugere kan tilmelde sig til dit program ved hjælp af deres Azure AD-konto via en ligner dem i sociale medier og mobilprogrammer velkendte samtykke oplevelse.  Alle brugere kan tilmelde dig og logge på et program, der er integreret med Azure AD uden IT deltagelse.  Lær mere om [signering af dit program til at logge på Azure AD-konto](../../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Gå til brugere, administrere klargøring af brugeren, og styre adgangen til dit program

**Gå til brugere i kataloget.**  Brug Graph API til at hjælpe brugere med at søge og gå til andre personer i organisationen, når du vil invitere andre eller give adgang, i stedet for at kræve dem til at skrive e-mail-adresser.  Brugerne kan gennemse ved hjælp af en velkendte adresse adressekartotek typografi grænseflade, herunder få vist oplysninger om organisatorisk hierarki.  Lær mere om [Graph API](../active-directory-graph-api.md).

**Genbruge Active Directory-grupper og distributionslister allerede administration af din kunde.**  Azure AD indeholder de grupper, kunden allerede bruger til mail fordeling og adgangskontrol.  Brug af Graph-API, igen bruge disse grupper i stedet for at kræve kunderne til at oprette og administrere en separat række grupper i dit program.  Gruppeoplysninger kan også sendes til dit program i sign in tokens.  Lær mere om [Graph API](../active-directory-graph-api.md).

**Brug Azure AD til at styre, hvem der har adgang til dit program.**  Administratorer og ejere af programmet i Azure AD kan tildele adgang til programmer til bestemte brugere og grupper.  Ved hjælp af Graph API, kan du læse denne liste og bruge det til at styre klargøring og deaktivere klargøring af ressourcer og få adgang til dit program.

**Brug Azure AD for roller baseret adgangskontrol.**  Administratorer og ejere af programmet kan tildele brugere og grupper til roller, som du definerer, når du har registreret dit program i Azure AD.  Rolleoplysninger sendes til dit program i sign in tokens og kan også læses ved hjælp af Graph API.  Lær mere om at [bruge Azure AD om tilladelse](http://blogs.technet.com/b/ad/archive/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles.aspx).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Få adgang til brugerens profil, kalender, mail, kontakter, filer og mere

**Azure AD er den tilladelse server til Office 365 og andre tjenester til Microsoft business.**  Hvis du understøtter Azure AD til logon på dit program eller support sammenkædning din aktuelle brugerkonti til Azure AD-brugerkonti ved hjælp af OAuth 2.0, kan du anmode om læse / skrive-adgang for en brugerprofil, kalender, mail, kontakter, filer og andre oplysninger.  Du kan problemfrit skrive begivenheder til brugerens kalender, og læse eller skrive filer til deres OneDrive.  Lær mere om [at få adgang til Office 365-API'er](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Hæve dit program i Azure og Office 365 markeder

**Hæve dit program til millioner af organisationer, der allerede bruger Azure AD.**  Brugere, søge og gennemse disse markedspladser bruger allerede et eller flere skytjenester, hvilket gør dem kvalificerede cloud-kunder.  Lær mere om at fremme dit program i [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Når brugere tilmelde sig for dit program, vises den i deres Azure AD access panel og Office 365-appstarteren.**  Brugerne kan hurtigt og nemt gå tilbage til dit program senere forbedring bruger aftale.  Lær mere om den [Azure AD få adgang til panel](../active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Sikker kommunikation i enhed-tjenesten og tjenester

**Ved hjælp af Azure AD for Identitetsstyring af tjenester og enheder reducerer den kode, skal du skrive og gør det muligt for IT til at administrere adgang.**  Tjenester og enheder kan få tokens fra Azure AD ved hjælp af OAuth og bruge disse tokens til at få adgang til internettet API'er.  Ved hjælp af Azure AD kan du undgå skrive komplekse godkendelse kode.  Da identiteter for tjenester og enheder er gemt i Azure AD IT kan administrere nøgler og tilbagekaldte certifikater på ét sted i stedet for at de skal gøres separat i dit program.

## <a name="benefits-of-integration"></a>Fordele ved Integration

Integration med Azure AD leveres med fordele, der ikke kræver, at du kan skrive yderligere programkode.

### <a name="integration-with-enterprise-identity-management"></a>Integration med Enterprise identitet Management

**Hjælp dit program er i overensstemmelse med IT-politikker.**  Organisationer integrere deres enterprise identitet management-systemer med Azure AD, så når en person forlader en organisation, vil de automatisk miste adgang til dit program uden IT, der er brug at udføre ekstra trin.  IT kan administrere, hvem der kan få adgang til dit program og afgøre, hvilke access politikker er påkrævede - til eksempel Multi-Factor authentication - reducere behovet for at skrive programkode for at overholde komplekse firmapolitikker.  Azure AD giver administratorer en detaljeret overvågningsloggen med hvem logget på dit program så IT kan spore brugen.

**Azure AD udvider Active Directory i skyen, så programmet kan integreres med AD.**  Mange organisationer over hele verden bruger Active Directory som deres vigtigste logon og identitet administrationssystem og kræver deres programmer til at arbejde med AD.  Integration med Azure AD integrerer din app med Active Directory.

### <a name="advanced-security-features"></a>Avancerede sikkerhedsfunktioner

**Multi-Factor authentication.**  Azure AD giver oprindelige Multi-Factor godkendelse.  IT-administratorer kan kræve Multi-Factor authentication til at få adgang til dit program, så du ikke behøver at kode denne understøttelse dig selv.  Lær mere om [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Uoverensstemmende logon registrering.**  Azure AD behandler mere end en milliarder logon dagen, når du bruger machine learning algoritmer til at registrere mistænkelig aktivitet og give besked, IT-administratorer af eventuelle problemer.  Ved at understøtte Azure AD-logon, bliver dit program fordelen ved denne beskyttelse. Få mere at vide om [visning af Azure Active Directory får adgang til rapporten](../active-directory-view-access-usage-reports.md).

**Betinget adgang.**  Ud over Multi-Factor authentication administratorer kan kræve, at bestemte betingelser opfyldes, før brugerne kan logge på, at dit program.  Betingelser, der kan angives omfatter IP-adresseområder klientenheder, medlemskab af angivne grupper og tilstanden for den enhed, der bruges til access.  Lær mere om [betinget Azure Active Directory-adgang](../active-directory-conditional-access.md).

### <a name="easy-development"></a>Nem udvikling

**Branche standard protokoller.**  Microsoft er forpligtet til understøttende branchestandarder.  Azure AD understøtter SAML 2.0, OpenID forbinde 1.0, OAuth 2.0 og WS-Federation 1.2 godkendelsesprotokoller.  Graph API er OData 4.0 kompatibel.  Hvis dit program allerede understøtter SAML 2.0 eller OpenID forbinde 1.0 protokoller for organisationsnetværket logon, kan der være at tilføje understøttelse af Azure AD enkle.  Lær mere om [Azure AD understøttes godkendelsesprotokoller](../active-directory-authentication-protocols.md).

**Åbn kilden biblioteker.**  Microsoft yder fuldt understøttet Åbn kilde biblioteker til populære sprog og platforme til hastighed udvikling.  Koden er givet i licens under Apache 2.0, og du kan frit deler sig og bidrage tilbage til projekterne.  Lær mere om [Azure AD-godkendelse biblioteker](../active-directory-authentication-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Global tilstedeværelse og høj tilgængelighed

**Azure AD er implementeret i datacentre over hele verden og administreres og overvåges rundt.**  Azure AD er administrationssystem identitet til Microsoft Azure og Office 365, og der er implementeret i 28 datacentre over hele verden.  Directory-data er garanti for, at replikeres til mindst tre datacentre.  Global Indlæs balancere sikre, at brugere får adgang til den nærmeste kopi af Azure AD, der indeholder data, og igen omdirigere automatisk anmodninger om at andre datacentre Hvis der registreres et problem.

## <a name="next-steps"></a>Næste trin

[Komme i gang skrive kode](../active-directory-developers-guide.md#getting-started).

[Log brugere på ved hjælp af Azure AD](../active-directory-authentication-scenarios.md)
