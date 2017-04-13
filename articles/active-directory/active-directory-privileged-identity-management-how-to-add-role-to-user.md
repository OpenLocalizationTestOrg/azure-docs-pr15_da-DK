<properties
   pageTitle="Hvordan du kan tilføje eller fjerne en brugerrolle | Microsoft Azure"
   description="Lær at føje roller til privilegerede identiteter med Azure Active Directory privilegerede Identitetsstyring af programmet."
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
   ms.date="10/24/2016"
   ms.author="kgremban"/>

# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Azure AD privilegerede identitet administration: Hvordan du kan tilføje eller fjerne en brugerrolle

Med Azure Active Directory (AD), kan en global administrator (eller virksomhedens administrator) opdatere hvilke brugere der **permanent** tildelt til roller i Azure AD. Dette er færdig med PowerShell-cmdlet'er som `Add-MsolRoleMember` og `Remove-MsolRoleMember`. Eller de kan bruge Azure klassisk portalen som beskrevet i [tildele administratorroller i Azure Active Directory](active-directory-assign-admin-roles.md).

Azure AD privilegerede identitet Management programmet kan privilegerede rolle administratorer foretage permanente rolletildelinger, samt. Desuden kan privilegerede rolle administratorer foretage brugere **berettiget** til administratorroller. Administrator berettiget kan aktivere rollen, når de skal bruge den, og derefter tilladelserne udløber, når de er færdig.

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>Administrere roller med PIM i portalen Azure

I din organisation, kan du tildele brugere til forskellige administrative roller i Azure AD, Office 365 og andre Microsoft-tjenester og programmer.  Flere oplysninger om de tilgængelige roller kan findes på [roller i Azure AD PIM](active-directory-privileged-identity-management-roles.md).

Hvis du vil tilføje eller fjerne en bruger i en rolle ved hjælp af privilegerede identitet Management, få vist dashboardet PIM. Klik derefter enten klikke på knappen **brugere i administratorroller** , eller vælge en bestemt rolle (som Global Administrator) fra tabellen roller.

> [AZURE.NOTE] Hvis du endnu ikke har aktiveret PIM i portalen Azure, kan du gå til [komme i gang med Azure AD PIM](active-directory-privileged-identity-management-getting-started.md) få mere at vide.

Hvis du vil give en anden brugeradgang til PIM selve, er de roller, PIM kræver, at brugeren skal have beskrevet yderligere i [Sådan kan du give adgang til PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="add-a-user-to-a-role"></a>Tilføje en bruger til en rolle

1. Vælg feltet **Azure AD privilegerede identitet Management** på dashboardet i [Azure-portalen](https://portal.azure.com/).
2. Vælg **Administrer privilegerede roller**.
3. Vælg den rolle, du vil administrere i tabellen **rolle oversigt** .
4. Vælg **Tilføj**bladet rolle.
5. Klik på **Vælg brugere,** og Søg efter bruger på bladet **Vælg brugere** .  
6. Vælg brugeren, fra listen over søgeresultater, og klik på **udført**.
4. Klik på **OK** for at gemme dit valg. Den bruger, du har valgt vises på listen som berettiget til rollen.

> [AZURE.NOTE]
>Nye brugere i en rolle kan kun rollen som standard. Hvis du vil gøre rollen permanent, skal du klikke på brugeren på listen. Brugerens oplysninger vises i en ny blade. Marker **gør rettighed** i menuen bruger oplysninger.  
>Hvis en bruger kan ikke registrere til Azure Multi-Factor Authentication (MFA) eller bruger en Microsoft-konto (som regel @outlook.com), skal du gøre dem permanente i alle deres roller. Gyldigt administratorer bliver bedt om at registrere til MFA under aktivering.

Nu, hvor brugeren er berettiget til en rolle, du oplyse dem om, at de kan aktivere det ifølge instruktionerne i [Sådan aktiveres eller deaktiveres en rolle](active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="remove-a-user-from-a-role"></a>Fjerne en bruger fra en rolle

Du kan fjerne brugere fra berettiget rolletildelinger, men Sørg for, at der er altid mindst én bruger, der er en permanent global administrator.

Følg disse trin for at fjerne en bestemt bruger fra en rolle:

1. Gå til rollen på listen rolle, enten ved at vælge en rolle i dashboardet Azure AD PIM eller ved at klikke på knappen **brugere i administratorroller** .
2. Klik på brugeren i listen over brugere.
3. Klik på **Fjern**. En meddelelse beder dig for at bekræfte.
4. Klik på **Ja** for at fjerne rollen fra brugeren.

Hvis du ikke er sikker på, hvilke brugere stadig har brug for deres rolletildelinger, derefter kan du [starte en access-Gennemse for rollen](active-directory-privileged-identity-management-how-to-start-security-review.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Næste trin
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
