<properties
    pageTitle="Uden licens anvendelsesrapport | Microsoft Azure"
    description="Uden licens brugen rapport kan du identificerer brugere uden licens, der bruger betalt Azure AD-funktioner."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="unlicensed-usage-report"></a>Anvendelsesrapport for uden licens

Uden licens brugen rapport kan du identificerer brugere uden licens, der bruger betalt Azure AD-funktioner. Dette giver mulighed for at forbedre og brug af licenser, du har købt og for at identificere du ved, når du skal muligvis flere licenser. 

Rapporten viser aktive brugen af funktionerne betalt i de seneste 30 dage. 

## <a name="report-structure"></a>Rapport struktur
 
| Kolonnenavn          |    Beskrivelse |
| :--                  | :--         |
| Brugere uden licens      |    Navnet på brugeren |
| Funktion              | Navnet på funktionen. For eksempel: betinget adgang |
| Program, der åbnes | Navnet på det program, der bruges med funktionen. For eksempel: Office 365 SharePoint Online |

 
> [AZURE.NOTE] Hvis en brugerkonto er blevet slettet kolonnen 'Uden licens bruger' udfyldes med et ID, som 1003000090D8B285


## <a name="conditional-access-feature"></a>Funktionen betinget adgang

Brugere uden licens, markeres, når de får adgang til en tjeneste, der har betinget access politikken anvendes Hvis de ikke har en Azure AD Premium-licens. 

Dette gælder for MFA / placering politikker samt enhed politikker, der bruger Intune.
 

## <a name="see-also"></a>Se også

- [Ved hjælp af betinget Access med Office 365 og andre Azure Active Directory forbindelse apps](active-directory-conditional-access.md)
- [Introduktion til betinget adgang til Azure AD](active-directory-conditional-access-azuread-connected-apps.md) 


