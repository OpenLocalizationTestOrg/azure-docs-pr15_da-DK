<properties
    pageTitle="Aktivér Enterprise tilstand Roaming i Azure Active Directory | Microsoft Azure"
    description="Ofte stillede spørgsmål om Enterprise tilstand globale indstillinger i Windows-enheder. Enterprise tilstand Roaming giver brugere med en samlet oplevelse på tværs af deres Windows-enheder og reducerer den tid, det er nødvendigt for at konfigurere en ny enhed."
    services="active-directory"
    keywords="Enterprise tilstand roaming, windows skyen, hvordan du aktiverer enterprise tilstand roaming"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>



# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Aktivér Enterprise tilstand Roaming i Azure Active Directory

Enterprise tilstand Roaming er tilgængelige for en hvilken som helst organisation med et abonnement på Premium Azure Active Directory (Azure AD). Du kan finde flere oplysninger om, hvordan du får et Azure AD-abonnement, [Azure AD produkt side](https://azure.microsoft.com/services/active-directory).

Når du aktiverer Enterprise tilstand Roaming, tildeles organisationen automatisk licenser til et gratis, begrænset brug abonnement til Azure Rights Management. Dette gratis abonnement er begrænset til at kryptere og dekryptere enterprise indstillinger og programmet data synkroniseres af tjenesten Enterprise tilstand Roaming; Du skal have et betalt abonnement bruge alle mulighederne i Azure Rights Management.

Efter at opnå en Premium Azure AD-abonnement, skal du følge disse trin for at aktivere Enterprise tilstand Roaming:

1. Log på portalen Azure klassisk.
2. Vælg **ACTIVE DIRECTORY**til venstre, og vælg derefter den mappe, hvor du vil aktivere Enterprise tilstand Roaming.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming.png)
3. Gå til fanen **KONFIGURER** øverst.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-configure.png)
4.  Rul ned på siden og vælge **brugere kan SYNKRONISERE indstillinger og ENTERPRISE APP-DATA**, og klik derefter på **Gem**.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-select-all-sync-settings.png)

Til en Windows 10-enhed til roaming indstillinger med Enterprise-tilstand roamingtjenesten skal enheden godkende ved hjælp af en Azure AD-identitet. For enheder, der er sammenføjet til Azure AD, er brugerens primære logon Azure AD-identitet, så der kræves ingen yderligere konfiguration. IT-administratoren skal [forbinde enhederne medlem af et domæne til Azure AD til Windows 10 oplevelser](active-directory-azureadjoin-devices-group-policy.md)for enheder, der bruger en traditionel lokalt Active Directory.

## <a name="sync-data-storage"></a>Synkroniser datalagring
Enterprise tilstand globale data er placeret i en eller flere [Azure områder](https://azure.microsoft.com/regions/ ) , der bedst justerer med værdien land/område er angivet i forekomsten af Azure Active Directory. Enterprise tilstand globale data er opdelt baseret på tre overordnede geografiske områder: Nordamerika, EMEA og APAC. Enterprise tilstand globale data for lejeren er placeret lokalt med et geografisk område og replikeres ikke på tværs af områder.  For eksempel kunder, der har deres land/område værdi være angivet til en af EMEA-lande som "Frankrig" eller "Zambia" har deres data, der er hostet af de Azure områder i Europa eller i en.  Kunder, som Angiv værdi deres land/område i Azure AD til en af Nordamerika lande, såsom "USA" eller "Canada" får deres data, der er hostet på en eller flere af de Azure områder i USA.  Kunder, som Angiv værdi deres land/område i Azure AD til en af APAC lande, såsom "Australien" eller "New Zealand" får deres data, der er placeret i en eller flere af de Azure områder i Asien.  Sydamerikanske lande og Antarktis data skal placeres i en eller flere Azure områder i USA.  Værdien for land/område er angivet som en del af processen til oprettelse af Azure AD directory og kan ikke redigeres efterfølgende. 

Hvis du har brug for flere oplysninger om data lagerplacering, Rapportér en brugertilladelse med [Azure understøtter](https://azure.microsoft.com/support/options/).

## <a name="manage-enterprise-state-roaming"></a>Administrere Enterprise tilstand Roaming
Azure AD globale administratorer kan aktivere og deaktivere Enterprise tilstand Roaming i portalen Azure klassisk.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-manage.png)

Globale administratorer kan begrænse indstillinger for synkronisering til bestemte sikkerhedsgrupper.

Globale administratorer kan også se en statusrapport til hver bruger enhed synkronisering ved at vælge en bestemt bruger på listen Active Directory forekomst **brugere** og klikke på fanen **enheder** og vælge Vis **synkronisering indstillinger og app virksomhedsdata enheder**.
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-device-sync-settings.png)

