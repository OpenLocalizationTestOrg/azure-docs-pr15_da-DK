<properties
    pageTitle="Azure API Management ofte stillede spørgsmål om | Microsoft Azure"
    description="Se svar på ofte stillede spørgsmål, mønstre og bedste fremgangsmåder i Azure API Management."
    services="api-management"
    documentationCenter=""
    authors="miaojiang"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="mijiang"/>

# <a name="azure-api-management-faqs"></a>Ofte stillede spørgsmål om administration af Azure API

Få svar på ofte stillede spørgsmål, mønstre og bedste fremgangsmåder til Azure API Management.

## <a name="frequently-asked-questions"></a>Ofte stillede spørgsmål

-   [Hvordan kan jeg stille Microsoft Azure API Management teamet et spørgsmål?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)
-   [Hvad betyder det, når en funktion ikke er i preview?](#what-does-it-mean-when-a-feature-is-in-preview)
-   [Hvordan kan jeg sikre forbindelsen mellem API Management gateway og mine back end-tjenester?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
-   [Hvordan kan jeg kopiere mine API Management service forekomst til en ny forekomst?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
-   [Kan jeg administrere mine API Management forekomst fra et program?](#can-i-manage-my-api-management-instance-programmatically)
-   [Hvordan føjer jeg en bruger til gruppen Administratorer?](#how-do-i-add-a-user-to-the-administrators-group)
-   [Hvorfor er politikken, som jeg vil tilføje ikke tilgængelig i politikeditoren?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
-   [Hvordan bruger jeg API versionsstyring API administration?](#how-do-i-use-api-versioning-in-api-management)
-   [Hvordan konfigurerer jeg flere miljøer i en enkelt API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
-   [Kan jeg bruge SOAP med API Management?](#can-i-use-soap-with-api-management)
-   [Er konstanten API Management gateway IP-adresse? Kan jeg bruge det i firewallregler?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
-   [Kan jeg konfigurere en OAuth 2.0 godkendelse-server med AD FS-sikkerhed?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
-   [Hvilke routing metode anvender API Management i installationer til flere geografiske placeringer?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
-   [Kan jeg bruge en skabelon til Azure ressourcestyring til at oprette en forekomst af API Management service?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
-   [Kan jeg bruge et selvsigneret SSL-certifikat til back-end?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
-   [Hvorfor får jeg en godkendelsesfejl, når jeg forsøger at klone et CIFFER lager?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
-   [Fungerer API Management med Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
-   [Kan jeg flytte et API Management-tjenesten fra et abonnement til en anden?](#can-i-move-an-api-management-service-from-one-subscription-to-another)


### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Hvordan kan jeg stille Microsoft Azure API Management teamet et spørgsmål?

Du kan kontakte os ved hjælp af en af disse indstillinger:

-   Stil dit spørgsmål i vores [API Management MSDN-forum](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
-   Sende en mail til <apimgmt@microsoft.com>.
-   Send os en anmodning om funktion i [Azure feedback-forummet](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Hvad betyder det, når en funktion ikke er i preview?

Når der er en funktion i Vis udskrift, betyder det, vi aktivt søger feedback på, hvordan funktionen arbejde for dig. En funktion i Vis udskrift er funktionelt fuldført, men det er muligt, at vi foretager en ændring i svar på kundefeedback seneste. Vi anbefaler, at du ikke er afhængige af en funktion, der er i udskrift på dit produktionsmiljø. Hvis du har din feedback om preview-funktioner, skal du fortælle os, via en af indstillingerne for kontaktpersoner i [hvordan kan jeg stille Microsoft Azure API Management teamet et spørgsmål?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Hvordan kan jeg sikre forbindelsen mellem API Management gateway og mine back end-tjenester?

Har du forskellige valgmuligheder til at sikre forbindelsen mellem API Management gateway og back end-tjenester. Du kan:

-   Brug HTTP basisgodkendelse. Se [konfigurere API indstillinger](api-management-howto-create-apis.md#configure-api-settings)kan finde flere oplysninger.
- Brug SSL fælles godkendelse, som beskrevet i [Sådan sikre back end - tjenester ved hjælp af klientcertifikat i Azure API Management](api-management-howto-mutual-certificates.md).
- Brug IP-whitelisting på din back end-tjeneste. Hvis du har en Standard- eller Premium niveau API Management forekomst, konstant IP-adressen for gateway. Du kan angive din whitelist tillade denne IP-adresse. Du kan få IP-adressen på din API Management forekomst på dashboardet i portalen Azure.
- Oprette forbindelse mellem din API Management forekomst til et virtuelt Azure-netværk. Se, [hvordan du konfigurerer VPN-forbindelser i Azure API Management](api-management-howto-setup-vpn.md)kan finde flere oplysninger.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Hvordan kan jeg kopiere mine API Management service forekomst til en ny forekomst?

Du har flere muligheder, hvis du vil kopiere en API Management forekomst til en ny forekomst. Du kan:

-   Brug sikkerhedskopien, og gendanne funktionen API administration. Se, [hvordan du implementere nedbrud ved hjælp af tjenesten sikkerhedskopiering og gendannelse i Azure API Management](api-management-howto-disaster-recovery-backup-restore.md)kan finde flere oplysninger.
-   Oprette din egen sikkerhedskopiering og gendannelse funktion ved hjælp af [API Management REST-API](https://msdn.microsoft.com/library/azure/dn776326.aspx). Brug REST-API til at gemme og gendanne objekterne fra den forekomst af tjenesten, du vil.
-   Hent konfigurationen af tjenesten ved hjælp af ciffer, og Overfør det derefter til en ny forekomst. Se, [hvordan du gemmer og konfigurere konfigurationen API Management-tjenesten ved hjælp af ciffer](api-management-configuration-repository-git.md)kan finde flere oplysninger.

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Kan jeg administrere mine API Management forekomst fra et program?

Ja, kan du administrere API Administration fra et program ved hjælp af:

-   [API Management REST-API](https://msdn.microsoft.com/library/azure/dn776326.aspx).
-   [Microsoft Azure ApiManagement Service Management bibliotek SDK](http://aka.ms/apimsdk).
-   [Tjenesten installation](https://msdn.microsoft.com/library/mt619282.aspx) og [Administration af tjenesten](https://msdn.microsoft.com/library/mt613507.aspx) PowerShell-cmdlet'er.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Hvordan føjer jeg en bruger til gruppen Administratorer?

Her er, hvordan du kan føje en bruger til gruppen Administratorer:

1. Log på [Azure-portalen](https://portal.azure.com).
2. Gå til ressourcegruppen, der indeholder den API Management-forekomst, du vil opdatere.
3. Tildele rollen **Api Management bidragyder** i administration af API til brugeren.

Den tilføjede Bidragyder kan nu bruge Azure PowerShell- [cmdletter](https://msdn.microsoft.com/library/mt613507.aspx). Her er, hvordan du logger på som administrator:

1. Brug den `Login-AzureRmAccount` til at logge på.
2. Angiv konteksten til det abonnement, der har tjenesten ved hjælp af `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`.
3. Få en enkelt sign-on – URL ved hjælp af `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Brug URL-adressen til at få adgang til administrationsportalen.


### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Hvorfor er politikken, som jeg vil tilføje ikke tilgængelig i politikeditoren?

Hvis politikken, som du vil tilføje vises nedtonet eller nedtonet i politikeditoren, skal du sørge for, at du er i den korrekte omfang til politikken. Hver Politikerklæringen henvender sig til du kan bruge i bestemte områder og politik sektioner. Hvis du vil gennemse politik sektioner og områder for en politik, i afsnittet politikkens brugen i [politikker for administration af API](https://msdn.microsoft.com/library/azure/dn894080.aspx).


### <a name="how-do-i-use-api-versioning-in-api-management"></a>Hvordan bruger jeg API versionsstyring API administration?

Har du nogle valgmuligheder til brug af API versionsstyring API administration:

-   Du kan konfigurere API'er for at repræsentere forskellige versioner i administration af API. Du kan for eksempel har to forskellige API'er, MyAPIv1 og MyAPIv2. En udvikler kan vælge den version, udvikleren vil bruge.
-   Du kan også konfigurere din API med en URL-adressen, der ikke indeholder en version målgruppe, for eksempel https://my.api. Konfigurer derefter en version målgruppe på hver operation [Omskrive URL-adressen](https://msdn.microsoft.com/library/azure/dn894083.aspx#RewriteURL) skabelon. Du kan for eksempel har en handling med en [URL-adresse til skabelon](api-management-howto-add-operations.md#url-template) , der kaldes /resource og en [URL-adresse til omskrivning](api-management-howto-add-operations.md#rewrite-url-template) skabelon, der kaldes /v1/Resource. Du kan ændre værdien version segmentet separat for hver handling.
-   Hvis du gerne vil beholde en "standard" version målgruppe i URL-adressen til det API-tjenesten, markerede handlinger, Angiv en politik, der bruger politikken [angive back end-tjeneste](https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBackendService) til at ændre stien til back end-anmodning.

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Hvordan konfigurerer jeg flere miljøer i en enkelt API?

Hvis du vil konfigurere flere miljøerne, for eksempel et testmiljø og et produktionsmiljø i en enkelt API, har du to muligheder. Du kan:

-   Host forskellige API'er i samme lejer.
-   Være vært for de samme API'er på forskellige lejere.

### <a name="can-i-use-soap-with-api-management"></a>Kan jeg bruge SOAP med API Management?

[SOAP pass-through](http://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) support er nu tilgængelig. Administratorer kan importere WSDL af deres SOAP-tjeneste, og Azure API Management opretter en SOAP front-end. Portalen dokumentation for udviklere, test konsollen, politikker og analytics er alle tilgængelige for SOAP-tjenester.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>Er konstanten API Management gateway IP-adresse? Kan jeg bruge det i firewallregler?

I Standard- og Premium lag er den offentlige IP-adresse (VIP) API Management lejeren statisk for levetiden for lejeren, med nogle undtagelser. IP-adresse ændringer under disse omstændigheder:

-   Tjenesten er slettet og derefter genoprettes.
-   Tjenesten abonnementet er suspenderet (for eksempel for nonpayment), og klik derefter på ny.
-   Du tilføjer eller fjerner Azure virtuelt netværk (du kan bruge virtuelt netværk kun på Premium niveauet).

Den internationale adresse ændres til flere områder installationer, hvis området er vacated, og klik derefter på ny (du kan bruge flere områder installation kun på Premium niveauet).

Premium niveau lejere, der er konfigureret til flere områder installation er tildelt en offentlig IP-adresse i område.

Du kan åbne din IP-adresse (eller adresser i et område med flere installation) på siden lejer på portalen Azure.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Kan jeg konfigurere en OAuth 2.0 godkendelse-server med AD FS-sikkerhed?

For at lære at konfigurere en OAuth 2.0 godkendelse-server med Active Directory Federation Services (AD FS) sikkerhed, skal du se [Ved hjælp af ADFS API administration](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Hvilke routing metode anvender API Management i installationer til flere geografiske placeringer?

Administration af API bruger [ydeevne trafik routing metode](../traffic-manager/traffic-manager-routing-methods.md#performance-traffic-routing-method) i installationer til flere geografiske placeringer. Indgående trafik sendes til den nærmeste API gateway. Hvis ét område går offline, sendes automatisk indgående trafik til den næste nærmeste gateway. Få mere at vide om routing metoder i [trafik Manager rute metoder](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Kan jeg bruge en skabelon til Azure ressourcestyring til at oprette en forekomst af API Management service?

Ja. Se skabelonen [Azure API Management-tjenesten](http://aka.ms/apimtemplate) til hurtig start.

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Kan jeg bruge et selvsigneret SSL-certifikat til back-end?

Ja. Her er, hvordan du bruger et selvsigneret certifikat Secure Sockets Layer (SSL) til back-end:

1. Oprette en [back-end](https://msdn.microsoft.com/library/azure/dn935030.aspx) -enhed ved hjælp af API administration.
2. Indstil egenskaben **skipCertificateChainValidation** til **Sand**.
3. Hvis du ikke længere vil tillade selvsignerede certifikater, Slet back end-enheden, eller angive egenskaben **skipCertificateChainValidation** til **Falsk**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Hvorfor får jeg en godkendelsesfejl, når jeg forsøger at klone et ciffer lager?

Hvis du bruger ciffer Legitimationsstyring, eller hvis du forsøger at klone et ciffer lager ved hjælp af Visual Studio, kan du støde på et kendt problem med dialogboksen Windows-legitimationsoplysninger. Dialogboksen begrænser adgangskodelængde til 127 tegn, og det afkorter Microsoft oprettede adgangskoden. Vi arbejder på forkorte adgangskoden. Nu, skal du bruge ciffer fest for at klone ciffer lageret.

### <a name="does-api-management-work-with-azure-expressroute"></a>Fungerer API Management med Azure ExpressRoute?

Ja. API administration fungerer med Azure ExpressRoute.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Kan jeg flytte et API Management-tjenesten fra et abonnement til en anden?

Ja. Til at få mere at vide hvordan, se [flytte ressourcer til en ny ressourcegruppe eller et andet abonnement](../resource-group-move-resources.md).
