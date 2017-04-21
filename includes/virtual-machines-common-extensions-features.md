



Få mere at vide om VM supportmedarbejdere, og hvordan de fungerer for at understøtte VM filtypenavne for at se [VM Agent og VM filtypenavne oversigt](../articles/virtual-machines/virtual-machines-windows-classic-manage-extensions.md).

##<a name="azure-vm-extensions"></a>Azure VM filtypenavne

VM filtypenavne implementere de fleste af de kritiske funktioner, du vil bruge med dine FOS, herunder grundlæggende funktioner som nulstilling af adgangskoder, konfigurere RDP, og mange andre. Da nye udvidelser er tilføjet hele tiden, antallet af mulige funktioner din FOS understøtter i Azure fortsætter med at øge. Flere grundlæggende VM udvidelser installeres som standard, når du opretter din VM fra Billedgalleri, herunder **IaaSDiagnostics** (aktuelt Windows FOS kun), **VMAccess**og **BGInfo** (også i øjeblikket kun Windows). Men ikke alle udvidelser er implementeret på både Windows og Linux på et bestemt tidspunkt, på grund af konstant strømmen af funktionsopdateringer og nye udvidelser.

##<a name="connectivity-and-basic-management"></a>Forbindelsen og grundlæggende administration

Følgende filtypenavne er meget vigtigt for at aktivere, genaktivere, eller deaktivere grundlæggende forbindelse med din FOS, når de oprettes og kører.

|VM filtypenavn|Beskrivelse af funktion|Få mere at vide
|---|---|---|
|VMAccessAgent (Windows)|Oprette, opdatere og nulstille brugeroplysninger og RDP forbindelse konfigurationer.|[Windows](../articles/virtual-machines/virtual-machines-windows-classic-extensions-customscript.md)
|VMAccessForLinux (Linux)|Oprette, opdatere og nulstille brugeroplysninger og SSH forbindelse konfigurationer.|[Linux](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)

##<a name="deployment-and-configuration-management"></a>Installation og af konfigurationsadministration

Følgende filtypenavne understøtter forskellige typer af installation og konfiguration af administrationsscenarier og funktioner. Se også afsnittet om VM handlinger og administration under.

