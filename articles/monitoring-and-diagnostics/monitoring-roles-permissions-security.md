<properties
    pageTitle="Komme i gang med roller, tilladelser og sikkerhed med Azure skærm | Microsoft Azure"
    description="Lær, hvordan du bruger Azure skærm indbyggede roller og tilladelser til at begrænse adgangen til overvågning ressourcer."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Komme i gang med roller, tilladelser og sikkerhed med Azure skærm

Mange teams skal nøje fastsætte adgang til overvågning af data og indstillinger. Eksempelvis hvis du har gruppemedlemmer, der fungerer udelukkende på overvågning (supportmedarbejdere, devops teknikere), eller hvis du bruger en administreret tjeneste-udbyder, kan du give dem adgang til overvågningsdata kun mens begrænse deres mulighed for at oprette, redigere eller slette ressourcer. I denne artikel viser, hvordan du hurtigt vil anvende en indbygget overvågning RBAC rolle til en bruger i Azure eller oprette dine egne brugerdefinerede rolle til en bruger, der kræver begrænset overvågning tilladelser. Den derefter behandler overvejelser om sikkerheden for dine Azure skærm-relaterede ressourcer, og hvordan du kan begrænse adgangen til de data, de indeholder.

## <a name="built-in-monitoring-roles"></a>Indbyggede overvågning roller

Azure skærm indbyggede roller er udviklet til at begrænse adgangen til ressourcer i et abonnement, mens du stadig aktivere dem, der er ansvarlig for overvågning infrastruktur for at hente og konfigurere de ønskede data. Azure skærm indeholder to out box roller: A overvågning Reader og bidragyder overvågning.

### <a name="monitoring-reader"></a>Overvågning Reader

Personer, der er tildelt rollen overvågning læser kan se alle overvågningsdata i et abonnement, men kan ikke ændre en ressource eller redigere nogen indstillinger, der er relateret til overvågning ressourcer. Denne rolle er relevante for brugere i en organisation, som support eller handlinger teknikere, der skal kunne:

