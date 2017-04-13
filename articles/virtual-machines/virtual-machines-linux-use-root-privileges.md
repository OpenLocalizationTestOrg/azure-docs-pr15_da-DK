<properties 
    pageTitle="Bruge rod rettigheder på Linux virtuelle maskiner | Microsoft Azure" 
    description="Lær at bruge rod rettigheder på en Linux virtuel maskine i Azure." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="szarkos" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="szark"/>


# <a name="using-root-privileges-on-linux-virtual-machines-in-azure"></a>Brug af root-privilegier på Linux virtuelle maskiner i Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Som standard på `root` bruger er deaktiveret på Linux virtuelle maskiner i Azure. Brugere kan køre kommandoer med administratorrettigheder ved hjælp af den `sudo` kommandoen. Oplevelsen variere, afhængigt af hvordan systemet blev klargjort.

1. **SSH nøgle og adgangskode eller adgangskode kun** - den virtuelle maskine blev klargjort med enten et certifikat (`.CER` filer) eller SSH nøgle samt en adgangskode, eller blot et brugernavn og din adgangskode. I dette tilfælde `sudo` bliver bedt om at brugerens adgangskode, før du udfører kommandoen.

2. **Kun SSH nøgle** – den virtuelle maskine blev klargjort med et certifikat (`.cer`, `.pem`, eller `.pub` filer) eller SSH nøgle, men ingen adgangskode.  I dette tilfælde `sudo` **ikke** Spørg efter brugerens adgangskode, før du udfører kommandoen.


## <a name="ssh-key-and-password-or-password-only"></a>SSH nøgle og den adgangskode, eller kun adgangskode

Logge på ved hjælp af SSH nøgle eller adgangskode godkendelse Linux virtual machine og derefter køre kommandoer ved hjælp af `sudo`, f.eks.:

    # sudo <command>
    [sudo] password for azureuser:

I dette tilfælde vil brugeren blive bedt om en adgangskode. Når du har angivet adgangskoden `sudo` kører kommandoen med `root` rettigheder.

Du kan også aktivere passwordless sudo ved at redigere den `/etc/sudoers.d/waagent` fil, f.eks.:

    #/etc/sudoers.d/waagent
    azureuser ALL = (ALL) NOPASSWD: ALL

Denne ændring vil tage højde for passwordless sudo af brugeren "azureuser".

## <a name="ssh-key-only"></a>SSH centrale kun

Logge på ved hjælp af SSH vigtige godkendelse Linux virtual machine og derefter køre kommandoer ved hjælp af `sudo`, f.eks.:

    # sudo <command>

I dette tilfælde brugeren vil **ikke** blive bedt om en adgangskode. Når du trykker på `<enter>`, `sudo` kører kommandoen med `root` rettigheder.

 
