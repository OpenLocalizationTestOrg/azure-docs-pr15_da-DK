<properties
    pageTitle="Overføre IaaS ressourcer fra klassisk til Azure ressourcestyring ved hjælp af Azure CLI | Microsoft Azure"
    description="I denne artikel indeholder en gennemgang af overførslen platform understøttes af ressourcer fra klassisk til Azure ressourcestyring ved hjælp af Azure CLI"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="cynthn"/>

# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Overføre IaaS ressourcer fra klassisk til Azure ressourcestyring ved hjælp af Azure CLI

Disse trin viser dig, hvordan du bruger Azure kommandolinjen (CLI) kommandoer til at overføre infrastruktur som en tjeneste (IaaS) ressourcer fra den klassiske implementeringsmodel til implementeringsmodel Azure ressourcestyring. I artiklen kræver [Azure CLI](../xplat-cli-install.md).

>[AZURE.NOTE] Alle de handlinger, der er beskrevet her er idempotent. Hvis du har et problem end ikke-understøttede funktioner eller en konfigurationsfejl, anbefaler vi, at du prøv igen på Forbered afbrydelse eller bekræftelse handling. Platformen prøv derefter handlingen igen.

## <a name="step-1-prepare-for-migration"></a>Trin 1: Forbered til overførsel

Her er nogle få bedste fremgangsmåder, som vi anbefaler, at når du evaluerer overføre IaaS ressourcer fra klassisk til Ressourcestyring:

- Læs gennem [listen over ikke-understøttede konfigurationer eller funktioner](virtual-machines-windows-migration-classic-resource-manager.md). Hvis du har virtuelle maskiner, der bruger ikke-understøttede konfigurationer eller funktioner, anbefaler vi, at du venter fra funktionskonfiguration/support følger senere. Du kan også fjerne denne funktion eller flytte fra denne konfiguration til at aktivere overførsel, hvis det passer til dine behov.
-   Hvis du har automatiseret scripts, der implementerer din infrastruktur og programmer i dag, du forsøge at oprette et lignende test konfigurationen ved hjælp af disse scripts til overførsel. Alternativt kan du konfigurere eksempel miljøer ved hjælp af portalen Azure.

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Trin 2: Angive dit abonnement og registrere provideren

For overførselsscenarier, du har brug at konfigurere dit miljø til begge klassisk og ressourcestyring. [Installer Azure CLI](../xplat-cli-install.md) , og [Vælg dit abonnement](../xplat-cli-connect.md).

Log på din konto.
    
    azure login

Vælg det Azure abonnement ved hjælp af følgende kommando.

    azure account set "<azure-subscription-name>"

>[AZURE.NOTE] Registrering er en tid trin, men den skal udføres én gang før du forsøger overførsel. Uden at registrere får du vist følgende fejlmeddelelse 

>   *BadRequest: Abonnement er ikke registreret for overførsel.* 

Registrere provideren overførsel ressource ved hjælp af følgende kommando. Bemærk, at denne kommando i nogle tilfælde får timeout. Dog bliver registreringen vellykket.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Vent fem minutter for tilmelding til at afslutte. Du kan kontrollere status for godkendelse ved hjælp af følgende kommando. Sørg for, at RegistrationState er `Registered` før du fortsætter.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Skift nu CLI til den `asm` tilstand.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Trin 3: Sørg for, at du har tilstrækkelig Azure ressourcestyring Virtual Machine kerner i det Azure område for din aktuelle installation eller VNET

I dette trin skal du skifte til `arm` tilstand. Gøre dette med følgende kommando.

```
azure config mode arm
```

