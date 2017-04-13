<properties
    pageTitle="Oversigt over Azure Active Directory-domænetjenester | Microsoft Azure"
    description="Oversigt over Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services"></a>Azure AD-domænetjenester

## <a name="overview"></a>Oversigt
Azure-infrastrukturtjenester gør det muligt at installere en lang række computing løsninger på en smart måde. Med virtuelle Azure-computere, du kan installere næsten øjeblikkeligt, og du betaler kun med minuttet. Ved hjælp af support til Windows, Linux, SQL Server, Oracle, IBM, SAP og BizTalk, kan du installere en hvilken som helst arbejdsbelastningen, alle sprog, på næsten alle-operativsystemet. Disse fordele gør det muligt at overføre ældre programmer installeres lokalt til Azure, gemme på funktionsdygtige udgifter.

Det vigtigste aspekt af overførsel lokale programmer til Azure håndtering af disse programmer identitet kundernes behov. Directory-aware programmer kan stole på LDAP for læsning eller skrive-adgang til virksomhedens adressekartotek eller er afhængige af integreret Windows-godkendelse (Kerberos eller NTLM-godkendelse) til at godkende slutbrugere. Line of business (LOB) programmer, der kører på Windows Server er typisk installeret på domæne tilsluttet maskiner, så de kan administreres sikkert ved hjælp af gruppepolitik. Til lokale 'elevator-og-Skift' programmer i skyen skal disse afhængigheder på virksomhedens identitet infrastrukturen løses.

Administratorer slår ofte til en af følgende løsninger til at opfylde deres programmer, der er implementeret i Azure identitet behov:

- Installere en-til-websted VPN-forbindelse mellem arbejdsbelastninger, som kører i Azure infrastrukturtjenester og i virksomhedens adressekartotek i det lokale miljø.
- Udvide virksomhedens AD domæner/infrastruktur ved at konfigurere replika domæne enheder ved hjælp af Azure virtuelle computere.
- Installere et enkeltstående domæne i Azure ved hjælp af domæne enheder installeret som Azure virtuelle computere.

Alle disse fremgangsmåder lide fra høj omkostninger og administrative omkostninger. Administratorer skal installere domæne enheder ved hjælp af virtuelle maskiner i Azure. Desuden de skal bruge til at administrere, secure, programrettelse, overvåge, sikkerhedskopiere og fejlfinding i forbindelse med disse virtuelle maskiner. Afhængigheden af VPN-forbindelser til den lokale mappe får arbejdsbelastninger, som er implementeret i Azure som risikabel midlertidige netværk fejl eller afbrydelser. Disse netværk udfald resultere også i nederste oppetid og reduceret pålidelighed til disse programmer.

Vi designet Azure AD Domain Services til at levere nemmere alternativ.


## <a name="introducing-azure-ad-domain-services"></a>Introduktion til Azure AD-domænetjenester
Azure AD-domænetjenester indeholder administreret domænetjenester som forbindelse til et domæne, gruppen politikken, LDAP, Kerberos/NTLM-godkendelse, der er fuldt kompatible med Windows Server Active Directory. Du kan bruge disse domænetjenester uden brug af for dig at implementere, administrere og programrettelse domænecontrollere i skyen. Azure AD-domænetjenester integrerer med din eksisterende Azure AD-lejer, og som derfor gør det muligt for brugerne at logge på med deres virksomhedens legitimationsoplysninger. Desuden kan kan du bruge eksisterende grupper og brugerkonti til at sikre adgang til ressourcer, således at sikre en jævnere 'elevator-og-Skift' lokale ressourcer til Azure infrastrukturtjenester.

