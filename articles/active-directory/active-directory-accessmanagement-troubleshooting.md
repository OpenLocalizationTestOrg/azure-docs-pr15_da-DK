
<properties
    pageTitle="Fejlfinding i forbindelse med dynamisk medlemskab for grupper | Microsoft Azure"
    description="Tip til fejlfinding for dynamisk medlemskab for grupper i Azure AD."
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


# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Fejlfinding i forbindelse med dynamisk medlemskaber for grupper

**Jeg har konfigureret en regel for en gruppe, men ingen medlemskaber opdateret i gruppen**<br/>Kontrollér, at indstillingen **Aktivér gruppe uddelegeret** er angivet til **Ja** under fanen **Konfigurer** . Denne indstilling vises kun, hvis du er logget på som en bruger, som er tildelt en licens til Azure Active Directory Premium. Kontrollér værdierne for brugerattributter for reglen: er der brugere, der opfylder reglen?

**Jeg har konfigureret en regel, men nu medlemmer på reglen er fjernet**<br/>Dette er forventede funktionsmåde. Medlemmer af gruppen fjernes, når en regel er aktiveret eller ændret. De brugere, der returneres af evalueringen af reglen tilføjes som medlemmer til gruppen.     

**Jeg kan ikke se medlemskab ændres direkte, når jeg tilføjer eller ændrer en regel, hvorfor ikke?**<br/>Dedikeret medlemskab evaluering er færdig med jævne mellemrum i en asynkron i baggrunden. Hvor lang tid tager processen bestemmes af antallet af brugere i mappen og størrelsen på den gruppe, der er oprettet som et resultat af reglen. Typisk får mapper med et lille antal brugere vist ændringerne af gruppemedlemskabet i mere end et par minutter. Mapper med et stort antal brugere kan tage 30 minutter eller længere tid at udfylde.

Disse artikler giver yderligere oplysninger om Azure Active Directory.

* [Adgangskontrol til ressourcer med Azure Active Directory-grupper](active-directory-manage-groups.md)
* [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
* [Hvad er Azure Active Directory?](active-directory-whatis.md)
* [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
