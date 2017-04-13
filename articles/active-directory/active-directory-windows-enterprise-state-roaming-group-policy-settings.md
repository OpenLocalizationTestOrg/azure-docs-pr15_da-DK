<properties
    pageTitle="Indstillinger for politik og MDM grupper | Microsoft Azure"
    description="Indeholder oplysninger om Gruppepolitik og mobilenhed administrationsindstillinger (MDM), der skal bruges på virksomhedens ejes enheder. Disse politikker anvendes på brugerens hele enhed."
    services="active-directory"
    keywords="Hvad er gruppe politik og MDM indstillinger for Enterprise tilstand Roaming, Enterprise tilstand Roaming, windows skyen"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="group-policy-and-mdm-settings"></a>Indstillinger for Gruppepolitik og MDM

Brug disse Gruppepolitik og mobilenhed administrationsindstillinger (MDM) kun på virksomhedens ejes enheder, fordi disse politikker anvendes på brugerens hele enhed. Anvende en politik for MDM for at deaktivere synkronisering af indstillinger for en personlig, effekt bruger ejes enhed en negativ brugen af den pågældende enhed. Andre brugerkonti på enheden, desuden også påvirkes af politikken.

Virksomheder, vil administrere roaming til personlige (ikke-administreret) enheder kan bruge portalen Azure til at aktivere eller deaktivere roaming i stedet for at bruge Gruppepolitik eller MDM.
I følgende tabel beskrives de politikindstillinger, der er tilgængelige.

## <a name="mdm-settings"></a>Indstillinger for MDM
Politikindstillinger MDM gælder for både Windows 10 og Windows 10 Mobile.  Understøttelse af Windows 10 Mobile findes kun til Microsoft-konto baseret roaming via brugerens OneDrive-konto.  Se afsnittet "Enheder og slutpunkter" oplysninger om, hvilke enheder understøttes til Azure AD baseret synkronisering.

| Navn                               | Beskrivelse                                                          |
|------------------------------------|----------------------------------------------------------------------|
| Tillade forbindelse til Microsoft-konto | Gør det muligt for brugerne at godkende ved hjælp af en Microsoft-konto på enheden |
| Tillad Synkroniser mine indstillinger             | Gør det muligt for brugerne at gennemsøge indstillinger i Windows og app-data Hvis du deaktiverer denne politik deaktiverer synkronisering samt sikkerhedskopier på mobile enheder                  |

## <a name="group-policy-settings"></a>Indstillinger for Gruppepolitik
Indstillingerne for Gruppepolitik gælder for Windows 10-enheder, der er tilsluttet et Active Directory-domæne. Tabellen indeholder også ældre indstillinger, der ville ser ud til at administrere synkroniseringsindstillinger, men det ikke virker for Enterprise tilstand Roaming til Windows 10, der er angivet med 'Do not use' i beskrivelsen.

| Navn                                | Beskrivelse |
|-------------------------------------|-------------|
| Konti: Bloker Microsoft-konti  |Denne indstilling forhindrer brugere i at tilføje nye Microsoft-konti på denne computer|
| Synkroniser ikke                         |Forhindrer brugere for at gennemsøge indstillinger i Windows og app-data|
| Synkroniserer ikke tilpasse             |Deaktiverer synkronisering af gruppen temaer|
| Synkroniser ikke browserindstillinger        |Deaktiverer synkronisering af gruppen Internet Explorer|
| Synkroniser ikke adgangskoder               |Deaktiverer synkronisering af adgangskoder gruppe|
| Synkroniser ikke andre Windows-indstillinger  |Deaktiverer synkronisering af gruppen af andre Windows-indstillinger|
| Synkroniser ikke skrivebord med personlige indstillinger |Brug ikke; har ingen virkning|
| Synkroniser ikke på betalte forbindelser  |Deaktiverer roaming på forbrugsafregnede forbindelser, som mobildata 3 G|
| Synkroniser ikke apps                    |Brug ikke; har ingen virkning|
|Synkroniser ikke app-indstillinger             |Deaktiverer roaming af app-data|
|Synkroniser ikke startindstillinger           |Brug ikke; har ingen virkning|


## <a name="related-topics"></a>Relaterede emner
- [Enterprise tilstand Roaming oversigt](active-directory-windows-enterprise-state-roaming-overview.md)
- [Aktivere enterprise tilstand roaming i Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
- [Indstillinger og data roaming ofte stillede spørgsmål](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Windows 10 globale indstillinger reference](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
