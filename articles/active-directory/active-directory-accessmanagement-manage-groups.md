<properties
    pageTitle="Administration af grupper i Azure Active Directory | Microsoft Azure"
    description="Hvordan du kan oprette og administrere grupper for at administrere Azure brugere ved hjælp af Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/29/2016"
    ms.author="curtand"/>


# <a name="managing-groups-in-azure-active-directory"></a>Administration af grupper i Azure Active Directory

> [AZURE.SELECTOR]
- [Azure-portalen](active-directory-groups-create-azure-portal.md)
- [Azure klassisk portal](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)


En af funktionerne i Azure Active Directory (Azure AD) brugeradministration er muligheden for at oprette grupper af brugere. Du kan bruge en gruppe til at udføre administrationsopgaver som tildeling af licenser eller tilladelser til et antal brugere på én gang. Du kan også bruge grupper til at tildele adgangsrettigheder til at

- Ressourcer som objekterne i bibliotek
- Ressourcer, som er ekstern i forhold til mappen, som SaaS programmer, Azure services, SharePoint-websteder eller lokale ressourcer

Ejeren af en ressource kan desuden også tildele adgang til en ressource til en Azure AD-gruppe, der er ejet af en anden. Denne tildeling giver medlemmer af denne gruppeadgang til ressourcen. Derefter håndterer ejeren af gruppen medlemskab i gruppen. Effektivt, delegerer ejeren af ressource til ejeren af gruppen tilladelse til at tildele brugere til deres ressource.

## <a name="how-do-i-create-a-group"></a>Hvordan opretter jeg en gruppe?

Afhængigt af de tjenester, som din organisation har oprettet et abonnement, kan du oprette en gruppe, ved hjælp af en af følgende:
- portalen Azure klassisk
- Office 365-konto-portalen
- portalen Windows Intune-konto

Vi vil beskrive opgaver som udført i portalen Azure klassisk. Du kan finde flere oplysninger om brug af ikke-Azure portaler til at administrere din Azure AD-mappe, kan du se [administrere din Azure AD-mappe](active-directory-administer.md).

1. Vælg **Active Directory**i [Azure klassisk portal](https://manage.windowsazure.com), og vælg derefter navnet på mappen for din organisation.

2. Vælg fanen **grupper** .

3. Vælg **Tilføj gruppe**.

4. I vinduet **Tilføj gruppe** skal du angive navn og beskrivelse af en gruppe.


## <a name="how-do-i-add-or-remove-individual-users-in-a-security-group"></a>Hvordan tilføje eller fjerne individuelle brugere i en sikkerhedsgruppe?

**Føje en individuel bruger til en gruppe**

1. Vælg **Active Directory**i [Azure klassisk portal](https://manage.windowsazure.com), og vælg derefter navnet på mappen for din organisation.

2. Vælg fanen **grupper** .

3. Åbn den gruppe, du vil tilføje medlemmer. Åbn fanen **medlemmer** af den valgte gruppe, hvis den ikke allerede vises.

4. Vælg **Tilføj medlemmer**.

5. På siden **Tilføj medlemmer** skal du vælge navnet på brugeren eller en gruppe, du vil tilføje som medlem af denne gruppe. Sørg for, at dette navn føjes til ruden **valgte** .


**Fjerne en individuel bruger fra en gruppe**

1. Vælg **Active Directory**i [Azure klassisk portal](https://manage.windowsazure.com), og vælg derefter navnet på mappen for din organisation.

2. Vælg fanen **grupper** .

3. Åbn den gruppe, du vil fjerne medlemmer.

4. Vælg fanen **medlemmer** , Vælg navnet på det medlem, du vil fjerne fra denne gruppe, og klik derefter på **Fjern**.

6. Bekræft kommandoprompten, du vil fjerne dette medlem fra gruppen.


## <a name="how-can-i-manage-the-membership-of-a-group-dynamically"></a>Hvordan kan jeg administrere medlemskabet af en gruppe dynamisk?

I Azure AD, kan du meget nemt konfigurere en simpel regel til at bestemme, hvilke brugere der skal være medlem af gruppen. En simpel regel er en, der gør det kun en enkelt sammenligning. Eksempelvis hvis en gruppe er tildelt til et SaaS til computeren, kan du konfigurere en regel til at tilføje brugere, der har en stilling af "Sælger". Denne regel derefter giver adgang til dette SaaS program for alle brugere med stilling i mappen.

Når en hvilken som helst attributterne for en bruger ændring systemet evaluerer alle regler for dynamisk gruppe i en mappe, skal du se Hvis attributten ændringen af brugeren vil udløse en gruppe tilføjer eller fjerner. Hvis en bruger, der opfylder en regel for en gruppe, føjes de som et medlem til den pågældende gruppe. Hvis de ikke længere opfylder reglen for en gruppe, de er medlem af, fjernes de som en medlemmer fra den pågældende gruppe.

> [AZURE.NOTE] Du kan konfigurere en regel for dynamisk medlemskab på sikkerhedsgrupper eller Office 365-grupper. Indlejrede gruppemedlemskaber understøttes ikke i øjeblikket for gruppe-baserede tildeling til programmer.
>
> Dynamisk medlemskaber for grupper kræver Azure AD Premium-licens tildeles til
>
> - Den administrator, der administrerer reglen for en gruppe
> - Alle medlemmer af gruppen

**Aktivere dynamisk medlemskab for en gruppe**

1. Vælg **Active Directory**i [Azure klassisk portal](https://manage.windowsazure.com), og vælg derefter navnet på mappen for din organisation.

2. Vælg fanen **grupper** , og Åbn den gruppe, du vil redigere.

3. Vælg fanen **Konfigurer** , og angiv derefter **Aktivere dynamisk medlemskaber** til **Ja**.

4. Konfigurere en simpel enkelt regel for gruppen til at styre, hvordan dynamiske medlemskab for denne gruppefunktioner. Sørg for, at den **tilføje brugere hvor** indstilling er markeret, og vælg derefter en egenskab på listen (for eksempel afdeling, stilling, osv.)

5. Vælg derefter en betingelse (ikke er lig med er lig med ikke starter med, starter med, ikke indeholder, indeholder, og Sammenlign Match).

6. Angiv en Sammenligningsværdi for den valgte brugeregenskab.

Se [Brug af attributter til at oprette avancerede regler](active-directory-accessmanagement-groups-with-advanced-rules.md)for at få mere for at vide om, hvordan du opretter *Avancerede* regler (regler, der kan indeholde flere sammenligninger) til dynamiske gruppemedlemskab.

## <a name="additional-information"></a>Yderligere oplysninger

Disse artikler giver yderligere oplysninger om Azure Active Directory.

* [Adgangskontrol til ressourcer med Azure Active Directory-grupper](active-directory-manage-groups.md)

* [Azure Active Directory-cmdletter til konfiguration af gruppeindstillinger](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)

* [Hvad er Azure Active Directory?](active-directory-whatis.md)

* [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
