<properties
    pageTitle="Hvordan du kan bruge Service API (Python) - funktion vejledning"
    description="Lær, hvordan til at udføre almindelige opgaver til administration af tjenesten fra Python fra et program."
    services="cloud-services"
    documentationCenter="python"
    authors="lmazuel"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="lmazuel"/>

# <a name="how-to-use-service-management-from-python"></a>Sådan bruger du Service Management fra Python

> [AZURE.NOTE] Service Management API erstattes med den nye ressource Management API, tilgængelige i preview-versionen.  Se i [dokumentationen til Azure ressourcestyring](http://azure-sdk-for-python.readthedocs.org/) for oplysninger om brug af det nye ressource Management API fra Python.

Denne vejledning viser, hvordan du automatisk udføre almindelige service management opgaver fra Python. Klassen **ServiceManagementService** i [Azure SDK for Python](https://github.com/Azure/azure-sdk-for-python) understøtter programmeringsmæssig adgang til meget af den service management-relaterede funktionalitet, som er tilgængelig i [Azure klassisk portal] [ management-portal] (såsom **oprette, opdatere, og slette skytjenester, installationer, data management services og virtuelle computere**). Denne funktion kan være praktiske til opbygning af programmer, der skal have programmeringsmæssig adgang til administration af tjenesten.

## <a name="WhatIs"> </a>Hvad er Service Management
Service Management API giver programmeringsmæssig adgang til meget af den service management-funktionalitet, der er tilgængelige via [Azure klassisk portal][management-portal]. Azure SDK for Python tillader dig at administrere dine skytjenester og lagerplads firmaer.

Hvis du vil bruge Service Management API, skal du [oprette en Azure-konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"> </a>Begreber
Azure SDK for Python ombrydes [Azure Service Management API][svc-mgmt-rest-api], som er en REST-API. Alle API handlinger udføres over SSL og godkendt indbyrdes ved hjælp af x.509-v3 certifikater. Management-tjenesten kan få adgang til fra en tjeneste, der kører i Azure eller direkte via internettet fra et program, der kan sende en HTTPS-anmodning og modtage en HTTPS-svar.

## <a name="Installation"> </a>Installation

Alle de funktioner, der er beskrevet i denne artikel findes i den `azure-servicemanagement-legacy` pakke, som du kan installere ved hjælp af pip. For flere oplysninger om installation (for eksempel, hvis du er ny bruger af Python), finder du i denne artikel: [installere Python og Azure SDK](../python-how-to-install.md)

## <a name="Connect"> </a>Sådan: oprette forbindelse til administration af tjenesten
Hvis du vil oprette forbindelse til slutpunktet Service Management, skal du dit Azure abonnement-ID og et gyldigt management-certifikat. Du kan få dit abonnement-ID via [Azure klassisk portal][management-portal].

> [AZURE.NOTE] Siden Azure SDK for Python v0.8.0 er det nu muligt at bruge certifikater, der er oprettet med OpenSSL, når der benyttes i Windows.  Det kræver Python 2.7.4 eller nyere. Vi anbefaler, at brugere at bruge OpenSSL i stedet for .pfx, siden understøttelse af .pfx certifikater fjernes sandsynligt i fremtiden.

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Administration af certifikater på Mac-Windows-Linux (OpenSSL)
Du kan bruge [OpenSSL](http://www.openssl.org/) til at oprette din management-certifikat.  Du rent faktisk brug at oprette to certifikater, én for serveren (en `.cer` filer) og én til klienten (en `.pem` filer). Til at oprette den `.pem` fil ved at udføre:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Til at oprette den `.cer` certifikatet, udføre:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Du kan finde flere oplysninger om Azure certifikater, [Oversigt over certifikater til Azure Cloud Services](./cloud-services-certs-create.md). Du kan finde en fuldstændig beskrivelse af OpenSSL parametre på [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Når du har oprettet disse filer, du har brug at overføre den `.cer` fil til Azure via handlingen "Overfør" under fanen "Indstillinger" [Azure klassisk portal][management-portal], og du skal være opmærksom på, hvor du gemte den `.pem` fil.

Når du har fået dit abonnement-ID, oprettes et certifikat, og der er overført den `.cer` fil til Azure, du kan oprette forbindelse til Azure management slutpunktet ved at overføre abonnement-id og stien til den `.pem` fil, der skal **ServiceManagementService**:

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

I det foregående eksempel `sms` er et **ServiceManagementService** objekt. Klassen **ServiceManagementService** er den primære klasse, der bruges til at administrere Azure tjenester.

### <a name="management-certificates-on-windows-makecert"></a>Administration af certifikater i Windows (MakeCert)

Du kan oprette et selvsigneret certifikat på din computer ved hjælp af `makecert.exe`.  Åbn en **kommandoprompt Visual Studio** som en **administrator** , og brug følgende kommando, erstatter *AzureCertificate* med navnet på certifikatet, du vil bruge.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Kommandoen opretter den `.cer` fil, og installerer det i den **personlige** lager. Se [Oversigt over certifikater til Azure Cloud Services](./cloud-services-certs-create.md)for flere oplysninger.

Når du har oprettet certifikatet, du har brug at overføre den `.cer` fil til Azure via handlingen "Overfør" under fanen "Indstillinger" [Azure klassisk portal][management-portal].

Når du har fået dit abonnement-ID, oprettes et certifikat, og der er overført den `.cer` fil til Azure, du kan oprette forbindelse til Azure management slutpunktet ved at overføre abonnement-id og placeringen af certifikatet i dit **personlige** lager til **ServiceManagementService** (igen, erstatte *AzureCertificate* med navnet på dit certifikat):

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

I det foregående eksempel `sms` er et **ServiceManagementService** objekt. Klassen **ServiceManagementService** er den primære klasse, der bruges til at administrere Azure tjenester.

## <a name="ListAvailableLocations"> </a>Sådan: liste over tilgængelige placeringer

Du kan få vist de placeringer, der er tilgængelige for værtstjenester, bruge den **listen\_placeringer** metode:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Når du opretter en skybaseret tjeneste eller lagringstjeneste skal du angive en gyldig placering. Den **listen\_placeringer** metode altid returnerer en fortegnelse over de tilgængelige placeringer. Og denne du skriver er de tilgængelige placeringer:

- Vest Europe
- Nord Europe
- Sydøstasien
- Sydøstasien
- Centrale USA
- Nord centrale USA
- Syd centrale USA
- Vest USA
- Indtastning af østasiatiske USA
- Japan øst
- Japan vest
- Brasilien syd
- Australien øst
- Australien Sydøst

## <a name="CreateCloudService"> </a>Sådan: oprette en skybaseret tjeneste

Når du opretter et program og køre i Azure, kaldes den kode og konfiguration af sammen en Azure [skybaseret tjeneste][] (kendt som en *hostet service* fra de tidligere Azure versioner). Den **oprette\_hostet\_service** metode kan du oprette en ny hostet tjeneste ved at give et hostet service name (som skal være entydige i Azure), en etiket (automatisk kodet til base64), en beskrivelse og en placering.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Du kan få vist alle de tilknyttede tjenester for dit abonnement med den **listen\_hostet\_services** metode:

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Hvis du vil have oplysninger om en bestemt hostet tjeneste, kan du gøre det ved at overføre hostet service navnet til den **få\_hostet\_service\_egenskaber** metode:

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Når du har oprettet en skybaseret tjeneste, du kan installere din kode til tjenesten med den **oprette\_installation** metode.

## <a name="DeleteCloudService"> </a>Sådan: slette en skybaseret tjeneste

Du kan slette en skybaseret tjeneste ved at overføre navnet på tjenesten til den **slette\_hostet\_service** metode:

    sms.delete_hosted_service('myhostedservice')

Før du kan slette en tjeneste, slettes alle installationer for tjenesten først. (Se [Sådan: slette en installation](#DeleteDeployment) få mere at vide.)

## <a name="DeleteDeployment"> </a>Sådan: slette en installation

Hvis du vil slette en installation, skal du bruge den **slette\_installation** metode. Følgende eksempel viser, hvordan du sletter en installation med navnet `v1`.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Sådan: oprette en lagringstjeneste

En [lagringstjeneste](../storage/storage-create-storage-account.md) giver dig adgang til Azure [BLOB](../storage/storage-python-how-to-use-blob-storage.md), [tabeller](../storage/storage-python-how-to-use-table-storage.md)og [køer](../storage/storage-python-how-to-use-queue-storage.md). Hvis du vil oprette en lagringstjeneste, skal du et navn til tjenesten (mellem 3 og 24 små bogstaver og unikt i Azure), en beskrivelse, et navn (op til 100 tegn, automatisk kodet til base64) og en placering. I følgende eksempel viser, hvordan du opretter en lagringstjeneste ved at angive en placering.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mystorageaccount'
    label = 'mystorageaccount'
    location = 'West US'
    desc = 'My storage account description.'

    result = sms.create_storage_account(name, desc, label, location=location)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Note i det foregående eksempel, der status for det **oprette\_lagerplads\_konto** handling kan hente den ved at overføre det resultat, der returneres af **oprette\_lagerplads\_konto** til den **få\_handlingen\_status** metode.  

Du kan angive kontiene lager og deres egenskaber med den **listen\_lagerplads\_konti** metode:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>Sådan: slette en lagringstjeneste

Du kan slette en lagringstjeneste ved at overføre lagerplads service navnet til den **slette\_lagerplads\_konto** metode. Slette en lagringstjeneste sletter alle data i tjenesten (BLOB, tabeller og køer).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>Sådan: liste over tilgængelige operativsystemer

Du kan få vist de operativsystemer, der er tilgængelige for værtstjenester, bruge den **listen\_operativsystem\_systemer** metode:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Du kan også bruge den **listen\_operativsystem\_system\_familier** metode, der grupperer operativsystemer af familie:

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"> </a>Sådan: oprette et operativsystembillede

Hvis du vil tilføje en afbildning af operativsystemet til billede-lager, bruge den **tilføje\_os\_billede** metode:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mycentos'
    label = 'mycentos'
    os = 'Linux' # Linux or Windows
    media_link = 'url_to_storage_blob_for_source_image_vhd'

    result = sms.add_os_image(label, media_link, name, os)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Du kan få vist de operativsystembilleder, der er tilgængelige, bruge den **listen\_os\_billeder** metode. Den indeholder alle platform billeder og bruger billeder:

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Media link: ' + image.media_link)
        print('')

## <a name="DeleteVMImage"> </a>Sådan: slette et billede af operativsystem

Hvis du vil slette et billede af brugeren, skal du bruge den **slette\_os\_billede** metode:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Sådan: oprette en virtuel maskine

Hvis du vil oprette en virtuel maskine, skal du først oprette en [skybaseret tjeneste](#CreateCloudService).  Opret derefter den virtuelt installation ved hjælp af den **oprette\_virtuelle\_maskine\_installation** metode:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Name of an os image as returned by list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

    # Destination storage account container/blob where the VM disk
    # will be created
    media_link = 'url_to_target_storage_blob_for_vm_hd'

    # Linux VM configuration, you can use WindowsConfigurationSet
    # for a Windows VM instead
    linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

    os_hd = OSVirtualHardDisk(image_name, media_link)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=os_hd,
        role_size='Small')

## <a name="DeleteVM"> </a>Sådan: slette en virtuel maskine

Hvis du vil slette en virtuel maskine, er du først slette installation ved hjælp af den **slette\_installation** metode:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

Skytjenesten kan derefter slettes ved hjælp af den **slette\_hostet\_service** metode:

    sms.delete_hosted_service(service_name='myvm')

##<a name="how-to-create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Sådan: Oprette en virtuel maskine fra et hentede virtuelt billede

For at optage en VM billede, du først ringe på **registrere\_vm\_billede** metode:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    # replace the below three parameters with actual values
    hosted_service_name = 'hs1'
    deployment_name = 'dep1'
    vm_name = 'vm1'

    image_name = vm_name + 'image'
    image = CaptureRoleAsVMImage    ('Specialized',
        image_name,
        image_name + 'label',
        image_name + 'description',
        'english',
        'mygroup')

    result = sms.capture_vm_image(
            hosted_service_name,
            deployment_name,
            vm_name,
            image
        )

Derefter bruger for at sikre dig, at du har hentet billedet korrekt, den **listen\_vm\_billeder** api, og Sørg for, at dit billede vises i resultaterne:

    images = sms.list_vm_images()

Brug til at oprette den virtuelle maskine ved hjælp af skærmklippet til sidst på **oprette\_virtuelle\_maskine\_installation** metode som før, men denne gang overfører i vm_image_name i stedet

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=None,
        role_size='Small',
        vm_image_name = image_name)

Du kan få mere at vide om at hente en Linux virtuel maskine, [hvordan du kan registrere en Linux virtuel maskine.](../virtual-machines/virtual-machines-linux-classic-capture-image.md)

Du kan få mere at vide om at hente en Windows virtuel maskine, [hvordan du kan registrere en Windows virtuel maskine.](../virtual-machines/virtual-machines-windows-classic-capture-image.md)

## <a name="What's Next"> </a>Næste trin

Nu hvor du har lært de grundlæggende regler for administration af tjenesten, kan du få adgang til [komplet API referencedokumentation til Azure Python SDK](http://azure-sdk-for-python.readthedocs.org/) og udføre komplekse opgaver nemt for at administrere dit python program.

Du kan finde yderligere oplysninger finder [Python Developer Center](/develop/python/).

[What is Service Management]: #WhatIs
[Concepts]: #Concepts
[How to: Connect to service management]: #Connect
[How to: List available locations]: #ListAvailableLocations
[How to: Create a cloud service]: #CreateCloudService
[How to: Delete a cloud service]: #DeleteCloudService
[How to: Create a deployment]: #CreateDeployment
[How to: Update a deployment]: #UpdateDeployment
[How to: Move deployments between staging and production]: #MoveDeployments
[How to: Delete a deployment]: #DeleteDeployment
[How to: Create a storage service]: #CreateStorageService
[How to: Delete a storage service]: #DeleteStorageService
[How to: List available operating systems]: #ListOperatingSystems
[How to: Create an operating system image]: #CreateVMImage
[How to: Delete an operating system image]: #DeleteVMImage
[How to: Create a virtual machine]: #CreateVM
[How to: Delete a virtual machine]: #DeleteVM
[Next Steps]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[skybaseret tjeneste]:/services/cloud-services/

