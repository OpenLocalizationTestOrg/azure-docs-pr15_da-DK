<properties
    pageTitle="Tilpasse attributtilknytninger | Microsoft Azure"
    description="Få mere at vide, hvilke attributter tilknytninger for SaaS apps i Azure Active Directory er, hvordan du kan ændre dem for at løse dine forretningsbehov."
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


# <a name="customizing-attribute-mappings"></a>Tilpasse attributtilknytninger


Microsoft Azure AD yder support til brugerklargøring af til SaaS tredjepartsprogrammer som Salesforce, Google Apps og andre. Hvis du har brugerklargøring af til en tredjepart SaaS program, der er aktiveret, styrer portalen Azure Management attributværdierne i form af en konfiguration, kaldet "attributtilknytning".

Der er et forudkonfigurerede sæt af attributtilknytninger mellem Azure AD bruger og hver SaaS app brugerobjekter. Visse apps administrere andre typer objekter, som grupper eller kontakter. <br> 
Du kan tilpasse standard attributtilknytningerne efter virksomhedens behov. Det betyder, kan du ændre eller slette eksisterende attributtilknytninger eller oprette nye attributtilknytninger.

Azure AD-portalen, kan du få adgang til denne funktion ved at klikke på attributter i værktøjslinjen i et SaaS program.

> [AZURE.NOTE] Linket **attributter** er kun tilgængelig, hvis du har brugerklargøring aktiveret for en SaaS-program. 


![Salesforce][1] 


Når du klikker på attributter på værktøjslinjen, på listen over aktuelle tilknytninger, der er konfigureret til et SaaS program.

Følgende skærmbillede viser et eksempel af dette:



![Salesforce][2]  


I det foregående eksempel kan du se, at attributten **Fornavn** for et administreret objekt i Salesforce udfyldes med **givenName** værdien af den sammenkædede Azure AD-objekt.

Hvis du enten vil tilpasse tilknytningerne attribut eller hvis du vil nulstille tilpassede indstillinger for tilbage til standardkonfiguration, kan du gøre dette ved at klikke på knappen Relaterede på værktøjslinjen i bunden af et program.


![Salesforce][3]  


Der findes attributtilknytninger, der kræves af en SaaS program tilladelse til at fungere korrekt. I tabellen attributter skal have relateret attributtilknytningerne **Ja** som værdi for attributten **påkrævet** . Hvis en attributtilknytning er påkrævet, kan du slette den. I dette tilfælde er **slette** funktion ikke tilgængelig.

For at ændre en eksisterende attributtilknytning skal blot vælge tilknytningen, og klik derefter på **Rediger**. Dette viser en dialogboksside, der gør det muligt at ændre den valgte attribut-tilknytning.


![Redigere attributten tilknytning][4]  



## <a name="understanding-attribute-mapping-types"></a>Forstå attribut tilknytning typer


Med attributten tilknytninger deltager du kontrolelementet hvordan attributter i en tredje udfyldes SaaS program. Der er fire forskellige typer tilknytning understøttes:

- **Direkte** – målattributten er udfyldt med værdien af en attribut på det sammenkædede objekt i Azure AD.


- **Konstant** – målattributten er udfyldt med en bestemt tekststreng, du har angivet.


- **Udtryk** - målattributten er udfyldt baseret på resultatet af et script-lignende udtryk. Du kan finde flere detaljer, [Du skriver udtryk for attributten tilknytninger i Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).


- **Ingen** - målattributten er venstre uændrede. Men hvis målattributten er aldrig tom, det udfyldes med standardværdien, du angiver.



Ud over disse fire grundlæggende attribut tilknytning typer understøtter brugerdefineret attributtilknytninger begrebet tildeling af en **Standardværdi** . Værditildeling standard sikrer, at attributten target udfyldes med en værdi, hvis der ikke er hverken en værdi i Azure AD eller på målobjektet.

Microsoft Azure AD indeholder en meget effektiv gennemførelse af processen en synkronisering. I et initialiseret miljø behandles kun de objekter, der kræver opdateringer under en synkronisering cyklus. Opdatere attributten tilknytninger har indflydelse på ydeevnen for en synkronisering cyklus. Dette skyldes, at en opdatering af attribut tilknytning konfigurationen kræver, at alle administrerede objekter for at være reevaluated. På grund af dette er det anbefalede bedste fremgangsmåde at holde antallet på hinanden følgende ændringer til tilknytningerne attribut som minimum.


##<a name="related-articles"></a>Relaterede artikler

- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
- [Automatisere bruger klargøring/ophævelse af klargøring til SaaS Apps](active-directory-saas-app-provisioning.md)
- [Skrive udtryk for attributten tilknytninger](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Angivelse af område filtre til klargøring af bruger](active-directory-saas-scoping-filters.md)
- [Brug af SCIM til at aktivere automatisk klargøring af brugere og grupper fra Azure Active Directory til programmer](active-directory-scim-provisioning.md)
- [Klargøring af beskeder-konto](active-directory-saas-account-provisioning-notifications.md)
- [Liste over selvstudier om, hvordan du integrerer SaaS Apps](active-directory-saas-tutorial-list.md)


<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png
