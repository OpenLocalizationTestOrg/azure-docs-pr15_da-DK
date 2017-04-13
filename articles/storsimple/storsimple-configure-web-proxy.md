<properties 
   pageTitle="Konfigurere webtjenesteproxy til en StorSimple enhed | Microsoft Azure"
   description="Lær at bruge Windows PowerShell til StorSimple til at konfigurere web proxyindstillingerne for enheden StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-web-proxy-for-your-storsimple-device"></a>Konfigurere webtjenesteproxy for enheden StorSimple

## <a name="overview"></a>Oversigt

Dette selvstudium beskriver, hvordan du bruger Windows PowerShell til StorSimple til at konfigurere og få vist web proxyindstillingerne for enheden StorSimple. Web proxyindstillinger anvendes af StorSimple enheden, når kommunikere med skyen. En web proxy-server, der bruges til at tilføje endnu et lag med sikkerhed, filter indhold cache til øget krav til båndbredde eller lige hjælp med analytics.

Webtjenesteproxy er en valgfri konfiguration for enheden StorSimple. Du kan konfigurere webtjenesteproxy kun via Windows PowerShell til StorSimple. Konfigurationen er en proces på følgende måde:

1. Du først konfigurere web proxyindstillinger via konfigurationsguiden eller Windows PowerShell-cmdletter StorSimple.

2. Du aktiverer derefter konfigureret web proxyindstillingerne via Windows PowerShell for StorSimple cmdletter.

Når web proxykonfigurationen er fuldført, kan du se proxyindstillinger konfigurerede web i både tjenesten Microsoft Azure StorSimple Manager og Windows PowerShell til StorSimple. 

Når du har læst dette selvstudium, vil du kunne:

- Konfigurere webtjenesteproxy ved hjælp af guiden til konfiguration og -cmdletter
- Aktivere webtjenesteproxy ved hjælp af cmdletter
- Få vist indstillinger for websted proxy i portalen Azure klassisk
- Foretage fejlfinding under konfiguration af web proxy


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Konfigurere webtjenesteproxy via Windows PowerShell til StorSimple

Du kan bruge et af følgende til at konfigurere web proxy-indstillinger:

- Konfigurationsguiden til fører dig gennem trinnene til konfiguration.

- -Cmdletter i Windows PowerShell til StorSimple.

Hver af disse metoder er beskrevet i følgende afsnit.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Konfigurere webtjenesteproxy via konfigurationsguiden

Du kan bruge konfigurationsguiden til fører dig gennem trinnene til konfiguration af web proxy. Udfør følgende trin for at konfigurere webtjenesteproxy på din enhed.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Sådan konfigureres webtjenesteproxy via konfigurationsguiden

1. Vælg indstilling 1, **Log på med fuld adgang** i menuen Seriel konsol og angive **enhed administratoradgangskode**. Skriv følgende kommando for at starte en session med guiden Konfiguration af:

    `Invoke-HcsSetupWizard`

2. Hvis dette er første gang, du har brugt konfigurationsguiden til registrering af enhed, skal du konfigurere alle de nødvendige netværksindstillinger, indtil du når til web proxykonfigurationen. Hvis enheden er allerede registreret, kan du acceptere alle konfigurerede netværksindstillinger, indtil du når til web proxykonfigurationen. I guiden, når du bliver bedt om at konfigurere web proxyindstillinger, du Skriv **Ja**.

