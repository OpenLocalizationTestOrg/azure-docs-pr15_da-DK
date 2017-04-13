<properties
    pageTitle="Sådan installeres automatisk Azure AD-program-Proxy forbindelsen | Microsoft Azure"
    description="Beskriver, hvordan du udfører en uovervåget installation af Azure AD-program-Proxy forbindelse til at levere sikker fjernadgang til dine lokale apps."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="how-to-silently-install-the-azure-ad-application-proxy-connector"></a>Sådan installeres automatisk Azure AD-program-Proxy forbindelsen

Du vil kunne sende en installationsscript til flere Windows-servere eller til Windows-servere, der ikke har aktiveret brugergrænsefladen. Dette emne forklares det, hvordan du opretter et Windows PowerShell-script, som gør det muligt for automatisk installation til at installere og registrere den Azure AD-program-Proxy-forbindelse.

## <a name="enabling-access"></a>Aktivere adgang
Proxyen fungerer ved at installere en tynd Windows Server-tjeneste, der hedder forbindelsen i dit netværk. For programmet Proxy forbindelsen til at arbejde det skal være registreret hos din Azure AD-mappe ved hjælp af en global administrator og adgangskode. Dette er normalt angivet under installation af forbindelseskomponenter i et pop op-dialogboksen. Alternativt kan du bruge Windows PowerShell til at oprette en legitimationsoplysninger objekt for at angive dine registreringsoplysninger, eller du kan oprette din egen token og bruge det til at angive dine registreringsoplysninger.

## <a name="step-1--install-the-connector-without-registration"></a>Trin 1: Installere forbindelsen uden registrering


Installere forbindelse MSIs uden at registrere forbindelsen på følgende måde:


1. Åbn en kommandoprompt.
2. Køre følgende kommando i som/q betyder, at automatisk installation - installationen bliver ikke bedt om at accepter licensaftalen slutbrugerens.

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="step-2-register-the-connector-with-azure-active-directory"></a>Trin 2: Registrere forbindelsen med Azure Active Directory
Dette kan gøres ved hjælp af en af følgende metoder:


- Registrere forbindelsen ved hjælp af et Windows PowerShell legitimationsoplysninger objekt
- Registrere forbindelsen ved hjælp af et token, der er oprettet offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registrere forbindelsen ved hjælp af et Windows PowerShell legitimationsoplysninger objekt


1. Opret objektet Windows PowerShell-legitimationsoplysninger ved at køre følgende, hvor "<username>"og"<password>" skal erstattes med brugernavnet og adgangskoden til dit bibliotek:

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword

2. Gå til **C:\Program Files\Microsoft AAD Proxy Appforbindelse** og køre scriptet ved hjælp af objektet PowerShell legitimationsoplysninger, du har oprettet, hvor $cred er navnet på PowerShell legitimationsoplysninger objekt, du har oprettet:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred


### <a name="register-the-connector-using-a-token-created-offline"></a>Registrere forbindelsen ved hjælp af et token, der er oprettet offline

1. Oprette en offline token ved hjælp af klassen AuthenticationContext ved hjælp af værdierne i kodestykket:


        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }





2. Når du har oprette tokenet en SecureString ved hjælp af tokenet: <br>
`$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
3. Kør følgende Windows PowerShell-kommandoen, hvor SecureToken er navnet på det token, du oprettede ovenfor og tenantID er din lejer GUID: <br>
`RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`



## <a name="see-also"></a>Se også

- [Aktivere proxyen for Azure Active Directory](active-directory-application-proxy-enable.md)
- [Publicere programmer, der bruger dit eget domænenavn](active-directory-application-proxy-custom-domains.md)
- [Aktivere enkelt Log på](active-directory-application-proxy-sso-using-kcd.md)
- [Foretage fejlfinding af problemer, du har kørende med proxyen](active-directory-application-proxy-troubleshoot.md)

Se [proxyen blog](http://blogs.technet.com/b/applicationproxyblog/) for de seneste nyheder og opdateringer
