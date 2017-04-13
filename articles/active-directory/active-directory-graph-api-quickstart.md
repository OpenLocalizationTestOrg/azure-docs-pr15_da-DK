<properties
   pageTitle="Hurtig start for Azure AD grafen API | Microsoft Aure"
   description="Azure Active Directory Graph API leverer programmeringsmæssig adgang til Azure AD gennem OData REST-API slutpunkter. Programmer kan bruge Graph API til at udføre oprette, læse, opdatere og slette (CRUD) handlinger på directory-data og objekter."
   services="active-directory"
   documentationCenter="n/a"
   authors="PatAltimore"
   manager="mbaldwin"
   editor=""
   tags=""/>


   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="09/16/2016"
      ms.author="patricka"/>

# <a name="quickstart-for-the-azure-ad-graph-api"></a>Hurtig start for Azure AD grafen API

Azure Active Directory (AD) Graph API leverer programmeringsmæssig adgang til Azure AD gennem OData REST-API slutpunkter. Programmer kan bruge Graph API til at udføre oprette, læse, opdatere og slette (CRUD) handlinger på directory-data og objekter. Du kan for eksempel bruge Graph API til at oprette en ny bruger, få vist eller opdatere brugerens egenskaber, ændre brugerens adgangskode, tjekke gruppemedlemskabet for rollebaseret adgang, deaktivere eller slette brugeren. Se [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) og [Azure AD Graph API forudsætninger](https://msdn.microsoft.com/library/hh974476.aspx)kan finde flere oplysninger om de Graph API funktioner og programmer scenarier. 

> [AZURE.IMPORTANT] Azure AD Graph API-funktionalitet er også tilgængelig via [Microsoft Graph](https://graph.microsoft.io/), et samlet API, der indeholder API'er fra andre Microsoft-tjenester som Outlook, OneDrive, OneNote, teamplanlægning og Office Graph, der er tilgængelige via et enkelt slutpunkt og med en enkelt adgangstoken.

## <a name="how-to-construct-a-graph-api-url"></a>Sådan oprettes en graf API URL-adresse

For at få adgang til directory-data og objekter (med andre ord, ressourcer eller enheder), som du vil udføre CRUD handlinger i Graph API, kan du bruge URL-adresser baseret på den åbne Data (OData) protokol. De URL-adresser, der bruges i Graph API består af fire hoveddele: service rod, lejer id, ressource sti og streng forespørgselsindstillinger: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Brug eksemplet med følgende URL-adressen: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

- **Tjenestens rod**: I Azure AD Graph API-tjenestens rod er altid https://graph.windows.net.
- **Lejer-id**: det kan være et bekræftet (registrerede) domænenavn, i eksemplet ovenfor, contoso.com. Det kan også være en lejer objekt-ID eller "myorganiztion" eller "mig" alias. Du kan finde flere oplysninger, se [håndtere objekter og handlinger i Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)).
- **Ressource sti**: denne del af en URL-adresse angiver ressourcen for at anvendes sammen (brugere, grupper, en bestemt bruger eller en bestemt gruppe osv.) I det foregående eksempel er det på øverste niveau "grupper", som ressource angive. Du kan også tale om et bestemt objekt, for eksempel "brugere / {objectId}" eller "brugere/userPrincipalName".
- **Forespørgselsparametre**:? adskiller sektionen ressource sti i sektionen forespørgsel parametre. Der kræves for forespørgselsparameter "api-version" på alle anmodninger i Graph API. Graph API understøtter også følgende indstillinger for OData-forespørgsel: **$filter**, **$orderby**, **$Udvid**, **$top**og **$format**. Følgende forespørgselsindstillinger understøttes ikke i øjeblikket: **$count**, **$inlinecount**og **$skip**. Se [sideopdeling indstillinger i Azure AD Graph API understøttes forespørgsler, filtre, og](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options)få mere at vide.

## <a name="graph-api-versions"></a>Graph API versioner

Du angiver versionen til en graf API-anmodning i forespørgselsparameter "api-version". Version 1.5 og nyere bruger du en numeriske versionsværdi. API-version = 1,6. For tidligere versioner, skal bruge du en datostreng, der overholder formatet åååå-MM-DD for eksempel api-version = 2013-11-08. Funktionerne preview, brug af strengen "beta" for eksempel api-version = beta. Du kan finde flere oplysninger om forskellene mellem versionerne Graph API, [Azure AD Graph API versionsstyring](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Graph API-metadata

Returnere filen Graph API metadata, skal du tilføje segmentet "$metadata" efter lejer-id i URL-adressen For eksempel, returnerer følgende URL-adressen metadata for den demo virksomhed, der bruges af Graph Explorer: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Du kan angive denne URL-adressen i adresselinjen i en webbrowser for at få vist metadata. CSDL Metadatadokumentet returneres beskrives enheder og komplekse typer, deres egenskaber og funktioner og handlinger, der vises af versionen af Graph API, du har anmodet om. Udelade parameteren api-version returnerer metadata til den nyeste version.

## <a name="common-queries"></a>Almindelige forespørgsler

[Azure AD Graph API almindelige forespørgsler](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) viser almindelige forespørgsler, der kan bruges med Azure AD-graf, herunder forespørgsler, der kan bruges til at få adgang til webstedet på øverste ressourcer i mappen og forespørgsler til at udføre handlinger i mappen.

For eksempel `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` returnerer virksomhedsoplysninger for directory contoso.com.

Eller `https://graph.windows.net/contoso.com/users?api-version=1.6` viser en liste over alle user-objekter i directory contoso.com.

## <a name="using-the-graph-explorer"></a>Brug Graph Stifinder

Du kan bruge Graph Explorer til Azure AD Graph API til at forespørge directory-data, som du opretter dit program.

> [AZURE.IMPORTANT] Graph Explorer understøtter ikke skrive eller slette data fra en mappe. Du kan kun udføre Læs handlinger på adresselisten Azure AD med Graph Stifinder.

Følgende er et output, du vil se, hvis du skulle navigere til Graph Explorer, Vælg Brug Demo virksomhed, og angiv `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` til at vise alle brugere i mappen demo:

![Azure AD graph api explorer](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Indlæse Graph Explorer**: for at indlæse værktøjet skal du gå til [https://graphexplorer.cloudapp.net/](https://graphexplorer.cloudapp.net/). Klik på **Brug Demo virksomhed** for at køre Graph Explorer mod data fra en eksempel-lejer. Du behøver ikke legitimationsoplysninger for at bruge demofirmaet. Du kan alternativt klikke på **Log på** og logge på med legitimationsoplysningerne Azure AD-konto til at køre Graph Explorer mod din lejer. Hvis du kører Graph Explorer i forhold til dine egne lejer, skal du eller administratoren tilladelse under logon. Hvis du har et Office 365-abonnement, har du automatisk en Azure AD-lejer. Legitimationsoplysningerne, som du kan bruge til at logge på Office 365 er faktisk Azure AD-konti, og du kan bruge disse legitimationsoplysninger med Graph Stifinder.

**Køre en forespørgsel**: for at køre en forespørgsel, Skriv din forespørgsel i tekstfeltet anmodningen og klikke på **Hent** eller klikke på **Angiv** nøgle. Resultaterne vises i feltet svar. For eksempel `https://graph.windows.net/graphdir1.onmicrosoft.com /groups?api-version=1.6` viser alle gruppeobjekter i mappen demo.

Bemærk følgende funktioner og begrænsninger i Graph Explorer:
- Muligheden for autofuldførelse af ressource angiver. Se dette, klik på **Brug Demo virksomhed** , og klik derefter på i tekstfeltet anmodning (hvor virksomhedens URL-adresse vises). Du kan vælge en ressource, der er angivet på rullelisten.

- Understøtter "mig" og "myorganization" håndtere aliasser. Du kan for eksempel bruge `https://graph.windows.net/me?api-version=1.6` til at returnere brugerobjektet på den bruger, der er logget på eller `https://graph.windows.net/myorganization/users?api-version=1.6` til at returnere alle brugere i den aktuelle mappe. Bemærk, at ved hjælp af "mig" alias returnerer en fejl for demo virksomheden fordi der ikke er ikke logget på en bruger foretager anmodningen.

- Et svar sidehoveder afsnit. Dette kan bruges til at hjælpe med at fejlfinde problemer, der kan opstår, når du kører forespørgsler.

- En JSON-Fremviser til svaret med Udvid og Skjul funktioner.

- Ingen understøttelse af viser en miniature.

## <a name="using-fiddler-to-write-to-the-directory"></a>Brug af Fiddler til at skrive til mappen

I forbindelse med denne vejledning til hurtig start, kan du bruge Fiddler Web fejlfindingsværktøjet for at øve ydeevne 'skrive' handlinger mod dig Azure AD-mappe. Du kan finde flere oplysninger og installere Fiddler, skal du se [http://www.telerik.com/fiddler](http://www.telerik.com/fiddler).

I følgende eksempel skal bruge du Fiddler Web fejlfindingsværktøjet til at oprette en ny sikkerhedsgruppe 'MyTestGroup' i Azure AD-biblioteket.

**Hent et adgangstoken**: for at få adgang til Azure AD Graph klienter skal godkendes, at Azure AD først. Du kan finde flere oplysninger [Godkendelse scenarier, hvor Azure AD](active-directory-authentication-scenarios.md).

**Opret og køre en forespørgsel**: benytte følgende fremgangsmåde.

1. Åbn Fiddler Web fejlfinding og Skift til fanen **Komponist** .
2. Da du vil oprette en ny sikkerhedsgruppe, skal du vælge **indlæg** som metoden HTTP fra i rullemenuen. Du kan finde flere oplysninger om handlinger og tilladelser på en gruppeobjektet [gruppe](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#GroupEntity) i [Azure AD Graph RESTEN API Reference](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. Skriv følgende som URL-anmodningen i feltet ud for **indlæg**: `https://graph.windows.net/mytenantdomain/groups?api-version=1.6`.

    > [AZURE.NOTE] Du skal erstatte mytenantdomain med domænenavn af din egen Azure AD-mappe.

4. I feltet direkte under indlæg hente ned skal du skrive følgende:

    ```
Host: graph.windows.net
Authorization: your access token
Content-Type: application/json
```

    > [AZURE.NOTE] Udskift din &lt;din adgangstoken&gt; med adgangstoken til din Azure AD-mappe.

5. I feltet **anmodningsteksten** skal du skrive følgende:

    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
```

    Du kan finde flere oplysninger om oprettelse af grupper, [Opret gruppe](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Flere oplysninger om Azure AD-objekter og typer, der vises ved graf og oplysninger om de handlinger, der kan udføres på dem med Graph under [Azure AD Graph RESTEN API Reference](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Næste trin

- Lær mere om [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
- Lær mere om [Azure AD Graph API tilladelse områder](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
