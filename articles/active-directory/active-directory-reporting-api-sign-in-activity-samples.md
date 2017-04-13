<properties
    pageTitle="Azure Active Directory-logon aktivitet rapport API eksempler | Microsoft Azure"
    description="Hvordan du kan komme i gang med Azure Active Directory Reporting-API"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Azure Active Directory-logon aktivitet rapport API eksempler

Dette emne er en del af en samling af emner om Azure Active Directory rapportering API.  
Azure AD rapportering giver dig en API, der gør det muligt at få adgang til logon aktivitetsdata ved hjælp af kode eller relaterede værktøjer.  
Omfanget af dette emne er at give dig kodeeksempler til **logon aktivitet API**.

Se:

- [Overvågningslogge](active-directory-reporting-azure-portal.md#audit-logs) yderligere grundlæggende oplysninger
- [Introduktion til Azure Active Directory Reporting-API](active-directory-reporting-api-getting-started.md) til flere oplysninger om reporting API.

For spørgsmål, problemer eller feedback, skal du kontakte [AAD rapportering Hjælp](mailto:aadreportinghelp@microsoft.com).


## <a name="prerequisites"></a>Forudsætninger
Før du kan bruge eksemplerne i dette emne, skal du fuldføre [forudsætninger for at få adgang til den Azure AD rapportering API](active-directory-reporting-api-prerequisites.md).  


##<a name="powershell-script"></a>PowerShell-script

    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID       = "<clientId>"             # Should be a ~35 character string insert your info here
    $ClientSecret   = "<clientSecret>"         # Should be a ~44 character string insert your info here
    $loginURL       = "https://login.windows.net/"
    $tenantdomain   = "<tenantDomain>"
    $ daterange            # For example, contoso.onmicrosoft.com

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}

    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    $url = "https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&`$filter=signinDateTime ge $7daysago"
    
    $i=0
    
    Do{
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        Write-Output "Save the output to a file SigninActivities$i.json"
        Write-Output "---------------------------------------------"
        $myReport.Content | Out-File -FilePath SigninActivities$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)

    } else {
    
        Write-Host "ERROR: No Access Token"
    }




## <a name="executing-the-script"></a>Udførelse af script
Når du har redigeret scriptet, køre den og bekræfte, at de forventede data fra revision logges rapport returneres.

Scriptet returnerer output fra rapporten logon i JSON-format. Der oprettes også en `SigninActivities.json` fil med samme output. Du kan eksperimentere ved at ændre scriptet for at returnere data fra andre rapporter og kommentar ud outputformaterne, ikke behøver du.



## <a name="next-steps"></a>Næste trin

- Vil du tilpasse eksemplerne i dette emne? Se [Azure Active Directory-logon aktivitet API reference](active-directory-reporting-api-sign-in-activity-reference.md). 

- Hvis du vil se en komplet oversigt over brug af Azure Active Directory rapportering API, skal du se [Introduktion til Azure Active Directory rapportering API](active-directory-reporting-api-getting-started.md).

- Hvis du gerne vil vide mere om rapportering af Azure Active Directory, kan du se [Azure Active Directory rapportering Guide](active-directory-reporting-guide.md).  