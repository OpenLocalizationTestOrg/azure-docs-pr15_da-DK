<properties
    pageTitle="Azure Active Directory Domain Services: Synkronisering i administrerede domæner | Microsoft Azure"
    description="Forstå synkronisering i et administreret Azure Active Directory Domain Services-domæne"
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
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Synkronisering i et administreret Azure AD Domain Services-domæne
I følgende diagram vises Sådan fungerer synkronisering i Azure AD-domænetjenester administrerede domæner.

![Topologi for synkronisering i Azure AD-domænetjenester](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Synkronisering fra din lokale mappe på din Azure AD-lejer
Azure AD-forbindelse Synkroniser bruges til at synkronisere brugerkonti, gruppere medlemskaber og legitimationsoplysninger hash-værdi på din Azure AD-lejer. Egenskaber for bruger konti som UPN og SID (sikkerheds-id) synkroniseres lokalt. Hvis du bruger Azure AD-domænetjenester, synkroniseres også ældre legitimationsoplysninger hashes kræves til NTLM og Kerberos-godkendelse til din Azure AD-lejer.

Hvis du konfigurerer tilbageskrivning, synkroniseres dine lokale Active Directory forandringer i Azure AD-biblioteket. Eksempelvis hvis du ændrer din adgangskode, ved hjælp af Azure AD selvbetjening Skift funktioner, ændrede adgangskoden er opdateret i dine lokale AD-domæne.

> [AZURE.NOTE] Brug altid den seneste version af Azure AD-forbindelse til at sikre, at du har løsninger til alle kendte fejl.


## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Synkronisering fra Azure AD-lejer til dit administrerede domæne
Brugerkonti, gruppemedlemskaber og legitimationsoplysninger hashes synkroniseres fra din Azure AD-lejer til domænet Azure AD-domænetjenester administrerede. Denne synkroniseringsproces er automatisk. Du behøver ikke at konfigurere, overvåge eller administrere denne synkroniseringen. Processen til synkronisering er også en-way/envejs i art. Domænet administrerede er stort set skrivebeskyttet med undtagelse af en brugerdefineret afdelinger, du opretter. Derfor kan du foretage ændringer til brugerattributter, brugeradgangskoder eller være medlem af gruppen i administrerede domænet. Som et resultat, er der ingen omvendt synkronisering af ændringer fra domænet administrerede tilbage til din Azure AD-lejer.


## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synkronisering fra et miljø med flere områder lokalt
Mange organisationer har en ret komplekse lokalt identitet-infrastruktur bestående af flere konto områder. Azure AD-forbindelse understøtter synkronisering af brugere, grupper og legitimationsoplysninger hashes fra flere områder miljøer til din Azure AD-lejer.

Azure AD-lejer er derimod en meget nemmere og flad navneområde. Hvis du vil give brugere pålideligt adgang til sikret med Azure AD-programmer, du løse UPN konflikter på tværs af brugerkonti i forskellige områder. Din Azure AD-domænetjenester administrerede domæne bjørne Luk ligheden med din Azure AD-lejer. Derfor ser du en flad OU struktur i domænet administrerede. Alle brugere og grupper er gemt i objektbeholderen AADDC-brugere, uanset hvor de er blevet synkroniseret i lokale domæne eller område. Du har konfigureret en hierarkisk OU strukturere i det lokale miljø. Domænet administrerede har dog stadig en simpel flad OU struktur.


## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Udeladelser - hvad ikke er synkroniseret til din administrerede domæne
Følgende objekter eller attributter er ikke synkroniseret til din Azure AD-lejer eller til dit administrerede domæne:

- **Udelades attributter:** Du kan vælge at udelade bestemte attributter synkroniseres med din Azure AD-lejer fra dit lokale domæne ved hjælp af Azure AD-forbindelse. Disse udelukket attributter er ikke tilgængelige i dit administrerede domæne.

- **Gruppere politikker:** Gruppepolitikker, der er konfigureret i dit lokale domæne er ikke synkroniseret til din administrerede domæne.

- **Systemdrevshare:** På samme måde, indholdet af systemdrevshare på dit lokale domæne er ikke synkroniseret til din administrerede domæne.

- **Computerobjekter:** Computerobjekter til computere, der er knyttet til din lokale domæne er ikke synkroniseret til din administrerede domæne. Disse computere, der ikke har et tillidsforhold til domænet administrerede og hører til dit lokale domæne. I dit administrerede domæne, kan du finde computerobjekter kun til computere, du har eksplicit medlem af et domæne til det administrerede domæne.

- **Brugerbatch attributter for brugere og grupper:** Den primære bruger og primære gruppe sikkerheds-id'er fra dit lokale domæne synkroniseres til dit administrerede domæne. Eksisterende brugerbatch attributter for brugere og grupper synkroniseres dog ikke fra dit lokale domæne til dit administrerede domæne.

- **Organisation enheder (OU) strukturer:** Organisatoriske enheder, der er defineret i dit lokale domæne synkroniseres ikke til dit administrerede domæne. Der er to indbyggede afdelinger i domænet administrerede. Som standard har domænet administrerede en flad OU struktur. Du kan dog vælge at [oprette en brugerdefineret OU i domænet administrerede](./active-directory-ds-admin-guide-create-ou.md).


## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Hvordan bestemte attributter der synkroniseres til dit administrerede domæne
I følgende tabel vises nogle almindelige attributter og i denne artikel beskrives, hvordan de er synkroniseret til din administrerede domæne.

| Attribut i domænet administrerede | Kilde | Noter |
|:---|:---|:---|
|UPN|Brugerens UPN attribut i din Azure AD-lejer|Attributten UPN fra Azure AD-lejer synkroniseres, som er administrerede domænet. Derfor bruger den sikreste måde til at logge på domænet administrerede din UPN.|
|SAMAccountName|Brugerens mailNickname attribut i din Azure AD-lejer eller automatisk oprettede|Attributten SAMAccountName leveres fra attributten mailNickname i din Azure AD-lejer. Hvis flere brugerkonti har det samme mailNickname-attribut, er SAMAccountName automatisk oprettede. Hvis brugerens mailNickname eller UPN præfiks er længere end 20 tegn, genereres SAMAccountName automatisk at tilfredsstille 20 tegngrænsen på SAMAccountName attributter.|
|Adgangskoder|Brugerens adgangskode fra Azure AD-lejer|Legitimationsoplysninger hashes kræves til NTLM eller Kerberos-godkendelse (også kaldet supplerende legitimationsoplysninger) synkroniseres fra Azure AD-lejer. Hvis din Azure AD-lejer er en synkroniseret lejer, vil disse legitimationsoplysninger er hentet fra dit lokale domæne.|
|Primære bruger/gruppe SID|Automatisk oprettede|Den primære SID for bruger/gruppe konti er automatisk oprettede i domænet administrerede. Denne attribut ikke svarer til den primære bruger/gruppe SID for objektet i dine lokale AD-domæne. Denne uoverensstemmelse skyldes, at det administrerede domæne har et andet SID navneområde end din lokale domæne.|
|SID-historik for brugere og grupper|Lokalt primære bruger og gruppe SID|Attributten brugerbatch for brugere og grupper i domænet administrerede er indstillet til at matche tilsvarende primære brugeren eller gruppen SID i dit lokale domæne. Denne funktion gør elevator-og-skift af lokale programmer til administrerede domænet nemmere, da du ikke behøver at nyt ACL ressourcer.|

> [AZURE.NOTE] **Log på den administrerede domæne ved hjælp af UPN-formatet:** Attributten SAMAccountName muligvis automatisk oprettede for nogle brugerkonti i domænet administrerede. Hvis flere brugere har det samme mailNickname-attribut, eller brugere har alt for lange UPN præfikser, være SAMAccountName for disse brugere automatisk oprettede. Formatet SAMAccountName (for eksempel ' CONTOSO100\joeuser') er derfor ikke altid en pålidelig metode til at logge på domænenavn. Brugernes automatisk oprettede SAMAccountName kan variere fra deres UPN præfiks. Bruger UPN-formatet (for eksempel 'joeuser@contoso100.com') til at logge på administrerede domænet pålideligt.


## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Objekter, der ikke er synkroniseret til din Azure AD-lejer fra dit administrerede domæne
Som beskrevet i en foregående sektion i denne artikel, er der ingen synkronisering fra domænet administrerede tilbage til din Azure AD-lejer. Du kan vælge at [oprette en brugerdefineret organisatoriske enhed (OU)](./active-directory-ds-admin-guide-create-ou.md) i dit administrerede domæne. Desuden kan du oprette andre afdelinger, brugere, grupper eller tjenestekonti i disse brugerdefinerede afdelinger. Ingen af de objekter, der er oprettet i brugerdefineret afdelinger synkroniseres tilbage til din Azure AD-lejer. Disse objekter er tilgængelige til brug kun inden for domænet administrerede. Disse objekter er derfor ikke synlige ved hjælp af Azure AD PowerShell-cmdletter Azure AD Graph API eller ved hjælp af Azure AD management brugergrænseflade.


## <a name="related-content"></a>Relateret indhold
- [Funktioner - Azure AD-domænetjenester](active-directory-ds-features.md)

- [Installationsscenarier - Azure AD-domænetjenester](active-directory-ds-scenarios.md)

- [Azure AD-domænetjenester netværk overvejelser i forbindelse med](active-directory-ds-networking.md)

- [Introduktion til Azure AD-domænetjenester](active-directory-ds-getting-started.md)
