<properties
   pageTitle="Introduktion til Azure batchen CLI | Microsoft Azure"
   description="Få en hurtig introduktion til kommandoerne batchen i Azure CLI til administration af Azure batchen serviceressourcer"
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="big-compute"
   ms.date="09/30/2016"
   ms.author="marsma"/>

# <a name="get-started-with-azure-batch-cli"></a>Introduktion til Azure batchen CLI

På tværs af platforme Azure kommandolinjen (Azure CLI) gør det muligt at administrere din batchen konti og ressourcer som grupper, job og opgaver, inden Linux, Mac og Windows kommandoen er kommet. Du kan udføre og scripts mange af de samme opgaver, du kan udføre med batchen API'er, Azure portal og batchen PowerShell-cmdlet'er med CLI Azure.

I denne artikel er baseret på Azure CLI version 0.10.5.

## <a name="prerequisites"></a>Forudsætninger

* [Installer Azure CLI](../xplat-cli-install.md)

* [Oprette forbindelse Azure CLI til abonnementet Azure](../xplat-cli-connect.md)

* Skifte til **ressourcestyring tilstand**:`azure config mode arm`

>[AZURE.TIP] Vi anbefaler, at du opdaterer din Azure CLI installation ofte for at kunne udnytte tjenesten opdateringer og forbedringer.

## <a name="command-help"></a>Kommandoen Hjælp

Du kan få vist Hjælp-tekst for alle kommandoer i Azure CLI ved at tilføje `-h` som den eneste mulighed efter kommandoen. Eksempel:

* Til at få hjælp til de `azure` kommando skal du angive:`azure -h`
* For at få en liste over alle batchen kommandoer i CLI skal du bruge:`azure batch -h`
* For at få hjælp til at oprette en batchen konto skal du angive:`azure batch account create -h`

Når du er i tvivl, skal du bruge den `-h` kommandolinjen mulighed for at få hjælp til Azure CLI kommandoer.

## <a name="create-a-batch-account"></a>Oprette en Batch-konto

Format:

    azure batch account create [options] <name>

Eksempel:

    azure batch account create --location "West US"  --resource-group "resgroup001" "batchaccount001"

Opretter en ny batchen konto med de angivne parametre. Du skal angive mindst en placering, ressourcegruppe og kontonavn. Hvis du ikke allerede har en ressourcegruppe, oprette en ved at køre `azure group create`, og Angiv en af de Azure områder (såsom "Vest os") for den `--location` indstilling. Eksempel:

    azure group create --name "resgroup001" --location "West US"

> [AZURE.NOTE] Kontonavnet batchen skal være entydig i området Azure kontoen oprettes. Det kan kun indeholde små alfanumeriske tegn og skal være 3-24 tegn. Du kan ikke bruge specialtegn som `-` eller `_` i batchen kontonavne.

### <a name="linked-storage-account-autostorage"></a>Sammenkædede lagerplads konto (autostorage)

(Valgfrit) kan du sammenkæde en **generelle formål** lagerplads konto til kontoen batchen når du opretter den. Funktionen [programpakker](batch-application-packages.md) i batchen bruger blob-lager i en sammenkædet generelle formål lagerplads-konto, ligesom biblioteket [Batchen fil konventioner .NET](batch-task-output.md) . Disse valgfrie funktioner hjælper dig med at implementere de programmer, køres batchopgaverne og bevare dataene de landbrugsprodukter.

Hvis du vil sammenkæde en eksisterende Azure-lager-konto til en ny batchen-konto, når du opretter den, angive den `--autostorage-account-id` indstilling. Denne indstilling kræver fuldstændige ressource-ID for kontoen lagerplads.

Først skal vise kontoen lagerplads detaljer:

    azure storage account show --resource-group "resgroup001" "storageaccount001"

Brug værdien fra **URL-adressen** til den `--autostorage-account-id` indstilling. URL-værdien starter med "/ abonnementer /", der indeholder dit abonnement-ID og ressource stien til lagerplads konto:

    azure batch account create --location "West US"  --resource-group "resgroup001" --autostorage-account-id "/subscriptions/8ffffff8-4444-4444-bfbf-8ffffff84444/resourceGroups/resgroup001/providers/Microsoft.Storage/storageAccounts/storageaccount001" "batchaccount001"

