<properties
 pageTitle="HPC Pack klynge i Excel og SOA | Microsoft Azure"
 description="Komme i gang kører store Excel og SOA arbejdsmængder på en HPC Pack klynge i Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>

<tags
 ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/25/2016"
 ms.author="danlep"/>

# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Komme i gang med Excel og SOA arbejdsmængder på en HPC Pack klynge i Azure

I denne artikel beskrives, hvordan du installerer en Microsoft HPC Pack klynge på Azure virtuelle maskiner ved hjælp af en skabelon til Azure Hurtig start, eller du kan vælge en scriptet til implementering af Azure PowerShell. Computerklynge bruger Azure Marketplace VM billeder, der er udviklet til at køre Microsoft Excel eller service-rundt arkitektur (SOA) arbejdsmængder med HPC Pack. Du kan bruge klyngen til at køre enkle Excel HPC og SOA tjenester fra en lokal klientcomputer. Excel HPC tjenesterne indeholder Excel projektmappe overføre og brugerdefinerede funktioner i Excel eller brugerdefinerede funktioner.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

På et højt niveau, i følgende diagram vises HPC Pack klynge du opretter.

![HPC-klynge med noder, der kører arbejdsbelastninger, som Excel][scenario]

## <a name="prerequisites"></a>Forudsætninger

*   **Klientcomputeren** – du har brug for en Windows-baseret klientcomputer til at sende eksempel Excel og SOA job til klyngen. Du skal også en Windows-computer til at køre scriptet Azure PowerShell klynge installation (Hvis du vælger denne installationsmetode) og