|VM filtypenavn|Beskrivelse af funktion|Få mere at vide|
|---|---|---|
|**MSEnterpriseApplication**|Implementerer funktioner til understøttelse af Windows System Center.|[System Center 2012 R2 virtuelt roller](http://social.technet.microsoft.com/wiki/contents/articles/18274.system-center-2012-r2-virtual-machine-role-authoring-guide-resource-extension-package.aspx)|
|**Installere ottearmede blæksprutter** (DTK lokalnummer-baseret)|Understøtter automatiseret installation af ASP.NET web-programmer og Windows Services til udvikling, test- og miljøer.|[Introduktion til ottearmede blæksprutter installere](http://docs.octopusdeploy.com/display/OD/Getting%20started)|
|**Visual Studio Release Manager** (DTK lokalnummer-baseret)|Understøtter fortløbende-installation med Visual Studio.|[Automatisere-installationer med versionsstyring](https://msdn.microsoft.com/Library/vs/alm/Release/overview)|
|**CentosChefClient**|||
|**ChefClient**|Opretter en kok klient til Windows. (Kan også bruge filtypenavnet DTK nedenfor).|[Kok og Microsoft Azure](https://www.getchef.com/solutions/azure/)|
|**LinuxChefClient**|||
|**DockerExtension**|Installerer Docker daemonen at understøtte remote Docker kommandoer.|[Hvordan du bruger filtypenavnet Docker virtuelt](../articles/virtual-machines/virtual-machines-linux-dockerextension.md) Finde mere omfattende oplysninger i [Brugervejledning til Docker VM lokalnummer](https://github.com/Azure/azure-docker-extension/blob/master/README.md)|
|**DTK**|Udvidelse til PowerShell DTK (ønskede stat konfiguration).|[Azure PowerShell DTK (beskedteksten tilstand konfiguration) lokalnummer](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx)|
|**PuppetEnterpriseAgent**|Implementerer Puppet Enterprise-funktionerne. |[Puppet på Azure](http://puppetlabs.com/solutions/microsoft)|
|**CustomScriptExtension** (Windows) **CustomScriptForLinux** (Linux)|Aktiverer brugerdefinerede scripts på VM når som helst: Start eller i levetid.|[Brugerdefineret Script lokalnummer](../articles/virtual-machines/virtual-machines-windows-classic-extensions-customscript.md) | [Linux](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript)|
|**AzureCATExtensionHandler**|Forbruger diagnosticering dataene der indsamles via **IaaSDiagnostics** og par andre datakilder som [Azure Lagringsmål analyser](https://msdn.microsoft.com/library/azure/hh343270.aspx) og omdanner det til et aggregeret datasæt, der er relevante for SAP Host proces til at bruge|[Azure skærpet overvågning til SAP](https://azure.microsoft.com/blog/2014/06/04/azure-enhanced-monitoring-for-sap/)|

##<a name="security-and-protection"></a>Sikkerhed og beskyttelse

Filtypenavne i dette afsnit giver kritiske sikkerhedsfunktioner for din Azure FOS.

|VM filtypenavn|Beskrivelse af funktion|Få mere at vide|
|---|---|---|
|**CloudLinkSecureVMWindowsAgent**|Giver muligheden for at kryptere deres virtuelt data på et med flere lejer delte infrastruktur og fuldt kontrol over kryptering taster til deres krypterede data på Azure lagerplads infrastruktur Microsoft Azure-kunder.|[Sikring af Microsoft Azure virtuelle maskiner udnytte BitLocker og oprindelige OS kryptering](http://www.cloudlinktech.com/azure)|
|**McAfeeEndpointSecurity**|Beskytter dine VM mod skadelig software.|[McAfee](https://www.mcafeeasap.com/MarketingContent/default.aspx)|
|**TrendMicroDSA**|Aktiverer Trendmicro's Deep sikkerhed platform support for at give uautoriseret adgang registrering og forebyggelse, firewall, antimalware-, web omdømme, log inspektion og integritet overvågning.|[Sådan installerer og konfigurerer Trend Micro Deep Security som en tjeneste på en Azure VM](../articles/virtual-machines/virtual-machines-windows-classic-install-trend.md)|
|**PortalProtectExtension**|Beskytter mod trusler dit Microsoft SharePoint-miljø.|[Sikring af SharePoint-installationen på Azure](http://blog.trendmicro.com/securing-sharepoint-deployment-azure/)|
|**IaaSAntimalware**|Microsoft Antimalwareprogram til Azure Cloud Services og virtuelle maskiner er en beskyttelse i realtid funktion, der hjælper med at identificere og fjerne virus, spyware og anden ondsindet software, med konfigurerbar en besked, når kendte skadelig eller uønsket software forsøger at installere sig selv eller køre på systemet.|[Antimalwareprogram til Azure Cloud Services og virtuelle maskiner](../articles/azure-security-antimalware.md)|
|**SymantecEndpointProtection**|Symantec Endpoint Protection 12.1.4 giver mulighed for sikkerhed og ydeevne på tværs af fysiske og virtuelle systemer.|[Sådan installerer og konfigurerer Symantec Endpoint Protection på en Azure VM](../articles/virtual-machines/virtual-machines-windows-classic-install-symantec.md)

##<a name="vm-operations-and-management"></a>VM handlinger og administration

Understøtter funktioner til administration af almindelige handlinger og funktionsmåde. Se også afsnittet om installation og konfiguration af administration ovenfor.

|**VM filtypenavn**|Beskrivelse af funktion|Få mere at vide|
|---|---|---|
|**AzureVmLogCollector**|Du kan bruge **AzureVMLogCollector** lokalnummer efter behovet for perfom enkeltstående samling af logfiler fra en eller flere skyen Service FOS (fra både web roller og arbejder roller) og overføre de indsamlede filer til en Azure lagerplads konto – alt sammen uden at fra en fjernplacering logger på en af FOS. |[AzureLogCollector udvidelse](../articles/virtual-machines/virtual-machines-windows-log-collector-extension.md)|
|**IaaSDiagnostics**|Gør det muligt, deaktiveres, og konfigurerer Azure diagnosticering og bruges også ved **AzureCATExtensionHandler** til at understøtte SAP overvågning.|[Microsoft Azure virtuelt overvågning med Azure diagnosticering lokalnummer](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/)|
|**OSPatchingForLinux**|Administratoren Azure VM kan automatisere VM OS opdateres med de tilpassede konfigurationer. Du kan bruge filtypenavnet OSPatching til at konfigurere OS opdateringer til din virtuelle maskiner, herunder: angive, hvor ofte og når du installerer OS programrettelser, angive hvad programrettelser til at installere og konfigurere funktionen genstart efter opdateringer|[OS rettelse lokalnummer blogindlæg](https://azure.microsoft.com/blog/2014/10/23/automate-linux-vm-os-updates-using-ospatching-extension/). Se også den vigtige oplysninger om og kilde på GitHub på [OS rettelse filtypenavn](https://github.com/Azure/azure-linux-extensions).|

##<a name="developing-and-debugging"></a>Udvikling og fejlfinding

Disse VM udvidelser er angivet her til fuldstændighed, som de yder support til Visual Studio-relaterede funktioner og er ikke beregnet til at blive brugt direkte.

|VM filtypenavn|Beskrivelse af funktion|Få mere at vide|
|---|---|---|
|**VS14CTPDebugger**|Understøtter ekstern fejlfinding fra VS ved hjælp af Azure SDK 2.4|[Fejlfinding i Visual Studio Remote](https://msdn.microsoft.com/library/y7f5zaaa.aspx)|
|**VS2013Debugger**|Understøtter ekstern fejlfinding fra VS ved hjælp af Azure SDK 2.4||
|**VS2012Debugger**|Understøtter ekstern fejlfinding fra VS ved hjælp af Azure SDK 2.4||
|**RemoteDebugVS14CTP**|Understøtter ekstern fejlfinding fra VS ved hjælp af Azure SDK 2.3||
|**RemoteDebugVS2013**|Understøtter ekstern fejlfinding fra VS ved hjælp af Azure SDK 2.3||
|**RemoteDebugVS2012**|Understøtter ekstern fejlfinding fra VS ved hjælp af Azure SDK 2.3||
|**WebDeployForVSDevTest**|Installerer og konfigurerer IIS og Web installere på Windows Server. Fjerne eller deaktivere den understøttes ikke.|

##<a name="miscellaneous-features"></a>Diverse funktioner

Disse udvidelser understøttelse af andre VM funktioner, der kan være nyttige.

|VM filtypenavn|Beskrivelse af funktion|Få mere at vide|
|---|---|---|
|**BGInfo**|Viser et konsoliderede billede af nyttige serveroplysninger på skrivebordet, når du bruger RDP.|[BGInfo udvidelse](https://msdn.microsoft.com/library/mt589195.aspx)|
|**HpcVmDrivers**|Installerer, konfigurerer og vedligeholder enhedsdrivere remote direkte hukommelse access (RDMA) netværk på en størrelse A8 eller A9 VM, der kører Windows Server 2012 R2 eller Windows Server 2012. Aktiverer grupperet A8 eller A9 FOS for at bruge RDMA netværk, når du kører parallelle MPI programmer.|[Om A8, A9, A10 og A11 Beregn-intensivt forekomster](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md)

