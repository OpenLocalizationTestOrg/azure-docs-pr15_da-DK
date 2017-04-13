<properties
    pageTitle="Registrere en Linux VM skal bruges som en skabelon | Microsoft Azure"
    description="Lær at registrere og generalize et billede af en Linux-baserede Azure VM (virtual machine) oprettet med implementeringsmodel Azure ressourcestyring."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="iainfou"/>


# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>Registrere en Linux virtuel maskine, der kører på Azure

Følg trinnene i denne artikel for at generalize og registrere din Azure Linux virtuelle maskine (VM) i implementeringsmodel ressourcestyring. Når du generaliserer VM, kan du fjerne personlige oplysninger og forberede VM som skal bruges som et billede. Du kan derefter hente et billede af generalized virtuel harddisk (Virtuelle) til Operativsystemet, virtuelle harddiske om en vedhæftede data disk og en [ressourcestyring skabelon](../azure-resource-manager/resource-group-overview.md) til nye VM installationer. 

Konfigurere netværksressourcer for hver ny VM for at oprette FOS ved hjælp af billedet, og bruge skabelonen (en JavaScript Object Notation eller JSON,-fil) til at installere det fra hentede Virtuelle billederne. På denne måde, kan du gentage en VM med dens aktuelle softwarekonfiguration, du svarer til den måde, du bruger billeder i Azure Marketplace.

>[AZURE.TIP]Hvis du vil oprette en kopi af din eksisterende Linux VM med dens specialiserede tilstand for sikkerhedskopiering eller fejlfinding, kan du se [oprette en kopi af en Linux virtuel maskine, der kører på Azure](virtual-machines-linux-copy-vm.md). Og hvis du vil overføre en Linux VHD fra en lokal VM, skal du se [overføre og oprette en Linux VM fra brugerdefinerede disk billede](virtual-machines-linux-upload-vhd.md).  

## <a name="before-you-begin"></a>Inden du går i gang

Sikre, at du opfylder følgende forudsætninger:

* **Azure VM, der er oprettet i implementeringsmodel ressourcestyring** - Hvis du ikke har oprettet en Linux VM, kan du bruge [portal](virtual-machines-linux-quick-create-portal.md), [Azure CLI](virtual-machines-linux-quick-create-cli.md)eller [ressourcestyring skabeloner](virtual-machines-linux-cli-deploy-templates.md). 

    Konfigurere VM efter behov. For eksempel [tilføje datadisce](virtual-machines-linux-add-disk.md), anvende opdateringer, og installerer programmer. 
* **Azure CLI** - Installer [Azure CLI](../xplat-cli-install.md) på en lokal computer.

## <a name="step-1-remove-the-azure-linux-agent"></a>Trin 1: Fjern Azure Linux-agent

Først skal køre kommandoen **waagent** med parameteren **deprovision** på Linux VM. Denne kommando sletter filer og data for at gøre VM klar til generalisering. Yderligere oplysninger finder du i [Brugervejledning til Azure Linux Agent](virtual-machines-linux-agent-user-guide.md).

1. Oprette forbindelse til din Linux VM ved hjælp af en SSH-klient.

2. I vinduet SSH skal du skrive følgende kommando:

    ```
    sudo waagent -deprovision+user
    ```
    >[AZURE.NOTE] Kun Kør denne kommando på en VM, du vil kopiere som et billede. Det garanterer ikke, at billedet ikke er markeret med alle følsomme oplysninger eller er egnet til videredistribution.

3. Skriv **y** for at fortsætte. Du kan tilføje de **-tvinge** parameter for at undgå dette bekræftelsestrin.

4. Når kommandoen er fuldført, skal du skrive **Afslut**. Dette trin lukker SSH klienten.

    
## <a name="step-2-capture-the-vm"></a>Trin 2: Registrere VM

Brug Azure CLI generalize og registrere VM. Erstat eksempel parameternavne med dine egne værdier i eksemplerne nedenfor. Eksempel parameternavne omfatter **myResourceGroup**, **myVnet**og **myVM**.

5. Åbn Azure CLI og [logge på dit Azure abonnement](../xplat-cli-connect.md)fra din lokale computer. 

6. Kontrollér, at du er i ressourcestyring tilstand.

    ```
    azure config mode arm
    ```

