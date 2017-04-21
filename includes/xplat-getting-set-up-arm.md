<properties services="virtual-machines" title="Using Azure CLI with Azure Resource Manager" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## <a name="using-azure-cli-with-azure-resource-manager-arm"></a>Brug af Azure CLI med Azure ressourcestyring (ARM)

Før du kan bruge Azure CLI med ressourcestyring kommandoer og skabeloner til at udrulle Azure ressourcer og arbejdsmængder ved hjælp af grupper, skal du en konto hos Azure (selvfølgelig). Hvis du ikke har en konto, kan du få en [gratis Azure-prøveversion her](https://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE] Hvis du ikke allerede har en Azure-konto, men du har et abonnement på MSDN-abonnement, kan du få gratis Azure kredit ved at aktivere dine [her MSDN abonnement fordele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) – eller du kan bruge den gratis konto. Enten fungerer til Azure-adgang.

### <a name="step-1-verify-the-azure-cli-version"></a>Trin 1: Bekræft Azure CLI version

Hvis du vil bruge Azure CLI til vigtigt kommandoer og ARM skabeloner, du skal mindst have version 0.8.17. Du kan kontrollere din version, Skriv `azure --version`. Du bør se nogenlunde således:

    $ azure --version
    0.8.17 (node: 0.10.25)

Hvis du vil opdatere din version af Azure CLI, skal du se [Azure CLI](https://github.com/Azure/azure-xplat-cli).

### <a name="step-2-verify-you-are-using-a-work-or-school-identity-with-azure"></a>Trin 2: Bekræfte, at du bruger en arbejde eller skole identitet med Azure

Du kan kun bruge ARM kommandotilstand, hvis du bruger en [Azure Active Directory-lejer](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) eller et [Tjenestens hovednavn](https://msdn.microsoft.com/library/azure/dn132633.aspx). (Dette kaldes også *organisatoriske id'er*.)

For at se, hvis du har en skal du logge på ved at skrive `azure login` og bruger dit arbejde eller skole brugernavn og adgangskode, når du bliver bedt om. Hvis du har en, skal du se ud som følger:

    $ azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@contoso.onmicrosoft.com
    Password: *********
  	|info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription Visual Studio Ultimate with MSDN as default
    info:    Added subscription Azure Free Trial
    +
    info:    login command OK

Hvis du ikke kan se dette, skal du oprette en ny lejer (eller tjeneste vigtigste) med din Microsoft-kontoidentitet. (Dette er ofte tilfældet med personlige MSDN-abonnementer eller gratis abonnementer). For at oprette en arbejde eller skole-id'et fra din Azure konto, der er oprettet med en Microsoft-id, skal du se [knytte en Azure AD-mappe med et nyt Azure-abonnement](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant). Hvis du tror, skal du allerede har et organisatorisk id, skal du muligvis tale med den person, der oprettede firmaet for dig.

### <a name="step-3-choose-your-azure-subscription"></a>Trin 3: Vælge abonnementet Azure

Hvis du har kun ét abonnement i kontoen Azure, knytter Azure CLI sig til dette abonnement som standard. Hvis du har mere end ét abonnement, skal du markere det abonnement, du vil bruge ved at skrive `azure account set <subscription id or name> true` hvor _abonnement-id eller navn_ er abonnement-id eller på abonnementets navn, som du gerne vil arbejde med i den aktuelle session.

Du skal se ud som følger:

    $ azure account set "Azure Free Trial" true
    info:    Executing command account set
    info:    Setting subscription to "Azure Free Trial" with id "2lskd82-434-4730-9df9-akd83lsk92sa".
    info:    Changes saved
    info:    account set command OK

### <a name="step-4-place-your-azure-cli-in-the-arm-mode"></a>Trin 4: Placere dine Azure CLI i tilstanden ARM

Hvis du vil bruge tilstanden Azure ressource Management (ARM) med Azure CLI, Skriv `azure config mode arm`. Du skal se ud som følger:

    $ azure config mode arm
    info:    New mode is arm

> [AZURE.NOTE] Du kan skifte tilbage for at aktivere Azure service management kommandoer ved at skrive `azure config mode asm`.
