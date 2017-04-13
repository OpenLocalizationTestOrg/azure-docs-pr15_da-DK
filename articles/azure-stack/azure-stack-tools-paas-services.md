<properties
    pageTitle="Værktøjer og PaaS tjenester til Azure stak | Microsoft Azure"
    description="Lær, hvordan du kommer i gang med PaaS tjenester i Azure stablen."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="tools-for-azure-stack"></a>Værktøjer til Azure stak

Du kan hente de værktøjer, der er beskrevet nedenfor. Hvis du vil have besked om nye tjenester og værktøjer, skal du følge #AzureStack på Twitter.

## <a name="template-tools"></a>Skabelon værktøjer

### <a name="azure-stack-github-templates"></a>Azure stak Github skabeloner
Udforsk den voksende samling af [Azure stak GitHub skabeloner](https://github.com/Azure/AzureStack-QuickStart-Templates). Ligesom [Azure](https://github.com/Azure/azure-quickstart-templates)har skabelonerne "Hurtig Start" Azure ressourcestyring til formål at komme i gang med enkel dokumentkomponenter og eksempler, er du klar til at udrulle på Microsoft Azure stak Technical Preview bevis af konceptet miljøet. Inkluderes første part arbejdsbelastningen eksempler på [ad-ikke-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha), [sql-2014-ikke-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha), [sharepoint-2013-ikke-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha), som også som flere simple 101 skabeloner som [101-enkle-windows-vm](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm).


### <a name="marketplace-item-packaging-tool-and-sample"></a>Marketplace element emballagen værktøjet og eksempel
[Hente og bruge værktøjet emballagen](http://www.aka.ms/azurestackmarketplaceitem) til at oprette marketplace elementer til dine egne skabeloner til at føje til stak Azure marketplace. Oplysninger om, hvordan du opretter et element, der marketplace og gøre det tilgængeligt for din lejere kan findes i [oprette Marketplace element](azure-stack-create-and-publish-marketplace-item.md).

## <a name="developer-tools"></a>Udviklerværktøjer


### <a name="use-visual-studio-and-azure-stack-tp2-on-the-mas-con01-virtual-machine"></a>Brug Visual Studio og Azure stak TP2 på MAS CON01 virtuel maskine
Hvis du vil bruge Visual Studio på konsollen VM til at arbejde med Azure stak skabeloner, skal du installere de korrekte versioner af de nødvendige værktøjer. Brug følgende fremgangsmåde til at installere de understøttede versioner til TP2.

1. Brug Fjernskrivebord til at logge på virtuelt MAS CON01 med azurestack\azurestackadmin legitimationsoplysninger.
2. Installere og åbne Web Platform installationsprogrammet.
3. Finde og installere **Visual Studio Community 2015 med Microsoft Azure SDK - 2.9.5**.
4. Ved hjælp af **Tilføj/fjern programmer**, Fjern **Microsoft Azure PowerShell (Sept)** , der bliver installeret som en del af SDK.
5. Åbne Web Platform installationsprogrammet.
6. Finde og installere **Microsoft Azure PowerShell - Azure stak Technical Preview 2**. 
7. Genstart virtuelt MAS CON01.
8. Brug Fjernskrivebord til at logge på virtuelt MAS CON01 med azurestack\azurestackadmin legitimationsoplysninger.
9. Åbne Visual Studio og validere, du kan oprette forbindelse til Azure stak-miljø, hente skabeloner og så videre. 

### <a name="azure-powershell-sdk"></a>Azure PowerShell SDK
Azure PowerShell er et modul, der indeholder cmdletter for at administrere Azure og Azure stak med Windows PowerShell. Du kan bruge-cmdletter til at oprette, teste, installere og administrere løsninger og tjenester, der leveres via Azure stak platform.
[Hente Azure PowerShell SDK](http://aka.ms/azStackPsh) og [installere PowerShell](azure-stack-connect-powershell.md).

### <a name="azure-cross-platform-command-line-interfaces"></a>Azure på tværs af platform kommandoen linje grænseflader
Hurtigt installere Azure kommandolinjen (Azure CLI) for at bruge et sæt open source-shell-baserede kommandoer til oprettelse og administration af ressourcer i Microsoft Azure stak.

[Hent Windows CLI](http://aka.ms/azstack-windows-cli)

[Hente Mac CLI](http://aka.ms/azstack-linux-cli)

[Hente Linux CLI](http://aka.ms/azstack-mac-cli)

>[AZURE.NOTE]
>
> + Hvis du bruger en Mac eller Linux maskine, kan du også få CLI ved hjælp af kommandoen`npm install -g azure-cli@0.9.11`</br>
> + Hvis du får certifikat valideringsproblemer, køre kommandoen`set NODE_TLS_REJECT_UNAUTHORIZED=0`
