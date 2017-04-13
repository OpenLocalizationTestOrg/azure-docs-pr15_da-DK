<properties
   pageTitle="Anbefalede navngivningskonventioner for Azure ressourcer | Vejledning | Microsoft Azure"
   description="Anbefalede navngivningskonventioner for Azure ressourcer. Sådan navngives virtuelle maskiner, lager konti, netværk, virtuelle netværk, undernet og andre Azure enheder"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/27/2016"
   ms.author="christb"/>
   
# <a name="recommended-naming-conventions-for-azure-resources"></a>Anbefalede navngivningskonventioner for Azure ressourcer

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Valg af et navn for en ressource i Microsoft Azure er vigtigt, fordi:

- Det er svært at ændre et navn på et senere tidspunkt.
- Navne skal opfylde ressourcetype specifikke krav.

Ensartet navngivningsmetode gør det nemmere at finde ressourcer. De kan også angive rolle i en ressource i en løsning.

I denne artikel er en oversigt over de naming regler og begrænsninger for Azure ressourcer og en oprindelig plan række anbefalinger til navngivningskonventioner.  Du kan bruge disse anbefalinger som udgangspunkt til dine egne bestemte konventioner til dine behov.  

Hemmeligheden bag med navngivningskonventioner et godt om oprettelse af og følge dem på tværs af dine programmer og -organisationer. 

## <a name="naming-subscriptions"></a>Navngive abonnementer

Når du navngiver Azure abonnementer, detaljeret navne gør om kontekst og formålet med hvert Ryd abonnement.  Når du arbejder i et miljø med mange abonnementer, kan følge et delt navnekonventionen forbedre klarhed.

Et anbefalet mønster for navngivning abonnementer er:

`<Company> <Department (optional)> <Product Line (optional)> <Environment>`

- Virksomhed vil normalt være den samme for hvert abonnement. Virksomheden kan dog have underordnede firmaer i organisationsstruktur. Disse virksomheder kan administreres af en central IT-gruppe. I disse tilfælde kan de opdelte ved at få både overordnede firmanavnet (*Contoso*) og underordnede virksomhedens navn (*Nord vindhastighed bliver*).

- Afdeling er et navn i organisationen, hvor en gruppe af personer arbejder. Dette element i navneområdet som valgfrit.

- Produktlinje er et bestemt navn for et produkt eller en funktion, der udføres fra inden for afdelingen.
Dette er normalt valgfrit for interne tjenester og programmer. Men det anbefales at bruge for offentligt tjenester, der kræver nemt adskillelse og identifikation (f.eks. til klar adskillelse fakturering poster).

- Miljø er det navn, der beskriver de programmer eller tjenester, som Udviklingscenter, QA eller Prod.ordre installationslivscyklus.

| Virksomhed | Afdeling | Produkt eller tjeneste | Miljø | Fulde navn  |
----------| ---------- | ----------------------- | ----------- | ---------- |
| Contoso | SocialGaming | AwesomeService | Fremstilling | Contoso SocialGaming AwesomeService fremstilling |
| Contoso | SocialGaming | AwesomeService | Udviklingscenter | Contoso SocialGaming AwesomeService Udviklingscenter |
| Contoso | DET | InternalApps | Fremstilling | Contoso IT InternalApps fremstilling |
| Contoso | DET | InternalApps | Udviklingscenter | Contoso IT InternalApps Udviklingscenter |

<!-- TODO; include more information about organizing subscriptions for application deployment, pods, etc. -->

## <a name="use-affixes-to-avoid-ambiguity"></a>Brug foranstillede for at undgå tvetydighed

Når du navngiver ressourcer i Azure, anbefales det at bruge almindelige præfikser eller suffikser til at identificere den type og kontekst ved en ressource.  Mens alle oplysninger om type metadata kontekst er tilgængelig fra et program, skal du anvende almindelige foranstillede forenkler visuel identifikation.  Til at inkorporere foranstillede til din navnekonventionen, er det vigtigt at klart Angiv, om affiks, der er i starten af navnet (præfiks) eller i slutningen (suffiks).  

Her er to mulige navne til en tjeneste, der er vært for en beregning program f.eks.:

- SvcCalculationEngine (præfiks)
- CalculationEngineSvc (suffiks)

Foranstillede kan referere til forskellige aspekter, der beskriver de specifikke ressourcer. I følgende tabel vises nogle eksempler, der typisk bruges.

