
<properties
    pageTitle="Administrere ressourcer med Azure CLI | Microsoft Azure"
    description="Bruge Azure kommandolinjen (CLI) til at administrere Azure ressourcer og grupper"
    editor=""
    manager="timlt"
    documentationCenter=""
    authors="dlepow"
    services="azure-resource-manager"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="danlep"/>

# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Bruge Azure CLI til at administrere Azure ressourcer og grupper


> [AZURE.SELECTOR]
- [Portal](azure-portal/resource-group-portal.md) 
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [REST-API](resource-manager-rest-api.md)


Kommandolinjen Azure (Azure CLI) er et af flere værktøjer, du kan bruge til at installere og administrere ressourcer med ressourcestyring. I denne artikel introducerer almindelige måder at administrere Azure ressourcer og grupper ved hjælp af Azure CLI i ressourcestyring tilstand. Du kan finde oplysninger om brug af CLI til at udrulle ressourcer [Implementer ressourcer til Ressourcestyring skabeloner og Azure CLI](resource-group-template-deploy-cli.md). Baggrunden om Azure ressourcer og ressourcestyring, gå til [Azure ressourcestyring oversigt](azure-resource-manager/resource-group-overview.md).

>[AZURE.NOTE] Hvis du vil administrere Azure ressourcer med Azure CLI, skal du [installere Azure CLI](xplat-cli-install.md), og [log på Azure](xplat-cli-connect.md) ved hjælp af den `azure login` kommandoen. Sørg for, at CLI er i ressourcestyring tilstand (køre `azure config mode arm`). Hvis du har gjort disse ting, er du klar til at blive sendt.



## <a name="get-resource-groups-and-resources"></a>Få ressourcegrupper og ressourcer

### <a name="resource-groups"></a>Ressourcegrupper

Hvis du vil have en liste over alle grupper i dit abonnement og deres placering, du Kør denne kommando.

    azure group list
    

### <a name="resources"></a>Ressourcer
 Brug følgende kommando for at få vist alle de ressourcer i en gruppe, som én med navnet *testRG*.

    azure resource list testRG

Bruge en kommando som følger for at få vist en enkelt ressource i gruppen, som en VM med navnet *MyUbuntuVM*.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"
    
Bemærk parameteren **Microsoft.Compute/virtualMachines** . Denne parameter angiver typen den ressource, du anmoder om oplysninger på.
    
>[AZURE.NOTE]Når du bruger kommandoerne **azure ressource** end kommandoen **liste** , skal du angive API-versionen af ressourcen med parameteren **-o** . Hvis du er sikker på, om API-version, se skabelonfilen, og find feltet apiVersion for ressourcen. Yderligere oplysninger om API versioner i ressourcestyring finder du [ressourcestyring udbydere, områder, API versioner og skemaer](resource-manager-supported-services.md).

Når du får vist detaljer for en ressource, er ofte nyttigt at bruge den `--json` parameter. Denne parameter gør output lettere at læse, fordi nogle værdier er indlejrede strukturer eller af websteder. I følgende eksempel vises, returnerer resultatet af kommandoen **Vis** som et JSON-dokument.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json