7. Lukke den VM, som du allerede har fjernet ved hjælp af følgende kommando:

    ```
    azure vm deallocate -g MyResourceGroup -n myVM
    ```

8. Generalize VM med følgende kommando:

    ```
    azure vm generalize -g MyResourceGroup -n myVM
    ```

9. Kør kommandoen **azure vm registrere** , som henter VM nu. De virtuelle harddiske billede er hentet med navne, der begynder med **MyVHDNamePrefix**i eksemplet nedenfor, og indstillingen **-t** angiver en sti til skabelonen **MyTemplate.json**. 

    ```
    azure vm capture MyResourceGroup MyResourceGroup MyVHDNamePrefix -t MyTemplate.json
    ```

    >[AZURE.IMPORTANT]Virtuelle billedfiler bliver oprettet som standard i den samme lagerplads-konto, som den oprindelige VM bruges. Bruge den *samme lagerplads konto* til at gemme de virtuelle harddiske for alle nye FOS, du opretter ud fra billedet. 

6. For at finde placeringen af en skærmklippet skal du åbne skabelonen JSON i et tekstredigeringsprogram. Find det **billede** , findes i beholderen **system** **uri** i **storageProfile**. For eksempel minder URI billedets diskplads OS om`https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Trin 3: Opret en VM fra skærmklippet
Nu vil bruge billedet med en skabelon til at oprette en Linux VM. Disse trin viser, hvordan du bruger Azure CLI og skabelonen JSON fil, du har fået for at oprette VM i et nyt virtuelt netværk.

### <a name="create-network-resources"></a>Oprette netværksressourcer

Hvis du vil bruge skabelonen, skal du først konfigurere et virtuelt netværk og NIC til din nye VM. Vi anbefaler, at du opretter en ressourcegruppe til disse ressourcer på den placering, hvor VM billedet er gemt. Køre kommandoer, der ligner følgende skal du erstatte navnene for dine ressourcer og en passende Azure placering ("centralus" i disse kommandoer):

    azure group create MyResourceGroup1 -l "centralus"

    azure network vnet create MyResourceGroup1 myVnet -l "centralus"

    azure network vnet subnet create MyResourceGroup1 myVnet mySubnet

    azure network public-ip create MyResourceGroup1 myIP -l "centralus"

    azure network nic create MyResourceGroup1 myNIC -k mySubnet -m myVnet -p myIP -l "centralus"

### <a name="get-the-id-of-the-nic"></a>Få NIC Id

Du skal installere en VM fra billedet ved hjælp af JSON du gemte under registrering, du har brug for NIC. Id Hente den ved at køre følgende kommando:

    azure network nic show MyResourceGroup1 myNIC

**Id** i output ligner`/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic`



### <a name="create-a-vm"></a>Oprette en VM
Nu skal du køre følgende kommando for at oprette din VM fra VM skærmklippet. Bruge parameteren **-f** til at angive stien til filen template JSON, du har gemt.

    azure group deployment create MyResourceGroup1 MyDeployment -f MyTemplate.json

Du bliver bedt om at angive et nyt navn til VM, admin-brugernavn og adgangskode og Id NIC du tidligere har oprettet i kommando-output.

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    vmName: myNewVM
    adminUserName: myAdminuser
    adminPassword: ********
    networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic

I følgende eksempel vises det, du ser for en vellykket installation:

    + Initialisering af skabelonen konfigurationer og parametre
    + Oprette en installationsoplysninger: oprettet skabelon installation xxxxxxx
    + Venter på installation til at fuldføre data: DeploymentName: MyDeployment data: ResourceGroupName: MyResourceGroup1 data: ProvisioningState: lykkedes data: tidsstempel: xxxxxxx data: tilstand: trinvise data: navn typeværdi


    data:    ------------------  ------------  -------------------------------------

    data: vmName streng myNewVM


    data: vmSize streng Standard_D1


    data: adminUserName streng myAdminuser


    data: adminPassword SecureString Udefineret


    data: networkInterfaceId streng /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic oplysninger: gruppe installation oprette kommandoen OK

### <a name="verify-the-deployment"></a>Kontrollere installationen

Nu SSH til den virtuelle maskine, du har oprettet for at bekræfte installation og begynde at bruge den nye VM. Hvis du vil oprette forbindelse via SSH, finde IP-adressen på den VM, du har oprettet ved at køre følgende kommando:

    azure network public-ip show MyResourceGroup1 myIP

Den offentlige IP-adresse er angivet i kommando-output. Som standard du oprette forbindelse til Linux VM ved SSH på port 22.

## <a name="create-additional-vms"></a>Oprette yderligere FOS
Du kan bruge skærmklippet og skabelon til at udrulle yderligere FOS ved hjælp af trinnene i det forrige afsnit. Andre indstillinger for at oprette FOS fra billedet omfatter ved hjælp af en skabelon i Hurtig start eller køre kommandoen **azure vm oprette** .

### <a name="use-the-captured-template"></a>Brug af skabelonen hentede

Hvis du vil bruge skabelonen og skærmklippet, skal du gøre følgende (detaljerede i det forrige afsnit):

* Sørg for, at dit VM billede er i den samme lagerplads-konto, der er vært din VM Virtuelle.
* Kopiere JSON skabelonfilen, og Angiv et entydigt navn til OS disken for den nye VM Virtuelle (eller virtuelle harddiske). For eksempel i **storageProfile**, under **virtuelle**i **uri**, angive et entydigt navn til **osDisk** Virtuelle, svarende til`https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`
* Oprette en NIC i den samme eller et andet virtuelle netværk.
* Med den ændrede JSON skabelonfil kan du oprette en installation i ressourcegruppen, hvori du konfigurere det virtuelle netværk.

### <a name="use-a-quickstart-template"></a>Brug en skabelon på Hurtig start

Hvis du vil konfigurere automatisk, når du opretter en VM fra billedet netværket, kan du angive disse ressourcer i en skabelon. For eksempel se [101-vm--bruger-billede fra skabelon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) fra GitHub. Denne skabelon opretter en VM fra den brugerdefinerede afbildning og det er nødvendigt virtuelt netværk, offentlige IP-adresse og NIC ressourcer. Se, [hvordan du opretter en virtuel maskine fra et brugerdefineret billede ved hjælp af en ressourcestyring skabelon](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/)for en gennemgang af ved hjælp af skabelonen i portalen Azure.

### <a name="use-the-azure-vm-create-command"></a>Brug den azure vm Opret kommando

Normalt er det nemmest at bruge en ressourcestyring skabelon til at oprette en VM fra billedet. Du kan dog oprette VM _imperatively_ ved hjælp af kommandoen **azure vm create** med **-Q** (**– billede urn**) parameter. Hvis du bruger denne metode, kan du også overføre parameteren **-d** (**--os-disk-virtuelle**) for at angive placeringen af OS .vhd-filen til den nye VM. Denne fil skal være i beholderen virtuelle harddiske på kontoen lagerplads, hvor den Virtuelle billedfil er gemt. Kommandoen kopierer den virtuelle harddisk til den nye VM automatisk til objektbeholderen **virtuelle harddiske** .

Før du kører **azure vm oprette** med billedet, skal du benytte følgende fremgangsmåde:

1.  Opret en ressourcegruppe, eller Identificer en eksisterende ressourcegruppe til installationen.

2.  Oprette en offentlige IP-adresseressource og en NIC ressource til den nye VM. Vejledning til at oprette et virtuelt netværk, offentlige IP-adresse og NIC ved hjælp af CLI, skal du se tidligere i denne artikel. (**azure vm oprette** kan også oprette en NIC, men du har brug at overføre yderligere parametre for et virtuelt netværk og undernet).


Kør derefter en kommando, som overfører URI'er til både den nye OS Virtuelle fil og det nuværende billede. I dette eksempel en størrelse Standard_A1 VM oprettes i området af US.

    azure vm create MyResourceGroup1 myNewVM eastus Linux -d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" -Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd" -z Standard_A1 -u myAdminname -p myPassword -f myNIC

Indstillinger for endnu en kommando køre `azure help vm create`.

## <a name="next-steps"></a>Næste trin

For at administrere din FOS med CLI skal du se opgaverne i [Implementer og administrere virtuelle maskiner ved hjælp af Azure ressourcestyring skabeloner og Azure CLI](virtual-machines-linux-cli-deploy-templates.md).
