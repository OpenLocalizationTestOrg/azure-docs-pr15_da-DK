<properties
    pageTitle="Betinget access enhedspolitikker for Office 365-tjenester | Microsoft Azure"
    description="Få at vide om hvordan enhed-baserede betingelser styre adgangen til Office 365-tjenester. Mens informationsmedarbejdere (IWs) vil få adgang til Office 365-tjenester som Exchange og SharePoint Online på arbejdet eller i skolen fra deres personlige enheder, ønsker deres IT-administratoren adgang skal være secure.IT administratorer kan tildele betinget access enhedspolitikker for at beskytte virksomhedsressourcer, mens samtidig tillade IWs på kompatible enheder for at få adgang til tjenester."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>
# <a name="conditional-access-device-policies-for-office-365-services"></a>Betinget access enhedspolitikker for Office 365-tjenester

Ord, "betinget adgang" har mange betingelser, der er knyttet til den som Multi-Factor godkendt bruger, godkendte enhed, kompatibel enhed osv. Dette emne focusses primært på enheden-baserede betingelser til at styre adgangen til Office 365-tjenester. Mens informationsmedarbejdere (IWs) vil få adgang til Office 365-tjenester som Exchange og SharePoint Online på arbejdet eller i skolen fra deres personlige enheder, vil deres IT-administrator i access til at være sikre. IT-administratorer kan tildele betinget access enhedspolitikker for at beskytte virksomhedsressourcer, mens samtidig tillade IWs på kompatible enheder for at få adgang til tjenester. Betinget access politikker til Office 365 kan konfigureres fra Microsoft Intune betinget access portal.

Azure Active Directory håndhæves betinget access politikker for at sikre adgang til Office 365-tjenester. En lejeradministrator kan oprette en betinget access-politik, der blokerer en bruger på en ikke-kompatibel enhed får adgang til en O365-tjeneste. Brugeren skal overholde virksomhedens enhedspolitikker, før du kan få tildelt adgang til tjenesten. Administratoren kan alternativt kan også oprette en politik, der kræver, at brugerne at lige tilmelde deres enheder for at få adgang til en O365-tjeneste. Politikker kan anvendes på alle brugere i en organisation, eller begrænset til et par målgrupper og udvidet over tid for at medtage flere målgrupper.

Der er en betingelse til at gennemtvinge politikker for enheder for brugerne at registrere deres enheder med Azure Active Directory enhed Registration service. Du kan vælge for at aktivere Multi-Factor authentication (MFA) til at registrere enheder med Azure Active Directory enhed Registration service. MFA anbefales til Azure Active Directory enhed Registration service. Når MFA er aktiveret, kan brugere registrere deres enheder med Azure Active Directory enhed Registration service, bedt om anden faktor-godkendelse.

##<a name="how-does-conditional-access-policy-work"></a>Hvordan fungerer betinget access-politik?

Når en bruger anmodninger om adgang til O365-tjeneste fra en understøttede enheder platform, godkender Azure Active Directory brugeren og enheden, som brugeren starter anmodningen; og giver adgang til tjenesten kun, når brugeren er i overensstemmelse med den politik indstillet til tjenesten. Brugere, der ikke har deres enhed tilmeldt får korrigerende vejledning om, hvordan du tilmelder dig og bliver kompatibel at få adgang til virksomhedens O365-tjenester. Brugere på iOS og Android-enheder skal at tilmelde deres enheder ved hjælp af firmaportal program. Når en bruger tilmelder sit enheden, er enheden registreret med Azure Active Directory, og registreret til administration af enheder og overholdelse af regler. Kunder skal bruge tjenesten Azure Active Directory enhed registrering sammen med Microsoft Intune til at aktivere Administration af mobilenheder til Office 365-tjenesten. Tilmelding er en foreløbig nødvendige for brugerne at få adgang til Office 365-tjenester, når politikker for enheder, håndhæves.

Når en bruger tilmelder sit enhed korrekt, bliver enheden, der er tillid til. Azure Active Directory indeholder Single-Sign-On til at få adgang til virksomhedens programmer og gennemtvinger betinget access-politik for at give adgang til en tjeneste ikke kun først tid brugeren anmoder om adgang, men hver gang brugeren anmoder om at forny adgang. Brugeren adgang ikke til tjenester når logonoplysninger ændres, enhed har mistet/stjæles eller politikken ikke er opfyldt på tidspunktet for anmodning om fornyelse.

## <a name="deployment-considerations"></a>Overvejelser om installation:
Azure Active Directory enhed registrering tjeneste for enhed registrering skal bruge.

Når brugere skal godkendes lokalt, er Active Directory Federation Services (AD FS) (1,0 og ovenfor) påkrævet. Multi-Factor Authentication for arbejdsplads deltage i mislykkes, når identitetsudbyder ikke kan Multi-Factor authentication. For eksempel AD FS 2.0 ikke er Multi-Factor authentication-kompatible. Lejeradministrationen skal sikre, at lokale AD FS MFA stand til og en gyldig MFA metode er aktiveret, før du aktiverer MFA på Azure Active Directory enhed Registration service. Eksempelvis har AD FS på Windows Server 2012 R2 MFA-funktioner. Du skal også aktivere en ekstra gyldige (MFA) godkendelsesmetode på AD FS-serveren før MFA på Azure Active Directory enhed Registration service. Se konfigurere yderligere godkendelsesmetoder til AD FS kan finde flere oplysninger om understøttede MFA metoder i AD FS.

## <a name="frequently-asked-questions-faq"></a>Ofte stillede spørgsmål (FAQ)

Sp: Hvad er udelukkelse standardpolitik for ikke-understøttede Enhedsplatforme?

Svar på nuværende tidspunkt gennemtvinges selektivt betinget access politikker for brugere på iOS og Android-enheder. Programmer på andre Enhedsplatforme er som standard ikke påvirket af politikken betinget adgang til iOS og Android-enheder. Lejeradministrator kan dog vælge at tilsidesætte den globale politik for at nægte adgang til brugere på ikke-understøttede platforme.
Det er på den vejledning i at udvide betinget access-politik for brugere på andre platforme, herunder Windows.

Sp: Når betinget access-politik til Office 365-tjenester udvides til browseren baseret apps (for eksempel OWA, browserbaserede SharePoint).

Svar på nuværende tidspunkt er betinget adgang til Office 365-tjenester begrænset til RTF-programmer på enheden. Det er på den vejledning i at udvide betinget access-politik til brugere, der bruger tjenesterne fra browsere.