*   **Azure abonnement** - Hvis du ikke har et Azure-abonnement, kan du oprette en [gratis konto](https://azure.microsoft.com/pricing/free-trial/) på blot et par minutter.

*   **Kerner kvote** - det være nødvendigt at øge kvoten kerner, især hvis du installerer flere klyngenoder med Multifacetteret VM størrelser. Hvis du bruger en skabelon til Azure Hurtig start, er kvoten kerner i ressourcestyring per Azure område. Det er tilfældet, skal du kan øge kvoten i et bestemt område. Se [Azure abonnement begrænsninger, kvoter, og begrænsninger](../azure-subscription-service-limits.md). At øge en kvote, [Åbn en anmodning om online kundesupport](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratis.

*   **Microsoft Office-licens** – Hvis du installerer Beregn noder ved hjælp af et Marketplace HPC Pack VM billede med Microsoft Excel, en 30-dages evalueringsversion af Microsoft Excel Professional Plus 2013 er installeret. Efter beregning i perioden skal du angive en gyldig licens til Microsoft Office for at aktivere Excel for at fortsætte med at køre arbejdsmængder. Se [Excel aktivering](#excel-activation) senere i denne artikel. 


## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>Trin 1. Konfigurere en HPC Pack klynge i Azure

Vi viser to muligheder for at konfigurere klyngen: første, ved hjælp af en skabelon til Azure Hurtig start og Azure portalen og andet, ved hjælp af en Azure PowerShell installation script.


### <a name="option-1-use-a-quickstart-template"></a>Indstilling 1. Brug en skabelon på Hurtig start
Brug en skabelon til Azure Hurtig start for hurtigt og nemt at implementere en HPC Pack klynge i portalen Azure. Når du åbner skabelonen i portalen, får du en simpel brugergrænseflade, hvor du angiver indstillingerne for din klynge. Her er trinnene. 

>[AZURE.TIP]Hvis du vil, kan du bruge en [Azure Marketplace skabelon](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) , der opretter en lignende klynge specifikt for arbejdsbelastninger, som Excel. Trinnene variere i følgende.

1.  Gå til [oprette HPC-klynge skabelonside på GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Hvis du vil, kan du gennemse oplysninger om skabelonen og koden.

2.  Klik på **Implementer til Azure** for at starte en installation med skabelonen i portalen Azure.

    ![Installere skabelon til Azure][github]

3.  Følg disse trin for at angive parametrene for skabelonen HPC klynge i portalen.

    en. Angive eller ændre værdierne for parametrene skabelonen på siden **parametre** . (Klik på ikonet ud for hver indstilling for oplysninger om hjælp til). Eksempelværdier vises på følgende skærmbillede. I dette eksempel oprettes en klynge med navnet *hpc01* i *hpc.local* domænet bestående af en hoved node og 2 beregne noder. Beregn knuderne er oprettet ud fra et HPC Pack VM billede, der indeholder Microsoft Excel.

    ![Angiv parametre][parameters]

    >[AZURE.NOTE]Noden hoved VM oprettes automatisk fra det [seneste Marketplace billede](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) af HPC Pack 2012 R2 på Windows Server 2012 R2. Billedet er i øjeblikket er baseret på HPC Pack 2012 R2 Update 3.
    >
    >Beregne node FOS er oprettet ud fra seneste billedet af den valgte Beregn node familie. Vælg indstillingen **ComputeNodeWithExcel** for det seneste HPC Pack Beregn node-billede, der indeholder en vurderingsversion af Microsoft Excel Professional Plus 2013. Du skal installere en klynge, for generelle SOA sessioner eller Excel UDF overføre, vælge **ComputeNode** (uden Excel installeret).

    b. Vælg abonnementet.

    c. Oprette en ressourcegruppe for klynge som *hpc01RG*.

    d. Vælg en placering til ressourcegruppe, som centrale USA.

    e. Gennemse betingelserne på siden **juridiske betingelser** . Hvis du accepterer den, skal du klikke på **Køb**. Når du er færdig med at angive værdierne for skabelonen, klik derefter **Opret**.

4.  Når installationen er fuldført (det tager typisk omkring 30 minutter), eksportere certifikatfil klynge fra Klyngenoden hoved. På et senere tidspunkt, kan du importere dette offentlige certifikat på klientcomputeren med serversiden godkendelse af sikker HTTP-binding.

    en. Oprette forbindelse til noden hoved ved Fjernskrivebord fra Azure-portalen.

     ![Oprette forbindelse til noden hoved][connect]

    b. Brug standardprocedurer i Certifikatstyring til at eksportere certifikat hoved node (findes under Cert: \LocalMachine\My) uden privat nøgle. I dette eksempel eksportere *CN = hpc01.eastus.cloudapp.azure.com*.

    ![Eksportere certifikat][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>Mulighed 2. Bruge scriptet HPC Pack IaaS installation

Scriptet HPC Pack IaaS installation indeholder en anden alsidige måde at installere en HPC Pack klynge. Det opretter en klynge i modellen Klassisk installation, mens skabelonen, der bruger implementeringsmodel Azure ressourcestyring. Desuden er scriptet kompatibelt med et abonnement i Azure Global eller Azure Kina tjeneste.

**Forudsætninger, der er flere**

* **Azure PowerShell** - [installere og konfigurere Azure PowerShell](../powershell-install-configure.md) (version 0.8.10 eller nyere) på klientcomputeren.

* **Scriptet til implementering af HPC Pack IaaS** – kan du hente og udpakke den seneste version af scriptet fra [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Markér versionen af scriptet ved at køre `New-HPCIaaSCluster.ps1 –Version`. I denne artikel er baseret på version 4.5.0 eller senere af scriptet.

**Oprette konfigurationsfilen**

 HPC Pack IaaS installation scriptet bruger en XML-fil som input, der beskriver infrastrukturen i HPC-klynge. Erstatte værdier for dit miljø i følgende eksempel på konfigurationsfil for at installere en klynge bestående af en hoved node og 18 Beregn noder oprettet fra det Beregn node billede, der indeholder Microsoft Excel. Se filen Manual.rtf i script mappe og [oprette en HPC-klynge med HPC Pack IaaS installation script](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)kan finde flere oplysninger om af konfigurationsfilen.

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Noter om konfigurationsfilen**

* **VMName** for noden hoved **skal** være den samme som **ServiceName**eller SOA job mislykkes til at køre.

* Kontrollér, at du angiver **EnableWebPortal** , så hoved node certifikatet er oprettet og eksporteres.

* Filen angiver et efter konfigurationen PowerShell-script PostConfig.ps1, der kører på noden hoved. Følgende eksempelscript konfigurerer forbindelsesstrengen Azure-lager, fjerner rollen Beregn node fra noden hoved og viser alle noder online, når de er installeret. 

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**Køre scriptet**

1.  Åbn PowerShell-konsollen på klientcomputeren som administrator.

2.  Skift mappe til mappen script (E:\IaaSClusterScript i dette eksempel).

    ```
    cd E:\IaaSClusterScript
    ```
    
3.  Hvis du vil installere HPC Pack klynge, skal du køre følgende kommando. I dette eksemplet antages, at konfigurationsfilen er placeret i E:\HPCDemoConfig.xml.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

HPC Pack installation scriptet kører i et stykke tid. Én ting scriptet gør er at eksportere og download af klynge certifikat og gemme det i den aktuelle bruger dokumenter på klientcomputeren. Scriptet genererer en meddelelse, der ligner følgende. I følgende trin kan importere du certifikatet i den relevante lager.    
    
    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>Trin 2. Offload Excel-projektmapper og køre brugerdefinerede funktioner fra en lokal klient

### <a name="excel-activation"></a>Excel aktivering

Når du bruger ComputeNodeWithExcel VM billedet til fremstilling arbejdsbelastninger, skal du angive en gyldig Microsoft Office-licensnøgle for at aktivere Excel på noderne Beregn. Ellers evaluering version af Excel udløber efter 30 dage, og kører Excel-projektmapper mislykkes med COMException (0x800AC472). 

Du kan nulstille Excel til en anden 30 dage efter beregning i gang: Log på hovedet node og clusrun `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` på alle Excel beregne noder via HPC klynge Manager. Du kan nulstille maksimalt to gange. Herefter skal du angive en gyldig Office-licensnøgle.

Office Professional Plus 2013 installeret på VM billede er et volumenversion med en generisk lydstyrken licens nøgle (GVLK). Du kan aktivere via nøgleadministrationstjeneste (KMS) / Active Directory-Based aktivering (AD-BA) eller multiaktiveringsnøglen (MAK). 

    * For at bruge AD-KMS-BA skal bruge en eksisterende KMS-server eller konfigurere en ny ved hjælp af Microsoft Office 2013 lydstyrken License Pack. (Hvis du vil, konfigurere server på noden hoved.) Derefter aktivere tasten KMS host via internettet eller telefonen. Derefter clusrun `ospp.vbs` til at angive KMS server og port og aktivere Office på alle Excel beregne noder. 
    
    * Bruge MAK, første clusrun `ospp.vbs` input tasten og derefter aktivere alle Excel beregne noder via internettet eller telefonen. 

>[AZURE.NOTE]Detailsalg produktnøgler til Office Professional Plus 2013 kan ikke bruges med dette VM billede. Hvis du har gyldig nøgler og installationsmedierne til Office eller Excel-versioner end denne Office Professional Plus 2013 volumenversion, kan du bruge dem i stedet. Først skal du fjerne denne volumenversion og installere den version, du har. Noden geninstalleres Excel Beregn kan registreres som en tilpasset VM afbildning skal bruges i en installation på skala.

### <a name="offload-excel-workbooks"></a>Offload Excel-projektmapper

Følg disse trin for at offload en Excel-projektmappe, så den kører på HPC Pack klynge i Azure. For at gøre dette, skal du have Excel 2010 eller 2013, der allerede er installeret på klientcomputeren.

1. Bruge en af indstillingerne i trin 1 for at installere en HPC Pack klynge med Excel beregne node billede. Få klynge certifikatfil (.cer) og klynge brugernavn og din adgangskode.

2. Importere klynge-certifikat under Cert: \CurrentUser\Root på klientcomputeren.

3. Sørg for, at Excel er installeret. Oprette en Excel.exe.config-fil med følgende indholdet i den samme mappe som Excel.exe på klientcomputeren. Dette trin sikrer, at tilføjelsesprogrammet HPC Pack 2012 R2 Excel COM indlæser korrekt.

    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
    
4.  Konfigurere klienten til at sende job til HPC Pack klynge. En af mulighederne er at hente hele [HPC Pack 2012 R2 Update 3 installation](http://www.microsoft.com/download/details.aspx?id=49922) og installere HPC Pack klienten. Du kan også hente og installere [HPC Pack 2012 R2 Update 3 klient værktøjer](https://www.microsoft.com/download/details.aspx?id=49923) og den relevante Visual C++ 2010 redistributable for din computer ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555)).

5.  I dette eksempel skal bruge vi et eksempel på Excel-projektmappe med navnet ConvertiblePricing_Complete.xlsb. Kan du downloade den [her](https://www.microsoft.com/en-us/download/details.aspx?id=2939).

6.  Kopiere Excel-projektmappen til en arbejdsmappe som D:\Excel\Run.

7.  Åbn Excel-projektmappen. Klik på **COM-tilføjelsesprogrammer** på båndet **udvikling** og bekræfte, at HPC Pack Excel COM-tilføjelsesprogram er indlæst.

    ![Excel-tilføjelsesprogrammet til HPC Pack][addin]

8.  Redigere en VBA-makro HPCControlMacros i Excel ved at ændre de kommenterede linjer, som vist i følgende script. Erstatte relevante værdier for dit miljø.

    ![Excel-makro til HPC Pack][macro]

    ```
    'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
    Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"

    'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
    Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.Initialize ActiveWorkbook
    HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles

    'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
    HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
    HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
```

9.  Kopiere Excel-projektmappen til et Overfør directory som D:\Excel\Upload. Denne mappe er angivet i konstanten HPC_DependsFiles i VBA-makroer.

10. Hvis du vil køre projektmappen på klynge i Azure, skal du klikke på knappen **klynge** i regnearket.

### <a name="run-excel-udfs"></a>Køre Excel brugerdefinerede funktioner

Hvis du vil køre Excel brugerdefinerede funktioner, skal du følge de foregående trin 1-3 for at konfigurere klientcomputeren. For Excel brugerdefinerede funktioner skal du ikke har Excel-programmet installeret på Beregn noder. Så, når du opretter din klynge beregne noder, kan du vælge en normal beregne node billede i stedet for Beregn node billedet med Excel.

>[AZURE.NOTE] Der er en begrænsning på 34 tegn i Excel 2010 og 2013 klynge forbindelse dialogboksen. Du kan bruge denne dialogboks til at angive den klynge, der kører på brugerdefinerede funktioner. Hvis den fulde navn er længere (for eksempel hpcexcelhn01.southeastasia.cloudapp.azure.com), der ikke passer i dialogboksen. Løsningen er at angive en hele computeren variabel som *CCP_IAASHN* med værdien af det lange klyngenavn. Skriv *% CCP_IAASHN %* i dialogboksen som klynge hoved nodenavnet. 

Når klyngen er installeret, fortsætte med følgende trin for at køre en indbygget stikprøve Excel UDF. Du kan finde tilpassede Excel brugerdefinerede funktioner, disse [ressourcer](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) til at opbygge XLL og anvende dem på IaaS klynge.

1.  Åbn en ny Excel-projektmappe. Klik på **Tilføjelsesprogrammer**på båndet **udvikling** . Klik derefter på **Gennemse**i dialogboksen, gå til mappen %CCP_HOME%Bin\XLL32, og vælg stikprøvernes ClusterUDF32.xll. Hvis ClusterUDF32 ikke findes på klientcomputeren, skal du kopiere den fra mappen %CCP_HOME%Bin\XLL32 på noden hoved.

    ![Vælg UDF][udf]

2.  Klik på **filer** > **Indstillinger** > **Avanceret**. Under **formler**, skal du markere **Tillad brugerdefinerede XLL-funktioner til at køre en computerklynge**. Klik på **Indstillinger** , og Angiv det fulde navn i **klynge hoved nodenavn**. (Som relevante tidligere denne inputfeltet er begrænset til 34 tegn, så en lang klyngenavn ikke kan passer. Du kan bruge en hele computeren variabel her for en lang klyngenavn.)

    ![Konfigurere udf-filen][options]

3.  Klik på cellen med værdien =XllGetComputerNameC() for at køre UDF beregningen på klyngen, og tryk på Enter. Funktionen henter blot navnet på noden Beregn som UDF webstedsindholdstyper. For første gang beder en legitimationsoplysninger dialogboks om brugernavn og din adgangskode til at oprette forbindelse til IaaS klynge.

    ![Køre UDF][run]

    Når der er mange celler til at beregne, skal du trykke på Alt-Skift-Ctrl + F9 for at køre beregningen på alle celler.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>Trin 3. Køre en SOA arbejdsbyrde fra en lokal-klient

For at køre generelle SOA programmer på HPC Pack IaaS klynge skal først bruge af fremgangsmåder i trin 1 til at installere klyngen. Angive en standardressource beregne node billede i dette tilfælde, da du ikke skal bruge Excel på noderne Beregn. Følg disse trin.

1. Når du henter klynge certifikatet, skal du importere den på klientcomputeren under Cert: \CurrentUser\Root.

2. Installer [HPC Pack 2012 R2 Update 3 SDK](http://www.microsoft.com/download/details.aspx?id=49921) og [HPC Pack 2012 R2 Update 3 klient værktøjer](https://www.microsoft.com/download/details.aspx?id=49923). Disse funktioner gør det muligt at udvikle og køre SOA klientprogrammer.

3. Hent HelloWorldR2 [eksempelkode](https://www.microsoft.com/download/details.aspx?id=41633). Åbn HelloWorldR2.sln i Visual Studio 2010 eller 2012.

4. Opbygge EchoService projektet først. Installer tjenesten til IaaS klynge på samme måde, du installerer til en lokal klynge. Detaljeret vejledning, skal du se Readme.doc i HelloWordR2. Redigere og oprette HellWorldR2 og andre projekter, som beskrevet i følgende afsnit for at generere klientprogrammerne SOA, der kører på en Azure IaaS klynge.

### <a name="use-http-binding-with-azure-storage-queue"></a>Bruge HTTP-binding med Azure lagerplads kø

Foretage nogle få ændringer eksempelkoden for at bruge HTTP-binding med en Azure lagerplads kø skal.

* Opdatere klynge navne.

    ```
// Before
const string headnode = "[headnode]";
// After e.g.
const string headnode = "hpc01.eastus.cloudapp.azure.com";
or
const string headnode = "hpc01.cloudapp.net";
```

* Du kan også bruge standard TransportScheme i SessionStartInfo eller eksplicit angive den til Http.

```
    info.TransportScheme = TransportScheme.Http;
```

* Du kan bruge standard binding for BrokerClient.

    ```
// Before
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
// After
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
```

    Eller angive eksplicit ved hjælp af basicHttpBinding.

    ```
BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
```

* Du kan også angive flaget UseAzureQueue til sand i SessionStartInfo. Hvis ikke indstilles, den indstilles til SAND som standard, når klyngenavnet indeholder Azure domænesuffikser og TransportScheme er Http.

    ```
    info.UseAzureQueue = true;
```

###<a name="use-http-binding-without-azure-storage-queue"></a>Brug HTTP-binding uden Azure lagerplads kø

For at bruge HTTP-binding uden en Azure lagerplads kø skal eksplicit er angivet flaget UseAzureQueue til falsk i SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>Brug NetTcp binding

Hvis du vil bruge NetTcp indbinding, ligner konfigurationen til at oprette forbindelse til en lokal klynge. Du har brug at åbne et par slutpunkterne på noden hoved VM. Hvis du har brugt HPC Pack IaaS installation scriptet til at oprette klyngen, angive eksempelvis slutpunkterne på portalen Azure klassisk på følgende måde.


1. Stop VM.

2. Tilføje TCP-portnumre 9090, 9087, 9091, 9094 for sessionen, Broker, Broker arbejder og datatjenester, henholdsvis

    ![Konfigurere slutpunkter][endpoint]

3. Start VM.

Klientprogrammet SOA kræver ingen ændringer bortset fra at ændre hoved navnet til IaaS klynge fulde navn.

## <a name="next-steps"></a>Næste trin

* Se [disse ressourcer](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) for flere oplysninger om at køre arbejdsbelastninger, som Excel med HPC Pack.

* [Administrere SOA tjenester i Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) flere oplysninger om installation og administration af SOA tjenester med HPC Pack.

<!--Image references-->
[scenario]: ./media/virtual-machines-windows-excel-cluster-hpcpack/scenario.png
[github]: ./media/virtual-machines-windows-excel-cluster-hpcpack/github.png
[template]: ./media/virtual-machines-windows-excel-cluster-hpcpack/template.png
[parameters]: ./media/virtual-machines-windows-excel-cluster-hpcpack/parameters.png
[create]: ./media/virtual-machines-windows-excel-cluster-hpcpack/create.png
[connect]: ./media/virtual-machines-windows-excel-cluster-hpcpack/connect.png
[cert]: ./media/virtual-machines-windows-excel-cluster-hpcpack/cert.png
[addin]: ./media/virtual-machines-windows-excel-cluster-hpcpack/addin.png
[macro]: ./media/virtual-machines-windows-excel-cluster-hpcpack/macro.png
[options]: ./media/virtual-machines-windows-excel-cluster-hpcpack/options.png
[run]: ./media/virtual-machines-windows-excel-cluster-hpcpack/run.png
[endpoint]: ./media/virtual-machines-windows-excel-cluster-hpcpack/endpoint.png
[udf]: ./media/virtual-machines-windows-excel-cluster-hpcpack/udf.png
