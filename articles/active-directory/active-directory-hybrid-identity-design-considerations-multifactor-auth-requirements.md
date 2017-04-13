<properties
    pageTitle="Azure Active Directory hybride identitet Designovervejelser - fastlægge krav til multi-Factor authentication"
    description="Med betinget adgangskontrol kontrollerer Azure Active Directory de bestemte betingelser, du vælger når godkender brugeren og før du tillader adgang til programmet. Når disse betingelser er opfyldt, kan brugeren er godkendt og har adgang til programmet."
    documentationCenter=""
    services="active-directory"
    authors="femila"
    manager="billmath"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Bestemme Multi-Factor authentication krav til din hybrid identitet løsning

I denne verden med mobilitet med brugere adgang til data og programmer i skyen og fra enhver enhed, er det blevet største at sikring af disse oplysninger.  Hver dag er der en ny overskrift om en sikkerhedsbrist.  Selvom der er ingen garanti mod sådanne brud, giver Multi-Factor godkendelse, et ekstra sikkerhedslag for at forhindre disse brud på.
Start ved evaluering af organisationer kravene til multi-Factor authentication. Det vil sige, hvad er organisationen forsøg på at sikre.  Denne evaluering er vigtigt at definere de tekniske krav til at konfigurere og aktivere organisationer brugere til multi-Factor authentication.

>[AZURE.NOTE]
Hvis du ikke kender med MFA og dens virkemåde, anbefales det, at du læser i artiklen [Hvad er Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md) forudgående at fortsætte med at læse dette afsnit.

Sørg for at besvare følgende:

- Er din virksomhed forsøg på at sikre Microsoft apps? 
- Hvordan publiceres disse apps?
- Giver virksomheden fjernadgang til at tillade medarbejdere at få adgang til lokale apps?

Hvis det er tilfældet, hvilken type fjernadgang? Du skal også evaluere, hvor de brugere, der har adgang til disse programmer skal placeres. Denne evaluering er en anden vigtige trin til at definere den korrekte Multi-Factor authentication strategi. Sørg for at besvare spørgsmål, der er følgende:

- Hvor er brugerne, der skal være placeret?
- Kan de være placeret et sted?
- Din virksomhed vil fastsætte restriktioner efter brugerens placering?

Når du forstår disse krav, er det vigtigt at også Vurder brugerens krav til multi-Factor authentication. Denne evaluering er vigtigt, da det vil definere kravene til distribution af godkendelse i flere niveauer. Sørg for at besvare spørgsmål, der er følgende:

- Har brugerne kendskab til multi-Factor godkendelse?
- Nogle bruger bliver bedt om at angive yderligere godkendelse?  
 - Hvis det er tilfældet, hele tiden, når der kommer fra eksterne netværk eller adgang til bestemte programmer eller andre betingelser?
- Kræver brugerne kursus om, hvordan du konfigurerer og implementere Multi-Factor authentication?
- Hvad er de vigtigste scenarier, som din virksomhed ønsker at aktivere Multi-Factor godkendelse til brugerne?

Efter besvare de forrige spørgsmål, vil du kunne forstå, hvis der er Multi-Factor authentication allerede implementeret lokalt. Denne evaluering er vigtigt at definere de tekniske krav til at konfigurere og aktivere organisationer brugere til multi-Factor authentication. Sørg for at besvare spørgsmål, der er følgende:

- Skal din virksomhed til at beskytte privilegerede konti med MFA?
- Skal din virksomhed til at aktivere MFA for visse programmet for overholdelse af angivne standarder årsager?
- Skal din virksomhed til at aktivere MFA for alle brugere, der er omfattet af disse program eller kun administratorer?
- Du skal bruge har MFA altid er aktiveret eller kun, når brugerne er logget uden for virksomhedens netværk?


## <a name="next-steps"></a>Næste trin
[Definere en hybrid identitet indføring strategi](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)


## <a name="see-also"></a>Se også
[Design overvejelser i forbindelse med oversigt](active-directory-hybrid-identity-design-considerations-overview.md)
