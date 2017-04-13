<properties
   pageTitle="Tekniske forudsætninger for oprettelse af et virtuelt billede på Azure Marketplace | Microsoft Azure"
   description="Forstå kravene til oprettelse og implementere et virtuelt billede til Azure Marketplace for andre at købe."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="04/29/2016"
  ms.author="hascipio; v-divte"/>

# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Tekniske forudsætninger for oprettelse af et virtuelt billede på Azure Marketplace
Læs processen grundigt før du begynder, og forstå hvor og hvorfor hvert trin udføres. Som muligt, du skal forberede dine firmaoplysninger og andre data, hente nødvendige værktøjer og/eller oprette tekniske komponenter før du begynder processen til oprettelse af tilbud. Disse elementer skal være Ryd fra Gennemse i denne artikel.  

## <a name="download-needed-tools--applications"></a>Hente nødvendige værktøjer og programmer
Du skal have følgende elementer klar, før du begynder processen:

- Afhængigt af hvilket operativsystem du målretter, skal du installere [Azure PowerShell-cmdlet'er](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) eller [Linux kommandolinjen værktøj](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) fra [Azure Downloads](https://azure.microsoft.com/downloads/) -siden.
- Installer Azure Storage Explorer fra CodePlex.
- Downloade og installere værktøjet certificering Test til Azure certificeret:
  - [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). Du har brug for en Windows-baseret computer til at køre værktøjet certificering. Hvis du ikke har en Windows-baseret computer, der er tilgængelig, kan du køre værktøjet ved hjælp af en Windows-baseret VM i Azure.

## <a name="platforms-supported"></a>Understøttede platforme
Du kan udvikle Azure-baserede FOS på Windows eller Linux. Nogle elementer i en udgivelsesproces – som oprettelse af en Azure-kompatible virtuel harddisk (Virtuelle) – Brug forskellige værktøjer og trin, afhængigt af hvilket operativsystem du bruger:  

- Hvis du bruger Linux, kan du se afsnittet "Oprette en Azure-kompatible Virtuelle (Linux-baseret)" i [virtuelt billede publicering vejledning](marketplace-publishing-vm-image-creation.md).
- Hvis du bruger Windows, kan du se afsnittet "Oprette en Azure-kompatible Virtuelle (Windows-baseret)" i [virtuelt billede publicering vejledning](marketplace-publishing-vm-image-creation.md).

> [AZURE.NOTE] Du skal have adgang til en Windows-baseret computer til at:
- Køre værktøjet certificering validering.
- Oprette Virtuelle delt access signatur URL-adressen til den Virtuelle certificering bidrag.

## <a name="develop-your-vhd"></a>Udvikle din Virtuelle
Du kan udvikle Azure virtuelle harddiske i skyen eller lokalt:

- Skybaseret udvikling betyder, at alle udvikling trin udføres fra en fjernplacering på en virtuel bosiddende på Azure harddisk.
- Lokalt udvikling kræver, at downloade en virtuel harddisk og udvikling af den ved hjælp af lokale infrastruktur. Selvom det er muligt, anbefales det ikke. Bemærk, at udvikle til Windows eller SQL lokale kræver du har de relevante lokal licens taster. Du kan medtage eller installere SQL Server, når du har oprettet en VM. Du skal også basere dit tilbud på et godkendt SQL-billede fra Azure-portalen. Hvis du beslutter at udvikle lokalt, skal du udføre nogle trin anderledes end hvis du er blevet udvikling i skyen. Du kan finde relevante oplysninger i [oprette et lokalt VM billede](marketplace-publishing-vm-image-creation-on-premise.md).

## <a name="next-steps"></a>Næste trin
Nu, hvor du gennemgås forudsætningerne og fuldførte opgaver som det er nødvendigt, kan du flytte frem til at oprette dit virtuelt billede tilbud som beskrevet i [virtuelt billede publicering vejledning](marketplace-publishing-vm-image-creation.md).

## <a name="see-also"></a>Se også
- [Introduktion: hvordan du publicerer et tilbud til Azure Marketplace](marketplace-publishing-getting-started.md)
- [Oprette en virtuel maskine, der kører Windows Azure preview-portalen](../virtual-machines/virtual-machines-windows-hero-tutorial.md)


[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
