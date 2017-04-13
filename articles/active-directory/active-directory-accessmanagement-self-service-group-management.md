<properties
    pageTitle="Konfiguration af Azure Active Directory i access administration af selv tjenesteprogram | Microsoft Azure"
    description="Selvbetjening gruppeadministration brugere mulighed for at oprette og administrere sikkerhedsgrupper eller Office 365-grupper i Azure Active Directory og giver brugere mulighed for at anmodningen sikkerhedsgruppe eller Office 365 gruppemedlemskaber"
    services="active-directory"
    documentationCenter=""
  authors="curtand"
    manager="femila"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/10/2016"
    ms.author="curtand"/>

# <a name="setting-up-azure-active-directory-for-self-service-group-management"></a>Konfiguration af Azure Active Directory i selvbetjening gruppeadministration

Selvbetjening gruppeadministration mulighed brugere for at oprette og administrere sikkerhedsgrupper eller Office 365-grupper i Azure Active Directory (Azure AD). Brugere kan også anmode om sikkerhedsgruppe eller Office 365 gruppemedlemskaber, og klik derefter ejeren af gruppen kan godkende eller afvise medlemskab. På denne måde kan daglige kontrollen over gruppemedlemskab uddelegeres til personer, der forstår business konteksten for, at medlemskab. Funktioner til administration af selvbetjening gruppe findes kun til sikkerhedsgrupper og Office 365-grupper, men ikke til mailaktiverede sikkerhedsgrupper eller distributionslister.

Selvbetjening gruppeadministration omfatter to væsentlige scenarier i dag: tilbud om delegeret gruppeadministration af og selvbetjening gruppeadministration.

- **Gruppen uddelegeret** 
   et eksempel er administrator, der administrerer adgang til et SaaS program, der bruger firmaet. Administrere disse rettigheder bliver besværlige, så denne administrator beder om ejeren business til at oprette en ny gruppe. Administratoren tildeler adgang til programmet til den nye gruppe, og tilføjer til gruppen Alle personer, der allerede har adgang til til programmet. Ejeren af business kan derefter tilføje flere brugere, og disse brugere er automatisk klargjort til programmet. Ejeren af business behøver ikke at vente administratoren for at administrere adgangen for brugere. Hvis administratoren giver samme tilladelse til en chef i en anden business-gruppe, kan vedkommende også administrere adgangen for deres egne brugere. Hverken business ejeren eller leder kan få vist eller administrere hinandens brugere. Administratoren kan stadig se alle brugere, der har adgang til det program og Bloker adgangsrettigheder, hvis det er nødvendigt.

- **Selvbetjening gruppeadministration** 
   et eksempel på dette scenario er to brugere med både SharePoint Online-websteder, de opretter uafhængigt af hinanden. De vil give hinandens teams adgang til deres websteder. Hvis du vil gøre dette, de kan oprette en gruppe i Azure AD, og hver af dem markerer gruppen til at give adgang til deres websteder i SharePoint Online. Når nogen vil access, de anmoder om det fra panelet adgang, og efter godkendelse de få adgang til både SharePoint Online-websteder automatisk. Senere, beslutter de, at alle personer adgang til webstedet skal også få adgang til et bestemt SaaS program. Administratoren af programmet SaaS kan tilføje adgangsrettigheder til programmet til SharePoint Online-webstedet. Fra derefter af anmodninger, der får godkendt giver adgang til de to SharePoint Online-websteder og også til dette SaaS program.

## <a name="making-a-group-available-for-end-user-self-service"></a>Gøre en gruppe tilgængelige for slutbruger selvbetjening

1. Åbn din Azure AD-mappe på [Azure klassisk portal](https://manage.windowsazure.com).

2. Indstil **delegerede gruppeadministration** til aktiveret fanen **Konfigurer** .

3. Angive **brugere kan oprette sikkerhedsgrupper** eller **brugere kan oprette Office-grupper** til aktiveret.

Når **brugere kan oprette sikkerhedsgrupper** er aktiveret, vil alle brugere i mappen er tilladt at oprette nye sikkerhedsgrupper og føje medlemmer til disse grupper. Disse nye grupper vil også vises i panelet adgang for alle andre brugere. Hvis politikindstillingen i gruppen tillader det, kan andre brugere kan oprette anmodninger om at deltage i disse grupper. Hvis **brugere kan oprette sikkerhedsgrupper** er deaktiveret, brugere kan ikke oprette grupper og kan ikke ændre eksisterende grupper, der er en ejer. De kan stadig administrere medlemskaberne af disse grupper og godkende anmodninger fra andre brugere til at deltage i deres grupper.

Du kan også bruge **brugere, hvem der kan bruge selvbetjening for sikkerhedsgrupper** for at opnå en mere detaljerede adgangskontrol over selvbetjening gruppeadministration for dine brugere. Når **brugere kan oprette grupper** er aktiveret, er alle brugere i mappen tilladelse til at oprette nye grupper, og Føj medlemmer til disse grupper. Du er ved at angive også **brugere, hvem der kan bruge selvbetjening for sikkerhedsgrupper** til nogle, begrænser gruppere management til en begrænset gruppe af brugere. Når denne parameter er angivet til nogle, skal du føje brugere til gruppen SSGMSecurityGroupsUsers, før de kan oprette nye grupper og føje medlemmer til dem. Ved at angive **brugere, hvem der kan bruge selvbetjening for sikkerhedsgrupper** til alle, kan du aktivere alle brugere i mappen til at oprette nye grupper.

Du kan også bruge feltet **gruppe, der kan bruge selvbetjening for sikkerhedsgrupper** til at angive et brugerdefineret navn til en gruppe, hvis medlemmer kan bruge selvbetjening.

## <a name="additional-information"></a>Yderligere oplysninger

Disse artikler giver yderligere oplysninger om Azure Active Directory.

* [Adgangskontrol til ressourcer med Azure Active Directory-grupper](active-directory-manage-groups.md)

* [Azure Active Directory-cmdletter til konfiguration af gruppeindstillinger](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)

* [Hvad er Azure Active Directory?](active-directory-whatis.md)

* [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