| Højde | Eksempel | Noter |
| ------ | ------- | ----- |
| Miljø | Udviklingscenter, Prod.ordre, QA | Identificerer miljø til ressourcen |
| Placering | uw (OS vest), ue (OS øst) | Identificerer det område, hvor ressourcen er installeret |
| Forekomst | 01, 02 | For de ressourcer, der har mere end én navngivet forekomst (-webserverne osv.). |
| Produkt eller tjeneste | Tjenesten | Identificerer det produkt, programmet eller tjeneste, der understøtter ressourcen |
| Rolle | SQL, web, messaging | Identificerer rollen på den tilknyttede ressource |

Når udvikling af en bestemt Navnekonventionen for din virksomhed eller projekter, er det vigtigste er at vælge en fælles gruppe foranstillede og deres placering (suffiks eller præfiks).

## <a name="naming-rules-and-restrictions"></a>Navngivning af regler og begrænsninger

Hver ressource eller tjeneste type i Azure gennemtvinge et sæt navngive begrænsninger og omfang; en hvilken som helst navnekonventionen eller et mønster skal overholde de påkrævede naming regler og omfang.  For eksempel mens navnet på en VM knyttes til en DNS-navn (og dermed skal være entydige på tværs af alle Azure), er navnet på en VNET fastsat til gruppen ressource, som den oprettes i.

Generelt undgå nogen specialtegn (`-` eller `_`) som det første eller sidste tegn i en hvilken som helst navn. Disse tegn, der medfører, at de fleste valideringsregler mislykkes.

| Kategori | Tjenesten eller enhed | Omfang | Længde | Hus | Gyldige tegn | Foreslåede mønster | Eksempel |
| ------------- | ----------------- | ----- | ------ | ------ | ---------------- | ----------------- | ------- |
| Ressourcegruppe | Ressourcegruppe | Global | 1-64 | Bogstaver | Alfanumeriske, understregning og bindestreg | `<service short name>-<environment>-rg` | `profx-prod-rg` |
| Ressourcegruppe | Tilgængelighed sæt | Ressourcegruppe | 1-80 | Bogstaver | Alfanumeriske, understregning og bindestreg | `<service-short-name>-<context>-as` | `profx-sql-as` |
| Generelt | Mærke | Tilknyttet enhed | 512 (navn), 256 (værdi) | Bogstaver | Alfanumeriske | `"key" : "value"` | `"department" : "Central IT"` |
| Beregne | Virtuelt | Ressourcegruppe | 1-15 | Bogstaver | Alfanumeriske, understregning og bindestreg | `<name>-<role>-<instance>` | `profx-sql-001` |
| Lagerplads | Kontonavn lager (data) | Global | 3-24 | Små bogstaver | Alfanumeriske | `<service short name><type><number>` | `profxdata001` |
| Lagerplads | Kontonavn lager (diske) | Global | 3-24 | Små bogstaver | Alfanumeriske | `<vm name without dashes>st<number>` | `profxsql001st0` |
| Lagerplads | Navnet på objektbeholderen | Lagerplads konto | 3-63 |   Små bogstaver | Alfanumeriske og streg | `<context>` | `logs` |
| Lagerplads | BLOB navn | Objektbeholder | 1-1024 | Store og små bogstaver | Tegn en URL-adresse | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Lagerplads | Kø navn | Lagerplads konto | 3-63 | Små bogstaver | Alfanumeriske og streg | `<service short name>-<context>-<num>` | `awesomeservice-messages-001` |
| Lagerplads | Tabelnavn | Lagerplads konto | 3-63 |Bogstaver | Alfanumeriske | `<service short name>-<context>` | `awesomeservice-logs` |
| Lagerplads | Filnavn | Lagerplads konto | 3-63 | Små bogstaver | Alfanumeriske | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Netværk | Virtuelt netværk (VNet) | Ressourcegruppe | 2-64 | Case-insensitive | Alfanumeriske, bindestreg, understregning og periode | `<service short name>-[section]-vnet` | `profx-vnet` |
| Netværk | Undernet | Overordnede VNet | 2-80 | Case-insensitive | Alfanumeriske, understregningstegn, bindestreg og periode | `<role>-subnet` | `gateway-subnet` |
| Netværk | Netværksgrænsefladen | Ressourcegruppe | 1-80 | Case-insensitive | Alfanumeriske, bindestreg, understregning og periode | `<vmname>-<num>nic` | `profx-sql1-1nic` |
| Netværk | Netværk sikkerhedsgruppe | Ressourcegruppe | 1-80 | Case-insensitive | Alfanumeriske, bindestreg, understregning og periode | `<service short name>-<context>-nsg` | `profx-app-nsg` |
| Netværk | Netværk sikkerhedsgruppe regel | Ressourcegruppe | 1-80 | Case-insensitive | Alfanumeriske, bindestreg, understregning og periode | `<descriptive context>` | `sql-allow` |
| Netværk | Offentlige IP-adresse | Ressourcegruppe | 1-80 | Case-insensitive | Alfanumeriske, bindestreg, understregning og periode | `<vm or service name>-pip` | `profx-sql1-pip` |
| Netværk | Justering af belastning | Ressourcegruppe | 1-80 | Case-insensitive | Alfanumeriske, bindestreg, understregning og periode | `<service or role>-lb` | `profx-lb` |
| Netværk | Indlæse ikke-opgjorte regler Config | Justering af belastning | 1-80 | Case-insensitive | Alfanumeriske, bindestreg, understregning og periode | `descriptive context` | `http` |

