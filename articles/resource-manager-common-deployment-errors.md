<properties
   pageTitle="Foretage fejlfinding af almindelige fejl i Azure-installation | Microsoft Azure"
   description="Beskriver, hvordan du løser almindelige fejl, når du installerer ressourcer til Azure ved hjælp af Azure ressourcestyring."
   services="azure-resource-manager"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"
   keywords="installation fejl, azure-installation skal installeres til azure"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="tomfitz"/>

# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Foretage fejlfinding af almindelige fejl Azure-installation med Azure ressourcestyring

I dette emne beskrives, hvordan du kan løse nogle almindelige Azure-installation fejl, der kan opstå. Hvis du har brug for flere oplysninger om, hvad der gik galt med installationen, først se [visningen installationshandlinger](resource-manager-troubleshoot-deployments-portal.md) og derefter vende tilbage til denne artikel til hjælp til at løse fejlen. I afsnit i dette emne vises du får vist fejlkoden.

## <a name="invalid-template"></a>Ugyldige skabelon

Fejlen kan skyldes flere forskellige typer fejl. 

### <a name="syntax-error"></a>Syntaksfejl

Hvis du modtager en fejlmeddelelse, der angiver skabelonen kunne ikke validering, skal du muligvis et problem med syntaksen i skabelonen. 

    Code=InvalidTemplate 
    Message=Deployment template validation failed

Denne fejl er nem at lave fordi skabelon udtryk kan være besværlige. For eksempel indeholder følgende navn opgaven til en lagerplads konto ét sæt af parenteser, tre funktioner, tre sæt af parenteser, ét sæt af enkelte anførselstegn og én egenskab:

    "name": "[concat('storage', uniqueString(resourceGroup().id))]",

Hvis du ikke angiver den tilsvarende syntaks, skabelonen bruges til at beregne en værdi, der er anderledes end din hensigt.

Når du modtager denne type fejl, gennemgå omhyggeligt syntaksen for udtryk. Overvej at bruge en JSON editor som [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) eller [Visual Studio-kode](resource-manager-vs-code.md), som kan advare dig om syntaksfejl. 

### <a name="incorrect-segment-lengths"></a>Forkert segmentet længde

En anden ugyldige skabelon fejlen opstår, når ressourcenavnet ikke er i det korrekte format.

    Code=InvalidTemplate
    Message=Deployment template validation failed: 'The template resource {resource-name}' 
    for type {resource-type} has incorrect segment lengths.

En rod udjævne ressourcernes skal have ét mindre segment i navnet på end på ressourcetypen. Hvert segment er adskilt af en skråstreg. I eksemplet nedenfor typen har to segmenter og navnet har en målgruppe, så det er et **gyldigt navn**.

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "myHostingPlanName",
      ...
    }

Men det næste eksempel er **ikke et gyldigt navn** , fordi den ikke har det samme antal målgrupper som typen.

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "appPlan/myHostingPlanName",
      ...
    }

Underordnet ressourcer har type og navn det samme antal målgrupper. Dette antal målgrupper giver mening, fordi det fulde navn og type for underordnede indeholder overordnede navn og type. Derfor er det fulde navn stadig ét mindre segment end den fulde type. 

    "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "contosokeyvault",
            ...
            "resources": [
                {
                    "type": "secrets",
                    "name": "appPassword",
                    ...
                }
            ]
        }
    ]

Få segmenterne højre kan være svære med ressourcestyring typer, der er anvendt på tværs af ressource udbydere. For eksempel kræver anvende en ressource Lås på et websted en type med fire segmenter. Navnet er derfor tre segmenter:

    {
        "type": "Microsoft.Web/sites/providers/locks",
        "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
        ...
    }

### <a name="copy-index-is-not-expected"></a>Kopiér indeks forventes ikke

