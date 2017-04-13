<properties
    pageTitle="Lær, hvordan du administrerer AzureML webtjenester ved hjælp af API Management | Microsoft Azure"
    description="En vejledning, der viser, hvordan du administrerer AzureML webtjenester ved hjælp af API Management."
    keywords="Machine learning,-api administration"
    services="machine-learning"
    documentationCenter=""
    authors="roalexan"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="roalexan" />


# <a name="learn-how-to-manage-azureml-web-services-using-api-management"></a>Lær, hvordan du administrerer AzureML webtjenester ved hjælp af API Management

##<a name="overview"></a>Oversigt

Denne vejledning viser, hvordan du hurtigt at komme i gang med at bruge API administration til at administrere dine AzureML web-tjenester.

##<a name="what-is-azure-api-management"></a>Hvad er Azure API Management?

Azure API Management er en Azure-tjeneste, hvor du kan administrere REST-API slutpunkterne ved at definere brugeradgang, throttling brugen og dashboard overvågning. Klik [her](https://azure.microsoft.com/services/api-management/) for at få at vide om Azure API Management. Klik [her](../api-management/api-management-get-started.md) for at få en vejledning til, hvordan du kommer i gang med Azure API Management. Denne andre vejledning, som denne vejledning er baseret på, dækker flere emner, herunder meddelelse konfigurationer, niveau priser, håndtering af svar, brugergodkendelse oprettelse af produkter, udvikler abonnementer og brug dashboarding.

##<a name="what-is-azureml"></a>Hvad er AzureML?

AzureML er en Azure service for learning computer, hvor du kan nemt at opbygge, installere og dele avanceret analyse løsninger. Klik [her](https://azure.microsoft.com/services/machine-learning/) for at få oplysninger om AzureML.

##<a name="prerequisites"></a>Forudsætninger

For at fuldføre denne vejledning, skal du:

* En Azure-konto. Hvis du ikke har en Azure-konto, skal du klikke [her](https://azure.microsoft.com/pricing/free-trial/) for at få mere at vide om, hvordan du opretter en gratis prøveversion konto.
* En AzureML-konto. Hvis du ikke har en AzureML-konto, skal du klikke [her](https://studio.azureml.net/) for at få mere at vide om, hvordan du opretter en gratis prøveversion konto.
* Arbejdsområde, service og api_key for AzureML forsøg installeres som en webtjeneste. Klik [her](machine-learning-create-experiment.md) for at få mere at vide om, hvordan du opretter et AzureML forsøg. Klik [her](machine-learning-publish-a-machine-learning-web-service.md) for at få mere at vide om, hvordan du installerer et AzureML forsøg som en webtjeneste. Alternativt har tillæg A vejledning i, hvordan du opretter og teste et simpelt AzureML forsøg og installerer det som en webtjeneste.

##<a name="create-an-api-management-instance"></a>Oprette en API Management forekomst

Nedenfor finder du trin til brug af API administration til at administrere din AzureML webtjeneste. Først oprette en forekomst af tjenesten. Log på [Klassisk Portal](https://manage.windowsazure.com/) , og klik på **Ny** > **App Services** > **API Management** > **Opret**.

![Opret forekomst](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-instance.png)

Angive en entydig **URL-adresse**. Denne vejledning bruger **demoazureml** – skal du vælge noget andet. Vælg den ønskede **abonnement** og **område** for din tjenesteforekomst. Klik på knappen Næste, når du har foretaget dine valg.

![oprette-service-1](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-1.png)

Angiv en værdi på **Organisationens navn**. Denne vejledning bruger **demoazureml** – skal du vælge noget andet. Angiv din mailadresse i feltet **administrator e-mail** . Denne e-mail-adresse bruges til beskeder fra API-system.

![oprette-service-2](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-2.png)

Klik på afkrydsningsfeltet for at oprette din forekomst af tjenesten. *Det tager op til 30 minutter for en ny tjeneste skal oprettes*.

##<a name="create-the-api"></a>Oprette API

Når forekomsten af er oprettet, er næste trin at oprette API. En API består af et sæt handlinger, der kan startes fra en klientprogrammet. API handlinger er proxy til eksisterende webtjenester. Denne vejledning opretter API'er denne proxy til de eksisterende AzureML RRS og BES webtjenester.

API'er er oprettet og konfigureret fra portalen API publisher, som er åbnet via Azure klassisk portalen. Når publisher-portalen, vælge din tjenesteforekomst af.

![Vælg-service-forekomst](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-service-instance.png)

Klik på **Administrer** i portalen Azure klassisk for din API Management-tjenesten.

![administrere service](./media/machine-learning-manage-web-service-endpoints-using-api-management/manage-service.png)

Klik på **API'er** i menuen **Administration af API** til venstre, og klik derefter på **Tilføj API**.

![API-management-menuen](./media/machine-learning-manage-web-service-endpoints-using-api-management/api-management-menu.png)

Skriv **AzureML Demo API** som **Web API-navn**. Skriv **https://ussouthcentral.services.azureml.net** som **Web URL-adressen**. Skriv **azureml-demo** som **URL-adresse API suffiks**. Markere **HTTPS** som **URL-adresse API** farveskema. Vælg **Starter** som **produkter**. Når du er færdig, skal du klikke på **Gem** for at oprette API.

![tilføje-ny-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-new-api.png)

##<a name="add-the-operations"></a>Tilføj handlingerne

Klik på **Tilføj handling** for at føje handlinger til denne API.

![Tilføj handling](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-operation.png)

Vinduet **Ny handling** vises, og fanen **signatur** vælges som standard.

##<a name="add-rrs-operation"></a>Tilføje Ressourceposter handling

Først oprette en handling for tjenesten AzureML RRS. Vælg **Send** som **HTTP verber**. Skriv **/services/ /workspaces/ {arbejdsområdet} {tjenesten} / execute?api version = {apiversion} & detaljer = {detaljer}** som **URL-adresse til skabelon**. Skriv **Ressourceposter udføre** som **vist navn**.

![tilføje ressourceposter-handling-signatur](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

Klik på **svar** > **tilføje** på venstre og vælg **200 OK**. Klik på **Gem** for at gemme denne handling.

![tilføje-ressourceposter-handling-svar](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

##<a name="add-bes-operations"></a>Tilføje BES handlinger

Skærmbilleder er ikke inkluderet for BES handlinger, som minder meget om dem til at tilføje handlingen Ressourceposter.

###<a name="submit-but-not-start-a-batch-execution-job"></a>Sende et batchen udførelse af job (men ikke starter)

Klik på **Tilføj handling** for at tilføje handlingen AzureML BES til API. Vælg **Send** til **HTTP-verber**. Skriv **/services/ /workspaces/ {arbejdsområdet} {tjenesten} / jobs?api version = {apiversion}** til **URL-adresse til skabelon**. Skriv **BES sende** for **vist navn**. Klik på **svar** > **tilføje** på venstre og vælg **200 OK**. Klik på **Gem** for at gemme denne handling.

###<a name="start-a-batch-execution-job"></a>Starte et batchen udførelse af job

Klik på **Tilføj handling** for at tilføje handlingen AzureML BES til API. Vælg **Send** til **HTTP-verber**. Skriv **/workspaces/ {arbejdsområdet} /services/ {tjenesten} /jobs/ {job-id} / start?api version = {apiversion}** til **URL-adresse til skabelon**. Skriv **BES Start** for **vist navn**. Klik på **svar** > **tilføje** på venstre og vælg **200 OK**. Klik på **Gem** for at gemme denne handling.

###<a name="get-the-status-or-result-of-a-batch-execution-job"></a>Få status eller resultatet af et batchen udførelse af job

Klik på **Tilføj handling** for at tilføje handlingen AzureML BES til API. Vælg **få** for **HTTP-verber**. Skriv **/workspaces/ {arbejdsområdet} /services/ {tjenesten} /jobs/ {job-id} ?api-version = {apiversion}** til **URL-adresse til skabelon**. Skriv **BES Status** for **vist navn**. Klik på **svar** > **tilføje** på venstre og vælg **200 OK**. Klik på **Gem** for at gemme denne handling.

###<a name="delete-a-batch-execution-job"></a>Slette et batchen udførelse af job

Klik på **Tilføj handling** for at tilføje handlingen AzureML BES til API. Vælg **Slet** for **HTTP-verber**. Skriv **/workspaces/ {arbejdsområdet} /services/ {tjenesten} /jobs/ {job-id} ?api-version = {apiversion}** til **URL-adresse til skabelon**. Skriv **BES slette** for **vist navn**. Klik på **svar** > **tilføje** på venstre og vælg **200 OK**. Klik på **Gem** for at gemme denne handling.

##<a name="call-an-operation-from-the-developer-portal"></a>Ringe til en handling fra portalen udvikler

Handlinger kan kun kaldes direkte fra portalen udvikler, som indeholder en nem måde at få vist og teste handlinger for en API. I denne vejledning trin kan du ringe til metoden **Ressourceposter udføre** , der blev føjet til **AzureML Demo API**. Klik på **udvikler portal** i menuen øverst til højre på portalen klassisk.

![udvikler-portal](./media/machine-learning-manage-web-service-endpoints-using-api-management/developer-portal.png)

Klik på **API'er** fra den øverste menu, og klik derefter på **AzureML Demo API** for at få vist de tilgængelige handlinger.

![demoazureml-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/demoazureml-api.png)

Vælg **Ressourceposter udføre** for handlingen. Klik på **Prøv den**.

![prøve-it](./media/machine-learning-manage-web-service-endpoints-using-api-management/try-it.png)

Skriv dit **arbejdsområde**, **service**, **2.0** til **apiversion**og **Sand** for at få **mere at vide**for anmodning om parametre. Du kan finde dit **arbejdsområde** og **-tjenesten** i dashboardet AzureML web-tjenesten (se **Test webtjenesten** i tillæg A).

Til anmodning om overskrifter, skal du klikke på **Tilføj sidehoved** og skriv **Indholdstype** og **programmet/json**, og derefter klikke på **Tilføj sidehoved** , og skriv **godkendelse** og **bæreren <YOUR AZUREML SERVICE API-KEY> **. Du kan finde din **api-nøgle** i dashboardet AzureML web-tjenesten (se **Test webtjenesten** i tillæg A).

Skriv **{"Input": {"input1": {"ColumnNames": ["Kol2"], "værdier": [["Dette er en god dag"]]}}, "GlobalParameters": {}}** for anmodningsteksten.

![azureml-demo-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

Klik på **Send**.

![Send](./media/machine-learning-manage-web-service-endpoints-using-api-management/send.png)

Når en handlingen er aktiveret, viser portalen udvikler **Anmodes om en URL-adressen** fra back end-tjenesten, **status for svar**, **svar sidehoveder**og alle **svar indhold**.

![svar-status](./media/machine-learning-manage-web-service-endpoints-using-api-management/response-status.png)

##<a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Tillæg A - oprettelse og test af en simpel AzureML-webtjeneste

###<a name="creating-the-experiment"></a>Oprette forsøget

Nedenfor vises trinnene til at oprette en simpel AzureML forsøg og implementere det som en webtjeneste. De tager web service som input en vilkårlig tekstkolonne og returnerer et sæt af funktioner, der repræsenteres som heltal. Eksempel:

Tekst | Hash'et tekst
--- | ---
Dette er en god dag | 1 1 2 2 0 2 0 1

Først skal bruge en browser efter eget valg, gå til: [https://studio.azureml.net/](https://studio.azureml.net/) og angive dine logonoplysninger for at logge på. Dernæst skal du oprette en ny tom forsøg.

![Søg efter-forsøg-skabeloner](./media/machine-learning-manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Omdøb den til **SimpleFeatureHashingExperiment**. Udvid **Gemt datasæt** , og træk **Adressekartotek anmeldelser fra Amazon** til din forsøg.

![enkel-funktion-krypteres-forsøg](./media/machine-learning-manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Udvid **Datatransformation** og **håndtering** af, og træk **Vælg kolonner i datasæt** til din forsøg. Oprette forbindelse **Adressekartotek anmeldelser fra Amazon** til at **vælge kolonner i datasættet**.

![Vælg kolonner](./media/machine-learning-manage-web-service-endpoints-using-api-management/project-columns.png)

Klik på **Vælg kolonner i Dataset** , og klik på **Start kolonne Datavælger** , og vælg **Kol2**. Klik på markering for at anvende ændringerne.

![Vælg kolonner](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-columns.png)

Udvide **Tekst Analytics** , og træk **Funktion krypteres** til forsøget. Oprette forbindelse **Vælg kolonner i datasæt** til **funktion krypteres**.

![oprette forbindelse-project-kolonner](./media/machine-learning-manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Skriv **3** for **Hashing bitsize**. Dette vil oprette 8 (23) kolonner.

![krypteres bitsize](./media/machine-learning-manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

På dette tidspunkt, kan du klikke på **Kør** for at teste forsøget.

![køre](./media/machine-learning-manage-web-service-endpoints-using-api-management/run.png)

###<a name="create-a-web-service"></a>Oprette en webtjeneste

Opret nu en webtjeneste. Udvid **Webtjeneste** , og træk **Input** til din forsøg. Oprette forbindelse **Input** til **funktion krypteres**. Også trække **output** til din forsøg. Oprette forbindelse **Output** til **funktion krypteres**.

![output-til-funktion-krypteres](./media/machine-learning-manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Klik på **Publicer webtjeneste**.

![publicere-web-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/publish-web-service.png)

Klik på **Ja** for at publicere forsøget.

![Ja til at publicere](./media/machine-learning-manage-web-service-endpoints-using-api-management/yes-to-publish.png)

###<a name="test-the-web-service"></a>Teste webtjenesten

En AzureML webtjeneste består af RSS-(anmodning/svar service) og slutpunkter for BES (batchen udførelse af service). RSS er for synkron udførelse af. BES er til udførelse af asynkrone job. For at teste din webtjeneste med nedenstående eksempel Python kilde, skal du muligvis at hente og installere Azure SDK til Python (se: [Sådan installeres Python](../python-how-to-install.md)).

Du skal også **arbejdsområde**, **service**og **api_key** af din forsøg for eksempelkilden nedenfor. Du kan finde tjenesten arbejdsområde og ved at klikke på enten **Anmodning/svar** eller **Batchen udførelse af** din forsøg i dashboardet web-tjenesten.

![Find-arbejdsområde-og-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Du kan finde **api_key** ved at klikke på din forsøg i dashboardet web-tjenesten.

![Find-api-nøgle](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-api-key.png)

####<a name="test-rrs-endpoint"></a>Test Ressourceposter slutpunkt

#####<a name="test-button"></a>Knappen testbillede til

En nem måde at teste Ressourceposter slutpunktet er at klikke på **Test** på dashboardet til web-tjenesten.

![Test](./media/machine-learning-manage-web-service-endpoints-using-api-management/test.png)

Skriv **dette er en god dag** til **Kol2**. Klik på markeringen.

![angive data](./media/machine-learning-manage-web-service-endpoints-using-api-management/enter-data.png)

Du får vist noget i retning

![eksempel-output](./media/machine-learning-manage-web-service-endpoints-using-api-management/sample-output.png)

#####<a name="sample-code"></a>Eksempel på kode

En anden måde at teste din Ressourceposter er fra din klientkode. Hvis du klikker på **anmodning/svar** på den dashboard, og Rul til bunden, får du vist eksempler på kode til C#, Python og R. Du kan også få vist syntaksen i Ressourceposter anmodningen, herunder anmodningen URI, overskrifter og brødtekst.

Denne vejledning viser en arbejde Python eksempel. Du skal redigere den med det **arbejdsområde**, **service**og **api_key** af din forsøg.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
    data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
    }
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
    headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
    body = str.encode(json.dumps(data))
    try:
        req = urllib2.Request(url, body, headers)
        response = urllib2.urlopen(req)
        result = response.read()
        print "result:" + result
            except urllib2.HTTPError, error:
        print("The request failed with status code: " + str(error.code))
        print(error.info())
        print(json.loads(error.read()))

####<a name="test-bes-endpoint"></a>Test BES slutpunkt
Klik på **udførelse af batchen** på dashboardet og Rul til bunden. Du får vist eksempler på kode til C#, Python og R. Du kan også se syntaksen i BES anmodninger om at sende et job, starte et job, får status eller resultatet af et job og slette et job.

Denne vejledning viser en arbejde Python eksempel. Du vil ændre det med det **arbejdsområde**, **tjeneste**og **api_key** af din forsøg. Desuden skal du ændre **kontonavn lager**, **lager kontonøgle**og **lagerplads objektbeholder navn**. Endelig skal du ændre placeringen af **en fil** og placeringen af **outputfilen**.

    import urllib2
    import json
    import time
    from azure.storage import *
    workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
    service = "<REPLACE WITH YOUR SERVICE ID>"
    api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
    storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
    storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
    storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
    input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
    output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
    input_blob_name = "mydatablob.csv"
    output_blob_name = "myresultsblob.csv"
    def printHttpError(httpError):
    print("The request failed with status code: " + str(httpError.code))
    print(httpError.info())
    print(json.loads(httpError.read()))
    return
    def saveBlobToFile(blobUrl, resultsLabel):
    print("Reading the result from " + blobUrl)
    try:
        response = urllib2.urlopen(blobUrl)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    with open(output_file, "w+") as f:
        f.write(response.read())
    print(resultsLabel + " have been written to the file " + output_file)
    return
    def processResults(result):
    first = True
    results = result["Results"]
    for outputName in results:
        result_blob_location = results[outputName]
        sas_token = result_blob_location["SasBlobToken"]
        base_url = result_blob_location["BaseLocation"]
        relative_url = result_blob_location["RelativeLocation"]
        print("The results for " + outputName + " are available at the following Azure Storage location:")
        print("BaseLocation: " + base_url)
        print("RelativeLocation: " + relative_url)
        print("SasBlobToken: " + sas_token)
        if (first):
            first = False
            url3 = base_url + relative_url + sas_token
            saveBlobToFile(url3, "The results for " + outputName)
    return

    def invokeBatchExecutionService():
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
    blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
    print("Uploading the input to blob storage...")
    data_to_upload = open(input_file, "r").read()
    blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
    print "Uploaded the input to blob storage"
    input_blob_path = "/" + storage_container_name + "/" + input_blob_name
    connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
    payload =  {
        "Input": {
            "ConnectionString": connection_string,
            "RelativeLocation": input_blob_path
        },
        "Outputs": {
            "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
        },
        "GlobalParameters": {
        }
    }
        body = str.encode(json.dumps(payload))
    headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
    print("Submitting the job...")
    # submit the job
    req = urllib2.Request(url + "?api-version=2.0", body, headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = response.read()
    job_id = result[1:-1] # remove the enclosing double-quotes
    print("Job ID: " + job_id)
    # start the job
    print("Starting the job...")
    req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    url2 = url + "/" + job_id + "?api-version=2.0"

    while True:
        print("Checking the job status...")
        # If you are using Python 3+, replace urllib2 with urllib.request in the follwing code
        req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
        try:
            response = urllib2.urlopen(req)
        except urllib2.HTTPError, error:
            printHttpError(error)
            return
        result = json.loads(response.read())
        status = result["StatusCode"]
        print "status:" + status
        if (status == 0 or status == "NotStarted"):
            print("Job " + job_id + " not yet started...")
        elif (status == 1 or status == "Running"):
            print("Job " + job_id + " running...")
        elif (status == 2 or status == "Failed"):
            print("Job " + job_id + " failed!")
            print("Error details: " + result["Details"])
            break
        elif (status == 3 or status == "Cancelled"):
            print("Job " + job_id + " cancelled!")
            break
        elif (status == 4 or status == "Finished"):
            print("Job " + job_id + " finished!")
            processResults(result)
            break
        time.sleep(1) # wait one second
    return
    invokeBatchExecutionService()
