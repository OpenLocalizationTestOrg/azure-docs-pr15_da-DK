<properties
    pageTitle="Azure service grundlæggende om batchen | Microsoft Azure"
    description="Få mere at vide om brug af tjenesten Azure batchen til store parallelt og HPC arbejdsmængder"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/22/2016"
    ms.author="marsma"/>

# <a name="basics-of-azure-batch"></a>Grundlæggende om Azure batchen

Azure batchen gør det muligt at køre store parallelle og high-performance computing (HPC) programmer effektivt i skyen. Det er en platform-tjeneste, der planlægger Beregn-intensivt arbejde køres en administreret samling af virtuelle maskiner, og kan automatisk skala beregne ressourcer for at imødekomme dine sager.

Du definerer Azure Beregn ressourcer for at udføre dine programmer parallelt og skaleres til brug med tjenesten batchen. Du kan køre efter behov eller planlagte job, og du behøver ikke at manuelt oprette, konfigurere og administrere en HPC-klynge, individuelle virtuelle maskiner, virtuelle netværk eller et kompleks job og opgave planlægning infrastruktur.

## <a name="use-cases-for-batch"></a>Brug sager for batchen

Batch er en administreret Azure tjeneste, der bruges til *batchbehandling* eller *batchen computing*– kører en stor mængde ensartede opgaver for at få nogle ønskede resultat. Batchen computing er mest almindeligt anvendt af organisationer, der jævnligt at behandle, transformere og analysere store datamængder.

Batchen fungerer fint med reelt parallelle (også kaldet "embarrassingly parallel")-programmer og arbejdsmængder. Reelt parallelle arbejdsbelastninger opdeles nemt i flere opgaver, som udfører arbejde samtidigt på mange computere.

![Parallelle opgaver][1]<br/>

Nogle eksempler på arbejdsbelastninger, som behandles ofte ved hjælp af denne metode er:

* Finansielle risiko modellering
* Såfremt og hydrology dataanalyse
* Billedgengivelsen, analyse og behandling
* Medier kodningen og kodning
* Genetiske sekvens analysis
* Teknisk belastning analysis
* Teste software

Batchen kan også udføre parallelle beregninger med et reducere trin i slutningen og udføre mere komplekse HPC arbejdsbelastninger, som [Der passerer MPI (Message Interface)](batch-mpi.md) programmer.

Du kan finde en sammenligning mellem batchen og andre indstillinger til løsning af HPC i Azure [batchen og HPC løsninger](batch-hpc-solutions.md).

## <a name="developing-with-batch"></a>Udvikling af med batchen

