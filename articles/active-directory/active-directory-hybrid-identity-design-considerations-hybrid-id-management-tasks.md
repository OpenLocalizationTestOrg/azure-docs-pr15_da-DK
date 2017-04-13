<properties
    pageTitle="Azure Active Directory hybride identitet Designovervejelser - bestemme hybride identitet administrationsopgaver | Microsoft Azure"
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

# <a name="plan-for-hybrid-identity-lifecycle"></a>Plan for hybride identitet livscyklus 

Identitet er et af grundlaget for din mobilitet og programmet access strategi for enterprise. Uanset om du logger din mobilenhed eller SaaS app, er din identitet tasten til at få adgang til alt. På den højeste niveau omfatter en identitet management-løsning at gøre ensartede og synkronisering mellem din identitet typer lagre som omfatter automatisering og centralisering processen med at klargøring af ressourcer. Identitet løsningen skal være et centralt identitet på tværs af i det lokale miljø og skyen og også bruge andre former for identitetssammenslutninger til at vedligeholde centralt godkendelse og sikkert dele og samarbejde med eksterne brugere og virksomheder. Ressourcer interval fra operativsystemer og programmer til personer i eller tilknyttet en organisation. Organisationsstruktur kan ændres for at imødekomme klargøring politikker og procedurer.

Det er også vigtigt, at du har en identitet løsning rettet for at klæde dine brugere ved at give dem selvbetjening-oplevelsen til hele tiden produktiv. Din identitet løsning er mere robust, hvis det giver mulighed for single sign-on til brugere på tværs af alle de ressourcer, de har brug for, at få adgang til administratorer på alle niveauer kan bruge standardiseret procedurer til at styre brugerlegitimationsoplysninger. Nogle niveauer af administration kan reduceret eller fjernet, afhængigt af bredden af klargøring management-løsning. Desuden kan du sikkert distribuere funktioner til administration, manuelt eller automatisk, mellem forskellige organisationer. For eksempel kan en domæneadministrator tjene kun personer og ressourcer i det pågældende domæne. Denne bruger kan udføre administrative og klargøring opgaver, men har ikke tilladelse til at gøre konfigurationsopgaver, som oprettelse af arbejdsprocesser.


## <a name="determine-hybrid-identity-management-tasks"></a>Bestemme Hybrid identitet administrationsopgaver
Distribuere administrative opgaver i din organisation forbedrer nøjagtigheden og effektiviteten af administration og forbedrer saldoen på arbejdsbelastningen af en organisation. Følgende er Pivoter, der definerer et robust identitet administrationssystem.

 ![](./media/hybrid-id-design-considerations/Identity_management_considerations.png)


For at definere hybrid identitet administrationsopgaver, skal du forstå nogle væsentlige karakteristika for den organisation, der vil vedtagelse hybrid identitet. Det er vigtigt at forstå de aktuelle typer lagre bruges til identitet kilder. Med viden om de grundlæggende elementer, du vil have finde grundlæggende krav og baseret på, at du bliver nødt til at få mere detaljerede spørgsmål, som fører dig til en bedre design beslutning for din identitet løsning.  

Når du definerer disse krav, sikre, at mindst følgende spørgsmål besvares

- Indstillinger for klargøring: 
 - Understøtter hybrid identitet løsningen en robust kontoadministration access og klargøringssystem?
 - Hvordan er brugere, grupper og adgangskoder, der skal administreres?
 - Er identitet livscyklus management svarede? 
      - Hvor lang tid tager adgangskode opdateringer konto annullering?
      
- Håndtering af licenser: 
 - Betyder hybride identitet løsning håndtag licens management?
     - Hvis det er tilfældet, hvilke funktioner er tilgængelige?
- Håndterer løsningen administration af gruppe-baserede licenser? 
      -Hvis det er tilfældet, er det muligt at tildele en sikkerhedsgruppe til den? 
       -Hvis Ja, vil mappen skyen automatisk tildele licenser til alle medlemmer af gruppen? 
        -Hvad sker der, hvis en bruger er efterfølgende tilføjet eller fjernet fra gruppen, vil en licens bliver automatisk tildelt eller fjernet efter behov? 

- Integration med andre fra tredjepart identitetsudbydere:
- Kan denne hybridløsning integreres med tredjeparts identitetsudbydere at implementere enkeltlogon?
- Er det muligt at samle de forskellige identitetsudbydere i et sammenhængende identitetssystem?
- Hvis det er tilfældet, hvordan og hvilke der er de, og hvilke funktioner er der?

## <a name="synchronization-management"></a>Synkronisering af administration
Et mål for en identitet manager skal kunne få alle identitetsudbydere og holde dem synkroniseret. Du bevare dataene synkroniseret baseret på en autoritative master identitetsudbyder. I et scenarie med identitet hybrid, med en synkroniseret management-model, du administrerer alle brugeren og enheden identiteter i en lokal server og synkronisere den konti og eventuelt adgangskoder til skyen. Brugeren angiver de samme adgangskode lokalt som han eller hun fungerer i skyen, og på logon, adgangskoden er godkendt af løsningen identitet. Denne model anvender en katalogsynkronisering.
 
![](./media/hybrid-id-design-considerations/Directory_synchronization.png)Stort designet synkroniseringen af din hybrid identitet løsning Sørg for, at følgende spørgsmål besvares: • Hvad er de Synkroniser løsninger, der er tilgængelige for hybride identitet løsningen?
• Hvad er single sign-on funktioner, der er tilgængelige?
• Hvad er indstillingerne for identitetssammenslutninger mellem B2B og B2C?

## <a name="next-steps"></a>Næste trin
[Fastlægge hybride identitet management indføring strategi for](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)


## <a name="see-also"></a>Se også
[Design overvejelser i forbindelse med oversigt](active-directory-hybrid-identity-design-considerations-overview.md)