3. Angiv IP-adressen eller det fulde domænenavn (fulde Domænenavn) af din webproxyserver og det TCP-portnummer, som du vil have din enhed til at bruge ved kommunikation med skyen **Proxy URL-adresse**til. Brug følgende format:

    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`

    TCP-portnummer 8080 er angivet som standard.

4. Vælg godkendelsestype som **NTLM**, **grundlæggende**eller **ingen**. Grundlæggende er mindst sikre godkendelsen for konfigurationen af proxy-server. NT LAN Manager (NTLM) er en meget sikker og kompleks godkendelsesprotokol, der bruger et 3-vejs meddelelsessystem (nogle gange fire Hvis yderligere integritet er påkrævet) til at godkende en bruger. Standardgodkendelse er NTLM. Få mere at vide under [grundlæggende](http://hc.apache.org/httpclient-3.x/authentication.html) og [NTLM-godkendelse](http://hc.apache.org/httpclient-3.x/authentication.html). 

    > [AZURE.IMPORTANT] **De enhed overvågning diagrammer fungerer ikke, når grundlæggende i tjenesten StorSimple Manager eller NTLM-godkendelse er aktiveret i serverkonfiguration proxy til enheden. Overvågning diagrammer til at fungere sammen, skal du sikre dig, at godkendelse er indstillet til ingen.**

5. Hvis du bruger godkendelse, skal du angive en **Web Proxy brugernavn** og en **Adgangskode til Web Proxy**. Du skal også Bekræft adgangskoden.

    ![Konfigurere Webtjenesteproxy på StorSimple Device1](./media/storsimple-configure-web-proxy/IC751830.png)

Hvis du registrerer enheden for første gang, kan du fortsætte med registreringen. Hvis enheden allerede er registreret, afsluttes guiden. Indstillingerne for konfigureret, gemmes.

Webtjenesteproxy vil nu blive aktiveret også. Du kan springe [aktivere webtjenesteproxy](#enable-web-proxy) og gå direkte til [Vis web proxyindstillinger i portalen Azure klassisk](#view-web-proxy-settings-in-the-azure-classic-portal).


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Konfigurere webproxy via Windows PowerShell-cmdletter StorSimple

En anden måde at konfigurere web proxyindstillinger er via Windows PowerShell til StorSimple cmdletter. Udfør følgende trin for at konfigurere webtjenesteproxy.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Sådan konfigureres webtjenesteproxy via cmdletter

1. Vælg indstilling 1, **Log på med fuld adgang**i menuen Seriel konsol. Når du bliver bedt om det, du give **enhed administratoradgangskode**. Standardadgangskoden, er `Password1`.

2. Ved kommandoprompten skal du indtaste:

    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`

    Angiv og Bekræft adgangskoden, når du bliver bedt om det, som vist nedenfor.

    ![Konfigurere Webtjenesteproxy på StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

Webproxy er nu er konfigureret og skal være aktiveret.

## <a name="enable-web-proxy"></a>Aktivere webtjenesteproxy

Webtjenesteproxy er deaktiveret som standard. Når du konfigurerer proxyindstillinger web på enheden StorSimple, skal du bruge Windows PowerShell til StorSimple til at aktivere web proxyindstillinger.

> [AZURE.NOTE] **Dette trin kan ikke påkrævet, hvis du har brugt konfigurationsguiden til at konfigurere webtjenesteproxy. Webtjenesteproxy er automatisk aktiveret som standard efter en session med guiden Konfiguration.**

Du kan udføre følgende trin i Windows PowerShell til StorSimple til at aktivere webtjenesteproxy på din enhed:

#### <a name="to-enable-web-proxy"></a>Aktivere webtjenesteproxy

1. Vælg indstilling 1, **Log på med fuld adgang**i menuen Seriel konsol. Når du bliver bedt om det, du give **enhed administratoradgangskode**. Standardadgangskoden, er `Password1`.

2. Ved kommandoprompten skal du indtaste:

    `Enable-HcsWebProxy`

    Du har nu aktiveret web proxykonfiguration på enheden StorSimple.

    ![Konfigurere Webtjenesteproxy på StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-classic-portal"></a>Få vist indstillinger for websted proxy i portalen Azure klassisk

Proxyindstillinger web er konfigureret via brugergrænsefladen i Windows PowerShell og kan ikke ændres fra i klassisk portal. Du kan dog få vist disse konfigureret indstillinger på portalen klassisk. Udfør følgende trin for at få vist webtjenesteproxy.

#### <a name="to-view-web-proxy-settings"></a>Sådan får du vist web proxyindstillinger
1. Gå til **tjenesten StorSimple Manager > enheder**. Vælg, og klik på en enhed, og derefter gå til **Konfigurer**.
1. Rul ned på siden **Konfigurer** til **Web proxyindstillinger** sektion. Du kan få vist proxyindstillinger konfigurerede web på enheden StorSimple som vist nedenfor.

    ![Vis webproxy Management-portalen](./media/storsimple-configure-web-proxy/ViewWebProxyPortal_M.png)
 
## <a name="errors-during-web-proxy-configuration"></a>Fejl under konfiguration af web proxy

Hvis proxyindstillinger web er blevet konfigureret forkert, vises fejlmeddelelser for brugeren i Windows PowerShell til StorSimple. I følgende tabel beskrives nogle af disse fejlmeddelelser, deres årsag og anbefalede handlinger.

|Serienr.|HRESULT-fejlkode|Mulige egentlige årsag|Anbefalede handling|
|:---|:---|:---|:---|
|1.|0x80070001|Kommandoen Kør fra den passive controller, og det er ikke kommunikere med den aktive controller.|Kør kommandoen på den aktive controller. Hvis du vil køre kommandoen fra den passive controller, skal du løse forbindelsen fra passive til aktive controller. Du skal deltage Microsoft Support, hvis dette er brudt.|
|2.|0x800710dd - handlings-id'et er ikke gyldig|Indstillinger for proxyserver understøttes ikke på StorSimple virtuelle enhed.|Indstillinger for proxyserver understøttes ikke på StorSimple virtuelle enhed. Disse kan kun konfigureres på en StorSimple fysisk enhed.|
|3.|0x80070057 - ugyldig parameter|En af de parametre, der er knyttet til proxyindstillingerne er ikke gyldige.|URI er ikke angivet i korrekte format. Brug følgende format:`http://<IP address or FQDN of the web proxy server>:<TCP port number>`|
|4.|0x800706ba - RPC-serveren ikke tilgængelig|Den egentlige årsag er et af følgende:</br></br>Klynge er ikke op.</br></br>DataPath tjenesten kører ikke.</br></br>Kommandoen Kør fra passive controller, og det er ikke kommunikere med den aktive controller.|Kan du deltage Microsoft Support for at sikre, at klyngen er oprettet og datapath tjenesten kører.</br></br>Kør kommandoen fra den aktive controller. Hvis du vil køre kommandoen fra den passive controller, skal du sikre, at den passive controller kan kommunikere med den aktive controller. Du skal deltage Microsoft Support, hvis dette er brudt.|
|5.|0X800706be - RPC-kaldet mislykkedes|Klynge er nede.|Kan du deltage Microsoft Support for at sikre, at klyngen er op.|
|6.|0x8007138f - Klyngeressourcen blev ikke fundet|Platform tjenesten klyngeressource blev ikke fundet. Dette kan ske, når installationen ikke er korrekt.|Du muligvis skal udføre en fabrik nulstille på din enhed. Du muligvis til at oprette en platform ressource. Kontakt Microsoft Support til næste trin.|
|7.|0x8007138c - klyngeressource ikke online|Platform eller datapath klyngeressourcer er ikke online.|Kontakt Microsoft Support for at sikre, at datapath og platform service ressourcen er online.|

> [AZURE.NOTE] 
> 
> -  Ovennævnte liste over fejlmeddelelser er ikke omfattende. 
> - Fejl, der er relateret til web proxyindstillinger vises ikke i Azure klassisk portalen i din StorSimple Manager-tjeneste. Hvis der er et problem med webtjenesteproxy efter konfigurationen er fuldført, enhedens status ændres til **Offline** i portalen klassisk. |

## <a name="next-steps"></a>Næste trin

- Hvis du oplever problemer under installation af din enhed eller konfiguration af web proxyindstillinger, se [fejlfinding af installationen af StorSimple enhed](storsimple-troubleshoot-deployment.md).

- For at lære at bruge tjenesten StorSimple Manager, skal du gå til [bruge tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).