Du kan bruge følgende CLI kommando til at kontrollere den aktuelle mængde kerner, du har i Azure ressourcestyring. Hvis du vil vide mere om core kvoter, se [grænser og ressourcestyring Azure](../articles/azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Når du er færdig, ved at bekræfte dette trin, du kan skifte tilbage til `asm` tilstand.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Trin 4: Indstilling 1 – overføre virtuelle maskiner i en skybaseret tjeneste 

Hente en liste over skytjenester ved hjælp af følgende kommando, og vælg derefter den skybaseret tjeneste, du vil overføre. Bemærk, at hvis FOS i skytjenesten er i et virtuelt netværk, eller hvis de har web/arbejder roller, får du en fejlmeddelelse.

    azure service list

Kør følgende kommando for at få installation navnet til skytjenesten fra detaljeret output. I de fleste tilfælde er navnet på installation den samme som navnet på tjenesten skyen.

    azure service show <serviceName> -vv

Forberede virtuelle maskiner i skytjenesten for overførsel. Du har to muligheder for at vælge mellem.

Hvis du vil overføre FOS til et virtuelt netværk platform har oprettet, skal du bruge følgende kommando.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Hvis du vil overføre til et eksisterende virtuelt netværk i implementeringsmodel Ressourcestyring, skal du bruge følgende kommando.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> subnetName <vnetName>

Når Forberedelseshandlingen er fuldført, kan du gennemse detaljeret output til at få tilstanden overførsel af FOS og sikre, at de er i den `Prepared` tilstand.

    azure vm show <vmName> -vv

Kontrollere konfigurationen for de ressourcer, der er udarbejdet ved hjælp af CLI eller Azure portalen. Hvis du ikke er klar til overførsel, og du vil gå tilbage til den gamle tilstand, skal du bruge følgende kommando.

    azure service deployment abort-migration <serviceName> <deploymentName>

Hvis forberedt konfigurationen, kan du flytte fremad og Bekræft ressourcerne ved hjælp af følgende kommando.

    azure service deployment commit-migration <serviceName> <deploymentName>


    
## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Trin 4: Indstilling 2 – overføre virtuelle maskiner i et virtuelt netværk

Vælg det virtuelle netværk, du vil overføre. Bemærk, at hvis det virtuelle netværk indeholder web/arbejder roller eller FOS med ikke-understøttede konfigurationer, får du en fejl Valideringsmeddelelse.

Få alle de virtuelle netværk i abonnement ved hjælp af følgende kommando.

    azure network vnet list
    
Output ser nogenlunde således ud:

![Skærmbillede af kommandolinjen med navnet på det hele virtuelle netværk fremhævet.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

I eksemplet ovenfor er **virtualNetworkName** hele navnet **"Gruppe classicubuntu16 classicubuntu16"**.

Forbered det virtuelle netværk efter eget valg til overflytning ved hjælp af følgende kommando.

    azure network vnet prepare-migration <virtualNetworkName>

Kontrollere konfigurationen for forberedt virtuelle maskiner ved hjælp af CLI eller Azure portalen. Hvis du ikke er klar til overførsel, og du vil gå tilbage til den gamle tilstand, skal du bruge følgende kommando.

    azure network vnet abort-migration <virtualNetworkName>

Hvis forberedt konfigurationen, kan du flytte fremad og Bekræft ressourcerne ved hjælp af følgende kommando.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Trin 5: Overføre en lagerplads konto

Når du er færdig med overføre virtuelle maskiner, anbefales det du overfører kontoen lagerplads.

Forberede kontoen lagerplads til overflytning ved hjælp af følgende kommando

    azure storage account prepare-migration <storageAccountName>

Kontrollere konfigurationen for kontoen forberedt lagerplads ved hjælp af CLI eller Azure portalen. Hvis du ikke er klar til overførsel, og du vil gå tilbage til den gamle tilstand, skal du bruge følgende kommando.

    azure storage account abort-migration <storageAccountName>

Hvis forberedt konfigurationen, kan du flytte fremad og Bekræft ressourcerne ved hjælp af følgende kommando.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Næste trin

- [Platform understøttes overførsel af IaaS ressourcer fra klassisk til Ressourcestyring](virtual-machines-windows-migration-classic-resource-manager.md)
- [Tekniske undersøgelse på platform understøttes overførsel fra klassisk til Ressourcestyring](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