Azure AD-domænetjenester funktionalitet fungerer problemfrit sammen, uanset om din Azure AD-lejer er kun skyen eller synkroniseret med din lokale Active Directory.

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>Azure AD-domænetjenester til skyen kun organisationer
En skyen kun Azure AD-lejer (ofte kaldet 'administrerede lejere') har ikke et lokalt identitet miljøet. Med andre ord er brugerkonti, deres adgangskoder og gruppemedlemskaber alle oprindelige i skyen – det vil sige, oprettes og administreres i Azure AD. Overvej for et øjeblik, Contoso udelukkende en Azure AD-lejer. Som vist i nedenstående illustration, har Contosos administrator konfigureret et virtuelt netværk i Azure infrastrukturtjenester. Programmer og arbejdsbelastninger, som server er installeret på denne virtuelle netværk i Azure virtuelle computere. Da Contoso er en skybaseret kun lejer, oprettes og administreres i Azure AD alle bruger-id'er, deres legitimationsoplysninger og gruppemedlemskaber.

![Oversigt over tjenester Azure AD-domæne](./media/active-directory-domain-services-overview/aadds-overview.png)

Contosos IT-administrator kan aktivere Azure AD-domænetjenester for deres Azure AD-lejer og vælger at gøre domænetjenester tilgængelig i denne virtuelle netværk. Derefter Azure AD-domænetjenester danner en administreret domæne og gør det tilgængeligt i det virtuelle netværk. Alle brugerkonti, gruppemedlemskaber og brugerlegitimationsoplysninger, der er tilgængelige i Contosos Azure AD-lejer er også tilgængelige i dette nyoprettede domæne. Denne funktion gør det muligt for brugere i organisationen til at logge på det domæne, ved hjælp af deres virksomhedens legitimationsoplysninger – f.eks, når der oprettes forbindelse fra en fjernplacering til medlem af et domæne maskiner via Fjernskrivebord. Administratorer kan tildele adgang til ressourcer i det domæne, ved hjælp af eksisterende gruppemedlemskaber. Programmer, der er implementeret i virtuelle maskiner på det virtuelle netværk kan bruge funktioner som forbindelse til et domæne, LDAP-Læs, LDAP bind, NTLM og Kerberos-godkendelse og Gruppepolitik.

Nogle vigtige aspekter af det administrerede domæne, der er klargjort ved Azure AD-domænetjenester er som følger:

- Contosos IT-administratoren behøver ikke at administrere, programrettelse eller overvåge dette domæne eller en hvilken som helst domænecontrollere for denne administrerede domæne.
- Der er ingen grund til at administrere AD gentagelse for dette domæne. Brugerkonti, gruppemedlemskaber og legitimationsoplysninger fra Contosos Azure AD-lejer er automatisk tilgængelige i dette administrerede domæne.
- Da domænet administreres af Azure AD-domænetjenester, Contoso's IT-administratoren har ikke domæneadministrator eller virksomhedens Administrator rettigheder på dette domæne.


### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>Azure AD-domænetjenester hybrid organisationer
Organisationer med en hybrid IT-infrastruktur forbruge en blanding af skyen ressourcer og lokale ressourcer. Organisationerne synkronisere identitetsoplysninger fra deres lokale mappe til deres Azure AD-lejer. Som hybride organisationer se ud, hvis du vil overføre mere af deres lokale programmer i skyen, især ældre directory-aware programmer Azure AD-domænetjenester kan være praktisk at dem.

Litware Corporation er installeret [Azure AD-forbindelse](../active-directory/active-directory-aadconnect.md), hvis du vil synkronisere identitetsoplysninger fra deres lokale mappe til deres Azure AD-lejer. Oplysningerne om identitet, der er synkroniseret omfatter brugerkonti, deres legitimationsoplysninger hashes for godkendelse (synkronisering af adgangskoder) og gruppemedlemskaber.

> [AZURE.NOTE] **Synkronisering af adgangskoder er obligatorisk for hybride organisationer til at bruge Azure AD-domænetjenester**. Dette krav gælder, fordi brugernes legitimationsoplysninger, der skal bruges i administrerede domænet fra Azure AD Domain Services til at godkende disse brugere via NTLM- eller Kerberos godkendelsesmetoder.

