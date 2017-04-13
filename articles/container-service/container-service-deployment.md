<properties
   pageTitle="Installere en Azure objektbeholder tjenesten klynge | Microsoft Azure"
   description="Installere en Azure objektbeholder tjenesten klynge ved hjælp af portalen Azure, Azure CLI eller PowerShell."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, objektbeholdere, Micro-tjenester, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="rogardle"/>

# <a name="deploy-an-azure-container-service-cluster"></a>Installere en Azure objektbeholder tjenesten klynge

Azure objektbeholder tjenesten giver hurtig installation af populære open source-objektbeholder klynge og organisering løsninger. Ved hjælp af Azure objektbeholder tjenesten, kan du installere DC/OS og Docker Swarm klynger med Azure ressourcestyring skabeloner eller Azure portalen. Du installerer disse klynger ved hjælp af Azure virtuelt skala sæt, og klynger drage fordel af Azure netværk og lager tilbud. For at få adgang til Azure objektbeholder tjenesten, skal du et Azure-abonnement. Hvis du ikke har en, kan derefter du tilmelde dig en [gratis prøveversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

Dette dokument vejleder dig gennem implementere en Azure objektbeholder tjenesten klynge ved hjælp af [Azure-portalen](#creating-a-service-using-the-azure-portal), [Azure kommandolinjen (CLI)](#creating-a-service-using-the-azure-cli)og [Azure PowerShell-modulet](#creating-a-service-using-powershell).  

## <a name="create-a-service-by-using-the-azure-portal"></a>Oprette en tjeneste ved hjælp af portalen Azure

Log på portalen Azure, skal du vælge **Ny**og Søg på Azure Marketplace til **Azure objektbeholder tjenesten**.

![Oprette installation 1](media/acs-portal1.png)  <br />

Vælg **Azure objektbeholder tjenesten**, og klik på **Opret**.

![Oprette installation 2](media/acs-portal2.png)  <br />

Angiv følgende oplysninger:

- **Brugernavn**: Dette er navnet på bruger, der skal bruges til en konto på hver af virtuelle maskiner og virtuelt skala angiver i Azure objektbeholder tjenesten klynge.
- **Abonnement**: Vælg en Azure abonnement.
- **Ressourcegruppe**: Vælg en eksisterende ressourcegruppe, eller Opret en ny.
- **Placering**: Vælg en Azure område til Azure objektbeholder tjenesten installation.
- **SSH offentlig nøgle**: føje den offentlige nøgle, der skal bruges til godkendelse mod Azure objektbeholder tjenesten virtuelle computere. Det er meget vigtigt, at denne tast indeholder ingen linjeskift, og, den indeholder præfikset 'ssh-rsa' og 'username@domain' omvendt polsk. Det skal ligne følgende: **ssh rsa AAAAB3Nz... <> …... UcyupgH azureuser@linuxvm **. For at vide om oprettelse af Secure Shell (SSH) taster, skal du se [Linux]( https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-linux/) og [Windows]( https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-windows/) artikler.

Klik på **OK** , når du er klar til at fortsætte.

![Oprette installation 3](media/acs-portal3.png)  <br />

Vælg en organisering. Indstillingerne er:

- **DC/OS**: installerer en DC/OS klynge.
- **Swarm**: installerer en Docker Swarm klynge.

Klik på **OK** , når du er klar til at fortsætte.

![Oprette deployment 4](media/acs-portal4.png)  <br />

Angiv følgende oplysninger:

- **Master tælle**: antallet mastere i klyngen.
- **Agent Tæl**: For Swarm Docker, det vil være det indledende antal supportmedarbejdere i sættet agent skala. For DC/OS bliver det indledende antal supportmedarbejdere i et privat skala sæt. Desuden oprettes et sæt offentlige skalering, som indeholder et forudbestemt antal supportmedarbejdere. Antallet af supportmedarbejdere i dette offentlige skala sæt bestemmes af mastere for hvor mange der er oprettet i klynge – én offentlige agent for én master og to offentlige supportmedarbejdere for tre eller fem mastere.
- **Agent virtuelt størrelse**: størrelsen af agent virtuelle maskiner.
- **DNS-præfiks**: et verden entydigt navn, der skal bruges til præfiks vigtige dele af de fulde domænenavne for tjenesten.

Klik på **OK** , når du er klar til at fortsætte.

![Oprette installation 5](media/acs-portal5.png)  <br />

Klik på **OK** , når tjenesten datavalidering er afsluttet.

![Oprette installation 6](media/acs-portal6.png)  <br />

Klik på **Opret** for at starte installationsprocessen.

![Oprette installation 7](media/acs-portal7.png)  <br />

Hvis du har valgt at fastgøre installationen til Azure-portalen, kan du se status for installation.

![Oprette installation 8](media/acs-portal8.png)  <br />

Når installationen er fuldført, er Azure objektbeholder tjenesten klynge klar til brug.

## <a name="create-a-service-by-using-the-azure-cli"></a>Oprette en tjeneste ved hjælp af Azure CLI

Hvis du vil oprette en forekomst af Azure objektbeholder tjenesten ved hjælp af kommandolinjen, skal du et Azure-abonnement. Hvis du ikke har en, kan derefter du tilmelde dig en [gratis prøveversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). Du skal også have [installeret](../xplat-cli-install.md) og [konfigureret](../xplat-cli-connect.md) Azure CLI.

For at installere en DC/OS eller en Docker Swarm klynge, skal du vælge en af følgende skabeloner fra GitHub. Bemærk, at begge af disse skabeloner er de samme, med undtagelse af orchestrator standardindstillingen.

* [DC/OS skabelon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
* [Sværme skabelon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Dernæst skal du sørge for, at Azure CLI er tilsluttet et Azure-abonnement. Du kan gøre dette ved hjælp af følgende kommando:

```bash
azure account show
```
Hvis der ikke returneres en Azure-konto, kan du bruge følgende kommando til at logge på CLI Azure.

```bash
azure login -u user@domain.com
```

Konfigurer Azure CLI værktøjerne for at bruge Azure ressourcestyring.

```bash
azure config mode arm
```

Oprette en Azure ressourcegruppe og objektbeholder tjenesten klynge med følgende kommando, hvor:

- **RESOURCE_GROUP** er navnet på den ressourcegruppe, du vil bruge til denne tjeneste.
- **Placering** er det Azure område, hvor ressourcegruppe og Azure objektbeholder tjenesten installation oprettes.
- **TEMPLATE_URI** er placeringen af installationsfilen. Bemærk, at det skal være Raw-fil, ikke en markør GitHub UI. Vælg fil, azuredeploy.json i GitHub for at finde denne URL-adresse, og klik på knappen **Raw** .

> [AZURE.NOTE] Når du kører denne kommando, beder shell dig om installation parameterværdier.

```bash
azure group create -n RESOURCE_GROUP DEPLOYMENT_NAME -l LOCATION --template-uri TEMPLATE_URI
```

### <a name="provide-template-parameters"></a>Angiv skabelonparametre

Denne version af kommandoen skal du definere parametre interaktivt. Hvis du vil angive parametre, som en JSON-formateret streng, kan du gøre det ved hjælp af den `-p` skifte. Eksempel:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
```

Alternativt kan du kan angive en JSON-formateret parametre fil ved hjælp af den `-e` skifte:

```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
```

Se et eksempel parametre-fil med navnet `azuredeploy.parameters.json`, søge efter den med Azure objektbeholder tjenesten skabeloner i GitHub.

## <a name="create-a-service-by-using-powershell"></a>Oprette en tjeneste ved hjælp af PowerShell

Du kan også distribuere en Azure objektbeholder tjenesten klynge med PowerShell. Dette dokument er baseret på versionen 1.0 [Azure PowerShell-modulet](https://azure.microsoft.com/blog/azps-1-0/).

Hvis du vil installere et DC/OS eller Docker Swarm klynge, Vælg en af følgende skabeloner. Bemærk, at begge af disse skabeloner er de samme, med undtagelse af orchestrator standardindstillingen.

* [DC/OS skabelon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
* [Sværme skabelon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Før du opretter en klynge i abonnementet Azure, Kontrollér, at din PowerShell-session er blevet signeret til Azure. Du kan gøre dette med den `Get-AzureRMSubscription` kommandoen:

```powershell
Get-AzureRmSubscription
```

Hvis du har brug for til at logge på Azure, kan du bruge den `Login-AzureRMAccount` kommandoen:

```powershell
Login-AzureRmAccount
```

Hvis du anvender for en ny ressourcegruppe, skal du først oprette ressourcegruppen. Hvis du vil oprette en ny ressourcegruppe, skal du bruge den `New-AzureRmResourceGroup` kommando, og Angiv en ressource gruppe navn og en placering region:

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

Når du opretter en ressourcegruppe, kan du oprette din klynge med følgende kommando. URI på den ønskede skabelon, der angives for den `-TemplateUri` parameter. Når du kører denne kommando, beder PowerShell dig om installation parameterværdier.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
```

### <a name="provide-template-parameters"></a>Angiv skabelonparametre

Hvis du kender til PowerShell, ved du, at du kan navigere gennem de tilgængelige parametre for en cmdlet ved at skrive et minustegn (-) og derefter trykke på tabulatortasten. Denne samme funktionalitet fungerer også med parametre, du angiver i skabelonen. Så snart du skriver navnet på skabelonen, cmdlet henter skabelonen, fortolker parametrene, og tilføjer skabelonparametre til kommandoen dynamisk. Dette gør det meget nemt at angive parameterværdier skabelon. Og hvis du glemmer en påkrævet parameterværdi, PowerShell beder dig om værdien.

Nedenfor er kommandoen fuld med parametre, der er inkluderet. Du kan angive dine egne værdier for navnene på ressourcerne.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>Næste trin

Nu hvor du har en fungerende klynge, skal du se disse dokumenter til oprettelse og administration af oplysninger:

- [Oprette forbindelse til en Azure objektbeholder tjenesten klynge](container-service-connect.md)
- [Arbejde med Azure objektbeholder tjenesten og DC/OS](container-service-mesos-marathon-rest.md)
- [Arbejde med Azure objektbeholder tjenesten og Docker sværme](container-service-docker-swarm.md)
