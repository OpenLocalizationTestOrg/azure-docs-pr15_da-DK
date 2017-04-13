<properties
    pageTitle="Azure AD-forbindelse: Understøttede topologier | Microsoft Azure"
    description="Dette emne indeholder oplysninger om understøttede og ikke-understøttede topologier til Azure AD-forbindelse"
    services="active-directory"
    documentationCenter=""
    authors="AndKjell"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="billmath"/>

# <a name="topologies-for-azure-ad-connect"></a>Topologier til Azure AD-forbindelse
Formålet med dette emne er til at beskrive forskellige lokale og Azure AD topologier til Azure AD-forbindelse synkronisering som vigtige integrationsløsning. Det beskrives både understøttede og ikke-understøttede konfigurationer.

Forklaring til billederne i dokumentet:

Beskrivelse | Ikonet
-----|-----
Lokale Active Directory-område| ![AD](./media/active-directory-aadconnect-topologies/LegendAD1.png)
Active Directory med filtrerede import| ![AD](./media/active-directory-aadconnect-topologies/LegendAD2.png)
Azure AD-forbindelse Synkroniser server| ![Synkroniser](./media/active-directory-aadconnect-topologies/LegendSync1.png)
Azure AD Connect Synkroniser server "midlertidige tilstand"| ![Synkroniser](./media/active-directory-aadconnect-topologies/LegendSync2.png)
GALSync med FIM2010 eller MIM2016| ![Synkroniser](./media/active-directory-aadconnect-topologies/LegendSync3.png)
Azure AD-forbindelse Synkroniser server, detaljerede| ![Synkroniser](./media/active-directory-aadconnect-topologies/LegendSync4.png)
Azure AD-mappe |![AAD](./media/active-directory-aadconnect-topologies/LegendAAD.png)
Ikke-understøttede scenarie | ![Ikke-understøttede](./media/active-directory-aadconnect-topologies/LegendUnsupported.png)

