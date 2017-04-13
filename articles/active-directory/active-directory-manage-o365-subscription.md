<properties
   pageTitle="Administrere mappen for dit Office 365-abonnement i Azure | Microsoft Azure"
   description="Administration af et Office 365-abonnement directory, ved hjælp af Azure Active Directory og Azure klassisk portalen"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="manage-the-directory-for-your-office-365-subscription-in-azure"></a>Administrere mappen for dit Office 365-abonnement i Azure

I denne artikel beskrives, hvordan du administrerer en mappe, der er oprettet for et Office 365-abonnement ved hjælp af portalen Azure klassisk. Du skal være enten tjenesteadministratoren eller en samtidig administrator af en Azure-abonnement til at logge på portalen Azure klassisk. Hvis du endnu ikke har et Azure-abonnement, kan du tilmelde dig en [gratis 30-dages prøveversion](https://azure.microsoft.com/trial/get-started-active-directory/) i dag og installere din første skyen løsning under 5 minutter, ved hjælp af dette link. Sørg for at bruge kontoen arbejds- eller skolekonto, du bruger til at logge på Office 365.

Når du har fuldført Azure-abonnement, kan du logge på portalen Azure klassisk og få adgang til Azure tjenester. Klik på i Active Directory-udvidelse for at administrere den samme mappe, der godkender din Office 365-brugere.

Hvis du allerede har et Azure-abonnement, er du også nemt administreres en ekstra mappe. Michael Smith kan for eksempel har et Office 365-abonnement til Contoso.com. Hun har også et Azure abonnement, som hun har tilmeldt sig ved hjælp af sin Microsoft-konto, msmith@hotmail.com. I dette tilfælde administrerer han to mapper.

  Abonnement |  Office 365  |  Azure
  -------------- | ------------- | -------------------------------
  Vist navn |  Contoso  |     Azure Active Directory (Azure AD) standardmappen
  Domænenavn  |  Contoso.com  | msmithhotmail.onmicrosoft.com

Hun ønsker at administrere bruger-id'er i mappen Contoso, når hun er logget på Azure ved hjælp af sin Microsoft-konto, så han kan aktivere Azure AD-funktioner som multifaktor-godkendelse. I det følgende diagram til at illustrere.

![Diagram for at håndtere to uafhængige mapper](./media/active-directory-manage-o365-subscription/AAD_O365_03.png)

I dette tilfælde er de to mapper uafhængigt af hinanden.

## <a name="to-manage-two-independent-directories"></a>Til at administrere to uafhængige kataloger
For at Michael Smith til at administrere begge kataloger, mens han er logget på Azure som msmith@hotmail.com, han skal benytte følgende fremgangsmåde:

> [AZURE.NOTE]
> Disse trin kan udføres kun, når en bruger er logget på med en Microsoft-konto. Hvis brugeren er logget på med en arbejds- eller skolekonto, mulighed for at **bruge eksisterende mappe** ikke tilgængelig. Arbejds-eller skolekonto kan være bekræftet kun af en privat mappe (det vil sige, den mappe, hvor kontoen arbejds- eller skolekonto er gemt, og, der ejer virksomheden eller skolen).

1.  Log på [Azure klassisk portal](https://manage.windowsazure.com) som msmith@hotmail.com.
2.  Klik på **Ny** > **App services** > **Active Directory** > **Directory** > **Brugerdefineret oprette**.
3.  Klik på Brug eksisterende mappe, og marker afkrydsningsfeltet **jeg er klar til at være logget af nu** .
4.  Log på portalen Azure klassisk som global administrator af Contoso.onmicrosoft.com (for eksempel msmith@contoso.com).
5.  Når du bliver bedt om at **Brug mappen Contoso med Azure?**, klik på **Fortsæt**.
6.  Klik på **Log af nu**.
7.  Log på portalen Azure til klassisk som msmith@hotmail.com. Mappen Contoso og standardmappen vises i Active Directory-filtypenavn.

Når du har fuldført disse trin, msmith@hotmail.com er en global administrator i mappen Contoso.

## <a name="to-administer-resources-as-the-global-admin"></a>Til at administrere ressourcer som den globale administrator
Nu Lad os sige, at Jane Larsen skal administrere websteder og database ressourcer, der er knyttet til Azure abonnementet for msmith@hotmail.com. Før hun kan gøre det, skal Michael Smith udføre følgende fremgangsmåde:

1.  Log på [Azure klassisk portal](https://manage.windowsazure.com) ved hjælp af kontoen tjenesteadministratoren for Azure abonnementet (i dette eksempel msmith@hotmail.com).
2.  Overføre abonnementet til mappen Contoso: Klik på **Indstillinger for** > **abonnementer** > Vælg abonnementet > **Rediger Directory** > Vælg **Contoso (Contoso.com)**. Konti, der er flere administratorer af abonnementet, som en del af overførslen, eventuelt arbejde eller skole fjernes.
3.  Tilføje Jane Larsen som samtidig administratoren af abonnementet: Klik på **Indstillinger for** > **Administratorer** > Vælg abonnementet > **Tilføj** > type **JohnDoe@Contoso.com**.

## <a name="next-steps"></a>Næste trin
Du kan finde flere oplysninger om relationen mellem abonnementer og mapper, se, [hvordan et abonnement er knyttet til et bibliotek](active-directory-how-subscriptions-associated-directory.md).
