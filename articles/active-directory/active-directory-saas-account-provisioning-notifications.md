<properties
    pageTitle="Konto klargøring beskeder | Microsoft Azure"
    description="Lær at sikre, at du får besked om problemer, der er relateret til brugerklargøring af, der kræver din opmærksomhed ved at aktivere kontoen klargøring af meddelelser."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="account-provisioning-notifications"></a>Klargøring af beskeder-konto

Med brugerklargøring af, kan du automatisere processen med at administrere brugere i SaaS tredjepartsprogrammer. <br>
Dette er en automatiseret proces, men din interaktion med denne proces er fra tid til anden påkrævet. <br>
Dette gælder, for eksempel, når adgangskoden til den konto, du har konfigureret til at udveksle data med en tredje part SaaS program er udløbet. 

Ved at aktivere kontoen klargøring af meddelelser, kan du sikre dig, at du får besked om problemer, der er relateret til brugerklargøring af, der kræver din opmærksomhed.

Du kan aktivere eller deaktivere konto klargøring meddelelser som en del af din bruger klargøring konfiguration for en tredjepart SaaS programmet.

![Brugerklargøring af][1] 



Markér afkrydsningsfeltet relaterede på siden **Confirmation** dialogboksen for at aktivere kontoen klargøring beskeder, og skriv derefter mailalias for modtageren.

![Klargøring af beskeder-konto][2]
 


Du kan angive en distributionsliste som modtager. men det er vigtigt at være opmærksom på, at mailen indeholder links til rapporter, der er kun tilgængelig for Azure AD-administratorer.

Hvis du har konto klargøring af meddelelser, der er aktiveret, modtager du mails om alvorlige problemer, der er relateret til brugerklargøring af. For at undgå overbelastning mail, modtager du dog kun en mail om dagen for hvert SaaS program besked via e-mail er aktiveret for.


##<a name="related-articles"></a>Relaterede artikler

- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
- [Automatisere bruger klargøring/ophævelse af klargøring til SaaS Apps](active-directory-saas-app-provisioning.md)
- [Tilpasse attributtilknytninger til klargøring af bruger](active-directory-saas-customizing-attribute-mappings.md)
- [Skrive udtryk for attributten tilknytninger](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Angivelse af område filtre til klargøring af bruger](active-directory-saas-scoping-filters.md)
- [Brug af SCIM til at aktivere automatisk klargøring af brugere og grupper fra Azure Active Directory til programmer](active-directory-scim-provisioning.md)
- [Liste over selvstudier om, hvordan du integrerer SaaS Apps](active-directory-saas-tutorial-list.md)



<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png