<properties
   pageTitle="Hvordan du kan give adgang til PIM | Microsoft Azure"
   description="Få mere at vide, hvordan du føjer roller til brugere med filtypenavnet Azure Active Directory privilegerede identitet Management, så de kan administrere PIM."
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
   ms.date="09/22/2016"
   ms.author="kgremban"/>

# <a name="how-to-give-access-to-manage-azure-ad-privileged-identity-management"></a>Sådan kan du give adgang til at administrere Azure AD privilegerede identitet administration

Den globale administrator, der aktiverer Azure AD privilegerede identitet Administration (PIM) for en organisation automatisk få rolletildelinger og adgang til PIM. Ingen andre får skriveadgang som standard, men, herunder andre globale administratorer. Andre globale administratorer, sikkerhedsadministratorer af og sikkerhed læsere har skrivebeskyttet adgang til Azure AD PIM. Hvis du vil give adgang til PIM, kan den første bruger tildeles andre **privilegerede rolle** administratorrollen. Denne opgave skal udføres fra i PIM selve og kan ikke ændres via PowerShell eller andre portaler.

> [AZURE.NOTE] Administrere Azure AD PIM kræver Azure MFA. Da Microsoft-konti ikke kan tilmeldes Azure MFA, kan en bruger, der logger på med en Microsoft-konto kan ikke få adgang til Azure AD PIM.

Kontrollér, at der er altid mindst to brugere i en privilegerede rolle administratorrolle i tilfælde af, at en bruger er låst ude eller deres konto slettes.

## <a name="give-another-user-access-to-manage-pim"></a>Give en anden brugeradgang til at administrere PIM

1. Log på [Azure-portalen](https://portal.azure.com/) , og vælg den **Azure AD privilegerede identitet Management** app på dashboardet.
2. Vælg **Administrer privilegerede roller** > **privilegerede rolle administrator** > **Tilføj**.

    ![Tilføje privilegerede rolle administratorer - skærmbillede][1]

4. Trin 1 er allerede fuldført på bladet Tilføj administrerede brugere. Vælg trin 2, **Vælg brugere,** og Søg efter den bruger, du vil tilføje.

    ![Vælg brugere - skærmbillede][2]

6. Vælg brugeren, i søgeresultaterne, og klik på **udført**.
7. Klik på **OK** for at gemme dit valg. Den bruger, du har valgt vises på listen over privilegerede rolle administratorer.

    - Når du tildeler en ny rolle til en person, er de automatisk konfigureret som støtteberettigede at aktivere rollen. Hvis du vil gøre dem permanente i rollen, skal du klikke på brugeren på listen. Marker **gør rettighed** i menuen bruger oplysninger.

8. Sende brugeren et link til [Kom i gang med administration af Azure AD privilegerede identitet](active-directory-privileged-identity-management-getting-started.md).


## <a name="remove-another-users-access-rights-for-managing-pim"></a>Fjerne en anden brugers adgangsrettigheder til administration af PIM

Før du fjerner en person fra administratorrollen privilegerede rolle, altid kontrollere, at der vil stadig være to brugere, der er tildelt til den.

1. Klik på rollen **privilegerede rolle administrator**i dashboardet PIM.  Listen over brugere i øjeblikket i den pågældende rolle vises.
2. Klik på brugeren i listen over brugere.
3. Klik på **Fjern**.  Der vises en bekræftelsesmeddelelse.
4. Klik på **Ja** for at fjerne brugeren fra rollen.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Næste trin
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_add_PRA.png
[2]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_select_users.png