##<a name="data-retention"></a>Dataopbevaring
Data, der er synkroniseret til Azure via Enterprise tilstand Roaming bevares på ubestemt tid, medmindre der udføres en manuel sletning eller de pågældende data bestemmes skal være forældede. 

**Eksplicitte sletningen:** Data, der slettes, når administrator Azure sletter en bruger eller et bibliotek eller en administrator, som udtrykkeligt, at dataene er at blive slettet.

- **Sletning af brugeren**: Når en bruger slettes i Azure AD, brugerkontoen globale data vil blive markeret til sletning og slettes mellem 90 grader til 180 dage. 
- **Directory sletningen**: slette en hel mappe i Azure AD er en øjeblikkelig handling. Alle indstillinger dataene der er tilknyttet, directory markeres til sletning og vil blive slettet mellem 90 grader til 180 dage. 
- **På anmodning om sletning**: Hvis Azure AD-administratoren ønsker at slette en bestemt bruger data eller indstillinger data manuelt, kan administratoren indsende en brugertilladelse med [Azure understøtter](https://azure.microsoft.com/support/). 

**Forældede Datasletning**: Data, der ikke er blevet åbnet for et år ("opbevaringsperiode") bliver behandlet som gamle og kan blive slettet fra Azure. Opbevaringsperioden kan ændres, men vil ikke være mindre end 90 dage. Forældede dataene kan være et bestemt sæt Windows/application eller alle indstillingerne for en bruger. Eksempel:
 
- Hvis ingen enheder adgang til en bestemt indstillinger af websteder (f.eks., et program er fjernet fra enheden, eller en indstillinger gruppe som "Tema" er deaktiveret for alle brugerens enheder), og klik derefter samlingen vil være forældede efter opbevaringsperioden og kan slettes. 
- Hvis en bruger har deaktiveret indstillinger Synkroniser på alle sit enheder, derefter ingen af indstillinger dataene åbnes, og alle indstillinger dataene for den pågældende bruger bliver forældede og kan slettes efter opbevaringsperioden. 
- Hvis Azure AD directory administratoren deaktiverer Enterprise tilstand Roaming til hele kataloget, derefter alle brugere i den valgte mappe ikke synkroniseres længere indstillinger, og alle dataene på skærmen indstillinger for alle brugere bliver forældede og kan slettes efter opbevaringsperioden. 

**Gendannelse af slettet data**: opbevaringspolitikken data kan ikke konfigureres. Når dataene er blevet slettet permanent, er det ikke genoprettes. Men det er vigtigt at være opmærksom på, at indstillinger dataene kun blive slettet fra Azure, ikke slutbrugerlicensaftale enheden. Hvis en hvilken som helst enhed senere igen opretter forbindelse til Enterprise-tilstand roamingtjenesten, indstillingerne igen synkroniseret og gemmes i Azure.


## <a name="related-topics"></a>Relaterede emner
- [Enterprise tilstand Roaming oversigt](active-directory-windows-enterprise-state-roaming-overview.md)
- [Indstillinger og data roaming ofte stillede spørgsmål](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Gruppere politik og MDM indstillinger for synkronisering af indstillinger](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Windows 10 globale indstillinger reference](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
