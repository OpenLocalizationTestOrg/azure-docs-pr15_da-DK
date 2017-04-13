<properties
   pageTitle="Roller i PIM | Microsoft Azure"
   description="Få mere at vide, hvilke roller, der bruges til privilegerede identiteter med filtypenavnet Azure privilegerede identitet Management."
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

# <a name="roles-in-azure-ad-privileged-identity-management"></a>Roller i Azure AD følsomme identitet administration

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Du kan tildele brugere i organisationen at forskellige administrative roller i Azure AD. Disse rolletildelinger bestemme, hvilke opgaver såsom at tilføje eller fjerne brugere eller ændre Tjenesteindstillinger, brugerne er i stand til at udføre på Azure AD, Office 365 og andre Microsoft Online Services og forbundne programmer.  

En global administrator kan opdatere hvilke brugere der **permanent** tildelt til roller i Azure AD, ved hjælp af PowerShell-cmdlet'er såsom `Add-MsolRoleMember` og `Remove-MsolRoleMember`, eller via portalen klassisk som beskrevet i [tildele administratorroller i Azure Active Directory](active-directory-assign-admin-roles.md).

Azure AD rettigheder identitet Management (PIM) administrerer politikker for privilegerede adgangen for brugere i Azure AD. PIM tildeler brugere til en eller flere roller i Azure AD, og du kan tildele en skal være permanent i rollen, eller berettiget til rollen. Når en bruger tildeles permanent til en rolle eller aktiverer et gyldigt rolletildeling, og de kan administrere Azure Active Directory, Office 365 og andre programmer med de tilladelser, der er tildelt til deres roller.

Der er ingen forskel i dialogboksen Åbn, der er angivet til en person med et permanent kontra et gyldigt rolletildeling. Den eneste forskel er, at nogle personer ikke behøver at adgangen hele tiden. Der er oprettet berettiget til rollen, og kan aktiveres og fra når de skal bruge.

## <a name="roles-managed-in-pim"></a>Roller, der administreres i PIM

Administration af privilegerede identitet kan du tildele brugere til almindelige administratorroller, herunder:


- **Global administrator** (også kendt som virksomhedens administrator) har adgang til alle administrative funktioner. Du kan have mere end én global administrator i organisationen. Den person, der tilmelder sig til at købe Office 365 automatisk bliver en global administrator.
- **Privilegerede rolle administratoren** administrerer Azure AD PIM og opdaterer rolletildelinger for andre brugere.  
- **Fakturering administrator** foretager køb, administrerer abonnementer, administrerer supportbilletter og overvåger tjenestetilstanden.
- **Adgangskodeadministrator** nulstiller adgangskoder, administrerer serviceanmodninger og overvåger tjenestetilstanden. Adgangskodeadministratorer er begrænset til nulstilling af adgangskoder for brugere.
- **Tjenesteadministratoren** administrerer serviceanmodninger og skærme tjenestetilstand.

  > [AZURE.NOTE] Hvis du bruger Office 365, derefter før tildeling administratorrolle service til en bruger, først tildele brugeren administratortilladelser til en tjeneste, såsom Exchange Online.

- **Brugeradministrator** nulstiller adgangskoder, overvåger tjenestetilstanden og administrerer brugerkonti, brugergrupper og serviceanmodninger. Brugeradministrator kan ikke slette en global administrator, oprette andre administratorroller eller nulstille adgangskoder for global, fakturering og tjenesteadministratorer.
- **Exchange-administrator** har administratoradgang til Exchange Online via Exchange Administration (EAC), og der kan udføre næsten alle opgaver i Exchange Online.
- **SharePoint-administratoren** har administratoradgang til SharePoint Online via SharePoint Online administration, og kan udføre næsten alle opgaver i SharePoint Online.
- **Skype for Business-administrator** har administratoradgang til Skype for Business via Skype for Business-administrationscenter og kan udføre næsten alle opgaver i Skype for Business Online.

Læs disse artikler kan finde flere oplysninger om at [tildele administratorroller i Azure AD](active-directory-assign-admin-roles.md) og [tildele administratorroller i Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


Fra PIM kan du [tildele disse roller til en bruger](active-directory-privileged-identity-management-how-to-add-role-to-user.md) , så brugeren kan [aktivere rollen, når det er nødvendigt](active-directory-privileged-identity-management-how-to-activate-role.md).

Hvis du vil give en anden brugeradgang til at administrere i PIM selve, er de roller, PIM kræver, at brugeren skal have beskrevet yderligere i [Sådan kan du give adgang til PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>Roller, der ikke administreres i PIM

Roller i Exchange Online eller SharePoint Online, med undtagelse af dem, der er nævnt ovenfor, er repræsenteret ikke i Azure AD og så der ikke er synlige i PIM. Du kan finde flere oplysninger om ændring af detaljerede rolletildelinger i disse Office 365-tjenester, [tilladelser i Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Azure-abonnementer og ressourcegrupper også repræsenteret ikke i Azure AD. For at administrere Azure-abonnementer skal du se, [hvordan du tilføjer eller ændrer Azure administratorroller](../billing-add-change-azure-subscription-administrator.md) og yderligere oplysninger om Azure RBAC se [Azure Role-Based adgangskontrol](role-based-access-control-configure.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Brugerroller og logge på
For nogle Microsoft-tjenester og programmer kan tildele en bruger til en rolle måske ikke er tilstrækkelige til at aktivere denne bruger skal være administrator.

Adgang til portalen Azure klassisk kræver brugeren er en tjenesteadministratoren eller samtidig administrator for en Azure-abonnement, selvom brugeren ikke har brug for at kunne administrere Azure abonnementer.  For eksempel for at administrere indstillinger for søgekonfiguration til Azure AD i portalen klassisk skal skal en bruger være både en global administrator i Azure AD og en abonnement samtidig administrator på et Azure-abonnement.  Hvis du vil se, hvordan du tilføjer brugere til Azure abonnementer, se, [hvordan du tilføjer eller ændrer Azure administratorroller](../billing-add-change-azure-subscription-administrator.md).

Adgang til Microsoft Online Services kan kræve brugeren også tildeles en licens før de kan åbne tjenestens portal eller udføre administrative opgaver.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Tildele en licens til en bruger i Azure AD

1. Log på [Azure klassisk portalen] (http://manage.windowsazure.com) med en global administratorkonto eller en samtidig administratorkonto.
2. Markere **Alle elementer** fra hovedmenuen.
3. Vælg den mappe, du vil arbejde med, og som har licenser, der er knyttet til den.
4. Vælg **licenser**. Der vises på listen over tilgængelige licenser.
5. Vælg den plan, licens som indeholder de licenser, du vil dele.
6. Vælg **tildele brugere**.
7. Vælg den bruger, du vil tildele en licens til.
8. Klik på knappen **tildele** .  Brugeren kan nu logge på Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Næste trin
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
