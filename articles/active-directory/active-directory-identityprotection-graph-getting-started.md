<properties
    pageTitle="Introduktion til Azure Active Directory identitetsbeskyttelse og Microsoft Graph | Microsoft Azure"
    description="Indeholder en introduktion til Microsoft Graph-forespørgsel til en liste over risikoen begivenheder og tilknyttede oplysninger fra Azure Active Directory."
    services="active-directory"
    keywords="beskyttelse af Azure active directory din identitet, risikohændelse, sikkerhedsrisiko, sikkerhedspolitik, Microsoft Graph"
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
    ms.date="08/22/2016"
    ms.author="markvi"/>

# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Introduktion til Azure Active Directory identitetsbeskyttelse og Microsoft Graph

Microsoft Graph er Microsofts samlet API slutpunkt og hjem af [Azure Active Directory Identitetsbeskyttelses](active-directory-identityprotection.md) API'er. Vores første API, **identityRiskEvents**, kan du forespørgsel Microsoft Graph en liste over [risikoen begivenheder](active-directory-identityprotection-risk-events-types.md) og tilknyttede oplysninger. I denne artikel får du forespørgsler denne API i gang. For dybtgående Introduktion, fuld dokumentation og adgang til Explorer Graph se [Microsoft Graph websted](https://graph.microsoft.io/).

Der er tre trin til at få adgang til beskyttelse af din identitet data via Microsoft Graph:

1. Tilføje et program med en klient hemmeligt. 

2. Brug denne hemmeligt og et par andre dele af oplysninger til at godkende Microsoft Graph, hvor du modtager en godkendelse token. 

3. Brug dette token til at foretage anmodninger API slutpunkt og hente data for beskyttelse af din identitet tilbage.

Før du går i gang, skal du:

- Administratorrettigheder for at oprette programmet i Azure AD
- Navnet på din lejer domæne (for eksempel contoso.onmicrosoft.com)



## <a name="add-an-application-with-a-client-secret"></a>Tilføje et program med en klient hemmeligt


1. [Log på](https://manage.windowsazure.com) din Azure klassisk portalen som administrator. 

1. Klik på **Active Directory**i den venstre navigationsrude på. 

    ![Oprette et program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)

2. Vælg den mappe, hvor du vil aktivere katalogintegration fra listen **adresseliste** .

3. Klik på **programmer**i menuen øverst.

    ![Oprette et program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)

4. Klik på **Tilføj** i bunden af siden.

    ![Oprette et program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)

5. Dialogboksen **Hvad vil du gøre** , klik på **Tilføj et program, udvikling af min organisation**.

    ![Oprette et program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)


5. I dialogboksen **fortælle os om dit program** skal du udføre følgende trin:

    ![Oprette et program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)

    en. I tekstfeltet **navn** skal du skrive et navn for dit program (f.eks.: AADIP risikoen begivenhed API program).

    b. Vælg **Web Application og/eller Web API**som **Type**.

    c. Klik på **Næste**.


5. I dialogboksen **Egenskaber for App** skal du udføre følgende trin:

    ![Oprette et program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)

    en. I tekstfeltet **Sign-On URL-adresse** skal du skrive `http://localhost`.

    b. Skriv i tekstfeltet **App ID URI** `http://localhost`.

    c. Klik på **udført**.


Din kan nu konfigurere dit program.

![Oprette et program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)



## <a name="grant-your-application-permission-to-use-the-api"></a>Give dit program tilladelse til at bruge API


1. Klik på **Konfigurer**på programmets side, i menuen i øverst. 

    ![Oprette et program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)

2. Klik på **Tilføj program**i sektionen **tilladelser til andre programmer** .

    ![Oprette et program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)


2. I dialogboksen **tilladelser til andre programmer** skal du udføre følgende trin:

    ![Oprette et program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)

    en. Vælg **Microsoft Graph**.

    b. Klik på **udført**.


1. Klik på **tilladelser for tjenesteprogram: 0**, og vælg derefter **læse alle identitet risikoen hændelsesoplysninger**.

    ![Oprette et program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)

1. Klik på **Gem** nederst på siden.

    ![Oprette et program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)


## <a name="get-an-access-key"></a>Få en hurtigtast

1. Vælg 1 år som varighed i sektionen **taster** på dit program siden.

    ![Oprette et program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)

1. Klik på **Gem** nederst på siden.

    ![Oprette et program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

1. Kopiér værdien af din nye nøgle i afsnittet taster, og Indsæt det i et sikkert sted.

    ![Oprette et program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)

    > [AZURE.NOTE] Hvis du mister denne tast, har du vende tilbage til denne sektion og oprette en ny nøgle. Holde denne tast et hemmeligt: alle, der har det kan få adgang til dine data.


1. Kopiér **Klient-ID**i sektionen **Egenskaber** , og Indsæt det i et sikkert sted. 


## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Godkende til Microsoft Graph og forespørge identitet risikoen begivenheder API

På dette tidspunkt, du skal have:

- Brugerens klient-ID, du kopierede tidligere

- Den nøgle, du kopierede tidligere

- Navnet på din lejer domæne


Hvis du vil godkende, sende en indkaldelse til indlæg til `https://login.microsoft.com` med følgende parametre i brødteksten:

- grant_type: "**client_credentials**"

- ressource: "**https://graph.microsoft.com**"

- client_id:<your client ID>

- client_secret:<your key>


> [AZURE.NOTE] Du har brug at angive værdier for **client_id** og parameteren **client_secret** .

Hvis det lykkes, returnerer dette token en godkendelse.  
For at ringe API skal du oprette et sidehoved med følgende parameter:

    `Authorization`=”<token_type> <access_token>"


Når godkendelse, kan du finde token type og adgangstoken i den returnerede token.

Send denne overskrift som en anmodning til følgende API URL-adressen:`https://graph.microsoft.com/beta/identityRiskEvents`

Svaret, er Hvis det lykkes, en samling af identitet risikoen begivenheder og tilknyttede data i OData JSON-format, som kan fortolkes og behandles som behov.

Her er eksempelkode for godkendelse og ringer API ved hjælp af Powershell.  
Ved at tilføje din klient-ID centrale og lejer domæne.

    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 


## <a name="next-steps"></a>Næste trin

Tillykke, du har oprettet lige din første opkald til Microsoft Graph!  
Du kan nu forespørgsel identitet risikoen begivenheder og bruge dataene, men behov.

Kontrollere ud af, [dokumentation](https://graph.microsoft.io/docs) og meget mere på [Microsoft Graph websted](https://graph.microsoft.io/)for at få mere for at vide om Microsoft Graph og hvordan du opretter programmer, der bruger Graph API. Du skal også sørge for at oprette et bogmærke siden [Azure AD identitet beskyttelse API](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) , der viser alle tilgængelige i Graph identitet beskyttelse API'er. Når vi tilføjer nye måder at arbejde med beskyttelse af din identitet via API, skal se du dem på den pågældende side.


## <a name="additional-resources"></a>Yderligere ressourcer

- [Beskyttelse af Azure Active Directory din identitet](active-directory-identityprotection.md)

- [Typer af risikohændelser, der er registreret af Azure Active Directory identitetsbeskyttelse](active-directory-identityprotection-risk-events-types.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [Oversigt over Microsoft Graph](https://graph.microsoft.io/docs)

- [Azure AD-identitet beskyttelse tjenestens rod](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)
