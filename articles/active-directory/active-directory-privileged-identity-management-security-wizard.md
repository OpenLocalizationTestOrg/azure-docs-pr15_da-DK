<properties
   pageTitle="Guiden Azure AD privilegerede identitet Management sikkerhed"
   description="Første gang du bruger filtypenavnet Azure Active Directory privilegerede identitet Management, vises der med en guide, sikkerhed. I denne artikel beskrives trinnene til brug af guiden."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# <a name="the-azure-ad-privileged-identity-management-security-wizard"></a>Guiden Azure AD privilegerede identitet Management sikkerhed

Hvis du er den første person til at køre Azure privilegerede identitet Management (PIM) til din organisation, kan du der præsenteres med en guide. Guiden hjælper dig med at forstå sikkerhedsrisici af privilegerede identiteter, og hvordan du bruger PIM til at reducere disse risici. Du behøver ikke at foretage ændringer i eksisterende rolletildelinger i guiden, hvis du foretrækker at gøre det senere.

## <a name="what-to-expect"></a>Hvad du kan forvente

Før organisationen begynder at bruge PIM, alle rolletildelinger er permanente: brugerne er altid i disse roller, selvom de ikke i øjeblikket behøver deres rettigheder.  Det første trin i guiden viser en liste over høj rettigheder roller og hvor mange brugere der aktuelt findes i disse roller. Du kan analysere til en bestemt rolle vil vide mere om brugere, hvis en eller flere af disse har kendskab.

Det andet trin i guiden giver dig mulighed for at ændre administratorens rolletildelinger.  

> [AZURE.WARNING]Det er vigtigt, at du har mindst én global administrator og mere end én privilegerede rolle administrator med en organisationskonto (ikke en Microsoft-konto). Hvis der er kun én privilegerede rolle administrator, kan organisationen kan ikke administrere PIM, hvis kontoen slettes.
> Desuden holde rolletildelinger permanente Hvis en bruger har en Microsoft-konto (en konto, de kan bruge til at logge på Microsoft-tjenester som Skype og Outlook.com). Hvis du planlægger at kræve MFA til aktivering af rollen, vil denne bruger være låst ude.


Når du har foretaget ændringer, vises guiden ikke længere. Næste gang du eller en anden privilegerede rolle administrator bruger PIM, får du vist PIM dashboard.  

- Hvis du gerne vil tilføje eller fjerne brugere fra roller eller ændre tildelinger fra permanent til berettiget, du finder flere oplysninger ved [at tilføje eller fjerne en brugers rolle](active-directory-privileged-identity-management-how-to-add-role-to-user.md).
- Hvis du gerne vil give flere brugere adgang til at administrere PIM, kan du få mere at vide, [hvordan man kan give adgang til at administrere i PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).



## <a name="next-steps"></a>Næste trin
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
