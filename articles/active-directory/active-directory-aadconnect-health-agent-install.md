<properties
    pageTitle="Azure AD forbinde sundhed Agent-installation | Microsoft Azure"
    description="Dette er siden Azure AD forbinde tilstand, der beskriver agent-installation til AD FS og synkronisering."
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


# <a name="azure-ad-connect-health-agent-installation"></a>Azure AD Connect sundhed Agent-Installation

Dette dokument fører dig gennem installation og konfiguration af den Azure AD forbinde sundhed supportmedarbejdere. Du kan hente supportmedarbejdere fra [her](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent).

##  <a name="requirements"></a>Krav
Den følgende tabel er en liste over krav til brug af Azure AD forbinde tilstand.

| Krav | Beskrivelse|
| ----------- | ---------- |
|Azure AD-Premium| Azure AD forbinde tilstand er en funktion, Azure AD Premium og kræver Azure AD Premium. </br></br>Du kan finde flere oplysninger i [Introduktion til Azure AD Premium](active-directory-get-started-premium.md) </br>Hvis du vil starte en gratis 30-dages prøveversion, skal du se [starte en prøveversion.](https://azure.microsoft.com/trial/get-started-active-directory/)|
|Du skal være en global administrator for din Azure AD at komme i gang med Azure AD forbinde tilstand|Som standard kan kun globale administratorer installere og konfigurere sundhed supportmedarbejdere for at komme i gang, få adgang til portalen og udføre handlinger i Azure AD forbinde tilstand. Yderligere oplysninger finder du se [administrere din Azure AD-mappe](active-directory-administer.md). <br><br> Brug af rolle baseret adgangskontrol tillader du adgang til Azure AD forbinde tilstand til andre brugere i organisationen. Du kan finde flere oplysninger, se [rolle baseret adgangskontrol for Azure AD forbinde sundhed.](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) </br></br>**Vigtige:** Den konto, der bruges, når du installerer supportmedarbejdere skal være arbejds-eller skolekonto. Det kan være en Microsoft-konto. Yderligere oplysninger, du [tilmelde dig Azure som en organisation](sign-up-organization.md)
|Den Azure AD forbinde Health Agent er installeret på hver målrettede server| Azure AD forbinde sundhed kræver en agent-installation på målrettede servere, angive de data, der vises i portalen. </br></br>For eksempel for at hente data fra lokale infrastrukturen AD FS, skal agenten være installeret på AD FS, AD FS-Proxy og Web Application Proxy-Server. På samme måde for at hente data på dine lokale Active Directory-Domænetjenester infrastruktur, agenten skal være installeret på domænecontrollere. </br></br>**Vigtige:** Den konto, der bruges, når du installerer supportmedarbejdere skal være arbejds-eller skolekonto. Det kan være en Microsoft-konto. Yderligere oplysninger, du [tilmelde dig Azure som en organisation](sign-up-organization.md)|
|Udgående forbindelse til Azure service slutpunkterne|Under installationen og runtime kræver agenten forbindelse til Azure AD forbinde sundhed service slutpunkter. Hvis udgående forbindelse er blokeret, kan du sikre dig, at følgende slutpunkterne er føjet til listen over tilladte: </br></br><li>& #42. BLOB.Core.Windows.NET </li><li>& #42. Queue.Core.Windows.NET</li><li>adhsprodwus.servicebus.Windows.NET - Port: 5671 </li><li>https://Management.Azure.com </li><li>https://S1.adhybridhealth.Azure.com/</li><li>https://policykeyservice.DC.ad.MSFT.NET/</li><li>https://login.Windows.NET</li><li>https://login.microsoftonline.com</li><li>https://Secure.aadcdn.microsoftonline-p.com</li> |
|Firewallportene på den server, der kører på agent.| Agenten kræver følgende firewallporte til at være åben for at agent til at kommunikere med Azure AD sundhed service slutpunkterne.</br></br><li>TCP/UDP port 443</li><li>TCP/UDP-port 5671</li>
|Tillad følgende websteder, hvis IE udvidet sikkerhed er aktiveret| Hvis IE udvidet sikkerhed er aktiveret, skal følgende websteder være tilladt på den server, der skal have den agent er installeret.</br></br><li>https://login.microsoftonline.com</li><li>https://Secure.aadcdn.microsoftonline-p.com</li><li>https://login.Windows.NET</li><li>Sammenslutningsserver for din organisation, der er tillid til af Azure Active Directory. For eksempel: https://sts.contoso.com</li>




## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>Installation af Azure AD forbinde Health Agent for AD FS
Hvis du vil starte agent-installation, skal du dobbeltklikke på .exe-filen, du har hentet. Klik på Installer på det første skærmbillede.

![Bekræfte Azure AD Connect tilstand](./media/active-directory-aadconnect-health-requirements/install1.png)

Når installationen er fuldført, skal du klikke på Konfigurer nu.

![Bekræfte Azure AD Connect tilstand](./media/active-directory-aadconnect-health-requirements/install2.png)

En kommandoprompt er startet, efterfulgt af nogle PowerShell, som udfører Register-AzureADConnectHealthADFSAgent. Når du bliver bedt om at logge på Azure, gå frem og logge på.

![Bekræfte Azure AD Connect tilstand](./media/active-directory-aadconnect-health-requirements/install3.png)


Når du logger på, skal fortsat PowerShell. Når den er fuldført, kan du lukke PowerShell og konfigurationen er fuldført.

På dette tidspunkt tjenesterne skal sættes i gang automatisk tillade agent til at overvåge og indsamle data. Hvis du ikke har opfyldes alle de forudsætninger, der er beskrevet i forrige afsnit, vises advarsler i PowerShell-vinduet. Sørg for at fuldføre [krav](active-directory-aadconnect-health-agent-install.md#requirements) før du installerer agenten. Følgende skærmbillede er et eksempel på disse fejl.

![Bekræfte Azure AD Connect tilstand](./media/active-directory-aadconnect-health-requirements/install4.png)

For at bekræfte agenten er blevet installeret, skal du se efter følgende tjenester på serveren. Hvis du har afsluttet konfigurationen, skal de allerede køres. Ellers skal stoppes de indtil konfigurationen er fuldført.

- Azure AD Connect sundhed AD FS diagnosticering Service
- Azure AD Connect sundhed AD FS indsigt Service
- Azure AD Connect sundhed AD FS overvågning Service

![Bekræfte Azure AD Connect tilstand](./media/active-directory-aadconnect-health-requirements/install5.png)


### <a name="agent-installation-on-windows-server-2008-r2-servers"></a>Agent-installation på Windows Server 2008 R2-servere

Trin til Windows Server 2008 R2 servere:

1. Sørg for, at serveren kører på Service Pack 1 eller højere.
1. Deaktivere IE ESC til agent-installation:
1. Installere Windows PowerShell 4.0 på hver af serverne forud for installation af AD Health agent. Installere Windows PowerShell 4.0:
 - Installere [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) bruge følgende link til at hente offline installer.
 - Installere PowerShell ISE (fra Windows-funktioner)
 - Installere på [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
 - Installere Internet Explorer version 10 eller nyere på serveren. (Kræves af tjenesten tilstand til godkendelse, ved hjælp af dine Azure Administratoroplysninger).
1. Du kan finde flere oplysninger om installation af Windows PowerShell 4.0 på Windows Server 2008 R2 artiklen wiki [her](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### <a name="enable-auditing-for-ad-fs"></a>Aktivere overvågning for AD FS

> [AZURE.NOTE] Dette afsnit gælder kun for AD FS-sammenslutningsservere.

I rækkefølge for funktionen Brugsstatistik til at indsamle og analysere data, skal Azure AD forbinde sundhed agent oplysningerne i AD FS overvågningslogge. Disse logfiler er ikke aktiveret som standard. Brug følgende procedurer til at aktivere AD FS overvågning og til at finde overvågningslogge AD FS på AD FS-servere.

#### <a name="to-enable-auditing-for-ad-fs-20"></a>Aktivere overvågning for AD FS 2.0

1. Klik på **Start**, peg på **programmer**, peg på **Administration**, og klik derefter på **Lokal sikkerhedspolitik**.
2. Gå til mappen **Sikkerhed indstillinger\Sikkerhedsindstillinger\Lokale politikker\Tildeling af Rights Management** , og dobbeltklik derefter på Generer sikkerhedskontrol.
3. Kontrollér, AD FS 2.0-tjenestekonto vises under fanen **Lokale sikkerhedsindstillinger** . Hvis den ikke findes, skal du klikke på **Tilføj bruger eller gruppe** og føje det til listen, og klik derefter på **OK**.
4. Åbn en kommandoprompt med administratorrettigheder for at aktivere overvågning, og Kør følgende kommando:<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Luk lokal sikkerhedspolitik, og Åbn derefter snap-in administration. For at åbne snap-in administration, klik på **Start**, peg på **programmer**, peg på **Administration**og derefter klikke på AD FS 2.0 Administration.
6. Klik på Rediger sammenslutning Service egenskaber i ruden Handlinger.
7. Klik på fanen **hændelser** i dialogboksen **Egenskaber for sammenslutning tjenesten** .
8. Markér afkrydsningsfelterne **overvågning af vellykkede** og **mislykkede forsøg** .
9. Klik på **OK**.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Aktivere overvågning for AD FS på Windows Server 2012 R2

1. Åbne **Lokal sikkerhedspolitik** ved at åbne **Server Manager** på startskærmbilledet og Server Manager på proceslinjen på skrivebordet, og derefter klikke på **Værktøjer og lokale sikkerhedspolitik**.
2. Gå til mappen **Sikkerhed indstillinger\Sikkerhedsindstillinger\Lokale politikker\Tildeling af brugerrettigheder** , og dobbeltklik derefter på **Generer sikkerhedsovervågninger**.
3. Kontrollér, servicekonto AD FS vises under fanen **Lokale sikkerhedsindstillinger** . Hvis den ikke findes, skal du klikke på **Tilføj bruger eller gruppe** og føje det til listen, og klik derefter på **OK**.
4. Åbn en kommandoprompt med administratorrettigheder for at aktivere overvågning, og Kør følgende kommando:<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Luk **Lokal sikkerhedspolitik**, og Åbn derefter snap-in **AD FS administration** (i Server Manager skal du klikke på værktøjer og derefter vælge AD FS Management).
6. Klik på **Rediger sammenslutning Service egenskaber**i ruden Handlinger.
7. Klik på fanen **hændelser** i dialogboksen Egenskaber for sammenslutning tjenesten.
8. Markér afkrydsningsfelterne **overvågning af vellykkede og mislykkede forsøg** , og klik derefter på **OK**.






#### <a name="to-locate-the-ad-fs-audit-logs"></a>Find AD FS overvågningslog Logonfejl


1. Åbn **Logbog**.
2. Gå til Windows-logger, og vælg **sikkerhed**.
3. Klik på **Filter aktuelle logfiler**i højre side.
4. Vælg **AD FS overvågning**under begivenhed kilde.

![AD FS overvågningslogge](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING] Hvis der findes en gruppepolitik, der deaktivere AD FS overvågning, derefter på Azure AD forbinde sundhed Agent er til at indsamle oplysninger. Sørg for, at du ikke har en gruppepolitik, der kan deaktivere overvågning.

[//]: # (Start of Agent Proxy Configuration Section)

## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>Installation af Azure AD forbinde sundhed agent for synkronisering
Azure AD forbinde sundhed agent til synkronisering installeres automatisk i det seneste build af Azure AD-forbindelse. Hvis du vil bruge Azure AD-forbindelse til synkronisering, skal du hente den nyeste version af Azure AD-forbindelse og installere den. Du kan hente den nyeste version [her](http://www.microsoft.com/download/details.aspx?id=47594).

For at bekræfte agenten er blevet installeret, skal du se efter følgende tjenester på serveren. Hvis du har afsluttet konfigurationen, skal de allerede køres. Ellers skal stoppes de indtil konfigurationen er fuldført.

- Azure AD Connect sundhed Synkroniser indsigt tjeneste
- Azure AD Connect sundhed Synkroniser overvågning Service

![Bekræfte Azure AD Connect tilstand for synkronisering](./media/active-directory-aadconnect-health-sync/services.png)

> [AZURE.NOTE] Husk, at ved hjælp af Azure AD forbinde sundhed kræver Azure AD Premium. Hvis du ikke har Azure AD Premium, kan du ikke at fuldføre konfigurationen på portalen Azure. Se [siden med systemkrav](active-directory-aadconnect-health-agent-install.md#requirements)kan finde flere oplysninger.


## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>Manuel Azure AD forbinde tilstand for synkronisering registrering
Hvis Azure AD forbinde tilstanden for synkronisering agent registrering mislykkes, når du har installeret Azure AD-forbindelse, kan du bruge følgende PowerShell-kommando for manuelt at registrere agenten.

>[AZURE.IMPORTANT] Ved hjælp af denne PowerShell-kommando er kun påkrævet, hvis agent registreringen mislykkes, når du har installeret Azure AD-forbindelse.

Den følgende PowerShell kommandoen er påkrævet, kun når sundhed agent registrering mislykkes, når du har en vellykket installation og konfiguration af Azure AD-forbindelse. Azure AD forbinde sundhed tjenesterne starter efter agenten er blevet registreret.

Du kan manuelt registrere Azure AD forbinde sundhed agent for synkronisere ved hjælp af følgende PowerShell-kommando:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

Kommandoen tager følgende parametre:

- AttributeFiltering: $true (standard) - Hvis Azure AD-forbindelse ikke som kan synkroniseres standardattributten angive og er blevet tilpasset for at bruge et filtrerede attributsæt. $false på anden måde.
- StagingMode: $false (standard) - Hvis Azure AD-forbindelse serveren ikke er i arrangere tilstand, $true, hvis serveren er konfigureret til at være i arrangere tilstand.

Når du bliver bedt om godkendelse, skal du bruge den samme global administrator (såsom admin@domain.onmicrosoft.com) , der blev brugt til at konfigurere Azure AD-forbindelse.

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>Installation af Azure AD forbinde sundhed Agent til AD DS
Hvis du vil starte agent-installation, skal du dobbeltklikke på .exe-filen, du har hentet. Klik på Installer på det første skærmbillede.

![Bekræfte Azure AD Connect tilstand](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install1.png)

Når installationen er fuldført, skal du klikke på Konfigurer nu.

![Bekræfte Azure AD Connect tilstand](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install2.png)

En kommandoprompt er startet, efterfulgt af nogle PowerShell, som udfører Register-AzureADConnectHealthADDSAgent. Når du bliver bedt om at logge på Azure, gå frem og logge på.

![Bekræfte Azure AD Connect tilstand](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install3.png)

Når du logger på, skal fortsat PowerShell. Når den er fuldført, kan du lukke PowerShell og konfigurationen er fuldført.

På dette tidspunkt tjenesterne skal sættes i gang automatisk tillade agent til at overvåge og indsamle data. Hvis du ikke har opfyldes alle de forudsætninger, der er beskrevet i forrige afsnit, vises advarsler i PowerShell-vinduet. Sørg for at fuldføre [krav](active-directory-aadconnect-health-agent-install.md#requirements) før du installerer agenten. Følgende skærmbillede er et eksempel på disse fejl.

![Bekræfte Azure AD Connect tilstand til AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install4.png)

For at bekræfte agenten er blevet installeret, skal du se efter følgende tjenester på domænecontrolleren.

- Azure AD Connect sundhed AD DS indsigt Service
- Azure AD Connect sundhed AD DS overvågning Service

Hvis du har afsluttet konfigurationen, skal allerede køre disse tjenester. Ellers skal stoppes de indtil konfigurationen er fuldført.

![Bekræfte Azure AD Connect tilstand](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install5.png)

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds-on-server-core"></a>Installation af Azure AD forbinde sundhed Agent til AD DS på Server Core.
Når du har installeret .exe-filen, kan du udføre registreringsprocessen ved hjælp af følgende PowerShell-kommando:

`Register-AzureADConnectHealthADDSAgent -Credential $cred`

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Konfigurere Azure AD forbinde sundhed supportmedarbejdere for at bruge HTTP-Proxy
Du kan konfigurere Azure AD forbinde sundhed supportmedarbejdere til at arbejde med en HTTP-Proxy.

>[AZURE.NOTE]
- Brug af "Netsh WinHttp Angiv ProxyServerAddress" understøttes ikke, som agenten bruger System.Net til at oprette webanmodninger i stedet for Microsoft Windows HTTP-tjenester.
- Den konfigurerede Http-Proxy-adresse bruges til at pass-through-krypterede Https-meddelelser.
- Godkendte proxyer (ved hjælp af HTTPBasic) understøttes ikke.

### <a name="change-health-agent-proxy-configuration"></a>Konfiguration af Skift sundhed Agent Proxy
Du har følgende indstillinger for at konfigurere Azure AD forbinde Health Agent for at bruge en HTTP-Proxy.

>[AZURE.NOTE]Alle Azure AD forbinde Health Agent tjenester skal genstartes for at proxyindstillinger skal opdateres. Kør følgende kommando:<br>
Genstart tjenesten AdHealth *

#### <a name="import-existing-proxy-settings"></a>Importere eksisterende proxy indstillinger

##### <a name="import-from-internet-explorer"></a>Importere fra Internet Explorer
Indstillinger for Internet Explorer HTTP-proxy kan importeres, skal bruges af Azure AD forbinde sundhed aktører. På hver af de servere, der kører Health agent skal du udføre følgende PowerShell-kommando:

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### <a name="import-from-winhttp"></a>Importere fra WinHTTP
WinHTTP proxyindstillinger kan importeres, skal bruges af Azure AD forbinde sundhed aktører. På hver af de servere, der kører Health agent skal du udføre følgende PowerShell-kommando:

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### <a name="specify-proxy-addresses-manually"></a>Angive proxyadresser manuelt
Du kan angive en proxy-server manuelt på hver af de servere, der kører Agent tilstand ved at udføre følgende PowerShell-kommando:

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Eksempel: *sæt AzureAdConnectHealthProxySettings - HttpsProxyAddress myproxyserver: 443*

- "-adresse" kan være en DNS-løsning servernavn eller en IPv4-adresse
- "port" kan udelades. Hvis udelades derefter 443 er valgt som standardport.

#### <a name="clear-existing-proxy-configuration"></a>Ryd eksisterende proxykonfiguration
Du kan fjerne den eksisterende proxykonfiguration ved at køre følgende kommando:

    Set-AzureAdConnectHealthProxySettings -NoProxy


### <a name="read-current-proxy-settings"></a>Læs aktuelle proxyindstillinger
Du kan læse er konfigureret i øjeblikket proxyindstillingerne ved at køre følgende kommando:

    Get-AzureAdConnectHealthProxySettings


## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Test forbindelse til Azure AD forbinde sundhed Service
Det er muligt, kan opstår problemer, der kan medføre Azure AD forbinde sundhed agent at miste forbindelse med tjenesten Azure AD forbinde tilstand. Dette omfatter netværksproblemer, tilladelse problemer eller forskellige årsager.

Hvis agenten er i stand til at sende data til tjenesten Azure AD forbinde tilstand i mere end to timer, det er angivet med følgende besked i portalen: "sundhed servicedata er ikke opdateret." Du kan bekræfte, hvis den pågældende Azure AD Connect Health agent er i stand til at overføre data til tjenesten Azure AD forbinde tilstand ved at køre følgende PowerShell-kommando:

    Test-AzureADConnectHealthConnectivity -Role ADFS

Parameteren rolle tager aktuelt følgende værdier:

- ADFS
- Synkroniser
- LÆGGER

Du kan bruge flaget - ShowResults i kommandoen til at få vist detaljerede logfiler. Brug følgende eksempel:

    Test-AzureADConnectHealthConnectivity -Role Sync -ShowResult

>[AZURE.NOTE]Hvis du vil bruge forbindelsesværktøjet, skal du først udføre agent registrering. Hvis du ikke kan afslutte agent registrering skal du sørge for, at du har opfyldt alle [krav](active-directory-aadconnect-health-agent-install.md#requirements) til Azure AD forbinde tilstand. Denne forbindelsestesten udføres som standard under agent registrering.



## <a name="related-links"></a>Relaterede links

* [Azure AD Connect tilstand](active-directory-aadconnect-health.md)
* [Azure AD Connect sundhed handlinger](active-directory-aadconnect-health-operations.md)
* [Brug af Azure AD forbinde tilstand med AD FS](active-directory-aadconnect-health-adfs.md)
* [Ved hjælp af Azure AD forbinde tilstand til synkronisering](active-directory-aadconnect-health-sync.md)
* [Ved hjælp af Azure AD forbinde tilstand med AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect sundhed ofte stillede spørgsmål](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect sundhed versionshistorik](active-directory-aadconnect-health-version-history.md)
