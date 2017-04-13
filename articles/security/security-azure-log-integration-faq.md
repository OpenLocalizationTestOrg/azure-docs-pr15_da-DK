<properties
   pageTitle="Azure log integration ofte stillede spørgsmål om | Microsoft Azure"
   description="Disse ofte stillede spørgsmål finder du svar på spørgsmål om Azure log integration."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/23/2016"
   ms.author="TomSh"/>

# <a name="azure-log-integration-frequently-asked-questions-faq"></a>Azure log integration stillede ofte spørgsmål (FAQ)

Disse ofte stillede spørgsmål finder du svar på spørgsmål om Azure log integration, en webtjeneste, der gør det muligt at integrere rå logfiler fra dine Azure ressourcer i til din lokale sikkerhedsoplysninger og begivenhed Management (SIEM). Denne integration giver et samlet dashboard for alle dine aktiver, lokalt eller i skyen, så du kan samle, koordinere, analysere og Giv besked om sikkerhed begivenheder, der er knyttet til dine programmer.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs-from"></a>Hvordan kan jeg se kontiene lagerplads, hvorfra Azure log integration er at trække Azure VM logfiler fra?

Kør kommandoen **azlog kildelisten**.

## <a name="how-can-i-update-the-proxy-configuration"></a>Hvordan kan jeg opdatere proxykonfigurationen?

Hvis din proxyindstilling ikke tillade Azure lagerplads adgang direkte, skal du åbne **AZLOG. EXE. CONFIG** fil i **c:\Program Files\Microsoft Azure Log Integration**. Opdatere filen indeholder afsnittet **defaultProxy** med proxyadressen i din organisation. Når opdatering er færdig, skal du stoppe og starte tjenesten ved hjælp af kommandoerne **net stop azlog** og **net start azlog**.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Hvordan kan jeg se abonnementsoplysninger i Windows-hændelser?

Føje **subscriptionid** til det fulde navn under tilføjelse af kilden.

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  

Hændelsen XML indeholder metadata, som vist nedenfor, herunder abonnement-id.

![Begivenhed XML][1]

## <a name="error-messages"></a>Fejlmeddelelser

### <a name="when-running-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Når du kører kommandoen **azlog createazureid**, hvorfor får jeg vist følgende fejlmeddelelse?

Fejl:

  *Kunne ikke oprettes AAD program - lejer 72f988bf-86f1-41af-91ab-2d7cd011db37-eller anden grund = "Forbudt" - meddelelse = 'Ikke de nødvendige rettigheder for at fuldføre handlingen'.*

**Azlog createazureid** forsøger at oprette en tjeneste hovedstolen i alle Azure AD-lejere til abonnementer som Azure logon har adgang til. Hvis din Azure logon er kun en gæstebruger i pågældende Azure AD-lejer, derefter mislykkes kommandoen med 'Utilstrækkelig rettigheder til at fuldføre handlingen'. Anmode om lejeradministrator for at tilføje din konto som en bruger i lejeren.

### <a name="when-running-command-azlog-authorize-why-do-i-get-the-following-error"></a>Når du kører kommandoen **azlog Godkend**, hvorfor får jeg vist følgende fejlmeddelelse?

Fejl:

  *Advarsel om oprettelse af rolletildeling - AuthorizationFailed: klienten janedo@microsoft.com' med objekt id 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' ikke har tilladelse til at udføre handlingen 'Microsoft.Authorization/roleAssignments/write' over område '/ abonnementer/70 d 95299-d689-4c 97-b971-0d8ff0000000'.*

**Godkend Azlog** kommandoen tildeler rollen Læser til Azure AD-tjenesten vigtigste (oprettet med **Azlog createazureid**) til de abonnementer, der er angivet. Hvis Azure logon ikke er en samtidig Administrator eller en ejer af abonnementet, kan ikke 'Godkendelse mislykkede' fejlmeddelelse. Azure rollebaseret adgangskontrol (RBAC) af samtidig Administrator eller ejer skal bruges til at udføre denne handling.

## <a name="where-can-i-find-the-definition-of-the-properties-in-audit-log"></a>Hvor kan jeg finde definitionen af egenskaberne i overvågningsloggen?

Se:

- [Overvåge forskellige handlinger med ressourcestyring](../resource-group-audit.md)
- [Liste over management begivenheder i et abonnement i Azure skærm REST-API](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Hvor kan jeg finde oplysninger på Azure Sikkerhedscenter beskeder?

Se [administrere og reagere på sikkerhedsadvarsler i Azure Sikkerhedscenter](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Hvordan kan jeg ændre, hvad der indsamles med VM diagnosticering?

Se [Bruge PowerShell til at aktivere Azure diagnosticering i en virtuel maskine, der kører Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) få mere at vide om, hvordan du få, Rediger, og Angiv Azure diagnosticering i Windows *(WAD)* konfiguration. Følgende er et eksempel:

### <a name="get-the-wad-config"></a>Få WAD config

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

### <a name="modify-the-wad-config"></a>Ændre WAD Config

I følgende eksempel er en konfiguration, hvor kun Begivenhedid 4624 og Begivenhedid 4625 indsamles fra hændelseslog sikkerhed. Microsoft Antimalwareprogram hændelser, der indsamles fra systemets hændelseslog. Se [forbrug begivenheder] (https://msdn.microsoft.com/library/windows/desktop/dd996910 (v=vs.85) få at vide om brugen af XPath-udtryk.

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

### <a name="set-the-wad-configuration"></a>Konfiguration WAD

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Markér kontoen lagerplads for at sikre, at de korrekte hændelser der indsamles, når du har foretaget ændringer.

Hvis du har spørgsmål om Azure Log Integration, bedes du sende en mail til [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
