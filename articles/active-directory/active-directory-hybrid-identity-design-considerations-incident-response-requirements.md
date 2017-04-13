
<properties
    pageTitle="Azure Active Directory hybrid identitet Designovervejelser - fastlægge krav til hændelsen rResponse | Microsoft Azure krav "
    description="Finde ud af, overvågning og rapportering funktioner for hybride identitet-løsning, der kan bruges til ved IT for at udføre handlinger til at identificere og reducere en potentielle trusler"
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

# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Bestemme hændelsen svar krav til din hybrid identitet løsning

Store eller mellemstore organisationer højst sandsynligt har et [sikkerhed hændelsen svar](https://technet.microsoft.com/library/cc700825.aspx) på plads til at IT udføre handlinger i overensstemmelse hermed til niveauet af hændelse. Administrationssystem identitet er en vigtig komponent i processen hændelsen svar, fordi den kan bruges til at identificere, hvem der har udført en bestemt handling forhold til målet. Hybrid identitet løsningen skal kunne give overvågnings- og funktioner, der kan bruges til ved IT for at udføre handlinger til at identificere og reducere en potentiel truslen. I en typisk hændelsen svar plan kan du bruge følgende faser som en del af planen:

1.  Indledende vurdering.
2.  Hændelsen kommunikation.
3.  Skade bekæmpelse og risikoen begrænsning.
4.  Identifikation af hvad den var sammensat og alvor.
5.  Dokumentation bevare.
6.  Besked om til relevante parter.
7.  Systemgendannelse.
8.  Dokumentation.
9.  Skade og omkostninger vurdering.
10. Proces og planlægge revision.

Under Identifikation af hvad det blev sammensat og alvor fase, vil det være nødvendigt at identificere de systemer, der er blevet kompromitteret, filer, der har været åbnet og bestemme følsomheden for filerne, som. Systemet hybrid identitet skal kunne opfylder kravene til at hjælpe dig med at identificere den bruger, der har foretaget disse ændringer. 

## <a name="monitoring-and-reporting"></a>Overvågning og rapportering
Mange gange identitet systemet kan også hjælpe i indledende vurdering fase, især hvis systemet har opbygget i overvågning og -rapportering funktioner. IT-administratoren skal være i stand til at identificere en mistænkelig aktivitet under den indledende vurdering, eller systemet bør kunne udløse det automatisk på baggrund af en forudkonfigurerede opgave. Mange aktiviteter kan skyldes et mulige angreb, men i andre tilfælde et forkert konfigurerede system kan føre til et antal forkerte forekomster i et system til registrering af uautoriseret adgang. 

Administrationssystem identitet skal hjælpe IT-administratorer til at identificere og rapportere disse mistænkelige aktiviteter. Normalt kan disse tekniske krav opfyldes ved at overvåge alle systemer og har en reporting egenskab, der kan fremhæve potentielle trusler. Brug nedenstående spørgsmål til at hjælpe dig med at designe dit hybride identitet løsning under tage i betragtning hændelsen svar krav:

- Dit firma har et sikkerhed hændelsen svar på plads?
 - Hvis det er tilfældet, er den aktuelle identitet administrationssystem bruges som en del af processen?
- Skal din virksomhed til at identificere mistænkelige enkeltlogon-forsøg fra brugere på tværs af forskellige enheder?
- Skal organisationen registrerer potentielle kompromitteret brugerens legitimationsoplysninger?
- Skal din virksomhed skal overvåges brugerens adgang og en handling?
- Skal din virksomhed vil vide, når en bruger nulstille sin adgangskode?

## <a name="policy-enforcement"></a>Politik for aktivering

Under skader kontrolelement og risikoen reduktion-fasen er det vigtigt at reducere hurtigt de faktiske og potentielle virkninger af et angreb. Den handling, du tager kan på dette tidspunkt gøre forskellen mellem en underordnede og en overordnet. Nøjagtige svaret, afhænger af din organisation og angreb, der kan opstå art. Hvis den indledende vurdering afsluttet, en konto er blevet afsløret, skal du gennemtvinge politik for at blokere denne konto. Det er et enkelt eksempel, hvor du vil opgraderede administrationssystem identitet. Brug nedenstående spørgsmål til at hjælpe dig med at designe din hybride identitet løsning under tage i betragtning hvordan politikker er tvungne for at reagere på en igangværende hændelse:

- Din virksomhed har politikker på plads til Bloker brugere fra access netværket eventuelt?
 - Hvis det er tilfældet, kan den aktuelle løsning integreres med hybride identitet management system, du vil indføre?
- Skal din virksomhed gennemtvinge betinget adgang til brugere, der er i karantæne? 
 
>[AZURE.NOTE]
Sørg for at tage noter på hvert svar og forstå begrundelse bag svaret. [Definer strategi til databeskyttelse](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) vil gennemgå de tilgængelige indstillinger og fordele/ulemper ved hver indstilling.  Skal have ved har besvaret de spørgsmål, du vil vælge, hvilken indstilling, der bedst passer til din virksomhed.

## <a name="next-steps"></a>Næste trin
[Angive strategi for beskyttelse af data](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Se også
[Design overvejelser i forbindelse med oversigt](active-directory-hybrid-identity-design-considerations-overview.md)
