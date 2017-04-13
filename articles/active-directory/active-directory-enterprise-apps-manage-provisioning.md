<properties
    pageTitle="Bruger klargøring administration for enterprise-apps i Azure Active Directory preview | Microsoft Azure"
    description="Lær, hvordan du administrere bruger konto klargøring til enterprise apps ved hjælp af Azure Active Directory-eksempel"
    services="active-directory"
    documentationCenter=""
    authors="asmalser"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/12/2016"
    ms.author="asmalser"/>

#<a name="preview-managing-user-account-provisioning-for-enterprise-apps-in-the-new-azure-portal"></a>Eksempel: Administrere brugerkonto klargøring til virksomhedsapps i den nye Azure portal

I denne artikel beskrives, hvordan du bruger [Azure portal](https://portal.azure.com) til at administrere automatisk brugerkonto klargøring og deaktivere klargøring til programmer, der understøtter det, især dem, der er tilføjet i kategorien "udvalgt" i [Galleri med Azure Active Directory-program](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). Denne management oplevelse i den nye Azure portal er i øjeblikket i prøveversionen, og i denne artikel beskrives de nye funktioner samt nogle midlertidige begrænsninger, der er på plads i perioden, preview. [Hvad er i preview?](active-directory-preview-explainer.md)

Hvis du vil vide mere om automatisk bruger konto klargøring og hvordan det fungerer, skal du se [automatisere brugerklargøring og Deprovisioning til SaaS programmer med Azure Active Directory](active-directory-saas-app-provisioning.md).

##<a name="finding-your-apps-in-the-new-portal"></a>Finde dine apps i den nye portal

Og September 2016, kan alle programmer, der er konfigureret til single sign-on – i en mappe, som administrator af en mappe ved hjælp af [Azure Active Directory-program galleriet](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) i [Azure klassisk portal](https://manage.windowsazure.com), nu vises og administreres i den nye Azure portal.

Disse programmer kan findes i afsnittet **Virksomhedens programmer** i den nye Azure portal, som kan åbnes via menuen **Flere tjenester** i det venstre navigationsområde. Virksomhedsapps er apps, der er implementeret og bliver brugt af brugere i organisationen.

![Virksomhedens programmer blade][0]

Vælge linket **alle programmer** i venstre side viser en liste over alle apps, der er konfigureret, herunder apps, der er blevet tilføjet i galleriet. Hvis du vælger en app indlæser bladet ressource for den app, hvor rapporter kan ses for denne app og en række indstillinger kan administreres.

Indstillinger for klargøring brugerkontoen kan administreres ved at vælge **artikel Provisioning** i venstre side.

![Programmet ressource blade][1]


##<a name="provisioning-modes"></a>Klargøring af tilstande

Bladet **artikel Provisioning** starter med en **tilstand** i menuen, som viser, hvilke klargøring funktionsmåder understøttes til et enterprise-program, og gør det muligt at være konfigureret. De tilgængelige indstillinger omfatter:

* **Automatisk** – denne indstilling vises, hvis Azure AD understøtter automatisk API-baserede klargøring og/eller deaktivere klargøring af brugerkonti til dette program. Hvis du vælger denne tilstand vises en grænseflade, der hjælper administratorer gennem konfigurere Azure AD at oprette forbindelse til programmets brugeradministration API, oprette Kontotilknytninger og arbejdsprocesser, der definerer, hvordan brugeren konto skal dataflow mellem Azure AD og app, og administration af den Azure AD klargøring af tjenesten.

* **Manuel** – denne indstilling vises Hvis Azure AD ikke understøtter automatisk klargøring af brugerkonti til dette program. Denne indstilling betyder, at bruger poster af typen firma gemt i programmet på computeren skal administreres ved hjælp af en ekstern proces, baseret på bruger administration og klargøring af funktioner, der leveres af det pågældende program (som kan omfatte SAML JIT klargøring).


##<a name="configuring-automatic-user-account-provisioning"></a>Konfigurere automatiske bruger konto klargøring

Hvis du vælger indstillingen **automatisk** vises et skærmbillede, der er inddelt i fire sektioner:

###<a name="admin-credentials"></a>Administratoroplysninger
Dette er, hvor legitimationsoplysningerne, der kræves til Azure AD at oprette forbindelse til programmets brugeradministration API er angivet. Der kræves input varierer afhængigt af programmet. Se [konfiguration selvstudium for det pågældende bestemt program](active-directory-saas-app-provisioning.md#list-of-apps-that-support-automated-user-provisioning)for at få mere for at vide om de typer af legitimationsoplysninger og krav til bestemte programmer.

Hvis du vælger knappen **Test forbindelse** kan du teste legitimationsoplysningerne ved at få Azure AD forsøg på at oprette forbindelse til app klargøring af appen med de medfølgende legitimationsoplysninger.

###<a name="mappings"></a>Tilknytninger
Dette er hvor administratorer kan få vist og redigere hvilke bruger attributter flow mellem Azure AD og destinationsprogrammet, når brugerkonti klargjort eller opdateres.

Der er et forudkonfigurerede sæt af tilknytninger mellem Azure AD bruger og hver SaaS app brugerobjekter. Visse apps administrere andre typer objekter, som grupper eller kontakter. Valg af en af disse tilknytninger i tabel viser redigeringsprogrammet tilknytning til højre, hvor de kan vises og tilpasset.

![Programmet ressource blade][2]

Understøttede tilpasninger i første preview omfatter:

* Aktivering og deaktivering af tilknytninger for bestemte objekter, som brugerobjektet Azure AD til appen SaaS brugerobjektet.

* Redigerer, hvilke attributter flyde fra Azure AD brugerobjektet til den app brugerobjektet. Se [forstå attribut tilknytning typer](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types)kan finde flere oplysninger om attributten tilknytning.

* Filtrere hvad klargøring Azure AD skal udføre på destinationsprogrammet, som er en ny funktion i portalen Azure. I stedet for få Azure AD synkronisere alle objekter, kan du begrænse de handlinger, der er udført. Vælg, f.eks ved kun **Update**, Azure AD kun opdateringer eksisterende bruger konti i et program og ikke oprette nye. Kun vælger **Opret**, og Azure kun opretter nye brugerkonti, men opdatere ikke eksisterende linjer. Denne funktion kan administratorer at oprette forskellige tilknytninger for oprettelse af en konto og opdatere arbejdsprocesser. Fuld muligheden for at oprette flere tilknytninger per app er planlagt for senere i perioden preview.

###<a name="settings"></a>Indstillinger
Dette afsnit kan administratorer starte og stoppe den Azure AD klargøring service for det valgte program samt eventuelt rydde klargøring cachen og genstarte tjenesten.

Hvis klargøring er blive aktiveret for første gang for et program, aktivere tjenesten ved at ændre **Status for klargøring** til **til**. Dette får Azure AD klargøring af tjenesten til at udføre en indledende synkronisering, hvor den læser de brugere, der er tildelt i sektionen **brugere og grupper** , forespørger destinationsprogram til dem, og derefter udfører de klargøring handlinger, der er defineret i sektionen Azure AD **tilknytninger** . Under denne proces gemmer tjenesten klargøring cachelagrede data om, hvad det administration af brugerkonti, så ikke-administrerede konti i destinationsprogrammer, der var aldrig på området for tildelingen ikke påvirkes af deaktivere klargøring handlinger. Efter den indledende synkronisering synkroniserer klargøring automatisk tjenesten bruger og gruppeobjekter i et interval ti minutter.

Tjenesten klargøring afbrydes **Klargøring Status** ændres til **Off** blot. I denne tilstand Azure ikke oprette, opdatere, eller fjerne en bruger eller gruppeobjekter i appen. Ændre tilstanden tilbage til på bevirker, at tjenesten til at vælge, hvor den slap.

Markere afkrydsningsfeltet **Ryd aktuelle tilstand, og genstart synkronisering** og gemme stopper tjenesten klargøring gemmer de cachelagrede data om hvilke konti Azure AD administrerer, genstarter tjenesterne og udfører den indledende synkronisering igen. Denne indstilling kan administratorer til at starte installationsprocessen klargøring igen.

###<a name="synchronization-details"></a>Synkronisering af detaljer
Dette afsnit indeholder addition detaljer om driften af tjenesten klargøring, herunder de første og sidste tidspunkter, hvor tjenesten klargøring kørte mod programmet, og hvor mange bruger og gruppeobjekter er administreres.

Links er leveret til **artikel Provisioning aktivitetsrapport**, der indeholder en logfil over alle brugere og grupperer oprettede, opdaterede og fjernede mellem Azure AD og destinationen program, og til **artikel Provisioning fejlrapport** som giver mere detaljerede fejlmeddelelser for bruger og gruppeobjekter, der ikke kunne læses, oprettet, opdateres eller fjernet. 

[0]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-blade.PNG
[1]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning.PNG
[2]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning-mapping.PNG
