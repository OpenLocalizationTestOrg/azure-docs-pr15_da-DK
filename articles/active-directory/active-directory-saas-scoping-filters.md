<properties
    pageTitle="Attribut-baseret app klargøring med angivelse af område filtre | Microsoft Azure"
    description="Lær at bruge angiver området filtre til at forhindre objekter i apps, der understøtter automatiseret brugerklargøring fra faktisk ved at blive klargjort, hvis et objekt ikke opfylder virksomhedens behov."
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


# <a name="attribute-based-app-provisioning-with-scoping-filters"></a>Attribut-baseret app klargøring med angivelse af område filtre

Formålet med dette afsnit er at forklare, hvordan du bruger angiver området filtre til at definere attribut-baserede regler, der bestemmer, hvilke brugere der klargøres til programmet.





## <a name="clauses-and-scope-groups"></a>Delsætninger og omfang grupper


![Angivelse af område Filter][1] 




Angiver området filtre er defineret af en eller flere **omfang grupper**, hver især hold en eller flere **delsætninger**. For at se delsætninger for et særlig omfang gruppe skal du udvide det ved at klikke på pilen til venstre for navnet på den gruppe.

En **delsætning** bestemmer, hvilke brugere har tilladelse til at gå igennem filteret angiver området ved evaluering af hver enkelt brugers attributter. Du muligvis eksempelvis én-delsætning, der kræver, at en bruger 'tilstand' attribut er lig med New York, hvilket betyder, at brugerne New York der klargøres i programmet.

![Angivelse af område gruppenavn][2] 



Hver **gruppen omfang** starter med et obligatorisk- **delsætningen**som vist i skærmbilledet ovenfor. Denne delsætning blot siger, at brugeren skal først være tildelt til programmet inden det evalueres ved dine angiver området filtre. Denne delsætning kan ikke slettes eller redigeres.

Du kan tilføje nye delsætninger eller nye omfang grupper ved at trykke på den relevante knap. Du kan give hver gruppen omfang et navn ved at redigere egenskaben **Omfang gruppenavn** .





## <a name="how-scoping-filters-are-evaluated"></a>Hvordan angivelse af område filtre evalueres

Under klargøring af kan teste vi alle tildelte brugere mod dine angiver området filtre for at afgøre, hvis brugeren fortjener adgangen til programmet. Du kan tænke på hver enkelt-delsætning som værende en test, der skal sendes i rækkefølge for brugeren at undgå at få bortfiltreres. 

Hvis du har flere omfang grupper, der er defineret, kan hver bruger skal gå mindst én af dem for at få adgang til programmet. I grupperne omfang men skal brugeren godkendes af hver enkelt delsætning for at overføre bestemte omfang gruppen. 

Det vil sige, kan du betragte omfang grupper som værende eller ville sammen og kan du betragte delsætninger i dem som værende og vil sammen. For eksempel kan du overveje filteret angiver området nedenfor:


![Angivelse af område gruppenavn][2]  


Brugere skal opfylde følgende kriterier for at blive klargjort efter dette angiver området filter:

1. De skal tildeles til programmet.

2. De skal arbejde i afdelingen teknisk

3. De skal være arbejde i San Francisco eller Canada.


##<a name="related-articles"></a>Relaterede artikler

- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
- [Automatisere bruger klargøring og ophævelse af klargøring til SaaS programmer](active-directory-saas-app-provisioning.md)
- [Tilpasse attributtilknytninger til klargøring af bruger](active-directory-saas-customizing-attribute-mappings.md)
- [Skrive udtryk for attributten tilknytninger](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Klargøring af beskeder-konto](active-directory-saas-account-provisioning-notifications.md)
- [Brug af SCIM til at aktivere automatisk klargøring af brugere og grupper fra Azure Active Directory til programmer](active-directory-scim-provisioning.md)
- [Liste over selvstudier om, hvordan du integrerer SaaS Apps](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-scoping-filters/ic782811.png
[2]: ./media/active-directory-saas-scoping-filters/ic782812.png
[3]: ./active-directory-saas-scoping-filters/ic782813.png
