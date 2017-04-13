<properties
    pageTitle="Opgradering fra DirSync og Azure Active Directory-synkronisering | Microsoft Azure"
    description="I denne artikel beskrives, hvordan du opgraderer fra DirSync og Azure Active Directory-synkronisering til Azure AD-forbindelse."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="billmath"/>


# <a name="upgrade-windows-azure-active-directory-sync-dirsync-and-azure-active-directory-sync-azure-ad-sync"></a>Opgradere Windows Azure Active Directory-synkronisering ("DirSync") og Azure Active Directory-synkronisering ("Azure Active Directory-synkronisering")
Azure AD-forbindelse er den bedste måde at oprette forbindelse til din lokale mappe med Azure AD og Office 365. Dette er et godt tidspunkt at opgradere til Azure AD-forbindelse fra Windows Azure Active Directory-synkronisering (DirSync) eller Azure Active Directory-synkronisering, som disse værktøjer er nu er forældet og vil oprette forbindelse til slutningen af support på April 13 2017.

De to identity synkronisering værktøjer, som er udeladt blev tilbydes til kunder, enkelt område (DirSync) og til flere områder og andre avancerede kunder (Azure Active Directory-synkronisering). Disse værktøjer, der er blevet erstattet med en enkelt løsning, der er tilgængelige for alle scenarier: Azure AD-forbindelse. Det giver adgang til nye funktioner, forbedrede funktioner og understøttelse af nye scenarier. Skal kunne fortsætte med at synkronisere dine lokale identitetsdata til Azure AD og Office 365, anbefales det, at du opgraderer til Azure AD-forbindelse.

Den seneste version af DirSync blev udgivet i marts 2014, og den seneste version af Azure Active Directory-synkronisering blev udgivet i maj 2015.

## <a name="what-is-azure-ad-connect"></a>Hvad er Azure AD-forbindelse
Azure AD-forbindelse er efterfølgende DirSync og Azure Active Directory-synkronisering. Den kombinerer alle scenarier disse to understøttes. Du kan læse mere om det i [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md).

## <a name="deprecation-schedule"></a>Ændrede funktionsmåde tidsplan

Dato | Kommentar
 --- | ---
April 13 2016 | Windows Azure Active Directory-synkronisering ("DirSync") og Microsoft Azure Active Directory-synkronisering ("Azure Active Directory-synkronisering") er offentliggjort som forældet.
April 13 2017 | Understøtter ender. Kunder vil ikke længere kunne åbne en supportsag uden at opgradere til Azure AD-forbindelse først.

## <a name="how-to-transition-to-azure-ad-connect"></a>Sådan overgang til Azure AD-forbindelse
Hvis du kører DirSync, der er to måder, du kan opgradere: lokal opgradering og parallelle installation. En lokal opgradering anbefales til de fleste kunder og hvis du har en seneste operativsystem og mindre end 50.000 objekter. I andre tilfælde anbefales det at gøre en parallel installation, hvor konfigurationen af DirSync flyttes til en ny server, der kører Azure AD-forbindelse.

Hvis du bruger Azure Active Directory-synkronisering anbefales en opgradering. Hvis du vil, er det muligt at installere en ny Azure AD-forbindelse server parallelt og gøre en udslag overførsel fra serveren Azure Active Directory-synkronisering til Azure AD-forbindelse.

Løsning | Scenarie
----- | -----
[Opgradering fra DirSync](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md) | <li>Hvis du har en eksisterende DirSync-server, der allerede kører.</li>
[Opgradering fra Azure Active Directory-synkronisering](active-directory-aadconnect-upgrade-previous-version.md)| <li>Hvis du vil flytte fra Azure Active Directory-synkronisering.</li>

Hvis du vil se, hvordan du gør en opgradering fra DirSync til Azure AD-forbindelse, skal du se denne kanal 9 video:

> [AZURE.VIDEO azure-active-directory-connect-in-place-upgrade-from-legacy-tools]

## <a name="faq"></a>OFTE STILLEDE SPØRGSMÅL
**Sp: Jeg har modtaget en mailmeddelelse fra Azure Team og/eller en meddelelse fra Office 365-meddelelser, men jeg bruger Opret forbindelse.**  
Meddelelsen blev sendt også til kunder, der bruger Azure AD-forbindelse med en build-nummeret 1.0. \*.0 (ved hjælp af en pre 1.1 version). Microsoft anbefaler kunder til at holde dig orienteret med Azure AD-forbindelse udgaver. 1.1 [automatisk opgradering](active-directory-aadconnect-feature-automatic-upgrade.md) funktion gør det installeret nemt at altid har en nyere version af Azure AD-forbindelse.

**Sp: vil DirSync/Azure Active Directory-synkronisering stoppe med at arbejde på April 13 2017?**  
Nej. Datoen, når disse vil ikke længere kunne kommunikere med Azure AD bliver offentliggjort på et senere tidspunkt. Du vil kunne finde disse oplysninger i dette emne, når de er tilgængelige.

**Sp: hvilke DirSync-versioner kan jeg opgradere fra?**  
Det understøttes for at opgradere fra en hvilken som helst DirSync udgave i øjeblikket bruges.

**Sp: Hvad med den Azure AD-forbindelse til FIM/MIM?**  
Azure AD-forbindelse til FIM/MIM er **ikke** blevet offentliggjort som forældet. Det er på **funktion fryse**; ingen nye funktioner er tilføjet, og den modtager ingen fejlrettelser. Microsoft anbefaler kunder, der bruger den til at planlægge at flytte fra den til Azure AD-forbindelse. Det anbefales ikke starte en ny installationer ved hjælp af den. Denne forbindelse bliver offentliggjort forældet i fremtiden.

## <a name="additional-resources"></a>Yderligere ressourcer

* [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
