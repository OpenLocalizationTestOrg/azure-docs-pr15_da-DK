
<properties 
   pageTitle="Azure SDK til .NET 2,8 produktbemærkninger" 
   description="Azure SDK til .NET 2,8 produktbemærkninger" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/17/2016"
   ms.author="juliako"/>
 
# <a name="azure-sdk-for-net-28-281-and-282"></a>Azure SDK til .NET 2,8, 2.8.1 og 2.8.2

##<a name="overview"></a>Oversigt
 
I denne artikel indeholder produktbemærkninger (som indeholder kendte problemer og vigtige ændringer) til Azure SDK til .NET 2,8, 2.8.1 og 2.8.2 versioner. 

Se [Azure SDK 2,8 til Visual Studio 2013 og Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) meddelelsen komplet liste over nye funktioner og opdateringer, der er foretaget i denne udgave. 

##  <a name="azure-sdk-for-net-28"></a>Azure SDK til .NET 2,8

### <a name="download-azure-sdk-for-net-28"></a>Hente Azure SDK til .NET 2,8

[Azure SDK til .NET 2,8 til Visual Studio 2015](http://go.microsoft.com/fwlink/?LinkId=699285) 

[Azure SDK til .NET 2,8 til Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=699287)
 
### <a name="net-452-support"></a>.NET 4.5.2 understøtter 

####<a name="known-issues"></a>Kendte problemer

Azure .NET SDK 2,8 gør det muligt at oprette .NET 4.5.2 skyen servicepakker. Men 4.5.2 .NET framework installeres ikke på standard gæst OS billeder indtil januar 2016 gæst OS slip. Før 4.5.2 pågældende, .NET framework vil være tilgængelige via en separat gæst OS version – November 2015-02. Se siden [Azure gæst OS versioner og SDK kompatibilitet Matrix](../cloud-services/cloud-services-guestos-update-matrix.md) for at spore, hvornår billedet vil blive udgivet.  Når November 2015-02-billede er udgivet kan du vælge at bruge billedet ved at opdatere din skybaseret tjeneste konfigurationsfil fil (.cscfg). I konfigurationen af tjenesten fil indstille egenskaben osVersion elementet ServiceConfiguration til strengen "WA-GÆST-OS-4.26_201511-02". Hvis du vælger at tilmelde sig til at bruge dette billede kan du ikke længere få automatiske opdateringer til gæst OS. At få automatiske opdateringer i osVersion skal være indstillet til "*" og .NET 4.5.2 vil kun være tilgængelige via Automatiske opdateringer i januar 2016.

###<a name="azure-data-factory"></a>Azure Data Factory

####<a name="known-issues"></a>Kendte problemer 

Azure power shell script mislykkes under en **Fabrik dataskabelon** oprettelsen af projektet der involverer eksempeldata, hvis azure power shell version installeret på maskinen efter 0.9.8.

For at oprette denne type projekt, skal du installere [azure power shell version 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi).


### <a name="azure-resource-manager-tools"></a>Azure ressourcestyring værktøjer 

####<a name="breaking-changes"></a>Bryd ændringer

PowerShell-script, der leveres af Azure ressourcegruppe projektet er blevet opdateret i denne udgave til at fungere sammen med de nye Azure PowerShell-cmdletter version 1.0.  Denne nye script fungerer ikke fra med Visual Studio, når du bruger en version af SDK før 2,8.  

Scripts fra projekter, der er oprettet i tidligere versioner af SDK kører ikke fra i Visual Studio, når du bruger i 2,8 SDK.  Alle scripts fortsætter med at arbejde uden for Visual Studio med den relevante version af Azure PowerShell-cmdlet'er.  

I 2,8 SDK kræver version 1.0 af Azure PowerShell-cmdlet'er.  Alle andre versioner af SDK kræver version 0.9.8 af Azure PowerShell-cmdlet'er.  Finde flere oplysninger i [denne](http://go.microsoft.com/fwlink/?LinkID=623011) blog.

###<a name="web-tools-extensions"></a>Web Tools filtypenavne

####<a name="known-issues"></a>Kendte problemer

Følgende kendte problemer vil blive behandlet i følgende version.

- App Service relaterede skyen og Server Explorer bevægelse for ikke-produktiv miljøer (som Azure Kina eller Azure stak kunder) ikke virker. For kunder i disse påvirkede områder aktiverer hente profilen Publicer fra portalen Azure muligheden for publicering. Kommende versioner Reparer bevægelser som "Vedhæft fejlfindingsværktøj" og "Vis Streaming logfiler" Azure Kina og stak kunder. 
- Kunder kan se fejl under App Service oprettelse af når App indsigt forekomst til som de implementering af er i et område end af US. I disse scenarier aktivere oprette en App-tjeneste i portalen og hentning af Publicer profilen publicering scenarier. 

###<a name="azure-hdinsight-tools"></a>Azure HDInsight værktøjer

####<a name="new-updates"></a>Nye opdateringer

- Du kan udføre forespørgslen Hive i klynge via HiveServer2 med næsten ingen omkostninger og se jobbet logfiler realtid.
- I nye Hive udførelse af opgavevisningen du grave til tingene dybere skal du finde flere oplysninger og identificere potentielle problemer.

Du kan finde oplysninger [Azure SDK 2,8 til Visual Studio 2013 og Visual Studio-2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/). 

## <a name="azure-sdk-for-net-281"></a>Azure SDK til .NET 2.8.1

### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>Kendte problemer for Visual Studio 2013 og Visual Studio 2015
 
1. Udløste WebJob publiceres til pladser er Vis og fejl og ikke en tidsplan, men det kan skubbe WebJob til Azure. Kunder, der har brug for et planlagt job kan derefter bruge portalen Azure til konfiguration af tidsplanen for WebJob. 
2. Python kunder oplever problemer med fejlfinding. Tjenesten team rullende ud af en løsning til dette, men hvis kunder påvirkes, skal du lade Microsoft kender i forummerne eller på bloggen meddelelse eller slip noter kommentarer sektion. 
3. Kunder i bestemte områder (såsom syd Indien) påvirkes App Service klargøringsfejl. Dette er i overensstemmelse med portalen og kunder, der oplever dette problem kan bruge portalen Azure til at anmode om adgang til at publicere til disse geografisk-områder. Når de anmode om adgang til disse områder ved hjælp af skal den Azure portalen klargøring arbejde. 

##<a name="azure-sdk-for-net-282"></a>Azure SDK til .NET 2.8.2

Efter installationen af 2.8.2 værktøjer, kunder kan opleve følgende problem.         

- Hvis du bruger Windows 10 og ikke har installeret Internet Explorer, kan du få fejlen "Internet Explorer blev ikke fundet".
Du kan løse problemet ved at installere Internet Explorer ved hjælp af dialogboksen Tilføj/fjern Windows-komponenter.

Hvis du ser dette problem, kan du bruge funktionen Send en smiley til at rapportere det.

Få vist [denne](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) stil kan finde flere oplysninger.
##<a name="other-updates"></a>Andre opdateringer

Du kan finde andre opdateringer [om Familieforøgelse Azure SDK 2,8 skrive](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

##<a name="also-see"></a>Se også

[Azure SDK 2,8 meddelelse indlæg](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Support og lukning af oplysninger til Azure SDK til .NET og API'er](https://msdn.microsoft.com/library/azure/dn479282.aspx)

