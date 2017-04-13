
<properties
    pageTitle="Azure RemoteApp billede krav | Microsoft Azure"
    description="Få mere at vide om kravene til oprettelse af billeder skal bruges sammen med Azure RemoteApp"
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



# <a name="requirements-for-azure-remoteapp-images"></a>Krav til Azure RemoteApp billeder

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Azure RemoteApp bruger et billede af Windows Server 2012 R2 til at hoste alle de programmer, du vil dele med dine brugere. Hvis du vil oprette et brugerdefineret billede, kan du starte med et eksisterende billede eller [oprette en ny](remoteapp-create-custom-image.md).

> [AZURE.TIP] Vidste du, at abonnementet Azure RemoteApp giver dig adgang til et billede, Windows Server 2012 R2 i galleriet Azure VM, som du kan bruge til at oprette din egen skabelonbillede? [Finde ud af](remoteapp-image-on-azurevm.md).  


Krav til det billede, der kan overføres til brug sammen med Azure RemoteApp er:


- Brugerdefinerede programmer gemme ikke data lokalt på billedet. Disse billeder er uden status og bør kun indeholde programmer.
- Billedet indeholder ikke data, der kan gå tabt.
- Billedstørrelsen skal være et multiplum af MB. Hvis du forsøger at overføre et billede, der ikke er et præcist multiplum, mislykkes overførslen.
- Billedets størrelse skal være 127 GB eller mindre.
- Det skal være på en Virtuelle fil (VHDX filer understøttes ikke i øjeblikket).
- Den virtuelle harddisk må ikke være et virtuelt generering af 2.
- Den virtuelle harddisk kan være enten fast størrelse eller dynamisk voksende. En dynamisk voksende Virtuelle anbefales, da det tager mindre tid at overføre til Azure end en fast størrelse Virtuelle fil.
- Disken skal være initialiseret ved hjælp af den start post MBR (Master) partitionering typografi. Typografien GUID partition GPT (table) partition understøttes ikke.
- Den virtuelle harddisk skal indeholde en enkelt installation af Windows Server 2012 R2. Det kan indeholde flere enheder, men kun én, der indeholder en installation af Windows.
- Rollen Remote Desktop Session Host (RDSH) og computeroplevelsesfunktionen skal være installeret.
- Rollen Remote Desktop Connection Broker skal *ikke* installeres.
- Den kryptere File System (EFS) skal være deaktiveret.
- Billedet skal være blevet behandlet med Sysprep ved hjælp af parametrene **/oobe / generalize/shutdown** (må ikke bruge parameteren **/mode:vm** ).
- Overføre din Virtuelle fra en snapshot kæde understøttes ikke.

Se [oprette et Azure RemoteApp billede](remoteapp-imageoptions.md) kan finde flere oplysninger om at oprette billeder til Azure RemoteApp.
