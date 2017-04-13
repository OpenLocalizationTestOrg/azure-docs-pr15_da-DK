<properties
    pageTitle="Azure Active Directory-domænetjenester: Vejledning til Administration | Microsoft Azure"
    description="Oprette en organisatoriske enhed (OU) på Azure AD-domænetjenester administrerede domæner"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Oprette en organisatoriske enhed (OU) på en Azure AD-domænetjenester administrerede domæne
Azure AD-domænetjenester administrerede domæner omfatter to indbyggede beholdere, der kaldes 'AADDC computere' og 'AADDC brugere' henholdsvis. AADDC objektbeholderen har computerobjekter for alle computere, der er knyttet til administrerede domænet. Objektbeholderen 'AADDC brugere' indeholder brugere og grupper i Azure AD-lejer. Nogle gange kan være det nødvendigt at oprette tjenestekonti på administrerede domænet til at udrulle arbejdsmængder. Du kan oprette en brugerdefineret organisatoriske enhed (OU) på administrerede domænet og oprette tjenestekonti i pågældende OU til dette formål. I denne artikel beskrives, hvordan du opretter en OU i domænet administrerede.


## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Installere AD administration på et medlem af et domæne virtuel maskine til fjernadministration
Azure AD-domænetjenester administrerede domæner kan administreres ved hjælp af velkendte Active Directory-administration som Active Directory Administrative Center (ADAC) eller AD PowerShell. Lejer administratorer har ikke rettigheder til at oprette forbindelse til domænecontrollere på administrerede domænet via Fjernskrivebord. For at administrere administrerede domænet, skal du installere funktionen AD administration værktøjer på en virtuel maskine, der er knyttet til administrerede domænet. Se i artiklen [administrere et domæne, der Azure AD-domænetjenester administrerede](active-directory-ds-admin-guide-administer-domain.md) vejledning.

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Oprette en organisationsenhed på administrerede domænet
Nu, hvor AD administrationsværktøjer er installeret på domænenavn joinforbundne virtuelt, kan vi bruge disse værktøjer til at oprette en organisationsenhed på administrerede domænet. Udfør følgende trin:

> [AZURE.NOTE] Kun medlemmer af gruppen 'AAD DC administratorer' har de nødvendige tilladelser til at oprette en brugerdefineret OU. Sørg for, at du udføre følgende trin som en bruger, der hører til denne gruppe.

1. Klik på **Administration**fra startskærmen. Du bør se AD administrationsværktøjer installeret på den virtuelle maskine.

    ![Administrative funktioner, der er installeret på serveren](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. Klik på **Active Directory Administrative Center**.

    ![Active Directory Administrative Center](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. For at få vist domænenavn, skal du klikke på navnet på det domæne i venstre rude (for eksempel ' contoso100.com').

    ![ADAC - visning-domæne](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

4. Klik på **Ny** under noden domæne navn i ruden til højre **opgaver** . I dette eksempel skal vi Klik på **Ny** under noden 'contoso100(local)' i ruden til højre **opgaver** .

    ![ADAC - ny OU](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

5. Du bør se muligheden for at oprette en organisationsenhed. Klik på **Organisationsenhed** for at åbne dialogboksen **Opret organisationsenhed** .

6. Angiv et **navn** til den nye OU i dialogboksen **Opret organisationsenhed** . Give en kort beskrivelse til OU. Du kan også angive feltet **Administreres af** til OU. Hvis du vil oprette den brugerdefinerede OU, skal du klikke på **OK**.

    ![ADAC - OU dialogboksen Opret](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

7. Den nyoprettede OU vises nu i AD Administrative Center (ADAC).

    ![ADAC - OU oprettet](./media/active-directory-domain-services-admin-guide/create-ou-done.png)


## <a name="permissionssecurity-for-newly-created-ous"></a>Tilladelser/sikkerhed i forbindelse med nyoprettede afdelinger
Som standard tildelt brugeren (medlem af gruppen "AAD DC administratorer"), som oprettede den brugerdefinerede OU administratorrettigheder (fuld kontrol) over OU. Brugeren kan derefter gå videre og give tilladelser til andre brugere eller til gruppen 'AAD DC administratorer' efter behov. Som det ses i det følgende skærmbillede, brugeren 'bob@domainservicespreview.onmicrosoft.com' hvem der har oprettet den nye 'MyCustomOU' organisationsenhed tildeles fuld kontrol over den.

 ![ADAC - ny OU sikkerhed](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)


## <a name="notes-on-administering-custom-ous"></a>Noter om administration af brugerdefinerede afdelinger
Nu hvor du har oprettet en brugerdefineret OU, kan du gå videre og oprette brugere, grupper, computere og tjenestekonti i denne afdeling. Du kan ikke flytte brugere eller grupper fra de 'AAD DC brugere' OU til brugerdefineret afdelinger.

> [AZURE.WARNING] Brugerkonti, grupper, tjenestekonti og computerobjekter, som du opretter under brugerdefineret afdelinger er ikke tilgængelige i din Azure AD-lejer. Med andre ord viser disse objekter ikke ikke op ved hjælp af Azure AD Graph API eller i Azure AD-brugergrænseflade. Disse objekter er kun tilgængelige i domænet Azure AD-domænetjenester administrerede.


## <a name="related-content"></a>Relateret indhold

- [Administrere en administreret Azure AD Domain Services-domæne](active-directory-ds-admin-guide-administer-domain.md)

- [Active Directory Administrative centreret: Introduktion](https://technet.microsoft.com/library/dd560651.aspx)

- [-Tjenestekonti, trinvis vejledning](https://technet.microsoft.com/library/dd548356.aspx)