Behandling af parallelle arbejdsmængder med batchen gøres typisk fra et program ved hjælp af [Batchen API'er](#batch-development-apis). Med API'er batchen du oprette og administrere grupper af Beregn knuder (virtuelle maskiner) og planlægge job og opgaver til at køre på disse noder. En klientprogrammet eller tjeneste, som du opretter du bruger de batchen API'er til at kommunikere med tjenesten batchen.

Du kan effektivt behandle store arbejdsmængder for din organisation eller leverer en tjeneste front-end til dine kunder, så de kan køre job og opgaver – efter behov, eller på en tidsplan – på en, hundredvis eller endda tusindvis af noder. Du kan også bruge batchen som en del af en større arbejdsproces, der administreres af funktioner som [Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md).

> [AZURE.TIP] Når du er klar til at grave batchen API til en mere i dybden forståelse af de funktioner, den indeholder, skal du se [batchen funktionsoversigt for udviklere](batch-api-basics.md).

### <a name="azure-accounts-youll-need"></a>Azure konti skal du

Når du udvikler batchen løsninger, skal du bruge følgende konti i Microsoft Azure.

- **Azure-konto og abonnement** - Hvis du ikke allerede har et Azure-abonnement, kan du aktivere din [MSDN-abonnement få glæde][msdn_benefits], eller Tilmeld dig en [gratis Azure konto][free_account]. Når du opretter en konto, der oprettes et standard-abonnement for dig.

- **Batchen konto** – når programmerne interagere med tjenesten batchen, kontonavnet, URL-adressen på kontoen, og en hurtigtast bruges som legitimationsoplysninger. Alle dine batchen ressourcer som grupper, beregne noder, job, og opgaver er knyttet til en batchen konto. Du kan [oprette batchen konto](batch-account-create-portal.md) i portalen Azure.

- **Lagerplads konto** – batchen indeholder indbygget understøttelse af arbejde med filer i [Azure-lager][azure_storage]. Næsten alle batchen scenarie bruges Azure-lager – til at arrangere de programmer, der køres opgaverne og de data, behandling og til opbevaring af outputdata, der oprettes. For at oprette en lagerplads konto skal du se [om Azure lagerplads konti](./../storage/storage-create-storage-account.md).

### <a name="batch-development-apis"></a>Batchen udvikling API'er

Dine programmer og tjenester kan udstede direkte REST-API-opkald, skal du bruge en eller flere af de følgende klientbiblioteker eller en kombination af begge til at administrere beregne ressourcer og køre parallelle arbejdsbelastninger skaleres ved hjælp af tjenesten batchen.

| API    | API reference | Download | Kodeeksempler |
| ----------------- | ------------- | -------- | ------------ |
| **Batchen RESTEN** | [MSDN][batch_rest] | I/T. | [MSDN][batch_rest] |
| **Batchen .NET**    | [MSDN][api_net] | [NuGet][api_net_nuget] | [GitHub][api_sample_net] |
| **Batchen Python**  | [readthedocs.IO][api_python] | [PyPI][api_python_pypi] |[GitHub][api_sample_python] |
| **Batchen Node.js** | [github.IO][api_nodejs] | [npm][api_nodejs_npm] | - |
| **Batchen Java** (preview) | [github.IO][api_java] | [Maven][api_java_jar] | [GitHub][api_sample_java] |

### <a name="batch-resource-management"></a>Batchen ressourcestyring

Ud over klienten API'er, kan du også bruge følgende til at administrere ressourcer inden for kontoen batchen.

- [Batch PowerShell-cmdlet'er][batch_ps]: feltet Azure batchen-cmdletter i [Azure PowerShell](../powershell-install-configure.md) -modulet gør det muligt at administrere batchen ressourcer med PowerShell.

- [Azure CLI](../xplat-cli-install.md): feltet Azure-kommandolinjen (Azure CLI) er en værktøjer i tværs af platforme, der indeholder shell-kommandoer til arbejde med mange Azure tjenesterne, herunder batchen.

- [Batchen Management .NET](batch-management-dotnet.md) klientbibliotek: også tilgængelig via [NuGet][api_net_mgmt_nuget], du kan bruge biblioteket batchen Management .NET-klienten til at administrere batchen firmaer, kvoter og programpakker fra et program. Reference til biblioteket administration er på [MSDN][api_net_mgmt].

### <a name="batch-tools"></a>Batchen værktøjer

Mens ikke obligatorisk at opbygge løsninger ved hjælp af batchen, er her nogle værdifulde værktøjer til at bruge, mens du opbygge og foretage fejlfinding af dine batchen programmer og tjenester.

 - [Azure portal][portal]: Du kan oprette, overvåge og slette batchen grupper, job og opgaver i portalen Azure batchen blade. Du kan få vist oplysninger om status for disse og andre ressourcer, mens du kører din job, og endda hente filer fra Beregn knuderne i dine grupper (hente mislykkedes opgavens `stderr.txt` under fejlfinding, for eksempel). Du kan også hente RDP (Remote Desktop) filer, du kan bruge til at logge på til at beregne noder.

 - [Azure batchen Explorer][batch_explorer]: batchen Explorer indeholder tilsvarende batchen ressource management funktionalitet som portalen Azure, men i en separat Windows Presentation Foundation (WPF)-klientprogrammet. En af de batchen .NET eksempel-programmer, der er tilgængelige på [GitHub][github_samples], du kan bygge det med Visual Studio 2015 eller derover og bruge det til at gennemse og administrere ressourcer i kontoen batchen, mens du udvikle og fejlfinde dine batchen løsninger. Periode, gruppe og opgaveoplysninger, kan du hente filer fra Beregn noder, og oprette forbindelse til noder fra en fjernplacering ved hjælp af RDP (Remote Desktop) filer kan du hente med batchen Stifinder.

 - [Microsoft Azure-lager Explorer][storage_explorer]: Når du ikke en egentlig et Azure batchen værktøj, Storage Explorer er en anden værdifuldt værktøj til at deltage i, mens du udvikler og fejlfinding dine batchen løsninger.

## <a name="scenario-scale-out-a-parallel-workload"></a>Scenarie: Skala ud af en parallelle arbejdsbyrde

En almindelig løsning, der bruger batchen API'er til at interagere med tjenesten batchen omfatter skalering af reelt parallelle arbejde – som gengivelse af billeder til 3D-scenen – i forbindelse med en samling af Beregn noder. Denne samling af Beregn noder kan være "Gengiv farmen", der indeholder mange, hundredvis eller tusindvis af kerner til gengivelse tingene, f.eks.

I det følgende diagram viser en fælles batchen arbejdsproces med en klientprogrammet eller hostet tjenesten ved hjælp af batchen til at køre en parallelle arbejdsbyrde.

![Batchen løsning arbejdsproces][2]

I denne almindelige scenarie behandler programmet eller tjenesten computational belastning i Azure batchen ved at benytte følgende fremgangsmåde:

1. Overføre **input filer** og det **program** , der behandler disse filer til kontoen Azure-lager. Input-filer kan være de data, der skal behandle dit program, som økonomiske modeller data eller videofiler til ikke kodes. Programfiler kan være et program, der bruges til at behandle dataene, som en 3D-gengivelse programmet eller medier transcoder.

2. Oprette en batchen **puljen** af Beregn knuder på kontoen batchen – disse noder er de virtuelle maskiner, der udføres dine opgaver. Du kan angive egenskaber som [node størrelse](./../cloud-services/cloud-services-sizes-specs.md), deres operativsystem og placeringen i Azure lagringen af programmet for at installere når noderne deltager i puljen (det program, du har overført i trin #1). Du kan også konfigurere puljen [automatisk skalere](batch-automatic-scaling.md)– dynamisk tilpasse antallet af Beregn noder i puljen – som svar på arbejdsbelastningen, generere dine opgaver.

3. Oprette et **job** for at køre arbejdsbelastningen på gruppen af Beregn noder. Når du opretter et job, kan du knytte den til en batchen puljen.

4. Føje **opgaver** til jobbet. Når du har føjet opgaver til et job, planlægger tjenesten batchen automatisk opgaverne for udførelse af på Beregn knuderne i puljen. Hver enkelt opgave bruger det program, du har overført for at behandle de input filer.

    - 4a. Før du udfører en opgave, kan det hente data (input filerne), som det er for processen til noden Beregn den er tildelt til. Hvis programmet ikke har allerede er installeret på noden (se trin #2), det kan hentes her i stedet. Når overførslerne er fuldført, skal udføre opgaverne på deres tildelte noder.

5. Som opgaverne, der kører, kan du forespørge Batch for at overvåge statussen for jobbet og dets opgaver. Din klientprogrammet eller tjeneste kommunikerer med tjenesten batchen over HTTPS, og fordi du kan overvåge tusindvis af opgaver, der kører på tusindvis af Beregn noder, skal du sørge for at [forespørge tjenesten batchen effektivt](batch-efficient-list-queries.md).

6. Som de opgaver, der er fuldført, kan de overføre deres resultatdata til Azure-lager. Du kan også hente filer direkte fra Beregn noder.

7. Når din overvågning registrerer, at opgaverne i dit arbejde har fuldført, kan din klientprogrammet eller tjeneste hente output dataene til videre behandling eller evaluering.

Holde øje dette er blot én måde at bruge batchen, og dette scenario beskrives nogle af de tilgængelige funktioner. For eksempel du kan udføre [flere opgaver parallelt](batch-parallel-node-tasks.md) på hver beregningsnode, og du kan bruge [jobbet forberedelse og fuldførelse opgaver](batch-job-prep-release.md) til noderne forberede dine sager og derefter rydde op senere.

## <a name="next-steps"></a>Næste trin

Nu hvor du har en overordnet oversigt over tjenesten batchen, er det tid at gå dybere til at få mere at vide, hvordan du kan bruge det til at behandle dine Beregn-intensivt parallelle arbejdsmængder.

- Læs [batchen funktionsoversigt for udviklere](batch-api-basics.md), vigtige oplysninger til alle forberede bruge batchen. I artiklen indeholder flere oplysninger om batchen serviceressourcer som grupper, noder, job, og opgaver, og de mange API-funktioner, som du kan bruge bygge batchen programmet.

- [Introduktion til biblioteket Azure batchen til .NET](batch-dotnet-get-started.md) at lære, hvordan du bruger C# og biblioteket batchen .NET til at udføre en simpel arbejdsbelastningen ved hjælp af en fælles batchen arbejdsproces. I denne artikel skal være en af dine første Stop under lære at bruge tjenesten batchen. Der er også en [Python version](batch-python-tutorial.md) af selvstudiet.

- Hent [kodeeksempler på GitHub] [ github_samples] at se, hvordan både C# og Python kan arbejde sammen med batchen til arbejdsbelastninger, som eksempel tidsplan og proces.

- Se [Batchen læringssti] [ learning_path] at få en ide om de ressourcer, der er tilgængelige for dig, som du lære at arbejde med batchen.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png
