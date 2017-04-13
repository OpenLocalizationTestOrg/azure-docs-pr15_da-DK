<properties
    pageTitle="Konfigurere proxy og firewall-indstillinger i Log Analytics | Microsoft Azure"
    description="Konfigurere proxy og firewall-indstillinger, når din supportmedarbejdere eller OMS services for at bruge bestemte porte."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="banders;magoedte"/>

# <a name="configure-proxy-and-firewall-settings-in-log-analytics"></a>Konfigurere proxy og firewall-indstillinger i Log Analytics

Handlinger, der skal bruges til at konfigurere proxy og firewall-indstillinger for Log analyser i OMS variere, når du bruger Operations Manager og dens supportmedarbejdere kontra overvågning supportmedarbejdere, der har forbindelse til servere direkte. Gennemgå følgende afsnit for typen agent, som du bruger.

## <a name="configure-proxy-and-firewall-settings-with-the-microsoft-monitoring-agent"></a>Konfigurere proxy og firewall-indstillinger med Microsoft overvågning Agent

Det skal have adgang til portnummeret af dine domæner og URL-adresserne til Microsoft overvågning Agent til at oprette forbindelse til og registrere med tjenesten OMS. Hvis du bruger en proxyserver til kommunikationen mellem agenten og OMS-tjenesten, skal du sikre dig, at de relevante ressourcer er tilgængelige. Hvis du bruger en firewall til at begrænse adgang til internettet, skal du konfigurere din firewall til at tillade adgang til OMS. I følgende tabel vises de porte, der skal OMS.

|**Agent ressource**|**Porte**|**Spring HTTPS inspektion**|
|--------------|-----|--------------|
|\*. ods.opinsights.azure.com|443|Ja|
|\*. oms.opinsights.azure.com|443|Ja|
|\*. blob.core.windows.net|443|Ja|
|ods.systemcenteradvisor.com|443| |