<!-- TODO fill in the rest of these resources
| Networking | Azure Application Gateway | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | `<service or role>-aag` | `profx-aag`
| Networking | Azure Application Gateway Connection | Azure Application Gateway | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | `` | TODO
| Networking | Traffic Manager Profile | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | TODO | TODO
-->

## <a name="organizing-resources-with-tags"></a>Organisere ressourcer med mærker

Ressourcestyring Azure understøtter mærkning enheder med vilkårlig tekststrenge til at identificere kontekst og strømline automatisering.  For eksempel mærket `"sqlVersion: "sql2014ee"` der identificerer FOS i en installation, der kører SQL Server 2014 Enterprise Edition til kørsel af et automatiseret script mod dem.  Mærker skal bruges til at udvide og forbedre kontekst langs siden af de navngivningskonventioner, der er valgt.

> [AZURE.TIP]En anden fordel ved mærker er, mærker strækker sig over grupper, så du kan sammenkæde og koordinere objekter på tværs af forskellige installationer.

Hver ressource eller ressourcegruppe kan have op til **15** mærker. Kodenavnet er begrænset til 512 tegn, og tag værdi er begrænset til 256 tegn.

Referere til [Brug af mærker til at organisere dine Azure ressourcer](../resource-group-using-tags.md)kan finde flere oplysninger om mærkning ressource.

Nogle af de almindelige mærkning use cases er:

- **Fakturering**; Gruppere ressourcer og knytte dem til fakturerings- eller gebyr tilbage koder.
- **Tjenesten kontekst identifikation**; Identificere grupper af ressourcer på tværs af grupper til almindelige handlinger og gruppering
- **Kontrol af adgang og sikkerhedskontekst**; Administrativ rolle identifikation baseret på portefølje, system, service, app, forekomst osv.

> [AZURE.TIP]Mærke tidligt - mærke ofte.  Bedre for at have en oprindelig plan mærkning farveskema på plads, og Juster over tid i stedet for at tilpasse oplysninger.  

Et eksempel på nogle almindelige mærkning fremgangsmåder:

| Kodenavn | Nøgle | Eksempel | Kommentar |
| -------- | --- | ------- | ------- |
| Faktura til / interne kortejeren-ID | billTo  | `IT-Chargeback-1234` | Et internt i/o- eller faktureringsadministrator kode |
| Operatoren eller direkte ansvarlig person (DRI) | managedBy | `joe@contoso.com`  | Alias eller mailadresse |
| Projektnavn | Project-navn | `myproject`  | Navnet på linjen projekt eller produkt |
| Project-versionen | Project-version | `3.4`  | Version af linjen projekt eller produkt |
| Miljø | miljø | `<Production, Staging, QA >` | Påvirkning id | 
| Niveau | niveau | `Front End, Back End, Data` | Niveau eller rolle/kontekst identifikation |
| Data-profil | dataProfile | `Public, Confidential, Restricted, Internal` | Data gemt i ressourcen følsomhed |
 
## <a name="tips-and-tricks"></a>Tip og Tricks

Nogle typer ressourcer kan kræve ekstra forsigtig på navngive og konventioner.

### <a name="virtual-machines"></a>Virtuelle maskiner

Især i større topologier strømliner omhyggeligt navngive virtuelle maskiner identificere rolle og formålet med hver computer og aktivere mere forudsigelige scripting.

> [AZURE.WARNING]Hver virtuel maskine i Azure har både en Azure ressourcenavn og en operativsystem værtsnavn.  
> Hvis ressourcenavn og værtsnavn er forskellige, administration af FOS kan være en udfordring og undgås.
> Hvis en virtuel maskine er oprettet ud fra en .vhd, der allerede indeholder et konfigureret operativsystem med et værtsnavn.

