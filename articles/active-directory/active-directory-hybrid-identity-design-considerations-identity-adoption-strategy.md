<properties
    pageTitle="Azure Active Directory hybrid identitet Designovervejelser - definere en hybrid identitet indføring strategi | Microsoft Azure"
    description="Med betinget adgangskontrol kontrollerer Azure Active Directory de bestemte betingelser, du vælger når godkender brugeren og før du tillader adgang til programmet. Når disse betingelser er opfyldt, kan brugeren er godkendt og har adgang til programmet."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>


# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definere en hybrid identitet indføring strategi

I denne opgave, skal du definere hybrid identitet indføring strategi for dit hybride identitet løsning at imødekomme virksomhedens behov, der er beskrevet i:

- [Bestemme forretningsbehov](active-directory-hybrid-identity-design-considerations-business-needs.md)
- [Fastlægge krav til directory-synkronisering](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
- [Fastlægge krav til multi-Factor authentication](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definere virksomhedens behov strategi
De første opgave adresser afgøre organisationer virksomheden skal have.  Det kan være meget bred og omfang krybning kan opstå, hvis du ikke er forsigtig.  Hold det enkelt i starten, men Husk altid at planlægge et design, der skal tage højde og lette ændring i fremtiden.  Uanset om det er en simpel design eller en meget komplekse, er Azure Active Directory Microsoft Identity platformen, der understøtter Office 365, Microsoft Online Services og skyen opmærksom på programmer.

## <a name="define-an-integration-strategy"></a>Definere en strategi for integration
Microsoft har tre primære integrationsmuligheder som skyen identiteter, synkroniseret identiteter og samlet identiteter.  Du skal planlægge på vedtagelse af en af disse integration strategier.  Den strategi, du vælger kan variere og beslutningerne i at vælge en kan omfatte, hvilken type af brugeroplevelsen, du vil give, har du nogle af de eksisterende infrastruktur allerede lokalt, og hvad er den mest rentable.  
 
![](./media/hybrid-id-design-considerations/integration-scenarios.png)

De scenarier, der er defineret i den ovenstående figur er:

- **Skyen identiteter**: Dette er identiteter, der findes kun i skyen.  I forbindelse med Azure AD, vil de er placeret specifikt i Azure AD-biblioteket.
- **Synchronized**: Dette er identiteter, der findes i det lokale miljø og i skyen.  Brug af Azure AD-forbindelse, er disse brugere enten oprettet eller joinforbundne med eksisterende Azure AD-konti.  Brugerens adgangskode hash synkroniseres fra det lokale miljø i skyen på det, der kaldes en adgangskode hash.  Når ved hjælp af synkroniseret er en advarsel, hvis en bruger er deaktiveret i det lokale miljø, kan det tage op til 3 timer for den pågældende kontostatus vises i Azure AD.  Dette er på grund af tidsinterval synkronisering.
- **Federated**: disse identiteter findes både lokalt og i skyen.  Brug af Azure AD-forbindelse, er disse brugere enten oprettet eller joinforbundne med eksisterende Azure AD-konti.  
 
>[AZURE.NOTE]
Du kan finde flere oplysninger om synkroniseringen Læs indstillinger [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).

Den følgende tabel kan hjælpe med at bestemme fordele og ulemper ved hver af følgende strategier:

| Strategi         | Fordele                                                                                                                                                                                                                                                  | Ulemper                                                                                                                                                                                                                                                                                                                                                  |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Skyen identiteter** | Nemmere at administrere for mindre organisation. <br> Intet at installere på-lokale-ingen ekstra hardware behov<br>Nemt deaktiveret, hvis brugeren forlader virksomheden                                                                                                   | Brugere har behov at logge på når du åbner arbejdsmængder i skyen <br> Adgangskoder kan eller muligvis ikke det samme for skyen og lokale identiteter                                                                                                                                                                                                                     |
| **Synkroniseret**     | Lokale adgangskode skal godkende både lokalt og mapper i skyen <br>Nemmere at administrere for små, mellemstore eller store organisationer <br>Brugere kan have enkeltlogon (SSO) til nogle ressourcer <br> Microsoft foretrukne metode til synkronisering <br> Nemmere at administrere | Nogle kunder kan være tilbageholdende med at synkronisere deres mapper med skyen forfaldsdato bestemt virksomheds politi                                                                                                                                                                                                                                                  |
| **Del af et organisationsnetværk**        | Brugere kan have enkeltlogon (SSO) <br>Hvis en bruger afbrydes eller forlader, kontoen kan, deaktiveres med det samme og access tilbagekaldes,<br> Understøtter avancerede scenarier, der ikke kan, ved hjælp af synkroniseret                                           | Flere trin til at installere og konfigurere <br> Højere vedligeholdelse <br> Kan kræve ekstra hardware til STS-infrastruktur <br> Kan kræve ekstra hardware for at installere sammenslutningsserveren. Yderligere software er påkrævet, hvis AD FS bruges <br> Kræv omfattende opsætning til SSO <br> Kritisk punkt i fejl, hvis sammenslutningsserveren ned, brugere vil ikke kunne godkendes |

### <a name="client-experience"></a>Klient-oplevelse
Den strategi, du bruger, der bestemmer logon brugeroplevelsen.  De følgende tabeller giver dig oplysninger om, hvad brugerne kan forvente deres logonoplevelse skal være.  Bemærk, at ikke alle samlet identitetsudbydere understøtter SSO alle scenarier.

**Medlem af et Doman og private network-applikationer**:
 

|                              | Synkroniseret identitet      | Samlet identitet                                           |
|------------------------------|----------------------------|--------------------------------------------------------------|
| Webbrowsere                 | Formularbaseret godkendelse | enkelt Log på, nogle gange kan bruges til at levere organisations-ID |
| Outlook                      | Anmod om legitimationsoplysninger     | Anmod om legitimationsoplysninger                                       |
| Skype for Business (Lync)    | Anmod om legitimationsoplysninger     | enkelt Log på for Lync, bliver du bedt om legitimationsoplysninger til Exchange   |
| SkyDrive Pro                 | Anmod om legitimationsoplysninger     | single sign-on                                               |
| Office Pro Plus abonnement | Anmod om legitimationsoplysninger     | single sign-on                                               |

**Eksterne eller ikke pålidelige kilder**:

|                              | Synkroniseret identitet      | Samlet identitet                                           |
|------------------------------|----------------------------|--------------------------------------------------------------|
| Webbrowsere                 | Formularbaseret godkendelse |  Formularbaseret godkendelse |
| Outlook, Skype for Business (Lync), Skydrive Pro, Office-abonnement| Anmod om legitimationsoplysninger     | Anmod om legitimationsoplysninger                                       |
| Exchange ActiveSync    | Anmod om legitimationsoplysninger     | enkelt Log på for Lync, bliver du bedt om legitimationsoplysninger til Exchange   |
| Mobilapps                 | Anmod om legitimationsoplysninger     | Anmod om legitimationsoplysninger                                               |

Hvis du har besluttet dig fra opgave 1, at du har en part IdP eller er i gang for at bruge en til at levere sammenslutning med Azure AD 3, skal du være opmærksom på følgende understøttede funktioner:
- Enhver SAML 2.0-udbyder, som er kompatibel til SP-Lite profilen kan understøtte godkendelse til Azure AD og tilknyttede programmer
- Understøtter passive godkendelse, som gør det nemmere auth til OWA, SPO osv.
- Exchange Online-klienter kan være understøttet via SAML 2.0 udvidet klient profil (ECP)

Du skal også være opmærksom på, hvilke funktioner er ikke tilgængelige:

- Uden understøttelse af WS-sikkerhed og rettighedsadministration/organisationsnetværk, vil alle andre aktive klienter skifte
 - Det betyder, at ingen Lync-klient, OneDrive-klienten, Office-abonnement, før du Office 2016 Office Mobile
- Overgang af Office til passive godkendelse giver dem mulighed at understøtte ren SAML 2.0 IdPs, men understøttelse bliver stadig på grundlag af klient ved klient


>[AZURE.NOTE]
Læs artikel http://aka.ms/ssoproviders for den mest opdaterede liste.

## <a name="define-synchronization-strategy"></a>Angive strategi for synkronisering
I denne opgave, du vil definere de værktøjer, der skal bruges til at synkronisere organisationens lokale data til skyen, og hvad du skal bruge topologi.  Da de fleste organisationer bruger Active Directory, leveres oplysninger om brug af Azure AD-forbindelse til en adresse til ovenstående spørgsmål på nogle detaljer.  Miljøer, der ikke har Active Directory, er der oplysninger om brug af FIM 2010 R2 eller MIM 2016 til at hjælpe med at planlægge denne strategi.  Men fremtidige versioner af Azure AD-forbindelse understøtter LDAP-kataloger, så alt på tidslinjen, disse oplysninger kan muligvis hjælpe.

###<a name="synchronization-tools"></a>Værktøjer til mappesynkronisering
År, har flere værktøjer til mappesynkronisering fandtes og bruges til forskellige scenarier.  Azure AD-forbindelse er i øjeblikket gå til værktøjet efter eget valg til alle understøttede scenarier.  AAD Sync og DirSync er også stadig omkring og kan også være til stede i dit miljø nu. 

>[AZURE.NOTE]
Læs artikel, [Directory integration værktøjer sammenligning](active-directory-hybrid-identity-design-considerations-tools-comparison.md) for de seneste oplysninger om de understøttede funktioner af hvert værktøj.  

### <a name="supported-topologies"></a>Understøttede topologier
Når du definerer en strategi for synkronisering, bestemmes topologien, der bruges. Afhængigt af de oplysninger, der er angivet i trin kan 2 du bestemme, hvilke topologi er stort, at du bruger. Enkelt området enkelt Azure AD topologi er de mest almindelige og består af et enkelt Active Directory-område og en enkelt forekomst af Azure AD.  Dette der skal bruges i en størstedelen af scenarier og er den forventede topologi, når du bruger Azure AD forbinde standardinstallation, som vist i nedenstående illustration.
 
![](./media/hybrid-id-design-considerations/single-forest.png)Enkelt skov scenarie det er meget almindeligt for små og endda store organisationer med flere områder, som vist i figur 5.

>[AZURE.NOTE]
Du kan finde flere oplysninger om de forskellige lokale og Azure AD topologier med Azure AD-forbindelse synkronisering, skal du læse artiklen [topologier til Azure AD-forbindelse](active-directory-aadconnect-topologies.md).


![](./media/hybrid-id-design-considerations/multi-forest.png) 

Scenarie med flere områder

Hvis dette kassen derefter MFA-forest-enkelt Azure AD-topologi overvejes, hvis følgende elementer er sande:

- Brugere har kun 1 identitet på tværs af alle områder – entydigt id brugere afsnittet nedenfor beskrives det mere detaljeret.
- Brugeren skal godkende til det område, som deres identitet er placeret
- UPN og kilde anker (fast id) kommer fra denne skov
- Alle områder er tilgængelige for Azure AD-forbindelse – det betyder, at det ikke nødvendigt at være domæne tilsluttet sig og kan placeres i en DMZ, hvis dette letter dette.
- Brugere har kun én postkasse
- Det område, hvor en brugers postkasse er den bedste datakvalitet for attributter, der er synlige i det Exchange globale adresseliste (GAL)
- Hvis der er ingen postkasse på brugeren, kan derefter en hvilken som helst skov bruges til at bidrage disse værdier
- Hvis du har en sammenkædet postkasse, er der også en anden konto i et andet område, der bruges til at logge på.

>[AZURE.NOTE]
Objekter, der findes i både lokalt og i skyen, er "forbindelse" via et entydigt id. I forbindelse med katalogsynkronisering kaldes denne entydigt id for SourceAnchor. I konteksten af Single Sign-On kaldes dette på ImmutableId. [Designbegreber til Azure AD-forbindelse](active-directory-aadconnect-design-concepts.md#sourceanchor) til flere overvejelser om brugen af SourceAnchor.

Hvis ovenstående ikke er sand, og du har mere end én aktiv konto eller mere end én postkasse, vil Azure AD-forbindelse Vælg en og ignorere den anden.  Hvis du har sammenkædet postkasser, men ikke andre konti, disse konti kan ikke eksporteres til Azure AD, og brugeren bliver ikke et medlem af en gruppe.  Dette er forskellig fra hvordan det var tidligere med DirSync og er bevidst for bedre support disse scenarier med flere områder. Et scenarie med flere områder, vises i figuren nedenfor.
 
![](./media/hybrid-id-design-considerations/multiforest-multipleAzureAD.png) 
 
**Flere områder flere Azure AD-scenario**

Det anbefales at have en enkelt mappe i Azure AD for en organisation, men det understøttes den en 1:1 relation holdes mellem en Azure AD-forbindelse Synkroniser server og en Azure AD-mappe.  For hver forekomst af Azure AD skal du en installation af Azure AD-forbindelse.  Desuden Azure AD ved design er adskilt og brugere i én forekomst af Azure AD vil ikke kunne se brugere i en anden forekomst.

Det er muligt og understøttede opretter forbindelse én lokale forekomst af Active Directory til flere Azure AD-mapper, som vist i nedenstående illustration:

![](./media/hybrid-id-design-considerations/single-forest-flitering.png) 
 

**Enkelt skov spamfiltrering scenarie**

For at kunne udføre denne følgende skal være sande:

- Azure AD-forbindelse Synkroniser servere skal være konfigureret til filtrering, så de hver har et udelukker sæt objekter.  Dette er færdig, for eksempel ved angivelse af område hver server til et bestemt domæne eller OU.
- Et DNS-domæne kan kun registreres i en enkelt Azure AD directory så UPN brugerne i lokale AD skal bruge separat navneområder
- Brugere i én forekomst af Azure AD vil kun kunne se brugere fra deres forekomst.  De vil ikke kunne se brugere i andre forekomster
- Kun én af Azure AD-mapperne kan aktivere Exchange hybrid med lokale AD
- Fælles eksklusivitet gælder også for at tilbageskrivning.  Dette gør nogle tilbageskrivning funktioner understøttes ikke med denne topologi, da disse forudsætter en enkelt lokalt konfiguration.  Dette omfatter:
 - Gruppere tilbageskrivning med standardkonfiguration
 - Enhed tilbageskrivning


Vær opmærksom på, at følgende understøttes ikke og bør ikke vælges som en implementering:

- Det understøttes ikke for at få flere Azure AD-forbindelse Synkroniser servere at oprette forbindelse til den samme Azure AD-mappe, selvom de er konfigureret til at synkronisere udelukker sæt af objekt
- Det er ikke understøttet for at synkronisere den samme bruger til flere Azure AD-mapper. 
- Det er også ikke-understøttede for at sikre en konfiguration, ændre for at gøre brugere i en Azure AD skal vises som kontakter i en anden Azure AD-mappe. 
- Det er også ikke-understøttede til at ændre synkronisering af Azure AD-forbindelse for at oprette forbindelse til flere Azure AD-mapper.
- Azure AD-mapper er ved design isolerede. Det er ikke-understøttede ændre konfigurationen af synkronisering af Azure AD-forbindelse for at læse data fra en anden Azure AD-mappe i et forsøg på at opbygge et almindelige og samlet GAL mellem mapperne. Det er også ikke understøttes for at eksportere brugere som kontakter til en anden lokalt AD ved hjælp af synkronisering af Azure AD-forbindelse.


>[AZURE.NOTE]
Hvis din organisation begrænser computere på dit netværk i at oprette forbindelse til internettet, i denne artikel angiver slutpunkterne (FQDN'er, IPv4 og IPv6-adresseområder), skal du medtage i dine udgående Tillad lister og Internet Explorer til zonen Websteder af klienten computere at sikre, at computere kan korrekt brug af Office 365. Læs [Office 365 URL-adresser og IP-adresseområder](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US)for at få flere oplysninger.

## <a name="define-multi-factor-authentication-strategy"></a>Angive strategi for multi-Factor authentication
Du kan definere Multi-Factor authentication strategi for at bruge i denne opgave.  Azure Multi-Factor Authentication leveres i to forskellige version.  En er en skybaseret og den anden er baseret ved hjælp af Azure MFA Server lokalt.  Afhængigt af evalueringen du over du kan bestemme, hvilken løsning er den korrekte til din strategi for.  Du kan bruge tabellen nedenfor til at bestemme, hvilke bedste designindstillinger opfylde virksomhedens sikkerhedskrav:

Designindstillinger for multi-Factor:

| Aktiv til at sikre                                               | MFA i skyen | MFA lokalt |
|---------------------------------------------------------------|------------------|----------------|
| Microsoft-apps                                                | Ja              | Ja            |
| SaaS apps i galleriet app                                  | Ja              | Ja            |
| IIS-programmer, der er publiceret via Azure AD App-Proxy         | Ja              | Ja            |
| IIS-programmer, der ikke er udgivet via Azure AD App-Proxy | Nej               | Ja            |
| Fjernadgang som VPN, RDG                                     | Nej               | Ja            |

Selvom du muligvis har udlignet på en løsning til din strategi for, skal du stadig bruge evalueringen ovenfra på hvor dine brugere er placeret.  Dette kan medføre løsningen til at ændre.  Brug tabellen nedenfor for at hjælpe dig med at afgøre dette:

| Brugerplacering                                                       | Foretrukne designindstillinger                 |
|---------------------------------------------------------------------|-----------------------------------------|
| Azure Active Directory                                              | Multi-FactorAuthentication i skyen |
| Azure AD og lokale AD bruger sammenslutning med AD FS             | Begge                                    |
| Azure AD og on-premises AD ved hjælp af Azure AD Connect ingen synkronisering af adgangskoder | Begge                                    |
| Azure AD og on-premises ved hjælp af Azure AD-forbindelse med synkronisering af adgangskoder  | Begge                                    |
| Lokale AD                                                      | Multi-Factor Authentication Server      |

>[AZURE.NOTE]
Du skal også sørge for, at indstillingen Multi-Factor authentication design, du har valgt understøtter de funktioner, der kræves til dit design.  Læs [Vælg løsningen Multi-Factor sikkerhed for du](../multi-factor-authentication-get-started.md#what-am-i-trying-to-secure)kan finde flere oplysninger.

## <a name="multi-factor-auth-provider"></a>Multi-Factor Auth udbyder
Multi-Factor authentication er som standard tilgængelig for globale administratorer, der har en Azure Active Directory-lejer. Men hvis du vil udvide Multi-Factor authentication til alle dine brugere og/eller vil dine globale administratorer at drage fordel funktioner som management-portalen, brugerdefinerede hilsener og rapporter, derefter skal du købe og konfigurere Multi-Factor Authentication udbyder.

>[AZURE.NOTE]
Du skal også sørge for, at indstillingen Multi-Factor authentication design, du har valgt understøtter de funktioner, der kræves til dit design. 

##<a name="next-steps"></a>Næste trin
[Fastlægge krav til beskyttelse af data](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Se også
[Design overvejelser i forbindelse med oversigt](active-directory-hybrid-identity-design-considerations-overview.md)
