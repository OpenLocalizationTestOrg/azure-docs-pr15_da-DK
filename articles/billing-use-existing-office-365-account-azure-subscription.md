<properties
    pageTitle="Dele en enkelt Azure AD-lejer på tværs af Office 365 og Azure abonnementer | Microsoft Azure"
    description="Lær at dele din Office 365 Azure AD-lejer og brugere med abonnementet Azure eller omvendt"
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
    ms.date="08/17/2016"
    ms.author="cjiang"/>

# <a name="use-an-existing-office-365-account-with-your-azure-subscription-or-vice-versa"></a>Brug en eksisterende Office 365-konto med abonnementet Azure eller omvendt
Scenarie: Du allerede har et Office 365-abonnement og er klar til et Azure-abonnement, men du vil bruge de eksisterende Office 365-brugerkonti for abonnementet Azure. Alternativt kan du er en Azure abonnement og vil have en Office 365-abonnement for brugerne i din eksisterende Azure Active Directory. I denne artikel viser, hvor nemt det er at opnå begge.

> [AZURE.NOTE] I denne artikel gælder ikke for Enterprise-aftale (EA) kunder. Hvis du har brug for mere hjælp på en hvilken som helst sted i denne artikel kan [du kontakte support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) til at få dit problem løses hurtigt.


## <a name="quick-guidance"></a>Hurtig vejledning