![Azure AD-domænetjenester for Litware Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

Den foregående illustration viser, hvordan organisationer med en hybrid IT-infrastruktur, såsom Litware Corporation kan bruge Azure AD-domænetjenester. Litwares programmer og server arbejdsbelastninger, som kræver domain services er installeret på et virtuelt netværk i Azure infrastrukturtjenester. Litware's IT-administrator kan aktivere Azure AD-domænetjenester for deres Azure AD-lejer og vælger at gøre et administreret domæne tilgængelig i denne virtuelle netværk. Da Litware er en organisation med en hybrid IT-infrastruktur, synkroniseres brugerkonti, grupper og legitimationsoplysninger til deres Azure AD-lejer fra deres lokale mappe. Denne funktion gør det muligt for brugere til at logge på det domæne, ved hjælp af deres virksomhedens legitimationsoplysninger – f.eks, når forbindelse fra en fjernplacering til computere, der er tilsluttet domæne via Fjernskrivebord. Administratorer kan tildele adgang til ressourcer i det domæne, ved hjælp af eksisterende gruppemedlemskaber. Programmer, der er implementeret i virtuelle maskiner på det virtuelle netværk kan bruge funktioner som forbindelse til et domæne, LDAP-Læs, LDAP bind, NTLM og Kerberos-godkendelse og Gruppepolitik.

Nogle vigtige aspekter af det administrerede domæne, der er klargjort ved Azure AD-domænetjenester er som følger:

- Administrerede domænet er et enkeltstående domæne. Det er ikke en udvidelse af Litwares lokale domæne.
- Litware's IT-administrator ikke har brug at administrere, programrettelse, eller skærmen domænecontrollere for denne administrerede domæne.
- Der er ingen grund til at administrere AD gentagelse til dette domæne. Brugerkonti, gruppemedlemskaber og legitimationsoplysningerne fra Litwares lokal adresseliste synkroniseres med Azure AD via Azure AD-forbindelse. Disse brugerkonti, gruppemedlemskaber og legitimationsoplysninger er automatisk tilgængelige i administrerede domænet.
- Da domænet administreres af Azure AD-domænetjenester, Litware's IT-administratoren har ikke domæneadministrator eller virksomhedens Administrator rettigheder på dette domæne.


## <a name="benefits"></a>Fordele
Med Azure AD-domænetjenester, kan du udnytte følgende fordele:

-   **Simpel** – du kan opfylde virtuelle maskiner, der er installeret på Azure infrastrukturtjenester med nogle få enkle Klik identitet behov. Du behøver ikke at installere og administrere identitet infrastruktur i Azure eller opsætning connectivity tilbage til din lokale identitet infrastruktur.

-   **Integreret** – Azure AD-domænetjenester er fuldt integreret med Azure AD-lejer. Du kan nu bruge Azure AD som en integreret skybaseret enterprise-mappe, der caters til behovet i både din moderne programmer og traditionelle directory-aware programmer.

-   **Kompatible** – Azure AD-domænetjenester bygger på dokumenterede enterprise grade infrastrukturen i Windows Server Active Directory. Derfor kan dine programmer stole på en større grad af kompatibilitet med Windows Server Active Directory-funktioner. Ikke alle funktionerne i Windows Server AD er tilgængelige i Azure AD-domænetjenester. Tilgængelige funktioner er dog kompatibelt med de tilsvarende Windows Server AD-funktioner, du skal have i din lokale infrastruktur. LDAP, Kerberos, NTLM, Gruppepolitik og domæne joinforbindelse mulighederne udgør en fuldt udviklet tilbud, der er testet og afgrænset over forskellige versioner af Windows Server.

-   **Økonomisk** – med Azure AD-domænetjenester, du kan undgå den infrastruktur og styring arbejdsbyrde, der er knyttet til administration af identitet infrastruktur for at understøtte traditionelle directory-aware programmer. Du kan flytte programmerne til Azure infrastrukturtjenester og få glæde af større rabat på funktionsdygtige udgifter.
