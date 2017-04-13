<properties
    pageTitle="Azure AD og programmer: tildele grupper til programmet | Microsoft Azure"
    description="Sådan implementere gruppetildeling til Azure-programmer."
    services="active-directory"
    documentationCenter=""
    authors="IHenkel"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/03/2015"
    ms.author="inhenk"/>

# <a name="azure-ad-and-applications-assigning-groups-to-an-application"></a>Azure AD og programmer: tildele grupper til et program
Før du kan tildele brugere og grupper til et program, skal du har brug for tildelingen bruger. For at lære at kræve bruger tildeling, skal du se artiklen [Kræve bruger tildeling](active-directory-applications-guiding-developers-requiring-user-assignment.md) .

Denne artikel forudsætter, at du allerede har oprettet grupper i active directory, du bruger til dette program.

## <a name="assigning-groups-to-an-application"></a>Tildele grupper til et program
1. Log på portalen Azure med en administratorkonto.
2. Klik på elementet **Alle elementer** i hovedmenuen.
3. Vælg den mappe, du bruger til programmet.
4. Klik på fanen **programmer** .
5. Vælg programmet på listen over programmer, der er knyttet til denne mappe.
6. Klik på fanen **Brugere og grupper** .
7. Filtrere en liste over grupper i din active directory ved hjælp af rullelisten **grupper** .
8. Vælg gruppen.
9. Klik på **TILDEL**.
10. Klik på **Ja** , når du bliver bedt om.

## <a name="next-steps"></a>Næste trin
[AZURE.INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]
