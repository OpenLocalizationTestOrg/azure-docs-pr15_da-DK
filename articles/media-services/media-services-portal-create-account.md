<properties
    pageTitle=" Oprette en Azure Media Services-konto ved hjælp af Azure portal | Microsoft Azure"
    description="Dette selvstudium vejleder dig gennem trinnene til at oprette en Azure Media Services-konto ved hjælp af Azure portal."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/24/2016"
    ms.author="juliako"/>


# <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Oprette en Azure Media Services-konto ved hjælp af portalen Azure

> [AZURE.SELECTOR]
- [Portal](media-services-portal-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [RESTEN](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] For at fuldføre dette selvstudium skal have du en Azure konto. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/). 

Azure-portalen indeholder en metode til hurtigt at oprette en Azure Media Services (AMS)-konto. Du kan bruge din konto til at få adgang til Media Services, der gør det muligt at gemme, kryptere, kode, administrere og streame medieindhold i Azure. På det tidspunkt, du opretter en Media Services-konto, du også oprette en tilknyttet lagerplads konto (eller brug en eksisterende liste) i den samme geografiske område som Media Services-konto.

I denne artikel beskrives nogle almindelige begreber og viser, hvordan du opretter en Media Services-konto ved hjælp af Azure portal.

## <a name="concepts"></a>Begreber

Få adgang til Media Services kræver to tilknyttede konti:

- En Media Services-konto. Din konto giver dig adgang til et sæt af skybaseret Media Services, der er tilgængelige i Azure. En Media Services konto gemmer ikke faktiske medieindhold. I stedet gemmes metadata om medieindhold og medier behandler job i din konto. På det tidspunkt, du opretter kontoen, vælger du en tilgængelig Media Services område. Det område, du vælger er et datacenter, der gemmer metadata posterne for din konto.

    Tilgængelige Media Services (AMS) områder omfatter følgende: North Europe, vest Europe, vest USA, af USA, Sydøstasien, Østasien, Japan vest, Japan Øst. Media Services bruger ikke forbindelse grupper.
    
    AMS er nu også tilgængelig i de følgende datacentre: Brasilien Syd, Indien vest, Indien syd og Indien Central. Du kan nu bruge portalen Azure til at oprette medier tjenestekonti og udføre forskellige opgaver, der er beskrevet her. Live kodning er dog ikke aktiveret i disse datacentre. Yderligere, findes ikke i alle typer kode reserveret enheder i disse datacentre.
    
    - Brasilien Syd: Kun Standard og grundlæggende kodning reserveret enheder er tilgængelige.
    - Indien vest, Indien Syd: 

- En konto, Azure-lager. Lagerplads konti skal være placeret i samme geografiske område som Media Services-konto. Når du opretter en Media Services-konto, kan du enten vælge en eksisterende konto lagerplads i samme region, eller du kan oprette en ny lagerplads konto i det samme område. Hvis du sletter en Media Services-konto, slettes ikke BLOB i kontoen relaterede lagerplads.

## <a name="create-an-ams-account"></a>Oprette en AMS-konto

Trinnene i dette afsnit viser, hvordan du opretter en AMS-konto.

1. Log på på [Azure-portalen](https://portal.azure.com/).
2. Klik på **+ Ny** > **Web + Mobile** > **Media Services**.

    ![Oprette Media Services](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. Angiv obligatoriske værdier i **Opret MEDIA SERVICES konto** .

    ![Oprette Media Services](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Skriv navnet på den nye AMS-konto i **Kontonavn**. Et kontonavn Media Services er alle små bogstaver tal eller bogstaver, som ikke indeholder mellemrum, og er 3-24 tegn.
    2. Vælg mellem de forskellige Azure abonnementer, du har adgang til-abonnement.
    
    2. Vælg den nye eller eksisterende ressource i **Ressourcegruppe**.  En ressourcegruppe er en samling af ressourcer, der deler livscyklus, tilladelser og politikker. Få mere at vide [her](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. Vælg det geografiske område, der skal bruges til at gemme de medie og metadata poster til kontoen Media Services i **placering**. Dette område bruges til at behandle og streame medier. Kun de tilgængelige Media Services områder vises på rullelisten. 
    
    3. I **Lagerplads konto**, skal du vælge en lagerplads konto til at levere blob-lager af medieindhold fra kontoen Media Services. Du kan vælge en eksisterende lagerplads konto i det samme geografiske område som kontoen Media Services, eller du kan oprette en lagerplads konto. Der oprettes en ny konto lagerplads i samme område. Regler for lagerplads kontonavne er den samme som for Media Services konti.

        Lær mere om lagerplads [her](storage-introduction.md).

    4. Vælg **Fastgør til dashboard** for at se status for konto-installationen.
    
7. Klik på **Opret** nederst i formularen.

    Når kontoen er blevet oprettet, ændres status til at **køre**. 

    ![Indstillinger for Media Services](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Til at administrere kontoen AMS (for eksempel overføre videoer, kode aktiver, overvåge statussen job) bruge vinduet **Indstillinger** .

## <a name="manage-keys"></a>Administrere nøgler

Du har brug for kontonavnet og det primære vigtige oplysninger til automatisk at få adgang til kontoen Media Services.

1. Vælg din konto på Azure-portalen. 

    Vinduet **Indstillinger** vises til højre. 

2. Vælg **taster**i vinduet **Indstillinger** . 

    **Administrer nøgler** windows Viser kontonavnet og tasterne primære og sekundære vises. 
3. Tryk på knappen Kopiér til at kopiere værdierne.
    
    ![Media Services taster](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="next-steps"></a>Næste trin

Nu kan du overføre filer til din AMS-konto. Du kan finde flere oplysninger, kan du se [overføre filer](media-services-portal-upload-files.md).

## <a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