Du kan bruge følgende procedure til at konfigurere indstillinger for proxyserver til Microsoft overvågning Agent ved hjælp af Kontrolpanel. Du skal bruge proceduren for hver server. Hvis du har mange servere, der skal du konfigurere, kan det være nemmere at bruge et script til at automatisere denne proces. Hvis det er tilfældet, skal du se fremgangsmåden nedenfor for [at konfigurere proxyindstillinger for Microsoft overvågning Agent ved hjælp af et script](#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script).

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-control-panel"></a>Konfigurere indstillinger for proxyserver til Microsoft overvågning Agent ved hjælp af Kontrolpanel

1. Åbn **Kontrolpanel**.

2. Åbn **Microsoft overvågning Agent**.

3. Klik på fanen **Proxyindstillinger** .<br>  
  ![proxy under fanen Indstillinger](./media/log-analytics-proxy-firewall/proxy-direct-agent-proxy.png)

4. Vælg **Brug en proxyserver** , og Skriv URL-adressen og portnummeret, hvis der er nødvendige, svarer til eksemplet. Hvis din proxy-server kræver godkendelse, skal du skrive brugernavnet og adgangskoden for at få adgang til proxy-server.

Brug følgende fremgangsmåde til at oprette en PowerShell-script, som du kan køre for at angive proxyindstillingerne for hver agent, der forbinder direkte til servere.

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script"></a>Konfigurere indstillinger for proxyserver til Microsoft overvågning Agent ved hjælp af et script

Kopiere i følgende eksempel, opdatere den med oplysninger, der er specifikke for dit miljø, gemme den med filtypenavnet PS1 og derefter køre scriptet på alle computere, der forbinder direkte til OMS-tjenesten.

        
    param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

    # First we get the Health Service configuration object.  We need to determine if we
    #have the right update rollup with the API we need.  If not, no need to run the rest of the script.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

    $proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

    if (!$proxyMethod)
    {
         Write-Output 'Health Service proxy API not present, will not update settings.'
         return
    }

    Write-Output "Clearing proxy settings."
    $healthServiceSettings.SetProxyInfo('', '', '')

    $ProxyUserName = $cred.username

    Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
    $healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
        

## <a name="configure-proxy-and-firewall-settings-with-operations-manager"></a>Konfigurere proxy og firewall-indstillinger med Operations Manager

Det skal have adgang til portnumre af dine domæner og URL-adresser for en Operations Manager management gruppe til at oprette forbindelse til og registrere med OMS-tjenesten. Hvis du bruger en proxyserver til kommunikation mellem indholdsstyringsserver Operations Manager og OMS-tjenesten, skal du sikre dig, at de relevante ressourcer er tilgængelige. Hvis du bruger en firewall til at begrænse adgang til internettet, skal du konfigurere din firewall til at tillade adgang til OMS. Selvom en Operations Manager management server ikke er bag en proxy-server, være dens supportmedarbejdere. I dette tilfælde skal proxyserveren konfigureres på samme måde, som supportmedarbejdere er for at aktivere og tillade sikkerhed, og Log Management-løsning data til at få sendt til OMS-webtjeneste.

Hvis Operations Manager supportmedarbejdere til at kommunikere med tjenesten OMS, skal skal infrastrukturen Operations Manager (herunder supportmedarbejdere) have den korrekte proxyindstillinger og version. Proxyindstilling til supportmedarbejdere er angivet i konsollen Operations Manager. Din version skal være et af følgende:

- Operations Manager 2012 SP1 opdateringspakke 7 eller nyere
- Operations Manager 2012 R2 opdateringspakke 3 eller nyere


I følgende tabel vises de porte, der er relateret til disse opgaver.

>[AZURE.NOTE] Nogle af de følgende ressourcer omtale Advisor og drift viden, begge var tidligere versioner af OMS. Listen over ressourcer ændrer i fremtiden.

Her er en liste over agent ressourcer og porte:<br>

|**Agent ressource**|**Porte**|
|--------------|-----|
|\*. ods.opinsights.azure.com|443|
|\*. oms.opinsights.azure.com|443|
|\*.BLOB.Core.Windows.NET/\*|443|
|ods.systemcenteradvisor.com|443|
<br>
Her er en liste over management serverressourcer og porte:<br>

|**Management serverressource**|**Porte**|**Spring HTTPS inspektion**|
|--------------|-----|--------------|
|Service.systemcenteradvisor.com|443| |
|\*. service.opinsights.azure.com|443| |
|\*. blob.core.windows.net|443|Ja| 
|data.systemcenteradvisor.com|443| | 
|ods.systemcenteradvisor.com|443| | 
|\*. ods.opinsights.azure.com|443|Ja| 
<br>
Her er en liste over OMS og Operations Manager console-ressourcer og porte.<br>

|**OMS og Operations Manager console ressource**|**Porte**|
|----|----|
|Service.systemcenteradvisor.com|443|
|\*. service.opinsights.azure.com|443|
|\*. live.com|Port 80 og 443|
|\*. microsoft.com|Port 80 og 443|
|\*. microsoftonline.com|Port 80 og 443|
|\*. mms.microsoft.com|Port 80 og 443|
|login.Windows.NET|Port 80 og 443|
<br>

Benyt følgende fremgangsmåde til at registrere din Operations Manager management gruppe med OMS-tjenesten. Hvis du har problemer med at kommunikere mellem gruppen administration og OMS-tjenesten, kan du bruge datavalidering procedurer til fejlfinding i forbindelse med overførsel af data til OMS-tjenesten.

### <a name="to-request-exceptions-for-the-oms-service-endpoints"></a>Anmode om undtagelser for OMS service slutpunkter

1. Brug oplysningerne fra den første tabel, der præsenteres tidligere for at sikre, at de nødvendige til Operations Manager management server ressourcer er tilgængelige via en hvilken som helst firewalls kan det være nødvendigt.
2. Brug oplysningerne fra den anden tabel, der præsenteres tidligere for at sikre, at de nødvendige til konsollen handlinger i Operations Manager og OMS ressourcer er tilgængelige via en hvilken som helst firewalls kan det være nødvendigt.
3. Hvis du bruger en proxyserver med Internet Explorer skal du sikre dig, at den er konfigureret og fungerer korrekt. For at bekræfte, kan du åbne en sikker Internetforbindelse (HTTPS), for eksempel [https://bing.com](https://bing.com). Hvis sikker Internetforbindelse ikke fungerer i en browser, virker det sandsynligvis ikke i Operations Manager management console med webtjenester i skyen.

### <a name="to-configure-the-proxy-server-in-the-operations-manager-console"></a>Du kan konfigurere proxyserveren i konsollen Operations Manager

1. Åbne konsollen Operations Manager, og vælg **Administration af** arbejdsområdet.

2. Udvid **Funktionsdygtige viden**, og vælg derefter **Funktionsdygtige indsigt forbindelse**.<br>  
    ![Operations Manager OMS forbindelse](./media/log-analytics-proxy-firewall/proxy-om01.png)
3. Klik på **Konfigurer proxyserver**i visningen OMS forbindelse.<br>  
    ![Operations Manager OMS forbindelse konfigurere Proxy-Server](./media/log-analytics-proxy-firewall/proxy-om02.png)
4. I funktionsdygtige indsigt indstillinger guiden: Proxy-Server, Vælg **Brug en proxyserver for at få adgang til funktionsdygtige indsigt webtjenesten**og derefter skrive URL-adresse med port tallet, for eksempel **http://myproxy:80**.<br>  
    ![Operations Manager OMS proxyadresse](./media/log-analytics-proxy-firewall/proxy-om03.png)


### <a name="to-specify-credentials-if-the-proxy-server-requires-authentication"></a>Angive legitimationsoplysninger, hvis proxy-server kræver godkendelse
 Legitimationsoplysninger til proxyserveren og indstillinger skal overføres til administrerede computere, som rapporterer til OMS. Disse servere skal være i *Microsoft System Center Advisor overvågning Server gruppe*. Legitimationsoplysninger er krypteret i registreringsdatabasen af hver enkelt server i gruppen.

1. Åbne konsollen Operations Manager, og vælg **Administration af** arbejdsområdet.
2. Vælg **profiler**under **RunAs konfiguration**.
3. Åbn den **System Center Advisor køre som profil Proxy** -profil.  
    ![Billede af System Center Advisor køre som Proxy-profil](./media/log-analytics-proxy-firewall/proxy-proxyacct1.png)
4. Klik på **Tilføj** for at bruge en konto, der kører som i køre som profil guiden. Du kan oprette en ny firmapost Kør som eller brug en eksisterende konto. Denne konto skal have tilladelse til at gå gennem proxy-server.  
    ![Billede af Kør som guiden profil](./media/log-analytics-proxy-firewall/proxy-proxyacct2.png)
5. For at angive kontoen til at administrere skal du vælge **en valgte klasse, gruppen eller et objekt** at åbne objekt søgefeltet.  
    ![Billede af Kør som guiden profil](./media/log-analytics-proxy-firewall/proxy-proxyacct2-1.png)
6. Søge efter og vælg derefter **Microsoft System Center Advisor overvågning Server gruppe**.  
    ![Billede af søgefeltet objekt](./media/log-analytics-proxy-firewall/proxy-proxyacct3.png)
7. Klik på **OK** for at lukke dialogboksen Tilføj et Kør som konto.  
    ![Billede af Kør som guiden profil](./media/log-analytics-proxy-firewall/proxy-proxyacct4.png)
8. Fuldføre guiden, og Gem ændringerne.  
    ![Billede af Kør som guiden profil](./media/log-analytics-proxy-firewall/proxy-proxyacct5.png)


### <a name="to-validate-that-oms-management-packs-are-downloaded"></a>Til at validere pågældende OMS management packs hentes

Hvis du har føjet løsninger til OMS, kan du se dem i konsollen Operations Manager som management packs under **Administration**. Søge efter *System Center Advisor* til hurtigt at finde dem.  
    ![Management packs hentet](./media/log-analytics-proxy-firewall/proxy-mpdownloaded.png) eller du kan også kontrollere for OMS management packs ved hjælp af følgende Windows PowerShell-kommando i Operations Manager management server:

    ```
    Get-ScomManagementPack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version,KeyToken
    ```

### <a name="to-validate-that-operations-manager-is-sending-data-to-the-oms-service"></a>Hvis du vil validere pågældende Operations Manager bruges til at sende data til OMS-tjenesten

1. Åbn Performance-skærm (perfmon.exe) i indholdsstyringsserver Operations Manager, og vælg **Performance-skærm**.
2. Klik på **Tilføj**, og vælg derefter **Sundhed Service Management grupper**.
3. Tilføj alle de tællere, der starter med **HTTP**.  
    ![føje tællere](./media/log-analytics-proxy-firewall/proxy-sendingdata1.png)
4. Hvis konfigurationen af Operations Manager er god, du får vist aktivitet for systemtilstand Service Management tællere til begivenheder og andre dataelementer, baseret på de management packs, du tilføjede i OMS og politikken konfigurerede log af websteder.  
    ![Ydeevne skærm viser aktivitet](./media/log-analytics-proxy-firewall/proxy-sendingdata2.png)


## <a name="next-steps"></a>Næste trin

- [Tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md) til at tilføje funktionalitet og indsamle data.
- Bliv fortrolig med [log søgninger](log-analytics-log-searches.md) til at få vist detaljerede oplysninger, der indsamles af løsninger.