>[AZURE.NOTE] Hvis du vil gemme dataene JSON til fil ved hjælp af den &gt; tegn til at dirigere output til en fil. Eksempel:
>
> `azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

### <a name="tags"></a>Mærker

[AZURE.INCLUDE [resource-manager-tag-resources-cli](../includes/resource-manager-tag-resources-cli.md)]

## <a name="manage-resources"></a>Administrere ressourcer


For at tilføje en ressource som en lagerplads konto til en ressourcegruppe skal du køre en kommando, der svarer til:

    azure resource create testRG MyStorageAccount "Microsoft.Storage/storageAccounts" "westus" -o "2015-06-15" -p "{\"accountType\": \"Standard_LRS\"}"
    
Ud over at angive API-versionen af ressourcen med parameteren **-o** , kan du bruge parameteren **-p** til at sende en JSON-formateret streng med eventuelle nødvendige eller eventuelle yderligere egenskaber.
    
    
Hvis du vil slette en eksisterende ressource som en virtuel maskine, skal du bruge en kommando ud som følger.

    azure resource delete testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

Hvis du vil flytte eksisterende ressourcer til en anden ressourcegruppe eller et andet abonnement, skal du bruge kommandoen **azure ressource flytte** . I følgende eksempel viser, hvordan du flytter en Redis Cache til en ny ressourcegruppe. Angiv en kommasepareret liste over ressource-id er for at flytte parameteren **-i** .


    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"

## <a name="control-access-to-resources"></a>Styre adgangen til ressourcer

Du kan bruge Azure CLI til at oprette og administrere politikker for at styre adgangen til Azure ressourcer. Om Politikdefinitioner og tildele politikker til ressourcer, se [bruge politik for at administrere ressourcer og kontrollere adgangen](resource-manager-policy.md).

For eksempel definere politikken følgende for at afvise alle anmodninger hvor placering ikke er Vest amerikanske eller nord Central os, og gemme den på politik definition fil policy.json:

    {
    "if" : {
        "not" : {
        "field" : "location",
        "in" : ["westus" ,  "northcentralus"]
        }
    },
    "then" : {
        "effect" : "deny"
    }
    }

Kør kommandoen **Politikdefinition create** derefter:

    azure policy definition create MyPolicy -p c:\temp\policy.json
    
Denne kommando viser output svarende til følgende.

    + Oprette Politikdefinition MyPolicy data: politiknavn: MyPolicy data: PolicyDefinitionId: /subscriptions/###-###-###-###-###/providers/Microsoft.Authorization/policyDefinitions/MyPolicy

    data: PolicyType: Custom data: vist navn: Udefineret data: Beskrivelse: Udefineret data: PolicyRule: felt = placering, i = [westus, northcentralus], effekt = nægte

 Hvis du vil tildele en politik på det område, du vil bruge **PolicyDefinitionId** returneres fra den forrige kommando. I følgende eksempel dette område er abonnementet, men du kan begrænse til ressourcegrupper eller individuelle ressourcer:

    Azure politik oprette MyPolicyAssignment -p /subscriptions/###-###-###-###-###/providers/Microsoft.Authorization/policyDefinitions/MyPolicy -s /subscriptions/###-###-###-###-### /

Du kan få, ændre eller fjerne Politikdefinitioner ved hjælp af kommandoerne **Politikdefinition Vis**, **angive Politikdefinition**og **slette Politikdefinition** .

På samme måde, kan du få, ændre eller fjerne politik tildelinger ved hjælp af kommandoerne **politik vises**, **angive politik**og **slette politik** .


## <a name="export-a-resource-group-as-a-template"></a>Eksportere en ressourcegruppe som en skabelon

Til en eksisterende ressourcegruppe, kan du få vist skabelonen Ressourcestyring for ressourcegruppen. Eksportere skabelonen, der indeholder to fordele:

1. Du kan nemt automatisere fremtidige installationer af løsningen, fordi alle infrastruktur er defineret i skabelonen.

2. Du kan blive fortrolig med skabelonen syntaksen ved at kigge på JSON, der repræsenterer din løsning.

Ved hjælp af Azure CLI, kan du enten eksportere en skabelon, der repræsenterer den aktuelle tilstand for dit ressourcegruppe eller hente skabelonen, der blev brugt til en bestemt installation.

* **Eksportere skabelonen for en ressourcegruppe** – dette er nyttigt, når du har foretaget ændringer til en ressourcegruppe, og brug for at hente JSON repræsentationen af den aktuelle tilstand. Skabelonen genererede indeholder dog kun et minimale antal parametre og ingen variabler. De fleste af værdierne i skabelonen er faste. Før du anvender den oprettede skabelon, kan du konvertere flere af værdierne i parametre, så du kan tilpasse installationen til forskellige miljøer.

    Hvis du vil eksportere skabelonen for en ressourcegruppe til en lokal mappe, skal du køre den `azure group export` kommando som vist i følgende eksempel. (Erstatte en lokal mappe, der er relevante for dit operativsystemmiljø).

        azure group export testRG ~/azure/templates/

* **Hente en skabelon til en bestemt-installation** – dette er nyttigt, når du har brug at få vist den faktiske skabelon, der blev brugt til at implementere ressourcer. Skabelonen indeholder alle parametre og variabler, der er defineret for den oprindelige installation. Hvis nogen i organisationen har foretaget ændringer i ressourcegruppen uden for definitionen i skabelonen, kan denne skabelon ikke repræsenterer den aktuelle tilstand for ressourcegruppen.

    For at hente skabelonen bruges til en bestemt installation til en lokal mappe skal køre den `azure group deployment template download` kommandoen. Eksempel:

        azure group deployment template download TestRG testRGDeploy ~/azure/templates/downloads/
 
>[AZURE.NOTE] Skabelon Eksportér i Vis udskrift, og ikke alle ressourcetyper understøtter eksport af en skabelon. Når du forsøger at eksportere en skabelon, vises muligvis en fejlmeddelelse om nogle ressourcer, der ikke blev eksporteret. Hvis det er nødvendigt, definere manuelt disse ressourcer i skabelonen efter hente den.



## <a name="next-steps"></a>Næste trin

* For at få oplysninger om installationshandlinger og fejlfinding i forbindelse med installation fejl med Azure CLI skal du se [Vis installation operationer med Azure CLI](resource-manager-troubleshoot-deployments-cli.md).
* Hvis du vil bruge CLI til at konfigurere et program eller script for at få adgang til ressourcer, kan du se [Bruge Azure CLI til at oprette en sikkerhedskonto til at få adgang til ressourcer tjeneste](resource-group-authenticate-service-principal-cli.md).