- Hvis du allerede har et Office 365-abonnement og vil til at tilmelde sig Azure, kan du bruge indstillingen **Log på med din virksomhedskonto** . Derefter fortsætte med Azure tilmeldingsprocessen med din Office 365-konto. Se [detaljeret vejledning senere i denne artikel](#s1).

- Hvis du allerede har et abonnement til Azure og vil have en Office 365-abonnement, du logge på Office 365 med din Azure-konto. Derefter fortsætte med tilmelding trinnene. Når du har fuldført tilmeldingen, føjes Office 365-abonnement til den samme Azure Active Directory-forekomst, som abonnementet Azure tilhører. Se afsnittet [detaljeret vejledning senere i denne artikel](#s2)kan finde flere oplysninger.

>[AZURE.NOTE] Hvis du vil have en Office 365-abonnement, kontoen, du bruger til tilmelding skal være medlem af rollen Global administrator eller fakturering administrator mappe i din Azure Active Directory-lejer. [Lær at finde ud af rollen i Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory).

For at forstå, hvad sker der, når du tilføjer et abonnement til en konto, skal du se [baggrundsoplysninger senere i denne artikel](#background-information).

## <a name="detailed-steps"></a>Detaljeret vejledning
<a id="s1"></a>
### <a name="scenario-1-office-365-users-who-plan-to-buy-azure"></a>Scenarie 1: Office 365-brugere, der har planer om at købe Azure
I dette scenarie antager vi Kelley Wall er en bruger, der har et Office 365-abonnement, og har planer om at abonnere på Azure. Der findes to ekstra aktive brugere, Jane og Tina. Kelleys konto er admin@contoso.onmicrosoft.com.

![Office 365 bruger administration](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

For at tilmelde dig Azure, skal du følge disse trin:

1. Tilmeld dig Azure på [Azure.com](https://azure.microsoft.com/). Klik på **Prøv gratis**. På den næste side skal du klikke på **Start nu**.

    ![Prøv Azure gratis.](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)

2. Klik på **Log på med din virksomhedskonto**.

    ![Log på Azure.](./media/billing-use-existing-office-365-account-azure-subscription/3-sign-in-to-azure.png)

3. Logge på med din Office 365-konto. I dette tilfælde er det Kelley's Office 365-konto.

    ![Logge på med din Office 365-konto.](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)

4. Udfyld oplysninger, og fuldføre tilmeldingen.

    ![Udfyld oplysninger og fuldføre tilmeldingen.](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)

    ![Klik på Start, administration af min tjeneste.](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

Nu er du klar. I portalen Azure, skal du se de samme brugere, der vises. For at bekræfte, at følge disse trin:

1. Klik på **Start, administration af min tjeneste** i skærmbilledet tidligere.
2. Klik på **Gennemse**, og klik derefter på **Active Directory**.

    ![Klik på Gennemse, og klik derefter på Active Directory.](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Klik på **brugere**.

    ![Fanen brugere](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)

4. Alle brugere, herunder Kelley, vises som forventet.

    ![Liste over brugere](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>
### <a name="scenario-2-azure-users-who-plan-to-buy-office-365"></a>Scenarie 2: Azure brugere, der har planer om at købe Office 365

I dette scenarie skal Kelley Wall er en bruger, der har et Azure-abonnement under kontoen admin@contoso.onmicrosoft.com. Kelley ønsker at abonnere på Office 365 og bruge den samme mappe, som hun har allerede med Azure.

>[AZURE.NOTE] For at få et Office 365-abonnement, skal den konto, du bruger til logon være medlem af rollen Global administrator eller fakturering administrator mappe i din Azure Active Directory-lejer. [Lær, hvordan du kender rollen i Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory).

![Azure portalen abonnementsindstillinger](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![Azure Active Directory-brugere i portalen](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

Hvis du vil abonnere på Office 365, skal du følge disse trin:

1. Gå til [Office 365-produkt-side](https://products.office.com/business), og vælg derefter en plan, som egner sig for dig.
2. Når du vælger planen, vises den efterfølgende side. Ikke fyld i formularen. Klik på **Log på** i det øverste højre hjørne på siden.

    ![Side med prøveversion af Office 365](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)

3. Logge på med legitimationsoplysningerne konto. I dette eksempel er det Kelleys konto.

    ![Office 365-logon](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)

4. Klik på **Prøv nu**.

    ![Bekræft din ordre til Office 365.](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)

5. Klik på **Continue**på siden ordre kvittering.

    ![Office 365 rækkefølge kvittering](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

Nu er du klar. I Office 365 administration skal du se brugere fra mappen Contoso vises som aktive brugere. For at bekræfte, at følge disse trin:

1. Åbn Office 365 administration.
2. Udvid **brugere**, og klik derefter på **Aktive brugere**.

    ![Brugere i Office 365 admin](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### <a name="how-to-know-your-role-in-your-azure-active-directory"></a>Hvordan ved jeg din rolle i din Azure Active Directory

1. Log på [Azure-portalen](https://portal.azure.com/).
2. Klik på **Gennemse**, og klik derefter på **Active Directory**.

    ![Active Directory i portalen Azure](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Klik på **brugere**.

    ![Azure portalen standard Active Directory-brugere](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)

4. Klik på brugeren. I dette eksempel er brugeren Kelley Wall.

    Bemærk feltet i **Rolle i virksomheden**.

    ![Azure portalen bruger-id](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## <a name="background-information-about-azure-and-office-365-subscriptions"></a>Baggrundsoplysninger om Azure og Office 365-abonnementer
Office 365 og Azure Brug Azure Active Directory-tjenesten til at administrere brugere og abonnementer. Overvej et Azure directory som en beholder, hvori du kan gruppere brugere og abonnementer. Hvis du vil bruge den samme brugerkonto for dine Azure og Office 365-abonnementer, skal du kontrollere, hvilke abonnementer der er oprettet i den samme mappe. Husk på følgende punkter:

- Et abonnement oprettes under en mappe, ikke anden vej rundt.
- Brugere tilhører kataloger, ikke anden vej rundt.
- Et abonnement lander i mappen på den bruger, der opretter abonnementet. Dit Office 365-abonnement er derfor knyttet til den samme konto som abonnementet Azure, når du bruger denne konto til at oprette Office 365-abonnement.

![Baggrundsoplysninger](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Se, [hvordan Azure abonnementer er knyttet til Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md), kan finde flere oplysninger.

>[AZURE.NOTE] Azure abonnementer ejes af individuelle brugere i kataloget.

>[AZURE.NOTE] Office 365-abonnementer ejes af mappen sig selv. Brugere i kataloget har de nødvendige tilladelser, kan de fungere på disse abonnementer.

## <a name="next-steps"></a>Næste trin
Hvis du har anskaffet både Azure og Office 365-abonnementer separat tidligere, og du vil ikke have adgang til Office 365-lejer fra Azure abonnement, kan du se [knytte en Office 365-lejer med et Azure-abonnement](billing-add-office-365-tenant-to-azure-subscription.md).

> [AZURE.NOTE] Hvis du stadig har spørgsmål, skal [du kontakte support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) til at få dit problem løses hurtigt.