- [Navngivningskonventioner for Windows Server FOS](https://support.microsoft.com/en-us/kb/188997)

<!-- TODO - recommendations on naming VMs. -->

### <a name="storage-accounts-and-storage-entities"></a>Lagerplads konti og lagerplads enheder

Der findes to primære use cases for lagerplads konti – sikkerhedskopiering diske for FOS og lagring af data i blob, køer og tabeller.  Lagerplads konti, der bruges om en VM disk skal følge navnekonventionen knytte dem til overordnet VM navn (og med potentielle brug for flere lagerplads konti for avancerede VM lagerenheder, også anvende et talformat suffiks).

> [AZURE.TIP]Lagerplads konti – om for data eller diske - skal du følge en navngivningsregel, der gør det muligt for flere lagerplads konti, for at være opgraderede (det vil sige altid med et numerisk suffiks).

Det er muligt at konfigurere et brugerdefineret domænenavn til at få adgang til blob-data i kontoen Azure-lager.
Standardslutpunkt for tjenesten Blob er `https://mystorage.blob.core.windows.net`.

Men hvis du knytter et brugerdefineret domæne (såsom www.contoso.com) til blob slutpunkt for kontoen lagerplads, du kan også åbne blob data i din lagerplads konto ved hjælp af domænet. For eksempel med et brugerdefineret domænenavn `http://mystorage.blob.core.windows.net/mycontainer/myblob` kunne åbnes som `http://www.contoso.com/mycontainer/myblob`.

Se [konfigurere et brugerdefineret domænenavn til din Blob storage slutpunkt](../storage/storage-custom-domain-name.md)kan finde flere oplysninger om konfiguration af denne funktion.

Yderligere oplysninger om navngivning af blob, beholdere og tabeller:

- [Navngive og henvisninger til beholdere, BLOB og Metadata](https://msdn.microsoft.com/library/dd135715.aspx)
- [Navngive køer og Metadata](https://msdn.microsoft.com/library/dd179349.aspx)
- [Navngivning af tabeller](https://msdn.microsoft.com/library/azure/dd179338.aspx)

Et blob navn kan indeholde enhver kombination af tegn, men reserverede URL-adressen tegn skal udelades korrekt. Undgå blob navne, der slutter med et punktum (.), en skråstreg (/), eller en sekvens eller en kombination af to. Skråstregen er som konferencen, **virtual** directory separatoren. Brug ikke en omvendt skråstreg (\) i et blob-navn. Klienten API'er kan tillade, at det, men ikke derefter hash korrekt, og signaturer matcher ikke.

Det er ikke muligt at ændre navnet på et lager firma eller en objektbeholder, når den er blevet oprettet.
Hvis du vil bruge et nyt navn, skal du slette den og oprette en ny.

> [AZURE.TIP] Vi anbefaler, at du har oprettet en navngivningsregel for alle lagerplads brugerkonti og typer før gå i gang med udvikling af en ny tjeneste eller et program.

## <a name="example---deploying-an-n-tier-service"></a>Eksempel - implementering af en n-delt tjeneste

I dette eksempel skal vi definere en n-delt tjenestekonfiguration bestående af front end IIS-servere (vært i Windows Server FOS) med SQL Server (vært i to Windows Server FOS), en ElasticSearch klynge (vært 6 Linux FOS) og de tilknyttede lagerplads konti, virtuelt Network ressource gruppere og justering af belastning.

Vi begynder ved at definere de kontekstafhængige konventioner for dette program:

| Enhed | Konferencen | Beskrivelse  |
| ------ | ---------- | ------------ |  
| Navnet på tjenesten | `profx` | Det korte navn på programmet eller tjenesten er installeret |
| Miljø | `prod` | Dette gælder produktionsmiljø (i modsætning til QA, test osv.) |

Fra oprindeligt kan vi derefter afbilde konventionerne for hver af ressourcetyperne:

| Ressourcetype | Konferencen Base | Eksempel |
| ------------- | --------------- | ------- |
| Abonnement | `<Company> <Department (optional)> <Product Line (optional)> <Environment>` | `Contoso IT InternalApps Profx Production` |
| Ressourcegruppe | `servicename-rg` | `profx-rg` |
| Virtuelt netværk | `servicename-vnet` | `profx-vnet` |
| Undernet | `role-subnet` | `sql-vnet` |
| Justering af belastning | `servicename-lb` | `profx-lb` |
| Virtuelt | `servicename-role[number]` | `profx-sql0` |
| Lagerplads konto | `<vmnamenodashes>st<num>` | `profxsql0st0` |

Som det fremgår diagram følgende:

![programmet Topologidiagram] (media/guidance-naming-conventions/guidance-naming-convention-example.png "Eksempel topologi for søgeprogram")

## <a name="sample---azure-cli-script-for-deploying-the-sample-above"></a>Eksempel - Azure CLI script til implementering af eksemplet ovenfor

```bash
#!/bin/sh

#####################################################################
# Sample script using the Azure CLI to build out an application 
# demonstrating naming conventions.  
#
# Note; this script is not intended for production deployment, as it does 
# not create availability sets, configure network security rules, etc.
#####################################################################

# Set up variables to build out the naming conventions for deploying
# the cluster  
LOCATION=eastus2
APP_NAME=profx
ENVIRONMENT=prod
USERNAME=testuser
PASSWORD="testpass"

# Set up the tags to associate with items in the application
TAG_BILLTO="InternalApp-ProFX-12345"
TAGS="billTo=${TAG_BILLTO}"

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
SUBSCRIPTION=3e9c25fc-55b3-4837-9bba-02b6eb204331

# Set up the names of things using recommended conventions
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
VNET_NAME="${APP_NAME}-vnet"

# Set up the postfix variables attached to most CLI commands
POSTFIX="--resource-group ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}"

##########################################################################################
# Set up the VM conventions for Linux and Windows images

# For Windows, get the list of URN's via 
# azure vm image list ${LOCATION} MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

# For Linux, get the list or URN's via 
# azure vm image list ${LOCATION} canonical ubuntuserver
LINUX_BASE_IMAGE=canonical:ubuntuserver:16.04.0-DAILY-LTS:16.04.201602130

#########################################################################################
## Define functions 
create_vm ()
{
    vm_name=$1
    vnet_name=$2
    subnet_name=$3
    os_type=$4
    vhd_path=$5
    vm_size=$6
    diagnostics_storage=$7

    # Create the network interface card for this VM
    azure network nic create --name "${vm_name}-0nic" --subnet-name ${subnet_name} --subnet-vnet-name ${vnet_name} \
        --tags="${TAGS}" ${POSTFIX}

    # Create the storage account for this vm's disks (premium locally redundant storage -> PLRS)
    # Note the ${var//-/} syntax to remove dashes from the vm name
    storage_account_name=${vm_name//-/}st01
    azure storage account create --type=PLRS --tags "${TAGS}" ${POSTFIX} "${storage_account_name}"

    # Map the name of the diagnostics storage account to a blob URI for boot diagnostics
    # This is (currently) required when deploying with a named premium storage account 
    diag_blob="https://${diagnostics_storage}.blob.core.windows.net/"

    # Create the VM
    azure vm create --name ${vm_name} --nic-name "${vm_name}-0nic" --os-type ${os_type} \
        --image-urn ${vhd_path} --vm-size ${vm_size} --vnet-name ${vnet_name} --vnet-subnet-name ${subnet_name} \
        --storage-account-name "${storage_account_name}" --storage-account-container-name vhds --os-disk-vhd "${vm_name}-osdisk.vhd" \
        --admin-username "${USERNAME}" --admin-password "${PASSWORD}" \
        --boot-diagnostics-storage-uri "${diag_blob}" \
        --tags="${TAGS}" ${POSTFIX} 
}

###################################################################################################
# Create resources

# Step 1 - create the enclosing resource group
azure group create --name "${RESOURCE_GROUP}" --location "${LOCATION}" --tags "${TAGS}" --subscription "${SUBSCRIPTION}"

# Step 2 - create the network security groups

# Step 3 - create the networks (VNet and subnets)
azure network vnet create --name "${VNET_NAME}" --address-prefixes="10.0.0.0/8" --tags "${TAGS}" ${POSTFIX}
# TODO - does subnet support tagging?
azure network vnet subnet create --name gateway-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.1.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-master-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.2.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-data-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.3.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name sql-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.4.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}

# Step 4 - define the load balancer and network security rules
azure network lb create --name "${APP_NAME}-lb" ${POSTFIX}
# In a production deployment script, we'd create load balancer rules and 
# network security groups here

# Step 5 - create a diagnostics storage account
diagnostics_storage_account=${APP_NAME//-/}diag
azure storage account create --type=LRS --tags "${TAGS}" ${POSTFIX} "${diagnostics_storage_account}"

# Step 6.1 - Create the gateway VMs
for i in `seq 1 4`;
do
    create_vm "${APP_NAME}-gw${i}" "${APP_NAME}-vnet" "gateway-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}" 
done    

# Step 6.2 - Create the ElasticSearch master and data VMs
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-master${i}" "${APP_NAME}-vnet" "es-master-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-data${i}" "${APP_NAME}-vnet" "es-data-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done

# Step 6.3 - Create the SQL VMs
create_vm "${APP_NAME}-sql0" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
create_vm "${APP_NAME}-sql1" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
```
