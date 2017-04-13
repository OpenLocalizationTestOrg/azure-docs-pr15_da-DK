
<properties
    pageTitle="Azure Active Directory hybrid identitet Designovervejelser - fastlægge krav til access kontrolelementet | Microsoft Azure"
    description="Dækker søjler på identitet, og identificere access krav til ressourcer til brugere i et hybridmiljø."
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

# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Finde ud af krav til adgang til din hybride identitet løsning
Når en organisation Designer deres hybrid identitet løsning skal de kan bruge denne mulighed for at gennemse access krav til de ressourcer, som de har planer om at gøre det tilgængeligt for brugere. Adgang til data på tværs af alle fire søjler af identitet, som er:

- Administration
- Godkendelse
- Godkendelse
- Overvågning

De sektioner, der følger efter dækker godkendelse og autorisation i få mere at vide, administration og revision, er en del af den hybride identity livscyklus. Læs [fastslå hybrid identitet administrationsopgaver](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) kan finde flere oplysninger om disse funktioner.

>[AZURE.NOTE]
Læs [Feltet fire søjler for identitet - identitet Management i alder af Hybrid IT](http://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) kan finde flere oplysninger om hver enkelt af disse søjler.

## <a name="authentication-and-authorization"></a>Godkendelse og autorisation
Der er forskellige scenarier for godkendelse og autorisation, disse scenarier har særlige behov, der skal opfyldes af løsningen hybrid identitet, virksomheden skal indføre. Scenarier, der vedrører Business to Business (B2B) kommunikation kan tilføje en ekstra udfordring for IT-administratorer, fordi de skal sikre dig, at metoden godkendelse og autorisation bruges af organisationen kan kommunikere med deres forretningsforbindelser. Sørg for, at følgende spørgsmål besvares under processen design for godkendelse og autorisation krav:

- Din organisation skal godkende og Godkend kun brugere, der er placeret på deres identitet administrationssystem?
 - Er der nogen planer for B2B scenarier?
 - Hvis Ja, ved du allerede, hvilke protokoller (SAML, OAuth, Kerberos, Tokens eller certifikater) bruges til at oprette forbindelse begge virksomheder?
- Betyder løsningen hybrid identitet, du vil indføre support disse protokoller?

En anden vigtig ting at overveje er hvor lageret godkendelse, der anvendes af brugere og partnere vil være placeret og den administrative, der skal bruges. Overvej følgende to grundlæggende indstillinger:
- Centraliserede: i denne model brugerens legitimationsoplysninger, politikker og administration af kan være centralt lokalt eller i skyen.
- Hybrid: i denne model brugerens legitimationsoplysninger, politikker og administration af bliver centralt lokalt og en replikerede i skyen.

Hvilken model din organisation vil indføre kan variere, alt efter deres forretningsbehov, vil du besvare følgende spørgsmål for at identificere, hvor administrationssystem identitet er placeret, og den administrative tilstand at bruge:

- Din organisation har i øjeblikket en identitet administration i det lokale miljø?
 - Hvis det er tilfældet, kan de planlægge at beholde den?
 - Er der en hvilken som helst bestemmelser eller overholdelse krav, at din organisation skal følge, der bestemmer, hvor administrationssystem identitet skal være placeret?
- Din organisation anvender enkeltlogon til apps, der er placeret lokalt eller i skyen?
 - Hvis det er tilfældet, indføring af en hybrid identitetsmodel påvirker denne proces?

## <a name="access-control"></a>Adgangskontrol
Det er også vigtigt til at styre adgangsniveauet, der har disse brugere og niveauet for access administratorer vises der over de ressourcer, som de administrerer, mens godkendelse og autorisation er grundlæggende elementer for at aktivere adgang til virksomhedens data via brugerens validering. Din hybrid identitet løsning skal kunne give findelt adgang til ressourcer, delegering og rolle base adgangskontrol. Sørg for, at følgende spørgsmål besvares vedrørende adgangskontrol:

- Har din virksomhed mere end én bruger med administratorrettigheder til at administrere din identitetssystem?
 - Hvis det er tilfældet, hver bruger skal det samme adgangsniveau?
- Skal din virksomhed stedfortræderadgang til brugere til at administrere bestemte ressourcer?
 - Hvis Ja, hvor ofte sker dette?
- Din virksomhed nødt til at integrere access control-funktioner mellem i det lokale miljø og skyen ressourcer?
- Din virksomhed nødt til at begrænse adgangen til ressourcer efter nogle betingelser?
- Virksomheden ville have et program, der kræver brugerdefinerede styre adgangen til nogle ressourcer?
 - Hvis det er tilfældet, hvor disse apps findes (lokale eller i skyen)?
 - Hvis det er tilfældet, hvor er dem, der fokuserer på ressourcer, der er placeret (lokale eller i skyen)?

>[AZURE.NOTE]
Sørg for at tage noter på hvert svar og forstå begrundelse bag svaret. [Angive strategi til databeskyttelse](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) går hen over de tilgængelige indstillinger og fordele/ulemper ved hver indstilling.  Du vil vælge, hvilken indstilling, der bedst passer til virksomhedens behov ved at svare på disse spørgsmål.

## <a name="next-steps"></a>Næste trin

[Fastlægge krav til hændelsen svar](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Se også
[Design overvejelser i forbindelse med oversigt](active-directory-hybrid-identity-design-considerations-overview.md)