## <a name="delete-a-batch-account"></a>Slette en batchen konto

Format:

    azure batch account delete [options] <name>

Eksempel:

    azure batch account delete --resource-group "resgroup001" "batchaccount001"

Sletter den angivne Batch-konto. Når du bliver bedt om det, kan du bekræfte at du vil fjerne kontoen (konto fjernelse kan gå et stykke tid at fuldføre).

## <a name="manage-account-access-keys"></a>Administrer konto access-taster

Hurtigtast til at [oprette og redigere ressourcer](#create-and-modify-batch-resources) skal du i kontoen batchen.

### <a name="list-access-keys"></a>Liste over access-taster

Format:

    azure batch account keys list [options] <name>

Eksempel:

    azure batch account keys list --resource-group "resgroup001" "batchaccount001"

Viser konto nøglerne for den angivne Batch-konto.

### <a name="generate-a-new-access-key"></a>Opret en ny nøgle i access

Format:

    azure batch account keys renew [options] --<primary|secondary> <name>

Eksempel:

    azure batch account keys renew --resource-group "resgroup001" --primary "batchaccount001"

Genopretter den angivne kontonøgle for den angivne Batch-konto.

## <a name="create-and-modify-batch-resources"></a>Oprette og redigere batchen ressourcer

Du kan bruge Azure CLI til at oprette, læse, opdatere, og slette grupper (CRUD) batchen ressourcer, beregne noder, job og opgaver. Disse CRUD handlinger kræver din batchen kontonavn, hurtigtast og slutpunkt. Du kan angive dem med den `-a`, `-k`, og `-u` parametre eller angive [miljøvariabler](#credential-environment-variables) som CLI bruger automatisk (hvis udfyldt).

### <a name="credential-environment-variables"></a>Legitimationsoplysninger miljøvariabler

Du kan angive `AZURE_BATCH_ACCOUNT`, `AZURE_BATCH_ACCESS_KEY`, og `AZURE_BATCH_ENDPOINT` miljøvariabler i stedet for at angive `-a`, `-k`, og `-u` indstillinger på kommandolinjen for hver kommando, du udfører. Batchen CLI bruger disse variabler (Hvis angivet) så kan du udelade den `-a`, `-k`, og `-u` indstillinger. I resten af denne artikel antages det brug af variablerne miljø.

>[AZURE.TIP] Liste over dine nøgler med `azure batch account keys list`, og få vist firmaets slutpunktet med `azure batch account show`.

### <a name="json-files"></a>JSON-filer

Når du opretter batchen ressourcer som grupper og job, kan du angive en JSON-fil, der indeholder den nye ressource konfiguration i stedet for at overføre dens parametre som kommandolinjeparametre. Eksempel:

`azure batch pool create my_batch_pool.json`

Mens du kan udføre mange ressource oprettelse af handlinger ved hjælp af kun kommandolinjeparametre, kræver nogle funktioner en JSON-formateret fil, der indeholder ressourcedetaljer. For eksempel skal du bruge en JSON-fil, hvis du vil angive ressourcefiler for en start-opgave.

For at finde den JSON, der er nødvendige for at oprette en ressource skal referere til [batchen REST-API reference] [ rest_api] dokumentation på MSDN. Hver "Tilføj *ressourcetype"*emne indeholder eksempel JSON til oprettelse af den ressource, som du kan bruge som skabeloner til dine JSON-filer. For eksempel JSON til oprettelse af puljen kan findes i [Tilføj en gruppe til en konto][rest_add_pool].

>[AZURE.NOTE] Hvis du angiver en JSON-fil, når du opretter en ressource, ignoreres alle andre parametre, du angiver på kommandolinjen for ressourcen.

## <a name="create-a-pool"></a>Oprette en gruppe

Format:

    azure batch pool create [options] [json-file]

Eksempel (Virtual Machine konfiguration):

    azure batch pool create --id "pool001" --target-dedicated 1 --vm-size "STANDARD_A1" --image-publisher "Canonical" --image-offer "UbuntuServer" --image-sku "14.04.2-LTS" --node-agent-id "batch.node.ubuntu 14.04"

Eksempel (konfiguration af Cloud Services):

    azure batch pool create --id "pool002" --target-dedicated 1 --vm-size "small" --os-family "4"

Opretter en samling af Beregn noder i tjenesten batchen.

Som nævnt i [batchen funktionsoversigt](batch-api-basics.md#pool), har du to muligheder, når du vælger et operativsystem for knuderne i din gruppe: **Virtuelt konfiguration** og **Konfiguration af Cloud Services**. Brug den `--image-*` indstillinger for at oprette virtuelt konfiguration grupper og `--os-family` til at oprette konfiguration af Cloud Services grupper. Du kan ikke angive begge `--os-family` og `--image-*` indstillinger.

Du kan angive puljen [-programpakker](batch-application-packages.md) og kommandolinjen til en [start opgave](batch-api-basics.md#start-task). Hvis du vil angive ressourcefiler for start opgaven, skal du dog i stedet bruge en [JSON-fil](#json-files).

Slette en gruppe med:

    azure batch pool delete [pool-id]

>[AZURE.TIP] Se på [listen over virtuelt billeder](batch-linux-nodes.md#list-of-virtual-machine-images) for værdier, der er relevante den `--image-*` indstillinger.

## <a name="create-a-job"></a>Oprette en sag

Format:

    azure batch job create [options] [json-file]

Eksempel:

    azure batch job create --id "job001" --pool-id "pool001"

Tilføjer et job til batchen kontoen og angiver den gruppe, som dens opgaver udføre.

Slette en sag med:

    azure batch job delete [job-id]

## <a name="list-pools-jobs-tasks-and-other-resources"></a>Liste over grupper, job, opgaver og andre ressourcer

Hvert Batch ressourcetype understøtter en `list` kommando, som forespørger kontoen batchen og viser en liste over ressourcer af den pågældende type. For eksempel kan du angive grupper i din konto og opgaver i en sag:

    azure batch pool list
    azure batch task list --job-id "job001"

### <a name="listing-resources-efficiently"></a>Listen over ressourcer effektivt

For hurtigere forespørgsler, kan du angive indstillinger for **Vælg**, **filtrere**og **udvide** -delsætning for `list` handlinger. Brug disse indstillinger til at begrænse mængden data, der returneres af tjenesten batchen. Fordi al filtrering forekommer serversiden, krydser kun de data, du er interesseret i netværket. Brug disse delsætninger til at gemme båndbredde (og dermed tid) når du udfører handlinger på listen.

For eksempel returnerer dette kun de grupper, hvis id'er starter med "renderTask":

    azure batch task list --job-id "job001" --filter-clause "startswith(id, 'renderTask')"

Batchen CLI understøtter alle tre delsætninger: understøttes af tjenesten batchen:

* `--select-clause [select-clause]`Returnere et undersæt af egenskaber for de enkelte objekter
* `--filter-clause [filter-clause]`Returnere kun de enheder, der svarer til det angivne OData-udtryk
* `--expand-clause [expand-clause]`Få objektoplysninger i et enkelt underliggende RESTEN opkald. Delsætningen Udvid understøtter kun den `stats` egenskab på nuværende tidspunkt.

Oplysninger om de tre delsætninger: og ydeevne listen forespørgsler med dem, finder du [forespørge tjenesten Azure batchen effektivt](batch-efficient-list-queries.md).

## <a name="application-package-management"></a>Pakke programadministration

Programpakker er en forenklet måde at implementere programmer til Beregn knuderne i dine grupper. Du kan overføre programpakker med CLI Azure, administrere pakke versioner, og Slet pakker.

Sådan oprettes et nyt program og tilføjer en Pakkeversion:

**Opret** et program:

    azure batch application create "resgroup001" "batchaccount001" "MyTaskApplication"

**Tilføj** en programpakke:

    azure batch application package create "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" package001.zip

**Aktivér** pakken:

    azure batch application package activate "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" zip

Angiv den **standardversionen** for programmet:

    azure batch application set "resgroup001" "batchaccount001" "MyTaskApplication" --default-version "1.10-beta3"

### <a name="deploy-an-application-package"></a>Installere en programpakke

Når du opretter en ny gruppe, kan du angive en eller flere programpakker til installation. Når du angiver en pakke på klokkeslættet for oprettelsen af puljen, og installeres den i hver node som node joinforbindelser puljen. Pakker installeres også, når en node genstartet eller reimaged.

Angiv den `--app-package-ref` indstilling, når du opretter en gruppe for at installere en programpakke på den gruppe noder, som de deltager puljen. Den `--app-package-ref` indstilling accepterer en semikolonsepareret liste over programmet id'er skal installeres til noderne Beregn.

    azure batch pool create --pool-id "pool001" --target-dedicated 1 --vm-size "small" --os-family "4" --app-package-ref "MyTaskApplication"

Når du opretter en gruppe ved hjælp af kommandolinjeparametre, kan du ikke aktuelt angive *hvilken* programmet Pakkeversion skal installeres til noderne Beregn, for eksempel "1.10-beta3". Derfor skal du først angive et standardversionen for programmet med `azure batch application set [options] --default-version <version-id>` før du opretter puljen (se ovenfor). Du kan dog angive en Pakkeversion til puljen, hvis du bruger en [JSON-fil](#json-files) i stedet for kommandolinjeindstillinger, når du opretter puljen.

Du kan finde flere oplysninger om programpakker i [programmet installation med Azure batchen programpakker](batch-application-packages.md).

>[AZURE.IMPORTANT] [Kæde en Azure-lager konto](#linked-storage-account-autostorage) skal du til kontoen batchen bruge programpakker.

### <a name="update-a-pools-application-packages"></a>Opdatere en samling programpakker

Hvis du vil opdatere de programmer, der er tildelt til en eksisterende gruppe, udstede den `azure batch pool set` kommando med den `--app-package-ref` indstilling:

    azure batch pool set --pool-id "pool001" --app-package-ref "MyTaskApplication2"

Hvis du vil installere programpakken for at beregne noder allerede findes i en eksisterende gruppe, skal du genstarte eller reimage disse noder:

    azure batch node reboot --pool-id "pool001" --node-id "tvm-3105992504_1-20160930t164509z"

>[AZURE.TIP] Du kan få en liste over knuderne i en gruppe, sammen med deres node-id'er, med `azure batch node list`.

Husk på, skal du allerede har konfigureret programmet med en standardversion af før installation (`azure batch application set [options] --default-version <version-id>`).

## <a name="troubleshooting-tips"></a>Tip til fejlfinding

Dette afsnit er beregnet til at give dig ressourcer, der skal bruges, når fejlfinding af problemer med Azure CLI. Den nødvendigvis løse ikke problemerne, men det kan hjælpe dig med at indskrænke årsagen og peg dig for at hjælpe ressourcer.

* Brug `-h` for at hente **Hjælp-tekst** til en hvilken som helst CLI kommandoen

* Brug `-v` og `-vv` at få vist **detaljeret** kommandooutput; `-vv` er "ekstra" detaljeret og viser den faktiske RESTEN mødeindkaldelser og svar. Disse parametre er praktisk til at vise fulde fejl output.

* Du kan få vist **kommandooutput som JSON** med den `--json` indstilling. For eksempel `azure batch pool show "pool001" --json` viser pool001's egenskaber i JSON-formatet. Du kan derefter kopiere og redigere denne output skal bruges i en `--json-file` (se [JSON filer](#json-files) tidligere i denne artikel).

* [Batchen forum på MSDN] [ batch_forum] er en god hjælp ressource, og overvåges tæt af batchen teammedlemmer. Sørg for at sende dit spørgsmål der, hvis du støder på problemer eller vil have hjælp til en bestemt operation.

* Ikke alle batchen ressourcehandling understøttes i øjeblikket af Azure CLI. For eksempel kan ikke du i øjeblikket angive en pakke *version* for en gruppe, kun den pakke-ID. I så fald skal du muligvis angive en `--json-file` for din kommando i stedet for ved hjælp af kommandolinjeparametre. Sørg for at være opdateret med den seneste CLI version for at fortsætte efter fremtidige forbedringer.

## <a name="next-steps"></a>Næste trin

*  Se [program-installation med Azure batchen programpakker](batch-application-packages.md) til at finde ud af, hvordan du bruger denne funktion til at administrere og installere de programmer, du udfører på batchen Beregn noder.

* Se [forespørgsel tjenesten batchen effektivt](batch-efficient-list-queries.md) efter mere om at reducere antallet af elementer og typen oplysninger, der returneres for forespørgsler til batchen.

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx