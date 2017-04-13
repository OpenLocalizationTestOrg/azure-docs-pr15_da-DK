<properties
    pageTitle="Oprette en Windows virtuel maskine overvågning og diagnosticering ved hjælp af Azure ressourcestyring skabelon | Microsoft Azure"
    description="Bruge en Azure ressource manager skabelon til at oprette en ny Windows virtuel maskine med Azure diagnosticering filtypenavn."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="sbtron"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="saurabh"/>

# <a name="create-a-windows-virtual-machine-with-monitoring-and-diagnostics-using-azure-resource-manager-template"></a>Oprette en Windows virtuel maskine overvågning og diagnosticering ved hjælp af Azure ressourcestyring skabelon

Filtypenavnet Azure diagnosticering indeholder overvågning og diagnosticeringsfunktioner på en Windows-baseret Azure virtuelt. Du kan aktivere disse funktioner på den virtuelle maskine ved at medtage filtypenavnet som en del af skabelonen azure ressource manager. Se [Redigering Azure ressourcestyring skabeloner med VM](virtual-machines-windows-extensions-authoring-templates.md) for flere oplysninger om herunder ethvert lokalnummer som en del af et virtuelt skabelon. I denne artikel beskrives, hvordan du kan føje filtypenavnet Azure diagnosticering til en windows virtuelt skabelon.  
  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Tilføje filtypenavnet Azure diagnosticering til VM ressource definitionen 

For at aktivere filtypenavnet diagnosticering på en Windows virtuel maskine, skal du tilføje filtypenavnet som en VM ressource i skabelonen ressource manager.

For en enkelt ressourceleder baseret virtuelt føje lokalnummer konfigurationen til matrixen *ressourcer* til den virtuelle maskine: 

    "resources": [
                {
                    "name": "Microsoft.Insights.VMDiagnosticsSettings",
                    "type": "extensions",
                    "location": "[resourceGroup().location]",
                    "apiVersion": "2015-06-15",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
                    ],
                    "tags": {
                        "displayName": "AzureDiagnostics"
                    },
                    "properties": {
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "type": "IaaSDiagnostics",
                        "typeHandlerVersion": "1.5",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
                        },
                        "protectedSettings": {
                            "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                            "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                        }
                    }
                }
            ]


En anden almindelige konferencen er Tilføj lokalnummer konfiguration på noden ressourcer i roden af skabelonen i stedet for at definere den under den virtuelle maskine ressourcer node. Med denne metode har du udtrykkeligt angiver en hierarkisk relation mellem filtypenavnet og den virtuelle maskine med værdierne, *navn* og *type* . Eksempel: 
  
    "name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
    "type": "Microsoft.Compute/virtualMachines/extensions",

Filtypenavnet er altid knyttet til den virtuelle maskine, kan du enten direkte definere den under den virtuelle maskine ressource node direkte eller definere den på niveauet for base og bruge hierarkiske Navnekonventionen for at knytte den til den virtuelle maskine.

Filtypenavne konfigurationen er angivet i egenskaben *extensionProfile* for *VirtualMachineProfile*for virtuelt skala sæt.
   
Egenskaben *publisher* med værdien af **Microsoft.Azure.Diagnostics** og egenskaben *type* med værdien af **IaaSDiagnostics** identificerer entydigt filtypenavnet Azure diagnosticering.

Værdien af egenskaben *name* kan bruges til at referere til filtypenavnet i ressourcegruppen. Indstilling af det specifikt til **Microsoft.Insights.VMDiagnosticsSettings** , at det skal identificeres nemt ved den Azure klassisk portalen portalen at sikre, at de overvågning diagrammer vises korrekt i portalen Azure klassisk.

*TypeHandlerVersion* angiver versionen af filtypenavnet, du vil bruge. Angive *autoUpgradeMinorVersion* sikrer underordnede version til **Sand** , at du får den seneste underordnede version af det filtypenavn, der er tilgængelig. Det anbefales, at du altid angive *autoUpgradeMinorVersion* skal altid være **sandt** , så du altid få adgang til bruger filtypenavnet seneste tilgængelige diagnosticering med de nye funktioner og fejlrettelser. 

