<properties
   pageTitle="Administration af administrative enheder i Azure Active Directory"
   description="Ved hjælp af administrative enheder til mere detaljeret delegering af tilladelser i Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="administrative-units-management-in-azure-ad---public-preview"></a>Administration af administrative enheder i Azure AD - prøveversionen

I denne artikel beskrives administrative enheder – en ny Azure Active Directory-objektbeholder ressourcer, der kan bruges ved delegering administratorrettigheder over undersæt af brugere og anvender politikker til et undersæt af brugere. I Azure Active Directory aktivere administrative enheder central administratorer uddelegere tilladelser til administratorer af internationale eller for at angive politik på et detaljeret niveau.

Dette er nyttigt i organisationer med uafhængige divisioner, for eksempel en stor university, der består af mange selvstændige skoler (Business school, teknisk skole osv.), er uafhængigt af hinanden. Sådanne inddelinger har deres egne IT-administratorer, der kontrollere adgangen, administrere brugere og indstille politikker specifikt for deres division. Central Administratorer vil kunne give disse afdelingen administratorer tilladelser over brugere i deres bestemt divisioner. Mere specifikt i dette eksempel skal en central administrator kan, for eksempel oprette en administrativ enhed til en bestemt skole (Business skole) og udfylde den med kun de Business skole brugere. Derefter en central administrator kan føje Business skolen IT-medarbejdere til en relateret rolle, det vil sige, give IT-medarbejderne Business skole administratortilladelser kun over skole administrative afdeling.

> [AZURE.IMPORTANT]
> Du kan oprette og bruge administrative enheder, kun, hvis du aktiverer Azure Active Directory Premium. Du kan finde flere oplysninger i [Introduktion til Azure AD Premium](active-directory-get-started-premium.md).

En administrativ enhed er fra den centrale administrator synspunkt, et katalogobjekt, der kan oprettes og udfyldes med ressourcer. **Disse ressourcer kan være kun brugere i denne udgave.** Når du har oprettet og udfyldt, kan den administrative enhed bruges som et omfang til at begrænse tilladelsen tildelt kun over ressourcer, der er indeholdt i den administrative enhed.

## <a name="managing-administrative-units"></a>Administrere administrative enheder

I denne preview-versionen, kan du oprette og administrere administrative enheder ved hjælp af Azure Active Directory-modulet til Windows PowerShell-cmdletter.

Flere oplysninger om softwarekrav og installation af Azure AD-modulet og oplysninger om Azure AD-modulet-cmdletter til administration af administrative enheder, herunder syntaks, parameter beskrivelser og eksempler, kan du se [administrere Azure AD ved hjælp af Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).


## <a name="next-steps"></a>Næste trin
[Azure Active Directory-versioner](active-directory-editions.md)
