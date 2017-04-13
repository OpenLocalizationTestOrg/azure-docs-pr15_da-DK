<properties
    pageTitle="Kan ikke logge på Azure abonnement | Microsoft Azure"
    description="Beskriver, hvordan du udfører fejlfinding af nogle almindelige problemer med logon Azure-abonnement."
    services=""
    documentationCenter=""
    authors="genlin"
    manager="mbaldwin"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="genli"/>

# <a name="i-cant-sign-in-to-manage-my-azure-subscription"></a>Jeg kan ikke logge på til at administrere mine Azure abonnement

I denne artikel fører dig gennem nogle af de mest almindelige metoder til at løse problemer med logon.

## <a name="page-hangs-in-the-loading-status"></a>Siden hænger i indlæsningsstatus

Hvis siden internet browser hænger, kan du prøve hver af de følgende trin, indtil du kan få adgang til [Azure-portalen](https://portal.azure.com).

-   Opdatere siden.
-   Brug en anden internetbrowser.
-   Hvis du bruger Microsoft Internet Explorer, gå til portalen Azure ved hjælp af tilstanden InPrivate-Browsing. 

    A.  Klik på **værktøjer** ![knappen Funktioner](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Safety** > **InPrivate-Browsing**.

    B.  Gå til [Azure-portalen](https://portal.azure.com), og derefter logge på portalen.

## <a name="error-message-no-subscriptions-found"></a>Fejlmeddelelsen "Ikke fundet nogen abonnementer"

Hvis din konto ikke har tilstrækkelige tilladelser, kan du se meddelelsen **Intet abonnement fundet** fejl. Kun en kontoadministrator kan få adgang til [Kontoen Center](https://account.windowsazure.com/), ikke tjenesteadministratorer (Systemadministratorens) eller medadministratorer (CA).

**Scenarie 1: Fejlmeddelelse modtages i [Azure-portalen](https://portal.azure.com)**

Til at løse dette problem ved at [tilføje rollen som samtidig administrator eller ejer](billing-add-change-azure-subscription-administrator.md) for kontoen.

**Scenarie 2: Fejlmeddelelse modtages i [Azure konto Center](https://account.windowsazure.com/Subscriptions)**

Kontrollere, om den konto, du har brugt-kontoadministratoren. Følg disse trin for at bekræfte, hvem der-kontoadministratoren er:

1.  Log på [Azure-portalen](https://portal.azure.com).
2.  I menuen Hub skal du markere **abonnement**.
3.  Markere det abonnement, du vil kontrollere, og vælg derefter **Indstillinger**.
4.  Vælg **Egenskaber**. Kontoadministratoren af abonnementet vises i feltet **Konto administrator** .

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>Du logget automatisk på som en anden bruger

Dette problem kan opstå, hvis du bruger mere end én brugerkonto i en webbrowser.

Du kan løse problemet, kan du prøve et af følgende metoder:

-   Rydde cachen og slette Internetcookies. Klik på **Funktioner** i Internet Explorer, ![knappen Funktioner](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Internetindstillinger** > **slette**. Sørg for, at markeringen i afkrydsningsfelterne for midlertidige filer, cookies, adgangskode og browserdata er markeret, og klik derefter på Slet.

-   Nulstille indstillingerne for Internet Explorer for at gendanne en hvilken som helst personlige indstillinger, du har foretaget. Klik på **værktøjer** ![knappen Funktioner](./media/billing-cannot-login-subscription/Toolsbutton.png)> **Internetindstillinger** > **Avanceret** > Marker feltet **Slet personlige indstillinger** > **Nulstil**.

-   Gå til portalen Azure i InPrivate-Browsing tilstand. Klik på **værktøjer** ![knappen Funktioner](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Safety** > **InPrivate-Browsing**.

## <a name="need-help-contact-support"></a>Brug for hjælp? Kontakte support. 

Hvis du stadig har brug for hjælp, skal [du kontakte support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) for at få dit problem løses hurtigt. 