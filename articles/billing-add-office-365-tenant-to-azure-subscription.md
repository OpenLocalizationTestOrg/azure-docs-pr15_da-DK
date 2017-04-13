<properties
    pageTitle="Bruge en Office 365-lejer med et Azure abonnement | Microsoft Azure"
    description="Lær at tilføje en Office 365-mappe (lejer) til en Azure-abonnement for at gøre tilknytningen."
    services=""
    documentationCenter=""
    authors="JiangChen79"
    manager="mbaldwin"
    editor=""
    tags="billing,top-support-issue"/>

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="cjiang"/>

# <a name="associate-an-office-365-tenant-with-an-azure-subscription"></a>Knytte en Office 365-lejer til en Azure-abonnement
Hvis du har anskaffet både Azure og Office 365-abonnementer separat tidligere, og nu vil ikke have adgang til Office 365-lejer fra Azure abonnementet, er det nemt at gøre dette. I denne artikel beskrives, hvordan.

> [AZURE.NOTE] I denne artikel gælder ikke for Enterprise-aftale (EA) kunder.

## <a name="quick-guidance"></a>Hurtig vejledning
Brug din Azure-konto til at tilføje din Office 365-lejer for at knytte din Office 365-lejer til abonnementet Azure, og derefter knytter dit Azure abonnement til Office 365-lejer.

## <a name="detailed-steps"></a>Detaljeret vejledning
I dette scenarie skal Kelley Wall er en bruger, der har et Azure-abonnement under kontoen kelley.wall@outlook.com. Kelley også har en Office 365-abonnement under kontoen kelley.wall@contoso.onmicrosoft.com. Nu ønsker Kelley at få adgang til Office 365-lejer til Azure abonnementet.

![Skærmbillede af Azure Active Directory-status](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![Skærmbillede af Office 365 administrationscenter aktive brugere](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

### <a name="prerequisites"></a>Forudsætninger
De følgende forudsætninger er nødvendige for tilknytningen kan fungere korrekt:

- Du skal have legitimationsoplysninger af tjenesteadministratoren af Azure-abonnement. Flere administratorer kan ikke udføre et undersæt af trinnene.
- Du skal have legitimationsoplysninger for en global administrator af Office 365-lejer.
- E-mail-adressen på tjenesteadministratoren skal ikke indeholdt i Office 365-lejer.
- E-mail-adressen på tjenesteadministratoren skal ikke svare til en hvilken som helst global administrator for Office 365-lejer.
- Hvis du aktuelt bruger en e-mailadresse, der er både en Microsoft-konto og en organisationskonto, midlertidigt ændre tjenesteadministratoren af abonnementet Azure for at bruge en anden Microsoft-konto. Du kan oprette en ny Microsoft-konto på [siden for tilmelding af Microsoft-konto](https://signup.live.com/).


Hvis du vil ændre tjenesteadministratoren, skal du følge disse trin:

1. Log på [Kontoadministration portal](https://account.windowsazure.com/subscriptions).
2. Vælg det abonnement, du vil ændre.
3. Klik på **Rediger abonnementsoplysninger**.

    ![Skærmbillede af Azure abonnementsoplysninger med "Rediger abonnementsoplysninger" fremhævet](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)

4. Skriv mailadressen på den nye tjenesteadministratoren i **TJENESTEADMINISTRATOREN** .

    ![Skærmbillede af dialogboksen "Rediger dit abonnement"](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

### <a name="associate-the-office-365-tenant-with-the-azure-subscription"></a>Knytte Office 365-lejer til Azure-abonnement
Hvis du vil knytte Office 365-lejer til Azure-abonnement, skal du følge disse trin:

1.  Log på [Kontoadministration portal](https://account.windowsazure.com/subscriptions) med administratorrettigheder tjeneste.
2.  Vælg **ACTIVE DIRECTORY**i venstre rude.

    ![Skærmbillede af Active Directory-post](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

    > [AZURE.NOTE] Du bør ikke se Office 365-lejer. Hvis du kan se den, kan du springe næste trin.

    ![Skærmbillede af standardmappen af Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)

3. Tilføj Office 365-lejer til dit Azure-abonnement.

    en. Vælg **Ny** > **DIRECTORY** > **brugerdefinerede Opret**.

    ![Skærmbillede af Azure Active Directory brugerdefineret oprette](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)

    b. Vælg **Brug eksisterende mappe**på siden **Tilføj directory** under **DIRECTORY**. Vælg **jeg er klar til at være logget af nu**, og vælg derefter **udført** ![fuldføre ikonet](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

    ![Skærmbillede af "Brug eksisterende mappe"](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)

    c. Når du har logget af, kan du logge på med den globale administrator legitimationsoplysninger for din Office 365-lejer.

    ![Skærmbillede af Office 365 global administrator for logon](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)

    d. Vælg **Fortsæt**.

    ![Skærmbillede af kontrol](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)

    e. Vælg **Log af nu**.

    ![Skærmbillede af logge](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)

    f. Log på [Kontoadministration portal](https://account.windowsazure.com/subscriptions) med administratorrettigheder tjeneste.

    ![Skærmbillede af Azure-logon](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

    g. Du bør se din Office 365-lejer i dashboardet.

    ![Skærmbillede af dashboard](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

4. Ændre den mappe, der er knyttet til Azure abonnementet.

    en. Vælg **Indstillinger**.

    ![Skærmbillede af Azure klassisk indstillinger for portalen ikon](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)

    b. Vælg abonnementet Azure, og vælg derefter **Rediger DIRECTORY**.
    ![Skærmbillede af Azure abonnement Rediger directory](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)

    c. Klik på **Næste** ![næste-ikon](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).

    ![Skærmbillede af "Ændre den tilknyttede mappe"](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)

    > [AZURE.WARNING] Du får en advarsel, fjernes alle medadministratorer.

    ![Azure-Bekræft-directory-tilknytning](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)

    >[AZURE.WARNING] Derudover kan alle [Rollebaseret adgangskontrol (RBAC)](./active-directory/role-based-access-control-configure.md) brugere med tildelt adgang i ressourcegrupperne eksisterende også fjernes. Den advarsel, modtager du omtaler dog kun fjernelse af medadministratorer.

    ![tildelt-brugere-fjernet-ressource-grupper](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)

    d. Vælg **udført** ![fuldføre ikonet](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

5. Nu kan du tilføje din Office 365 organisationskonti som medadministratorer til Azure Active Directory-lejer.

    en. Vælg fanen **ADMINISTRATORER** , og vælg derefter **Tilføj**.

    ![Skærmbillede af Azure under fanen administratorer af klassisk portalen indstillinger](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)

    b. Angiv en organisationskonto i din Office 365-lejer, Vælg det Azure abonnement, og vælg derefter **udført** ![fuldføre ikonet](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

    ![Skærmbillede af Azure tilføje dialogboksen samtidig administrator](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)

    c. Gå tilbage til fanen **ADMINISTRATORER** . Du bør se organisationskonto vises som samtidig administrator.

    ![Skærmbillede af fanen administratorer](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)

6. Du kan derefter teste adgang med samtidig-administratoren.

    en. Log af portalen kontoadministration.

    b. Åbn [Kontoadministration portal](https://account.windowsazure.com/subscriptions) eller [Azure-portalen](https://portal.azure.com/).

    c. Hvis Azure-logon-siden har et link på **Log på med din organisation**, skal du vælge linket. Ellers skal du springe dette trin.

    ![Skærmbillede af Azure-logonside](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)

    d. Angive legitimationsoplysninger for samtidig administratoren, og vælg derefter **Log på**.

    ![Skærmbillede af Azure-logonside](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## <a name="next-steps"></a>Næste trin
Relaterede scenarier omfatter:

- Du allerede har et Office 365-abonnement og er klar til et Azure-abonnement, men du vil bruge de eksisterende Office 365-brugerkonti for abonnementet Azure.
- Du er en Azure abonnement og vil have en Office 365-abonnement for brugerne i din eksisterende Azure Active Directory-forekomst.

Hvis du vil lære at udføre disse opgaver, skal du se [Brug af dit eksisterende Office 365-konto med abonnementet Azure eller omvendt](billing-use-existing-office-365-account-azure-subscription.md).