Elementet *Indstillinger* indeholder konfigurationer egenskaberne for det filtypenavn, kan angive og læse tilbage fra filtypenavnet (også kaldet offentlige konfiguration). Egenskaben *xmlcfg* indeholder XML-baserede konfiguration for loggene diagnosticering tællere i ydeevne osv., som indsamles af diagnosticering agent. Få flere oplysninger om XML-skemaet selve [Diagnosticering konfiguration skema](https://msdn.microsoft.com/library/azure/dn782207.aspx) . En almindelig praksis er til at gemme den faktiske XML-konfiguration som en variabel i skabelonen Azure ressourcestyring og derefter sammenkæde og base64 kodes dem til at angive værdien for *xmlcfg*. I afsnittet på [diagnosticering konfigurationsvariabler](#diagnostics-configuration-variables) til at få flere oplysninger om, hvordan du gemmer xml i variabler. Egenskaben *storageAccount* angiver navnet på kontoen lagerplads diagnosticering data skal overføres. 
 
Egenskaberne i *protectedSettings* (også kaldet privat konfiguration) kan angives, men ikke kan læses tilbage efter indstilles. Hvilke kun skrive *protectedSettings* gør det nyttige til lagring af hemmeligheder som kontonøgle lagerplads hvor diagnosticering data skal skrives.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Angive diagnosticering lagerplads konto som parametre 

Ovenstående diagnosticering lokalnummer json kodestykke antages det to parametre *existingdiagnosticsStorageAccountName* og *existingdiagnosticsStorageResourceGroup* til at angive kontoen diagnosticering lagerplads, hvor diagnosticering data skal gemmes. Angive kontoen diagnosticering lagerplads, som en parameter gør det nemt at ændre kontoen, diagnosticering lagerplads på tværs af forskellige miljøer fx du overveje at bruge en anden diagnosticering lagerplads konto for at teste og et andet navn for din installationen produktionsmiljø.  

        "existingdiagnosticsStorageAccountName": {
            "type": "string",
            "metadata": {
        "description": "The name of an existing storage account to which diagnostics data will be transfered."
            }        
        },
        "existingdiagnosticsStorageResourceGroup": {
            "type": "string",
            "metadata": {
        "description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
            }
        }

Er det bedste fremgangsmåde at angive en diagnosticering lagerplads konto i en anden ressourcegruppe end ressourcegruppen for den virtuelle maskine. En ressourcegruppe kan betragtes som en installation enhed med sin egen levetid, et virtuelt kan installeres og genaktiveres som nye konfigurationer opdateringer er gjort det til den, men du vil fortsætte med at gemme diagnosticering dataene i den samme konto lagerplads på tværs af disse virtuelt installationer. Har du kontoen lager i en anden ressource gør det muligt for kontoen lagerplads til at acceptere data fra forskellige virtuelt installationer, hvilket gør det nemt at foretage fejlfinding af problemer på tværs af de forskellige versioner.

>[AZURE.NOTE] Hvis du opretter en windows virtuelt skabelon fra Visual Studio kan lagerplads standardkontoen være indstillet til at bruge den samme lagerplads-konto, hvor den virtuelle maskine Virtuelle er overført. Dette er at forenkle indledende installation af VM. Igen skal du angive skabelonen, hvis du vil bruge en anden lagerplads-konto, der kan overføres i som en parameter. 

## <a name="diagnostics-configuration-variables"></a>Diagnosticering konfigurationsvariabler
 
Ovenstående diagnosticering lokalnummer json kodestykke definerer en *accountid* variabel for at forenkle få kontonøgle lagerplads til diagnosticering opbevaring:   
    
    "accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"


Egenskaben *xmlcfg* til diagnosticering udvidelsen defineres ved hjælp af flere variabler, der sammenføjes sammen. Værdierne i disse variabler er i xml, så de skal udelades korrekt, når du angiver json variabler.

Nedenfor beskrives diagnosticering konfiguration xml, der indsamles af standard system niveau ydeevne tællere sammen med nogle diagnosticering infrastruktur logfiler og windows-hændelseslogge. Det har været escape-tegn og formateret korrekt, så konfigurationen kan direkte indsættes i sektionen variabler af din skabelon. Se [Diagnosticering konfiguration skema](https://msdn.microsoft.com/library/azure/dn782207.aspx) for en mere human læsbare eksempel XML-konfiguration.
    
        "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
        "wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
        "wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
        "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"

Noden målepunkter definition xml i ovenstående konfigurationen er et vigtigt konfiguration element, som den definerer, hvordan de definerede tidligere i xml i *PerformanceCounter* node tællere i ydeevne, som sammenlagt og gemt. 

> [AZURE.IMPORTANT] Disse målepunkter drive overvågning diagrammer og beskeder i portalen Azure.  Noden **målepunkter** med *resourceID* og **MetricAggregation** skal medtages i sektionen diagnosticering konfiguration for din VM, hvis du vil have vist VM overvågningsdata i portalen Azure. 

Følgende er et eksempel på XML-filen til målepunkter definitioner: 

        <Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>

Attributten *resourceID* identificerer entydigt virtuel maskine i dit abonnement. Sørg for at bruge funktionerne subscription() og resourceGroup(), så skabelonen opdaterer automatisk disse værdier, der er baseret på abonnement og ressourcegruppe du installerer til.

Hvis du opretter flere virtuelle maskiner i en løkke derefter nødt du til at udfylde *resourceID* værdi med en copyIndex() funktion til at skelne de enkelte individuelle VM korrekt. Værdien *xmlCfg* kan blive opdateret, for at understøtte denne på følgende måde:  

    "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 

MetricAggregation værdien af *PT1H* og *PT1M* angiver en sammenlægning i minuttet og en sammenlægning over en time.

## <a name="wadmetrics-tables-in-storage"></a>WADMetrics tabeller i lagerplads

Målepunkter konfigurationen ovenfor, genererer tabeller i kontoen diagnosticering lagerplads med følgende navngivningskonventioner:

- **WADMetrics** : Standard præfiks for alle WADMetrics tabeller
- **PT1H** eller **PT1M** : betyder, at tabellen indeholder aggregere data over 1 time eller 1 minut
- **P10D** : Angiver, at tabellen skal indeholde data i 10 dage fra når tabellen i gang ved at indsamle data
- **V2S** : strengkonstant
- **ååååmmdd** : den dato, hvor tabellen er startet indsamling af data

Eksempel: *WADMetricsPT1HP10DV2S20151108* skal indeholde målepunkter data sammenlagt over en time for 10 dage, der starter på 11-november-2015    

Alle tabeller, WADMetrics skal indeholde følgende kolonner:

- **PartitionKey**: partitionkey er opbygget baseret på *resourceID* værdien, der entydigt identificerer VM ressourcen. for f.eks.: 002Fsubscriptions:<subscriptionID>: 002FresourceGroups:002F<ResourceGroupName>: 002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
- **RowKey** : følger formatet <Descending time tick>:<Performance Counter Name>. Faldende tid aksemærker beregningen er maksimale tid aksemærker minus tidspunktet for begyndelsen af perioden sammenlægning. F.eks. Hvis perioden eksempel er startet på 10-november-2015 og 00:00Hrs UTC derefter beregningen ville være: DateTime.MaxValue.Ticks - (nye DateTime(2015,11,10,0,0,0,DateTimeKind.Utc). Akser). For hukommelse tilgængelige byte ydeevne tæller tasten række ser ud: 2519551871999999999__:005CMemory:005CAvailable:0020 byte
- **CounterName** : er navnet på tælleren ydeevne. Dette svarer til *counterSpecifier* defineres i XML-config.
- **Maksimale** : den maksimale værdi af tælleren ydeevne i sammenlægning perioden.
- **Mindste** : den mindste værdi for tælleren ydeevne i sammenlægning perioden.
- **Samlede** : summen af alle værdierne for tælleren ydeevne rapporteret i sammenlægning perioden.
- **Antal** : det samlede antal værdier, rapporteret for tælleren ydeevne.
- **Gennemsnitlig** : MIDDEL (samlet/antal) værdien af tælleren ydeevne i sammenlægning perioden.


## <a name="next-steps"></a>Næste trin

- Se [201-vm-overvågning-diagnosticering-udvidelse](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension) til en komplet eksempel på en skabelon på en Windows virtuelle maskine med diagnosticering lokalnummer   
- Installere ressource manager skabelonen ved hjælp af [Azure PowerShell](virtual-machines-windows-ps-manage.md) eller [Azure kommandolinjen](virtual-machines-linux-cli-deploy-templates.md)
- Få flere oplysninger om [redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md)







