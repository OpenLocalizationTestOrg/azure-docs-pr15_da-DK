<properties
    pageTitle="Oprette et Azure RemoteApp billede, der er baseret på en Azure VM | Microsoft Azure"
    description="Lær at oprette et billede til Azure RemoteApp ved at starte med en Azure virtuelt."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="create-a-azure-remoteapp-image-based-on-an-azure-virtual-machine"></a>Oprette et Azure RemoteApp-billede, der er baseret på en Azure virtuelt

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Du kan oprette Azure RemoteApp billeder (som indeholder de apps, du deler i af websteder) fra en Azure virtuel maskine. Du kan også vælge at bruge et virtuelt billede vi føjet til galleriet Azure VM billede, der opfylder alle Azure RemoteApp billede – du kan bruge VM billedet som et udgangspunkt til dine egne VM, hvis du vil have. Se lige efter "Windows Server Remote Desktop sessionsværten" billedet i biblioteket.

Der er to trin til at oprette dit eget billede, der er baseret på en Azure VM – oprette-billedet, og Overfør det derefter fra biblioteket Azure VM til Azure RemoteApp.

## <a name="create-a-custom-image-based-on-an-azure-vm"></a>Oprette et brugerdefineret billede, der er baseret på en Azure VM

Følg disse trin for at oprette et billede, der er baseret på en Azure VM.

1. Oprette en Azure virtuelt. Du kan bruge "Windows Server Remote Desktop sessionsværten" eller "Windows Server Remote Desktop Session Host med Microsoft Office 365 ProPlus" billedet fra galleriet Azure virtuelt billede. Dette billede opfylder alle Azure RemoteApp skabelon billede.

    Yderligere oplysninger finder du [oprette en VM kører Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

2. Oprette forbindelse til VM og installere og konfigurere de apps, du vil dele via RemoteApp. Sørg for at udføre eventuelle yderligere Windows konfigurationer, der kræves, før dine apps.

    Få mere at vide, se, [hvordan du logger på virtuelt kører Windows Server](../virtual-machines/virtual-machines-windows-classic-connect-logon.md).

3. Hvis du bruger et af billederne Windows Server Remote Desktop-sessionsværten, er der en inkluderet valideringsscript, der sikrer din VM opfylder RemoteApp pre-reqs. For at køre scriptet, skal du dobbeltklikke på **ValidateRemoteAppImage** på skrivebordet. Sørg for, at alle fejl, der rapporteres af scriptet, rettes, før du fortsætter til næste trin.

4. SYSPREP generalize og tage et skærmbillede. Se, [hvordan du registrere en Windows virtuel maskine til brug som en skabelon](../virtual-machines/virtual-machines-windows-classic-capture-image.md) for at få instruktioner.



## <a name="import-the-image-into-the-azure-remoteapp-image-library"></a>Importere billedet til billedbiblioteket er Azure RemoteApp

Følg disse trin for at importere det nye billede til Azure RemoteApp:

1. I fanen **Billeder af webstedsskabeloner** :
    - Hvis du har ingen eksisterende billeder, skal du klikke på **Overfør eller importere et billede af designskabelon**.
    - Hvis du allerede har mindst ét billede, skal du klikke på **+** at tilføje et nyt billede.

2. Vælg **Importér et billede fra din virtuelle maskiner** bibliotek, og klik derefter på **Næste**.

3. Vælg dit eget billede på listen, og Bekræft, at du har fulgt trinnene, da du oprettede dit billede på den næste side. Klik på **Næste**.
4. Angiv et navn til det nye RemoteApp billede og vælg en placering, og klik derefter på markering for at starte importen.

> [AZURE.NOTE] Du kan importere billeder fra en hvilken som helst Azure placering, der understøttes af virtuelle Azure-computere til en hvilken som helst Azure placering, der understøttes af Azure RemoteApp. Dialogboksen Importer kan tage op til 25 minutter afhængigt af placeringerne.

Du er nu klar til at oprette den nye samling, enten en [skyen](remoteapp-create-cloud-deployment.md) af websteder eller [hybrid](remoteapp-create-hybrid-deployment.md), afhængigt af dine behov.
