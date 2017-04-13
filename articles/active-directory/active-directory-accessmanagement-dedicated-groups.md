<properties
    pageTitle="Dedikeret grupper i Azure Active Directory | Microsoft Azure"
    description="Oversigt over, hvordan dedikeret grupper arbejde i Azure Active Directory, og hvordan de er oprettet."
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
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="curtand"/>

# <a name="dedicated-groups-in-azure-active-directory"></a>Dedikeret grupper i Azure Active Directory

I Azure Active Directory (Azure AD), funktionen dedikeret grupper automatisk opretter og udfylder medlemskab for Azure AD foruddefinerede grupper. Medlemmer af dedikeret grupper kan ikke tilføjes eller fjernes i Azure klassisk portal, Windows PowerShell-cmdlet'er, eller fra et program.

>[AZURE.NOTE] Dedikeret grupper kræver, at en Azure AD Premium-licens er tildelt til
>- den administrator, der administrerer reglen for en gruppe
>- alle brugere, der er valgt som reglen skal være medlem af gruppen

**Aktivere dedikeret grupper**

1. Vælg **Active Directory**i [Azure klassisk portal](https://manage.windowsazure.com), og Åbn derefter organisationens adressekartotek.

2. Vælg fanen **grupper** , og Åbn derefter den gruppe, du vil redigere.

3. Vælg fanen **Konfigurer** , og angiv derefter **Aktivere dedikeret grupper** til **Ja**.

Når parameteren aktivere dedikeret grupper er angivet til **Ja**, kan du yderligere aktivere mappen til automatisk at oprette gruppen Alle brugere dedikeret ved at angive den **aktivere "Alle brugere" gruppe** skifte til **Ja**. Du kan derefter også redigere navnet på denne dedikeret gruppe ved at skrive det i den **visningsnavnet for "Alle brugere" gruppe** felt.

Gruppen Alle brugere kan bruges til at tildele de samme tilladelser til alle brugere i mappen. For eksempel kan du give alle brugere i directory adgang til et SaaS program ved at give adgang til gruppen Alle brugere dedikeret til dette program.

Gruppen dedikeret alle brugere omfatter alle brugere i kataloget, herunder gæster og eksterne brugere. Hvis du har brug for en gruppe, der udelader de eksterne brugere, og derefter kan du udføre dette ved at oprette en gruppe med en attribut-baseret dynamisk regel som følgende:

                (user.userPrincipalName -notContains "#EXT#@")

For en gruppe, der udelader alle gæster skal du bruge en regel som følgende:

                (user.userType -ne "Guest")

Se [Brug af attributter til at oprette avancerede regler](active-directory-accessmanagement-groups-with-advanced-rules.md)for at få mere for at vide om, hvordan du opretter *Avancerede* regler (regler, der kan indeholde flere sammenligninger) til dynamiske gruppemedlemskab.


Disse artikler giver yderligere oplysninger om Azure Active Directory.

* [Adgangskontrol til ressourcer med Azure Active Directory-grupper](active-directory-manage-groups.md)
* [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
* [Hvad er Azure Active Directory?](active-directory-whatis.md)
* [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