- Få vist overvågning dashboards på portalen og oprette deres egne private overvågning dashboards.
- Forespørgsel på målepunkter bruger [Azure skærm REST-API](https://msdn.microsoft.com/library/azure/dn931930.aspx), [PowerShell-cmdlet'er](insights-powershell-samples.md)eller [på tværs af platforme CLI](insights-cli-samples.md).
- Forespørge på aktivitet loggen ved hjælp af den portal, Azure skærm REST-API, PowerShell-cmdlet'er eller på tværs af platforme CLI.
- Få vist [Indstillinger for diagnosticering](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) for en ressource.
- Få vist [log profil](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles) til et abonnement.
- Få vist Autoskalering indstillinger.
- Vis beskeder om aktivitet og indstillinger.
- Få adgang til programmet indsigt data og få vist dataene i AI Analytics.
- Søge Log Analytics (OMS) arbejdsområdedata herunder anvendelsesdata for arbejdsområdet.
- Få vist Log Analytics (OMS) administration af grupper.
- Hent søgeskemaet Log Analytics (OMS).
- Liste over Log Analytics (OMS) intelligence packs.
- Hente og udføre Log Analytics (OMS) gemte søgninger.
- Hent Log Analytics (OMS) lagerplads konfigurationen.

> [AZURE.NOTE] Denne rolle giver ikke læseadgang til logdata, der er streames til en begivenhed hub eller gemt i en lagerplads konto. [Se nedenfor](#security-considerations-for-monitoring-data) for oplysninger om konfiguration af adgang til disse ressourcer.

### <a name="monitoring-contributor"></a>Overvågning bidragyder

Personer, der er tildelt rollen overvågning Bidragyder kan få vist alle overvågningsdata i et abonnement og oprette eller ændre overvågningsindstillinger, men kan ikke ændre andre ressourcer. Denne rolle er et udsnit af rollen overvågning læser og egner sig til medlemmer af en organisation overvågning team eller administrerede tjenesteudbydere der ud over de tilladelser, der er ovenfor, også skal have lov til at:

- Publicere overvågning dashboards som et delt dashboard.
- Angive [Indstillinger for diagnosticering](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) for en resource.*
- Angive en subscription.* [log profil](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles)
- Angive indstillinger for beskeder om aktivitet og.
- Oprette programmet indsigt web test og -komponenter.
- Log Analytics (OMS) arbejdsområde delt taster.
- Aktivere eller deaktivere Log Analytics (OMS) intelligence packs.
- Oprette og slette og udføre Log Analytics (OMS) gemte søgninger.
- Oprette og slette Log Analytics (OMS) lagerplads konfigurationen.

* brugeren skal også separat gives ListKeys tilladelse på target ressourcen (lagerplads konto eller en begivenhed hub navneområde) til at angive en log profil eller diagnosticering indstilling.

> [AZURE.NOTE] Denne rolle giver ikke læseadgang til logdata, der er streames til en begivenhed hub eller gemt i en lagerplads konto. [Se nedenfor](#security-considerations-for-monitoring-data) for oplysninger om konfiguration af adgang til disse ressourcer.

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Overvågning af tilladelser og brugerdefinerede RBAC roller
Hvis de ovenstående indbyggede roller ikke opfylder nøjagtige behovet i dit team, kan du [oprette en brugerdefineret RBAC rolle](../active-directory/role-based-access-control-custom-roles.md) med mere detaljerede tilladelser. Nedenfor finder du de almindelige Azure skærm RBAC handlinger med beskrivelserne.

| Handling                                                   | Beskrivelse                                                                                                                                               |
|-------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Microsoft.Insights/AlertRules/[Read, Skriv, Slet]         | Læse/skrive/slette regler for påmindelser.                                                                                                                            |
| Microsoft.Insights/AlertRules/Incidents/Read                | Liste over hændelser (oversigt over reglen der udløste) til regler for påmindelser. Dette gælder kun for portalen.                                              |
| Microsoft.Insights/AutoscaleSettings/[Read, Skriv, Slet]  | Læse/skrive/slette Autoskalering indstillinger.                                                                                                                     |
| Microsoft.Insights/DiagnosticSettings/[Read, Skriv, Slet] | Læse/skrive/slette diagnosticering indstillinger.                                                                                                                    |
| Microsoft.Insights/eventtypes/digestevents/Read             | Denne tilladelse er nødvendigt for brugere, der skal have adgang til aktivitetslogfiler via portalen.                                                                   |
| Microsoft.Insights/eventtypes/values/Read                   | Liste over aktivitetslog begivenheder (management hændelser) i et abonnement. Denne tilladelse kan anvendes på både programmeringsmæssig og portalen adgang til loggen aktivitet. |
| Microsoft.Insights/LogDefinitions/Read                      | Denne tilladelse er nødvendigt for brugere, der skal have adgang til aktivitetslogfiler via portalen.                                                                   |
| Microsoft.Insights/MetricDefinitions/Read                   | Læs metriske definitioner (liste over tilgængelige metriske typer for en ressource).                                                                                  |
| Microsoft.Insights/Metrics/Read                             | Læs måleredskaber for en ressource.                                                                                                                              |

> [AZURE.NOTE] Få adgang til vigtige beskeder, indstillinger for diagnosticering og målepunkter til en ressource kræver, at brugeren har adgang til ressourcetype og omfanget af ressourcen. Oprette ("skrive") kræver en diagnosticering indstilling eller log profil, der arkiveres med en lagerplads firma eller en streams til begivenhed hubber, at brugeren også have ListKeys tilladelse på target ressourcen.

For eksempel ved hjælp af ovenstående tabel kan du oprette en brugerdefineret RBAC rolle til en "aktivitet Log læser" således:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Overvejelser om sikkerheden for overvågning af data
Overvågning af data – særligt logfiler – kan indeholde følsomme oplysninger som IP-adresser eller brugernavne. Overvågning af data fra Azure der findes tre grundlæggende formularer:
1. Loggen aktivitet, som beskriver alle kontrolelement plan handlinger på dit Azure-abonnement.
2. Diagnosticeringslogfiler, som er logfiler, der udsendes af en ressource.
3. Statistik, som der udledes af ressourcer.

Alle tre typer disse data kan gemmes i en lagerplads konto eller streames til begivenhed-Hub, hvor begge er generelle Azure ressourcer. Da disse generelle ressourcer, er oprette, slette og få adgang til dem en privilegerede handling, der normalt er reserveret til en administrator. Vi anbefaler, at du bruger følgende fremgangsmåder for overvågning-relaterede ressourcer for at undgå misbrug:

- Bruge en enkelt, dedikeret storage-konto til at overvåge data. Hvis du har brug at opdele overvågningsdata i flere lagerplads konti, dele aldrig brugen af en lagerplads konto mellem overvågning og ikke-overvågning data, som dette ved et uheld kan give dem, der kun skal have adgang til overvågningsdata (f.eks.) en tredjeparts SIEM) adgang til ikke-overvågning data.
- Bruge et enkelt, dedikeret Service Bus eller begivenhed Hub navneområde på tværs af alle indstillinger for diagnosticering af samme grund som ovenfor.
- Begrænse adgangen til overvågning-relaterede lagerplads firmaer eller begivenhed hubs ved at gemme dem i en separat ressourcegruppe og [bruge omfang](../active-directory/role-based-access-control-what-is.md#basics-of-access-management-in-azure) under din overvågning roller at begrænse adgangen til kun denne ressourcegruppe.
- Tildel aldrig tilladelsen ListKeys for lagerplads konti eller begivenhed hubs på abonnement omfang, når en bruger kun skal have adgang til overvågning af data. I stedet give disse rettigheder til brugeren ved en ressource eller ressourcegruppe (Hvis du har en dedikeret overvågning ressourcegruppe) omfang.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>At begrænse adgangen til overvågning-relaterede lagerplads konti
Når en bruger eller et program skal du have adgang til overvågning af data med en konto med lagerplads, skal du [oprette en konto SAS](https://msdn.microsoft.com/library/azure/mt584140.aspx) på kontoen lagerplads, der indeholder overvågningsdata med tjenesten niveau skrivebeskyttet adgang til blob-lager. I PowerShell, kan det se ud:

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Derefter kan du give tokenet til objektet, skal for at læse fra storage-konto, og det kan liste og læse fra alle BLOB i den pågældende lagerplads konto.

Du kan også hvis du har brug for til at styre denne tilladelse med RBAC, kan du tildele objektet Microsoft.Storage/storageAccounts/listkeys/action tilladelsen for den pågældende bestemt lagerplads konto. Det er nødvendigt for brugere, der har brug for at kunne angive diagnosticering eller logge profil at arkivere en lagerplads konto. For eksempel kan du oprette de følgende brugerdefinerede RBAC roller for en bruger eller program, der kun skal læse fra den ene lagerplads konto:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [AZURE.WARNING] Tilladelsen ListKeys gør det muligt for brugeren til at få vist tasterne primære og sekundære lagerplads konto. Disse taster Giv brugeren alle signerede tilladelser (læse, skrive, oprette blob, slette blob, osv.) på tværs af alle logget tjenester (blob kø tabel, og filer) i den pågældende lagerplads konto. Det anbefales at bruge en konto SAS, der er beskrevet ovenfor, når det er muligt.

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>At begrænse adgangen til overvågning-relaterede begivenhed hubber
Et lignende mønster kan efterfølges med begivenhed hubs, men du skal først oprette en dedikeret lytte godkendelse regel. Hvis du vil give adgang til et program, der kun skal lytte til overvågning-relaterede begivenhed hubs, skal du gøre følgende:

1. Oprette en delt access-politik på hubberne begivenhed, der er oprettet på at streaming overvågningsdata med kun lytte krav. Dette kan gøres på portalen. For eksempel kan du ringe til det "monitoringReadOnly." Hvis det er muligt, vil du give tasten direkte til forbrugeren og springe næste trin.
2. Hvis vedkommende skal kunne få de vigtigste ad hoc-, Giv brugeren handlingen ListKeys til den pågældende begivenhed hub. Dette er også nødvendigt for brugere, der har brug for at kunne angive diagnosticering eller log profil stream til begivenhed hubber. For eksempel kan du oprette en regel for RBAC:

   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```


## <a name="next-steps"></a>Næste trin
- [Læs mere om RBAC og tilladelser i ressourcestyring.](../active-directory/role-based-access-control-what-is.md)
- [Læs oversigt over overvågning i Azure](monitoring-overview.md)