Fejlen **InvalidTemplate** opstår, når du har anvendt elementet **kopi** på en del af den skabelon, der ikke understøtter dette element. Du kan kun anvende elementet kopi på en ressourcetype. Du kan ikke anvende kopi på en egenskab i en ressourcetype. For eksempel du anvende kopi på en virtuel maskine, men du kan ikke anvende den på OS diske til en virtuel maskine. I nogle tilfælde kan du konvertere en underordnet ressource til en overordnet ressource til at oprette en kopi løkke. Se [oprette flere forekomster af ressourcer i Azure ressourcestyring](resource-group-create-multiple.md)kan finde flere oplysninger om ved hjælp af kopier.

### <a name="parameter-is-not-valid"></a>Parameter er ikke gyldig

Hvis skabelonen angiver tilladte værdier for en parameter, og du angiver en værdi, ikke der er en af disse værdier, modtager du en meddelelse, der ligner følgende fejl:

    Code=InvalidTemplate; 
    Message=Deployment template validation failed: 'The provided value {parameter vaule} 
    for the template parameter {parameter name} is not valid. The parameter value is not 
    part of the allowed value(s)

Dobbelt kontrollere de tilladte værdier i skabelonen, og giv en under installationen.

## <a name="not-found-or-resource-not-found"></a>Blev ikke fundet (eller ressource blev ikke fundet)

Når din skabelon indeholder navnet på en ressource, der ikke kan løses, får du en fejl, der ligner:

    Code=NotFound;
    Message=Cannot find ServerFarm with name exampleplan.

Hvis du forsøger at installere den manglende ressource i skabelonen, skal du kontrollere, om du vil tilføje en afhængighed. Ressourcestyring optimerer installation ved at oprettelsen af ressourcer parallelt, når det er muligt. Hvis en ressource skal installeres efter en anden ressource, skal du bruge elementet **dependsOn** i din skabelon til at oprette en afhængighed på anden ressourcen. For eksempel, når du installerer en WebApp, skal findes App-serviceaftale. Hvis du ikke har angivet, at online afhænger af App serviceaftale, opretter ressourcestyring begge ressourcer på samme tid. Du modtager en fejlmeddelelse om, at App Service plan ressourcen ikke blev fundet, fordi der ikke findes endnu, når du forsøger at angive en egenskab på WebApp. Du kan undgå denne fejl ved at angive afhængigheden i WebApp.

    {
      "apiVersion": "2015-08-01",
      "type": "Microsoft.Web/sites",
      ...
      "dependsOn": [
        "[variables('hostingPlanName')]"
      ],
      ...
    }
 