## <a name="single-forest-single-azure-ad-tenant"></a>Enkelt skov, enkelt Azure AD-lejer
![Enkelt skov enkelt lejer](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

Den mest almindelige topologi er et enkelt område lokalt, med en eller flere domæner, og en enkelt Azure AD-lejer. Synkronisering af adgangskoder bruges til Azure AD-godkendelse. Udtrykkelig installationen af Azure AD-forbindelse understøtter kun denne topologi.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Enkelt område, flere Synkroniser servere til én Azure AD-lejer
![Enkelt skov filtreret ikke-understøttede](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

Det er ikke understøttet for at få flere Azure AD-forbindelse Synkroniser servere forbundet med den samme Azure AD-lejer, med undtagelse af en [midlertidige server](#staging-server). Det er ikke-understøttede, selvom disse er konfigureret til at synkronisere udelukker sæt objekter. Du kan have betragtning dette, hvis du ikke kan nå alle domæner i området fra en enkelt server eller for at distribuere belastning på tværs af flere server.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Flere områder, enkelt Azure AD-lejer
![Flere skov enkelt lejer](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

Mange organisationer har miljøer med flere lokale Active Directory-områder. Der er forskellige årsager til har mere end én lokale Active Directory-område. Typiske eksempler er design med konto er ressourcerelaterede områder og som et resultat efter fusion eller virksomhedskøb.

Når du har flere områder, alle områder skal være nås af enkelt Azure AD-forbindelse Synkroniser server. Du behøver ikke at deltage i serveren til et domæne. Hvis det er nødvendigt at nå alle skov kan serveren placeres i et DMZ-netværk.

Installationsguiden af Azure AD-forbindelse giver adskillige muligheder til konsolidering af brugere, der er repræsenteret i flere områder. Målet er, at en bruger repræsenteres kun én gang i Azure AD. Der er nogle almindelige topologier, som du kan konfigurere i den brugerdefinerede installationssti i installationsguiden. Vælg den tilsvarende indstilling, der repræsenterer topologien på siden, der **entydigt identificerer dine brugere**. Konsolideringen kun er konfigureret til brugere. Dublerede grupper konsolideres ikke med standardkonfiguration.

Almindelige topologier er beskrevet i næste afsnit: [separat topologier](#multiple-forests-separate-topologies), [fuld maske](#multiple-forests-full-mesh-with-optional-galsync)og [Konto-ressource](#multiple-forests-account-resource-forest).

Standardkonfigurationen i Azure AD-forbindelse Synkroniser antages:

1. Brugere har kun én aktiveret konto, og det område, hvor denne konto findes der bruges til at godkende brugeren. Denne antages det begge synkronisering af adgangskoder og sammenslutning. UserPrincipalName og sourceAnchor/immutableID kommer fra dette område.
2. Brugere har kun én postkasse.
3. Det område, hvor postkasse for en bruger har den bedste datakvalitet for attributter, der er synlige i det Exchange globale adresseliste (GAL). Hvis der er ingen postkasse på brugeren, kan en hvilken som helst område bruges til at bidrage disse attributværdier.
4. Hvis du har en sammenkædet postkasse, er der også en anden konto i et andet område, der bruges til logon.

Hvis disse forudsætninger ikke svarer til dit miljø, sker der følgende:

- Hvis du har mere end én aktiv konto eller mere end én postkasse, henter en synkronisering-program, og ignorere den anden.
- En sammenkædet postkasse uden aktive konto eksporteres ikke til Azure AD. Brugerkontoen vises ikke som et medlem i en gruppe. En sammenkædet postkasse i DirSync repræsenteres altid som en normal postkasse. Denne ændring er bevidst forskellige funktioner til at understøtte bedre scenarier med flere områder.

Kan finde flere oplysninger i [forstå standard configuation](active-directory-aadconnectsync-understanding-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Flere områder, flere Synkroniser servere til én Azure AD-lejer
![Flere skov flere Synkroniser ikke understøttes](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

Det understøttes ikke for at få mere end ét Azure Active Directory forbinde-synkronisering server har forbindelse til en enkelt Azure AD-lejer. Undtagelsen er brugen af en [midlertidige server](#staging-server).

### <a name="multiple-forests--separate-topologies"></a>Flere områder – separat topologier
**Brugere er repræsenteret kun én gang på tværs af alle mapper**

![Flere skov brugere én gang](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Flere skov Seperate topologier](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

Alle områder i det lokale miljø, behandles det som separate objekter i dette miljø, og ingen bruger ville være til stede i andre skov.
Hvert område har sin egen Exchange-organisation, og der er ingen GALSync mellem områder. Denne topologi kan være situation efter en fusion/acquisition eller i en organisation, hvor hver afdeling fungerer isolerede fra hinanden. Disse områder er i samme organisation i Azure AD og vises med en samlet GAL.
På dette billede er de enkelte objekter i hver skov repræsenteret én gang i metaverse og aggregeret på mållejeren Azure AD.

### <a name="multiple-forests--match-users"></a>Flere områder – Sammenlign brugere
**Bruger-id'er findes på tværs af flere mapper**

Fælles for alle disse scenarier er, fordeling og sikkerhedsgrupper kan indeholde en blanding af brugere, kontakter og FSPs (principper for fremmed sikkerhed)

FSPs bruges i ADDERER til at repræsentere medlemmer fra andre områder i en sikkerhedsgruppe. Alle FSPs er til det virkelige objekt i Azure AD.

### <a name="multiple-forests--full-mesh-with-optional-galsync"></a>Flere områder – hele masken med valgfri GALSync
**Bruger-id'er findes på tværs af flere mapper. Svarer til ved hjælp af: Mail attribut**

![Flere skov brugere Mail](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Flere skov fuld maske](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

En fuld maske topologi gør det muligt for brugere og ressourcer for at være placeret i en hvilken som helst område og ofte der ville være tovejs tillidsforhold mellem områder.

Hvis Exchange findes i mere end ét område, kan der du kan også være en lokal GALSync løsning. Hver bruger repræsenteres som en kontakt i alle andre områder. GALSync implementeres ofte ved hjælp af Forefront identitet Manager 2010 eller Microsoft identitet Manager 2016. Azure AD-forbindelse kan ikke bruges til lokale GALSync.

I dette scenarie skal er identitet objekter sammenføjet ved hjælp af attributten mail. En bruger med en postkasse i et område er sammenføjet med kontakter i andre områder.

### <a name="multiple-forests--account-resource-forest"></a>Flere områder-konto er ressourcerelaterede skov
**Bruger-id'er findes på tværs af flere mapper. Svarer til ved hjælp af: ObjectSID og msExchMasterAccountSID attributter**

![Flere skov brugere ObjectSID](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![Flere skov AccountResource](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

I en konto er ressourcerelaterede skov topologi har du en eller flere konto skov med aktive brugerkonti. Du har også en eller flere ressource skov med deaktiverede konti.

I dette scenarie en (eller flere) **ressourceskov** har tillid til alle **konto områder**. Et udvidet AD-skema med Exchange og Lync er typisk den ressourceskov. Alle Exchange og Lync tjenester samt andre delte tjenester kan findes i dette område. Brugere har en deaktiveret brugerkonto i dette område og postkassen er knyttet til konto skoven.

## <a name="office-365-and-topology-considerations"></a>Office 365 og topologi overvejelser i forbindelse med
Nogle arbejdsbelastninger, som Office 365, skal understøttede topologier visse begrænsninger. Hvis du planlægger at bruge et af følgende, derefter læse emnet understøttede topologier for arbejdsbyrde.

Arbejdsbelastning |  
--------- | ---------
Exchange Online | Hvis der er mere end ét Exchange organisation lokalt (det vil sige, Exchange er blevet installeret på mere end én skov), og derefter skal du bruge Exchange 2013 SP1 eller nyere. Oplysninger kan findes her: [hybridinstallationer med flere Active Directory-områder](https://technet.microsoft.com/library/jj873754.aspx)
Skype for Business | Hvis du bruger flere områder i det lokale miljø, understøttes kun konto er ressourcerelaterede skov topologien. Detaljer for understøttede topologier kan findes her: [påvirkning krav til Skype for Business Server 2015](https://technet.microsoft.com/library/dn933910.aspx)

## <a name="staging-server"></a>Midlertidige server
![Midlertidige Server](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD-forbindelse understøtter installerer en anden server i **midlertidige tilstand**. En server i denne tilstand læser data fra alle forbundne kataloger, men kan ikke skrive noget til forbundne kataloger. Det bruger normal synkronisering cyklussen og derfor har en opdateret kopi af datatypen identitet. I nedbrud hvor den primære server mislykkes, du kan skifte over til arrangering serveren. Du kan gøre dette i guiden Azure AD-forbindelse. Denne anden server kan helst være placeret i en anden datacenter, da ingen infrastruktur deles med den primære server. Manuelt skal du kopiere konfiguration ændringer på den primære server til den anden server.

En midlertidig server kan også bruges til at teste en ny brugerdefineret konfiguration og den effekt, der er på dine data. Du kan se ændringerne, og Juster konfigurationen. Når du er tilfreds med den nye konfiguration, kan du foretage midlertidige serveren active server og angive den gamle active server i arrangere tilstand.

Denne metode kan også bruges til at erstatte synkronisering af active serveren. Forberede den nye server, og den angives i arrangere tilstand. Sørg for, at det er i god tilstand, Deaktiver arrangere tilstand (gør det aktive), og luk den aktuelt aktive server.

Det er muligt at have mere end én midlertidige server, når du vil have flere sikkerhedskopier i forskellige datacentre.

## <a name="multiple-azure-ad-tenants"></a>Flere Azure AD-lejere
Microsoft anbefaler at have en enkelt lejer i Azure AD for en organisation.
Før du planlægger at bruge flere Azure AD-lejere, dækker disse emner almindelige scenarier, så du kan bruge en enkelt lejer.

Emne |  
--------- | ---------
Delegering ved hjælp af administrative enheder | [Administration af administrative enheder i Azure AD](active-directory-administrative-units-management.md)

![Flere skov flere lejer](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Der findes en 1:1-relation mellem en server til synkronisering af Azure AD-forbindelse og en Azure AD-lejer. For hver Azure AD-lejer skal du én Azure AD-forbindelse Synkroniser serverinstallation. Azure AD-lejer forekomster er ved at design isolerede og brugere i en kan ikke se brugere i anden lejeren. Hvis denne adskillelse er beregnet, og klik derefter dette er en understøttet konfiguration, men ellers skal du bruge enkelt Azure AD-lejer model.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>De enkelte objekter kun én gang i en Azure AD-lejer
![Enkelt skov filtreret](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

I denne topologi er én Azure AD-forbindelse Synkroniser server forbundet til hver Azure AD-lejer. Azure AD-forbindelse Synkroniser servere skal være konfigureret til at filtrere så hver har et udelukker sæt objekter til at betjene på. Du kan for eksempel begrænse hver server til et bestemt domæne eller OU. Et DNS-domæne kan kun registreres i en enkelt Azure AD-lejer. UPN brugerne i lokale AD skal bruge separat navneområder samt. For eksempel i billedet ovenfor tre separate UPN suffikser er registreret i lokale AD: contoso.com, fabrikam.com og internetadresse. Brugere i hver lokal AD domæne bruge et andet navneområde.

Der er ingen GALsync mellem Azure AD-lejer forekomster. Adressekartoteket i Exchange Online og Skype for Business kun viser brugere i samme lejer.

Denne topologi har følgende begrænsninger, der skal ellers understøttes scenarier:

- Kun én af Azure AD-lejere kan aktivere Exchange hybrid med lokale Active Directory.
- Windows 10-enheder kan kun knyttes til én Azure AD-lejer.

Kravet om udelukker sæt objekter gælder også for tilbageførsel. Nogle tilbageførsel-funktioner understøttes ikke med denne topologi, da disse funktioner forudsætter en enkelt konfiguration lokalt:

-   Gruppen tilbageførsel med standardkonfiguration
-   Enhed tilbageførsel

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Hvert objekt flere gange i en Azure AD-lejer
![Enkelt skov flere lejer ikke understøttes](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Enkelt skov flere forbindelser, der ikke understøttes](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

- Det er ikke understøttet for at synkronisere den samme bruger flere Azure AD-lejere.
- Det er ikke-understøttede til konfiguration af for at gøre brugere i en Azure AD vises som kontakter i en anden Azure AD-lejer.
- Det er ikke understøttet for at ændre synkronisering af Azure AD-forbindelse for at oprette forbindelse til flere Azure AD-lejere.

### <a name="galsync-by-using-writeback"></a>GALsync ved hjælp af tilbageførsel
![MultiForestMultiDirectoryGALSync1Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![MultiForestMultiDirectoryGALSync2Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Azure AD-lejere er ved at design isolerede.

- Det er ikke understøttet for at ændre konfigurationen af synkronisering af Azure AD-forbindelse for at læse data fra en anden Azure AD-lejer.
- Det er ikke understøttet for at eksportere brugere som kontakter til en anden lokalt AD ved hjælp af synkronisering af Azure AD-forbindelse.

### <a name="galsync-with-on-premises-sync-server"></a>GALsync med lokal synkronisering server
![MultiForestMultiDirectoryGALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

Det understøttes for at bruge FIM2010/MIM2016 i det lokale miljø til GALsync brugere mellem to Exchange organisationer. Brugere i en organisation vises som fremmed brugere/kontakter i andre organisationen. Disse forskellige lokale Active Directory kan derefter synkroniseres til deres egne Azure AD-lejere.

## <a name="next-steps"></a>Næste trin
Hvis du vil se, hvordan du installerer Azure AD-forbindelse til disse scenarier, kan du se [brugerdefineret installation af Azure AD-forbindelse](./connect/active-directory-aadconnect-get-started-custom.md).

Lær mere om at [synkronisere Azure AD-forbindelse](active-directory-aadconnectsync-whatis.md) konfigurationen.

Lær mere om at [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).
