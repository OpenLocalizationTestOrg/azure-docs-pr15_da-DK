
<properties
    pageTitle="Brug af Azure AD forbinde tilstand med synkronisering | Microsoft Azure"
    description="Dette er siden Azure AD forbinde tilstand, der beskriver, hvordan til at overvåge Azure AD-forbindelse synkronisering."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="using-azure-ad-connect-health-for-sync"></a>Ved hjælp af Azure AD forbinde tilstand til synkronisering
Følgende dokumentation er specifikke for overvågning Azure AD-forbindelse (Sync) med Azure AD forbinde tilstand.  Du kan finde oplysninger om overvågning af AD FS med Azure AD forbinde sundhed [Ved hjælp af Azure AD forbinde tilstand med AD FS](active-directory-aadconnect-health-adfs.md). Desuden kan finde oplysninger om overvågning Active Directory Domain Services med Azure AD forbinde tilstand i [Ved hjælp af Azure AD forbinde tilstand med Active Directory-Domænetjenester](active-directory-aadconnect-health-adds.md).

![Azure AD Connect tilstand for synkronisering](./media/active-directory-aadconnect-health-sync/sync-blade.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Beskeder for Azure AD forbinde tilstand for synkronisering
Sektionen Azure AD forbinde sundhed beskeder om synkronisering giver dig på listen over aktive beskeder. Hver besked indeholder relevante oplysninger, opløsning trin og links til relateret dokumentation. Du får vist en ny blade med yderligere oplysninger, samt trin, du kan bruge til at løse besked og links til yderligere dokumentation ved at vælge en aktiv eller løst besked. Du kan også få vist historiske data på beskeder, der er blevet løst tidligere.

Du kan bruge til at løse besked og links til yderligere dokumentation ved at vælge en besked, får du nogle med yderligere oplysninger samt trin.

![Azure AD-forbindelse synkroniseringsfejl](./media/active-directory-aadconnect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>Begrænset evalueringen af beskeder
Hvis Azure AD-forbindelse ikke bruger standardkonfigurationen (for eksempel, hvis attributten filtrering er ændret fra standardkonfigurationen til en brugerdefineret konfiguration), vil Azure AD forbinde sundhed agent ikke overføre de fejlhændelser, der er relateret til Azure AD-forbindelse.

Denne funktion begrænser evalueringen af beskeder af tjenesten. Du vil se et banner, der angiver denne betingelse i portalen Azure under din tjeneste.

![Azure AD Connect tilstand for synkronisering](./media/active-directory-aadconnect-health-sync/banner.png)

Du kan ændre dette ved at klikke på "Indstillinger" og tillade Azure AD forbinde Health agent overføre alle logonfejl.

![Azure AD Connect tilstand for synkronisering](./media/active-directory-aadconnect-health-sync/banner2.png)

## <a name="sync-insight"></a>Synkroniser indsigt
Administratorer ofte vil have at vide om den tid, det tager at synkronisere dine ændringer til Azure AD og mængden ændringer finder sted. Denne funktion gør det nemt at visualisere ved hjælp af den under grafer:   

- Ventetid på Synkroniser handlinger
- Objekt ændrede tendens

### <a name="sync-latency"></a>Synkroniser ventetid
Denne funktion giver en grafisk tendens i ventetid Synkroniser-handlinger (Importér, Eksportér osv.) for en forbindelse.  Dette giver en hurtig og nem måde at forstå, ikke kun forsinkelse af dine handlinger (større, hvis du har en stor mængde forandringer), men også en metode til at registrere afvigelser i ventetid, som kan kræve yderligere undersøgelse.

![Synkroniser ventetid](./media/active-directory-aadconnect-health-sync/synclatency02.png)

Som standard vises kun forsinkelse på ' eksporten ' til Azure AD-forbindelse.  Vælg Rediger diagram for at se flere handlinger på forbindelsen, eller Højreklik på diagrammet for at få vist handlinger fra andre forbindelser, eller klik på knappen "Rediger ventetid diagram", og vælg bestemt operation og forbindelser.

### <a name="sync-object-changes"></a>Synkroniser objektændringer
Denne funktion giver en grafisk tendens af antallet ændringer, der evalueres og eksporteres til Azure AD.  I dag, er forsøger at indsamle oplysningerne fra loggene synkronisering svært.  Diagrammet giver dig, ikke kun en nemmere måde at overvåge antallet af ændringer, som opstår i dit miljø, men også en visuel visning af de fejl, som opstår.

![Synkroniser ventetid](./media/active-directory-aadconnect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report-preview"></a>Objekt synkronisering på niveau fejlrapport (Preview)
Denne funktion giver en rapport om synkroniseringsfejl, der kan opstå, når synkroniseres identitetsdata mellem Windows Server AD og Azure AD ved hjælp af Azure AD-forbindelse.

- Rapporten dækker fejl, der er registreret i synkroniseringsklienten (Azure AD-forbindelse version 1.1.281.0 eller nyere)
- Den indeholder de fejl, der er opstået i den seneste synkronisering operation på Synkroniser-program. ("Eksportere" på den Azure AD-forbindelse.)
- Azure AD forbinde Health agent for synkronisering skal have udgående forbindelse til de nødvendige slutpunkter for rapporten for at medtage de nyeste data. Se [afsnittet krav](active-directory-aadconnect-health-agent-install.md#Requirements) til detaljer.
- Rapporten er **opdateret efter hver 30 minutter** ved hjælp af de data, der er overført af Azure AD forbinde Health agent for synkronisering.
Den indeholder følgende vigtige funktioner

    - Kategorisering af fejl
    - Liste over objekter med fejl hver kategori
    - Alle data om fejl på ét sted
    - Side om side-sammenligning af objekter med fejl på grund af en konflikt
    - Hente fejlrapporten som en CVS (kommer snart)

### <a name="categorization-of-errors"></a>Kategorisering af fejl
Rapporten kategoriserer de eksisterende synkroniseringsfejl i følgende kategorier:

| Kategori | Beskrivelse |
| -------------- | ----------- |
| Gentaget attribut | Fejl, når Azure AD-forbindelse forsøger oprette eller opdatere objekter med dublerede værdier over en eller flere attributter i Azure AD, der skal være entydigt i en lejer, som proxyAddresses, UserPrincipalName. |
| Data uoverensstemmelsen | Fejl, når den bløde match mislykkes, så det svarer til objekter, der medfører synkroniseringsfejl. |
| Fejl i datavalidering | Fejl på grund af ugyldige data, som ikke-understøttede tegn i kritiske attributter som UserPrincipalName, formatere fejl, der ikke består validering før skrives i Azure AD.|
| Store attribut | Fejl, når en eller flere attributter er større end de tilladte størrelse, længde eller tælle.|
| Andre | Alle andre fejl, der ikke passer i kategorierne ovenfor. Baseret på feedback, fordeles denne kategori i sub kategorier.

![Synkronisere fejl rapporten Oversigt over](./media/active-directory-aadconnect-health-sync/errorreport01.png)
![synkronisere fejl rapportkategorier](./media/active-directory-aadconnect-health-sync/errorreport02.png)

### <a name="list-of-objects-with-error-per-category"></a>Liste over objekter med fejl hver kategori
Analysere i hver kategori give på listen over objekter, der fører fejlen i den pågældende kategori.
![På listen over synkronisering fejl rapport](./media/active-directory-aadconnect-health-sync/errorreport03.png)

### <a name="error-details"></a>Flere oplysninger om fejlen
Følgende data findes i den detaljerede visning for de enkelte fejl

- Id'er til *AD objekt* involveret
- Id'er til *Azure AD objekt* involveret (hvis relevant)
- Fejlbeskrivelse og hvordan du retter
- Relaterede artikler

![Flere oplysninger om synkronisering fejlen rapport](./media/active-directory-aadconnect-health-sync/errorreport04.png)

### <a name="download-the-error-report-as-csv"></a>Hente fejlrapporten som CSV
Denne egenskab kommer snart. Holde dig orienteret om flere opdateringer.



## <a name="related-links"></a>Relaterede links
* [Fejlfinding under synkronisering](active-directory-aadconnect-troubleshoot-sync-errors.md)
* [Duplikere attribut fleksibilitet](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect tilstand](active-directory-aadconnect-health.md)
* [Azure AD Connect sundhed Agent-Installation](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect sundhed handlinger](active-directory-aadconnect-health-operations.md)
* [Brug af Azure AD forbinde tilstand med AD FS](active-directory-aadconnect-health-adfs.md)
* [Brug af Azure AD forbinde tilstand med AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect sundhed ofte stillede spørgsmål](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect sundhed versionshistorik](active-directory-aadconnect-health-version-history.md)