Du kan også se denne fejl, når ressourcen, der findes i en anden ressourcegruppe end den anvendte til. Følg i så fald [resourceId funktionen](resource-group-template-functions.md#resourceid) for at finde det fulde navn på ressourcen.

    "properties": {
        "name": "[parameters('siteName')]",
        "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
    } 

Hvis du forsøger at bruge funktionerne [reference](resource-group-template-functions.md#referenc) eller [listKeys](resource-group-template-functions.md#listkeys) med en ressource, der ikke kan løses, får du følgende fejl:

    Code=ResourceNotFound;
    Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource 
    group {resource group name} was not found.

Se efter et udtryk, der omfatter funktionen **reference** . Dobbelt Kontrollér, at parameterværdierne er korrekte.

## <a name="storage-account-already-exists-or-already-taken"></a>Lagerplads konto findes allerede (eller allerede taget)

Du skal angive et navn til den ressource, der er entydige på tværs af Azure for lagerplads-konti. Hvis du ikke angiver et entydigt navn, modtager du en fejl som:

    Code=StorageAccountAlreadyTaken 
    Message=The storage account named mystorage is already taken.

Du kan oprette et entydigt navn ved at sammenkæde dine navnekonventionen med resultatet af funktionen [uniqueString](resource-group-template-functions.md#uniquestring) .

    "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]",
    "type": "Microsoft.Storage/storageAccounts",

Hvis du installerer en lagerplads konto med samme navn som en eksisterende konto lagerplads i dit abonnement, men giver en anden placering, modtager du en fejlmeddelelse om lagerplads konto findes allerede i en anden placering. Enten slette eksisterende lagerplads kontoen, eller angive placeringen af den samme som den eksisterende lagerplads-konto.

## <a name="account-name-invalid"></a>Ugyldige kontonavn

Du får vist **AccountNameInvalid** fejlmeddelelse, når du forsøger at give en lagerplads konto et navn, der indeholder forbudte tegn. Lagerplads kontonavne skal være mellem 3 og 24 tegn og bruge tal og små bogstaver.

## <a name="no-registered-provider-found"></a>Ingen registrerede udbydere, der blev fundet

Når du installerer ressource, modtager du muligvis følgende fejlkode og fejlmeddelelse:

    Code: NoRegisteredProviderFound
    Message: No registered resource provider found for location {ocation} 
    and API version {api-version} for type {resource-type}.

Du får vist denne fejl for en af to årsager:

1. Placering, der ikke understøttes for ressourcetypen
2. API-version understøttes ikke af ressourcetypen
3. Provideren ressource er ikke registreret for dit abonnement

Fejlmeddelelsen bør give dig forslag til understøttede steder og API versioner. Du kan ændre din skabelon til en af de foreslåede værdier. De fleste udbydere er registreret automatisk af portalen Azure eller kommandolinjen du bruger, men ikke alle. Hvis du ikke har brugt en bestemt ressource udbyder før, skal du muligvis til at registrere udbyderen. Du kan finde flere oplysninger om ressource udbydere via PowerShell eller Azure CLI.

### <a name="powershell"></a>PowerShell

For at se registreringsstatus for din skal du bruge **Get-AzureRmResourceProvider**.

    Get-AzureRmResourceProvider -ListAvailable

Du kan registrere en udbyder, bruge **Register-AzureRmResourceProvider** og angive navnet på den ressource-udbyder, du vil registrere.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

For at få de understøttede placeringer for en bestemt type ressource skal du bruge:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

For at få de understøttede API versioner til en bestemt type ressource skal du bruge:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### <a name="azure-cli"></a>Azure CLI

Hvis du vil se, om udbyderen, der er registreret, skal du bruge den `azure provider list` kommandoen.

    azure provider list

Du kan registrere en ressource-udbyder ved at bruge den `azure provider register` kommando og angive *navneområde* til at registrere.

    azure provider register Microsoft.Cdn

For at se understøttede steder og API versioner for en ressource-udbyder, skal du bruge:

    azure provider show -n Microsoft.Compute --json > compute.json

## <a name="operation-not-allowed"></a>Handlingen er ikke tilladt

Du muligvis problemer, når installation overstiger en kvote, som kan være per ressourcegruppe, abonnementer, konti og andre områder. Dit abonnement kan for eksempel konfigureres til at begrænse antallet borekerner til et område. Hvis du forsøger at installere en virtuel maskine med flere kerner end den tilladte afstand, modtager du en fejlmeddelelse om kvoten er overskredet.
Fuldført kvote kan finde oplysninger i [Azure-abonnement og begrænsninger for tjenesten, kvoter, og begrænsninger](azure-subscription-service-limits.md).

Hvis du vil undersøge dit abonnement kvoter for kerner, kan du bruge den `azure vm list-usage` kommando i Azure CLI. Det følgende eksempel viser, at kvoten core for en gratis prøveversion konto er 4:

    azure vm list-usage

Der returneres:

    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

Hvis du installerer en skabelon, der opretter mere end fire kerner i området Vest USA, kan du få fejlen installation, der ser sådan ud:

    Code=OperationNotAllowed
    Message=Operation results in exceeding quota limits of Core. 
    Maximum allowed: 4, Current in use: 4, Additional requested: 2.

Eller i PowerShell, kan du bruge cmdlet'en **Get-AzureRmVMUsage** til.

    Get-AzureRmVMUsage

Der returneres:

    ...
    CurrentValue : 0
    Limit        : 4
    Name         : {
                     "value": "cores",
                     "localizedValue": "Total Regional Cores"
                   }
    Unit         : null
    ...

I disse tilfælde skal du gå til portalen og arkivere en support problem for at hæve dit kvote for det område, hvor du vil installere.

> [AZURE.NOTE] Husk, at kvoten for grupper, der for hvert enkelt område ikke for hele abonnementet. Hvis du har brug for til at udrulle 30 kerner i Vest USA, har du bede om 30 ressourcestyring kerner Vest USA. Hvis du vil installere 30 kerner i nogen af områderne, du har adgang til, skal du bede om 30 ressourcestyring kerner i alle områder.

## <a name="invalid-content-link"></a>Ugyldige indholdslink

Når du modtager en fejlmeddelelse om:

    Code=InvalidContentLink
    Message=Unable to download deployment content from ...

Du har sandsynligvis forsøgt at oprette et link til en indlejret skabelon, der ikke er tilgængelig. Dobbelt Kontrollér den URI, du har angivet for den indlejrede skabelon. Hvis skabelonen findes i en lagerplads-konto, skal du kontrollere URI er tilgængeligt. Du skal muligvis overfører et SAS token. Få mere at vide under [Brug af sammenkædede skabeloner med Azure ressourcestyring](resource-group-linked-templates.md).

## <a name="authorization-failed"></a>Godkendelse mislykkedes

Du kan modtage en fejlmeddelelse under installation, fordi det firma eller den tjeneste, der er primære forsøger at installere ressourcerne, der ikke har adgang til at udføre disse handlinger. Giver dig mulighed for Azure Active Directory eller administratoren for at styre, hvilke identiteter kan få adgang til hvilke ressourcer med et godt graden af præcision. Eksempelvis hvis din konto er tildelt rollen Læser, er du ikke kunne oprette ressourcer. Det er tilfældet, vises en fejlmeddelelse om, at godkendelsen mislykkedes.

Du kan finde flere oplysninger om rollebaseret adgangskontrol, [Azure Role-Based adgangskontrol](./active-directory/role-based-access-control-configure.md).

Ud over rollebaseret adgangskontrol, kan være begrænset handlinger din installation af politikker på abonnementet. Administratoren kan gennemtvinge konventioner om alle de ressourcer, der er implementeret i abonnementet via politikker. En administrator kan for eksempel kræver, at en bestemt mærke værdi er angivet for en ressourcetype. Hvis du ikke opfylder kravene politik, modtager du en fejl under installation. Du kan finde flere oplysninger om politikker, [Brug politik til administration af ressourcer og kontrollere adgangen](resource-manager-policy.md).

## <a name="troubleshooting-virtual-machines"></a>Fejlfinding i forbindelse med virtuelle maskiner

| Fejl | Artikler |
| -------- | ----------- |
| Brugerdefineret script lokalnummer fejl | [Windows VM lokalnummer fejl](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md)<br />eller<br />[Linux VM lokalnummer fejl](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md) |
| Billede af OS klargøringsfejl | [Nye Windows VM fejl](./virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md)<br />eller<br />[Nye Linux VM fejl](./virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md ) |
| Allokeringsfejl | [Windows VM allokeringsfejl](./virtual-machines/virtual-machines-windows-allocation-failure.md)<br />eller<br />[Linux VM allokeringsfejl](./virtual-machines/virtual-machines-linux-allocation-failure.md) |
| Secure Shell (SSH) fejl, når du forsøger at oprette forbindelse | [Sikre Shell forbindelser til Linux VM](./virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md) |
| Oprette forbindelse til program, der kører på VM fejl | [Program, der kører på Windows VM](./virtual-machines/virtual-machines-windows-troubleshoot-app-connection.md)<br />eller<br />[Program, der kører på en Linux VM](./virtual-machines/virtual-machines-linux-troubleshoot-app-connection.md) |
| Remote Desktop connection fejl | [Forbindelser til Fjernskrivebord til Windows VM](./virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md) |
| Forbindelsesfejl løses ved at geninstallere | [Geninstaller Virtual Machine til nye Azure node](./virtual-machines/virtual-machines-windows-redeploy-to-new-node.md) |
| Fejl i skyen-tjenesten | [Skyen serviceproblemer installation](./cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

## <a name="troubleshooting-other-services"></a>Fejlfinding i forbindelse med andre tjenester

Den følgende tabel er ikke en komplet liste over fejlfindingsværktøjer emner til Azure. I stedet fokuserer det på problemer i forbindelse med installation eller konfiguration af ressourcer. Hvis du har brug for hjælp til fejlfinding af problemer med kørselstidspunktet med en ressource, kan du se i dokumentationen til den Azure service.

| Tjenesten | Artikel |
| -------- | -------- |
| Automatisering | [Tip til fejlfinding af almindelige fejl i Azure automatisering](./automation/automation-troubleshooting-automation-errors.md) |
| Azure stak | [Fejlfinding i forbindelse med Microsoft Azure stak](./azure-stack/azure-stack-troubleshooting.md) |
| Azure stak | [Webapps og Azure stak](./azure-stack/azure-stack-webapps-troubleshoot-known-issues.md ) |
| Data Factory | [Foretage fejlfinding af problemer med Data Factory](./data-factory/data-factory-troubleshoot.md) |
| Tjenesten struktur | [Foretage fejlfinding af almindelige problemer, når du installerer tjenester på Azure Service struktur](./service-fabric/service-fabric-diagnostics-troubleshoot-common-scenarios.md) |
| Gendannelse af websteder | [Overvåge og fejlfinding i forbindelse med beskyttelse til virtuelle computere og fysiske servere](./site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| Lagerplads | [Overvåge, diagnosticere og fejlfinding i forbindelse med Microsoft Azure-lager](./storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple | [Foretage fejlfinding af problemer med installation StorSimple enheder](./storsimple/storsimple-troubleshoot-deployment.md) |
| SQL-Database | [Foretage fejlfinding af forbindelsesproblemer til Azure SQL-Database](./sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| SQL datawarehouse | [Fejlfinding i forbindelse med Azure SQL datawarehouse](./sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## <a name="understand-when-a-deployment-is-ready"></a>Forstå, hvornår en installation er klar

Azure ressourcestyring rapporter succes på en installation, når alle udbydere afkast installation korrekt. Men denne meddelelse betyder ikke nødvendigvis, at din ressourcegruppe er "aktiv og klar til brugerne." For eksempel en installation muligvis hente opgraderinger, venter på ressourcer, der er ikke-skabelon eller installere komplekse skriftsprog. Ressourcestyring ved ikke, når disse opgaver fuldført, fordi de ikke er aktiviteter, der registrerer en udbyder. I disse tilfælde kan det være stykke tid, før dine ressourcer er klar til brug af virkelige verden. Som et resultat, kan du forvente, at status for installation lykkes stykke tid, før din installation kan bruges.

Du kan forhindre Azure i rapportering installation succes, men ved at oprette et brugerdefineret script på den brugerdefinerede skabelon. Scriptet ved, hvordan du kan overvåge hele installationen for hele systemet parathed og returnerer korrekt, når brugere kan interagere med hele installationen. Hvis du vil sikre dig, er dit lokalnummer sidst til at køre, bruge egenskaben **dependsOn** i din skabelon.

## <a name="next-steps"></a>Næste trin

- Få vist [overvågningslog operationer med ressourcestyring](resource-group-audit.md)for at få mere for at vide om overvågning handlinger.
- Se [få vist installationshandlinger](resource-manager-troubleshoot-deployments-portal.md)for at få mere for at vide om handlinger til at bestemme fejlene under installation.
