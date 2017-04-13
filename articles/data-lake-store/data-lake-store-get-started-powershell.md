<properties
   pageTitle="Komme i gang med sø datalager | Azure"
   description="Bruge Azure PowerShell til at oprette en sø datalager konto og udføre grundlæggende handlinger"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/04/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Introduktion til Azure Data sø Store ved hjælp af Azure PowerShell

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST-API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Lær at bruge Azure PowerShell til at oprette en Azure datalager sø konto og udføre grundlæggende handlinger, sådan som oprette mapper, upload og download-datafiler, skal du slette din konto, osv. Du kan finde flere oplysninger om sø datalager, [Oversigt over sø datalager](data-lake-store-overview.md).

## <a name="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet, skal du have følgende:

* **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

* **Azure PowerShell 1.0 eller større**. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

## <a name="authentication"></a>Godkendelse

I denne artikel bruges en enklere godkendelse fremgangsmåde med sø datalager, der hvor du bliver bedt om at angive dine legitimationsoplysninger Azure-konto. Adgangsniveau til datalager sø konto og filsystem derefter er underlagt adgangsniveauet for den bruger logget på. Der er andre fremgangsmåder samt til at godkende sø datalager, som er **Slutbrugerlicensaftale godkendelse** eller **tjeneste til godkendelse**. Instruktioner og flere oplysninger om, hvordan du godkender, under [Godkend med sø datalager brug af Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-an-azure-data-lake-store-account"></a>Oprette en Azure datalager sø-konto

1. Åbn et nyt vindue i Windows PowerShell fra din computer, og Angiv følgende kodestykke for at logge på din Azure-konto, angive abonnementet og registrere provideren sø datalager. Når du bliver bedt om at logge på, skal du kontrollere du logger på som et af admininistrators/ejeren af abonnementet:

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"


2. En Azure datalager sø konto er knyttet til en Azure ressourcegruppe. Starte med at oprette en Azure ressourcegruppe.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Oprette en Azure ressourcegruppe] (./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Oprette en Azure ressourcegruppe")

2. Opret en Azure datalager sø-konto. Det navn, du angiver skal kun indeholde små bogstaver og tal.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Opret en Azure datalager sø-konto] (./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Opret en Azure datalager sø-konto")

3. Kontrollér, at kontoen blev oprettet.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    Output til dette skal være **sande**.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Oprette directory-strukturer i dit Azure datalager sø

Du kan oprette mapper under kontoen Azure sø datalager til at administrere og gemme data.

1. Angiv en rodmappe.

        $myrootdir = "/"

2. Oprette en ny mappe med navnet **mynewdirectory** under den angivne rod.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory

3. Kontrollér, at den nye mappe er blevet oprettet.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    Det skal vises et output ud som følger:

    ![Bekræfte Directory] (./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Bekræfte Directory")


## <a name="upload-data-to-your-azure-data-lake-store"></a>Overføre data til din Azure datalager sø

Sø datalager direkte på rodniveau eller en mappe, du har oprettet i kontoen, kan du overføre dine data. De kodestykker nedenfor viser, hvordan du overfører nogle eksempeldata til den mappe (**mynewdirectory**), du oprettede i forrige afsnit.

Hvis du leder efter nogle eksempeldata for at overføre, kan du få mappen **Ambulance Data** fra [Azure Data sø ciffer lager](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Hente filen og gemme den i en lokal mappe på din computer, som C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Omdøbe, hente og slette data fra dit datalager sø

Hvis du vil omdøbe en fil, skal du bruge følgende kommando:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Hvis du vil hente en fil, skal du bruge følgende kommando:

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Hvis du vil slette en fil, skal du bruge følgende kommando:

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Når du bliver bedt om det, kan du angive **Y** for at slette elementet. Hvis du har mere end én fil, der skal slettes, kan du give alle stier, der er adskilt af komma.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Slette kontoen Azure sø datalager

Brug følgende kommando til at slette kontoen sø datalager.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Når du bliver bedt om det, kan du angive **Y** for at slette kontoen.


## <a name="next-steps"></a>Næste trin

- [Sikre data i sø datalager](data-lake-store-secure-data.md)
- [Bruge Azure Data sø Analytics med sø datalager](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Bruge Azure HDInsight med sø datalager](data-lake-store-hdinsight-hadoop-use-portal.md)
