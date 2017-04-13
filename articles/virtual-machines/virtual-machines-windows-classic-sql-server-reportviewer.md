<properties 
    pageTitle="Brug ReportViewer på et websted | Microsoft Azure"
    description="Dette emne beskrives, hvordan du opretter et Microsoft Azure-websted med objektet visuelle ReportViewer på Studio, der viser en rapport, der er gemt på et Microsoft Azure Virtual Machine."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="guyinacube"
    manager="erikre"
    editor="monicar" 
    tags="azure-service-management" />
<tags 
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/04/2016"
    ms.author="asaxton" />

# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Brug ReportViewer på et websted, der er hostet i Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Du kan oprette et Microsoft Azure-websted med objektet visuelle ReportViewer på Studio, der viser en rapport, der er gemt på et Microsoft Azure Virtual Machine. Objektet ReportViewer er i et webprogram, du opretter ved hjælp af skabelonen ASP.NET Web application.

>[AZURE.IMPORTANT] ASP.NET MVC webprogram skabeloner understøtter ikke objektet ReportViewer.

Hvis du vil medtage ReportViewer i Microsoft Azure-webstedet, skal du udføre følgende opgaver.

- **Tilføje** Assemblies til installationspakken

- **Konfigurere** Godkendelse og autorisation

- **Publicer** ASP.NET webprogrammet til Azure

## <a name="prerequisites"></a>Forudsætninger

Læse afsnittet "generelle anbefaling og bedste fremgangsmåder" i [SQL Server Business Intelligence i virtuelle Azure-computere](virtual-machines-windows-classic-ps-sql-bi.md).

>[AZURE.NOTE] ReportViewer kontrolelementer leveres med Visual Studio, Standard Edition eller nyere. Hvis du bruger Web Developer Express Edition, skal du installere [MICROSOFT rapport FREMVISER 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747) for at bruge funktionerne ReportViewer runtime.
>
>ReportViewer, der er konfigureret i lokale behandling mode understøttes ikke i Microsoft Azure.

Gennemse hvidbogen [objektet Rapportfremviser til Reporting Services og Microsoft Azure virtuelt baseret rapportservere](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx).

## <a name="adding-assemblies-to-the-deployment-package"></a>Føje Assemblies til installationspakken

Når du hoster dine ASP.NET-program lokalt, skal ReportViewer assemblies er som regel installeret direkte i den globale assemblycache (GAC) på serveren, IIS under installationen af Visual Studio og kan åbnes direkte af programmet. Men, når du er vært for ASP.NET-program i skyen, Microsoft Azure tillader ikke noget, der skal være installeret i GAC, så skal du sikre dig ReportViewer samlinger er tilgængelige lokalt for dit program. Du kan gøre dette ved at tilføje referencer til dem i dit projekt og konfigurere dem, der skal kopieres lokalt.

I remote behandlingstilstand benytter objektet ReportViewer de følgende enheder:

- **Microsoft.ReportViewer.WebForms.dll**: indeholder ReportViewer kode, som du skal bruge ReportViewer på siden. En reference til denne samling føjes til projektet, når du slipper en ReportViewer-objektet på en ASP.NET-side i dit projekt.

- **Microsoft.ReportViewer.Common.dll**: indeholder klasser, der bruges af objektet ReportViewer på kørselstidspunktet. Det er ikke automatisk føjet til projektet.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Tilføje en reference til Microsoft.ReportViewer.Common

- Højreklik på dit projekts **referencer** node og vælg **Tilføj Reference**, Vælg samlingen under fanen .NET, og klik på **OK**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Til at gøre assemblies lokalt handicapvenlige ved ASP.NET-program

1. Klik på samlingen Microsoft.ReportViewer.Common i mappen **referencer** , så dens egenskaber vises i ruden Egenskaber.

1. I ruden egenskaber skal du angive **Lokale kopi** til sand.

1. Gentag trin 1 og 2 for Microsoft.ReportViewer.WebForms.

### <a name="to-get-reportviewer-language-pack"></a>At få ReportViewer Language Pack

1. Installere den relevante redistributable Microsoft rapport Fremviser 2012 Runtime-pakke fra [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=317386).

1. Vælg sproget på rullelisten, og siden viderestilles til siden tilsvarende download center.

1. Klik på **Hent** for at starte overførslen af ReportViewerLP.exe.

1. Når du har hentet ReportViewerLP.exe, skal du klikke på **Kør** for at installere det samme, eller klik på **Gem** for at gemme den på din computer. Hvis du klikker på **Gem**, har du glemt navnet på den mappe, hvor du gemmer filen.

1. Find den mappe, hvor du gemte filen. Højreklik på ReportViewerLP.exe, skal du klikke på **Kør som administrator**, og klik derefter på **Ja**.

1. Når du har kørt ReportViewerLP.exe, kan du se c:\windows\assembly har ressourcen filer **Microsoft.ReportViewer.Webforms.Resources** og **Microsoft.ReportViewer.Common.Resources**.

### <a name="to-configure-for-localized-reportviewer-control"></a>Hvis du vil konfigurere til lokaliseret ReportViewer kontrolelement

1. Hent og Installer Microsoft rapport Fremviser 2012 Runtime redistributable pakken ved at følge ovenstående angivne vejledningen.

1. Oprette <language> mappe i projektet, og Kopiér samlingen tilknyttede ressource filer der. Samling ressourcefiler skal kopieres er: **Microsoft.ReportViewer.Webforms.Resources.dll** og **Microsoft.ReportViewer.Common.Resources.dll**. Vælg samling ressourcefiler, og angiv **kopi til outputmappe** for at "**kopiere altid**" i ruden Egenskaber.

1. Angiv kultur & UICulture for webprojektet. Finde flere oplysninger om, hvordan du indstiller kultur og Brugergrænsefladen kultur på en ASP.NET-webside [Sådan: Angiv kultur og Brugergrænsefladen kultur for ASP.NET webside globalisering](http://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Konfigurere godkendelse og autorisation

ReportViewer skal bruge korrekte legitimationsoplysninger til at godkende med rapportserveren, og legitimationsoplysningerne, der skal godkendes af rapportserveren til at få adgang til rapporter, du vil. Du kan finde oplysninger om godkendelse, hvidbogen [objektet Rapportfremviser til Reporting Services og Microsoft Azure virtuelt baseret rapportservere](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Publicere ASP.NET webprogrammet til Azure

Få oplysninger om publicering af en ASP.NET-webprogram til Azure, gå [Sådan: overføre og publicere et webprogram til Azure fra Visual Studio](../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) og [komme i gang med Web Apps og ASP.NET](../app-service-web/web-sites-dotnet-get-started.md).

>[AZURE.IMPORTANT] Hvis kommandoen Tilføj Azure installationsprojekt eller tilføje Azure skyen tjenesten Project ikke vises, i genvejsmenuen i Solution Explorer skal du muligvis ændre Target ramme for projektet til .NET Framework 4.
>
>De to kommandoer giver grundlæggende de samme funktioner. En eller anden kommandoen vises i genvejsmenuen afhængigt af hvilken version af Microsoft Azure SDK du har installeret.

## <a name="resources"></a>Ressourcer

[Microsoft-rapporter](http://go.microsoft.com/fwlink/?LinkId=205399)

[SQL Server Business Intelligence i Azure virtuelle maskiner](virtual-machines-windows-classic-ps-sql-bi.md)

[Bruge PowerShell til at oprette en Azure VM med en rapportserver-tilstand](virtual-machines-windows-classic-ps-sql-report.md)

[Reporting Services objektet Rapportfremviser og Microsoft Azure baseret virtuelt rapportservere](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx)
