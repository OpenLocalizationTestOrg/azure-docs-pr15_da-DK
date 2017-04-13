<properties
    pageTitle="Hvad er Azure .NET SDK"
    description="Få mere at vide, hvad der skal medtages i Azure .NET SDK."
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor="mollybos"
    services=""/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/30/2016"
    ms.author="rachelap"/>

# <a name="what-is-the-azure-sdk-for-net"></a>Hvad er Azure SDK til .NET?

## <a name="overview"></a>Oversigt

Azure SDK til .NET er et sæt værktøjer til Visual Studio, kommandolinjen værktøjer, runtime binære filer og klientbiblioteker, der hjælper dig med at udvikle, teste og installere apps, der kører i Azure. I denne artikel beskriver, hvad du får, når du installerer SDK. Du kan hente SDK fra [Azure Downloads-siden](https://azure.microsoft.com/downloads/).

Azure SDK til .NET omfatter også [klientbiblioteker for arket Azure tjenester](http://go.microsoft.com/fwlink/?LinkId=510472). Disse biblioteker installeres separat ved hjælp af NuGet.

##<a id="included"></a>Hvad er inkluderet i Azure SDK til .NET

Azure SDK til .NET installationer følgende produkter:

- [Visual Studio Community-udgaven 2015](#vwd)
- [Microsoft Azure-lager Emulator](#stgemulator)
- [Microsoft Azure-lager værktøjer](#stgtools)
- [Microsoft Azure redigering værktøjer](#auth)
- [Microsoft Azure Emulator](#emulator)
- [HDInsight værktøjer til Visual Studio og Microsoft Hive ODBC-Driver](#hdinsight)
- [Microsoft Azure-biblioteker til .NET](#libraries)
- [Microsoft Azure Mobile App SDK](#mobile)
- [Microsoft Azure PowerShell](#ps)
- [Microsoft Azure værktøjer til Microsoft Visual Studio](#tools)
- [Microsoft ASP.NET og Webværktøjer til Visual Studio](#wte)
- [Microsoft Azure Data sø Tools til Visual Studio](#datalake)

###<a id="vwd"></a>Visual Studio Community-udgaven 2015

Hvis du ikke har Visual Studio på computeren, installere SDK [Visual Studio Community Edition 2015](https://www.visualstudio.com/products/visual-studio-community-vs).

###<a id="stgemulator"></a>Microsoft Azure-lager Emulator

[Azure lagerplads Emulator](http://msdn.microsoft.com/library/hh403989.aspx) bruger en forekomst af SQL Server og det lokale filsystem til at simulere Azure-lager (køer, tabeller, BLOB), så du kan teste lokalt.

###<a id="stgtools"></a>Microsoft Azure-lager værktøjer

Derved installeres [AzCopy](http://aka.ms/AzCopy), et værktøj på kommandolinjen, du kan bruge til at overføre data til og fra en Azure-lager-konto.

###<a id="auth"></a>Microsoft Azure redigering værktøjer

Dette omfatter følgende:

* [CSPack kommandolinjen værktøj](http://msdn.microsoft.com/library/gg432988.aspx) til oprettelse af installationspakker.
* [CSEncrypt værktøj på kommandolinjen](http://msdn.microsoft.com/library/hh404001.aspx) til at kryptere adgangskoder, der bruges til at få adgang til skyen service rolle forekomster via en forbindelse til Fjernskrivebord.
* Runtime binære filer, der skybaseret tjenesteprojekter kræver til at kommunikere med deres runtime-miljø og til diagnosticering. Disse binære filer er ikke tilgængelige i NuGet pakker.

###<a id="emulator"></a>Microsoft Azure Emulator

[Azure Emulator](http://msdn.microsoft.com/library/dn339018.aspx) simulerer skyen service-miljø, så du kan teste skyen service projekter lokalt på din computer, inden du implementerer dem til Azure.

###<a id="hdinsight"></a>HDInsight værktøjer til Visual Studio og Microsoft Hive ODBC-Driver

HDInsight værktøjer i Server Explorer gør det muligt at navigere Hive databaser og sammenkædede lagerplads konti til HDInsight klynger, oprette tabeller, og oprette og sende Hive forespørgsler. Se [Introduktion til brug af HDInsight Hadoop-værktøjer til Visual Studio](hdinsight/hdinsight-hadoop-visual-studio-tools-get-started.md)kan finde flere oplysninger.

###<a id="libraries"></a>Microsoft Azure-biblioteker til .NET

Dette omfatter følgende:

* NuGet-pakker til Azure-lager, Service Bus og cachelagring, der er gemt på computeren, så Visual Studio kan oprette nye skyen service projekter under offline.
* En Visual Studio plug-in'en, som gør det muligt for projekter [i rolle cachen](http://msdn.microsoft.com/library/dn386103.aspx) til at køre lokalt i Visual Studio.

###<a id="mobile"></a>Microsoft Azure Mobile App SDK

Værktøjer til at arbejde med [Azure App Service Mobile-Apps](app-service-mobile/app-service-mobile-value-prop.md).

###<a id="ps"></a>Microsoft Azure PowerShell

Azure PowerShell gør det muligt at [automatisere Azure miljø oprettelse af og installation](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

###<a id="tools"></a>Microsoft Azure værktøjer til Microsoft Visual Studio

Dette gør det muligt at arbejde med Azure ressourcer, primært Cloud Services og virtuelle maskiner:

* [Oprette, åbne, og udgive skyen service projekter](cloud-services/cloud-services-dotnet-get-started.md).
* [Opret installationspakker til skyen service projekter](http://msdn.microsoft.com/library/ff683672.aspx).
* [Oprette virtuelle Azure-computere under oprettelse af nye webprojekter](virtual-machines/virtual-machines-windows-classic-web-app-visual-studio.md).
* [Oprette PowerShell-scripts, mens du opretter nye virtuelle maskiner](http://msdn.microsoft.com/library/dn642480.aspx).
* [Få vist og administrere skyen project Tjenesteindstillinger i Visual Studio Projektegenskaber windows](http://msdn.microsoft.com/library/ee405486.aspx).
* Få vist og administrere [skytjenester](http://msdn.microsoft.com/library/ff683675.aspx), [virtuelle maskiner](http://msdn.microsoft.com/library/jj131259.aspx)og [Service Bus](http://msdn.microsoft.com/library/jj149828.aspx) i Server Explorer.
* [Køre i fejlsikret tilstand fra en fjernplacering til skytjenester og virtuelle computere](http://msdn.microsoft.com/library/ff683670.aspx).
* [Automatisere ressource klargøring ved hjælp af Azure ressource gruppere installation projekter](https://msdn.microsoft.com/library/dn872471.aspx)

###<a id="wte"></a>Microsoft App Service Tools til Visual Studio

Dette gør det muligt at arbejde med Azure websteder:

* [Udgiv webprojekter til Azure websteder](app-service-web/web-sites-dotnet-get-started.md).
* [Publicer console programmet projekter til Azure WebJobs](app-service-web/websites-dotnet-deploy-webjobs.md).
* [Opret Azure websteds- og SQL-Database ressourcer, mens du opretter et nyt webprojekt eller under publicering af et webprojekt](app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
* [Oprette PowerShell installation-scripts, mens du opretter nye websteder](http://msdn.microsoft.com/library/dn642480.aspx).
* [Administrer og foretage fejlfinding af Azure websteder i Server Explorer](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#sitemanagement).
* [Køre i fejlsikret tilstand fra en fjernplacering til websteder og WebJobs](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug).

>[AZURE.NOTE] Du behøver ikke at installere Azure SDK til .NET kunne bruge disse funktioner de er også inkluderet i Visual Studio-opdateringer.

##<a id="datalake"></a>Microsoft Azure Data sø Tools til Visual Studio

Du kan finde flere oplysninger, se [Selvstudium: udvikle U-SQL-scripts ved hjælp af Data sø Tools til Visual Studio](data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

##<a id="notincluded"></a>Hvad følger ikke, når du installerer Azure SDK til .NET

Der er et par ting, som du muligvis vil til Azure udvikling, der ikke er inkluderet, når du installerer SDK. De vigtigste af disse er følgende:

* [Klient-biblioteker](http://go.microsoft.com/fwlink/?LinkId=510472).

    Azure SDK indeholder klientbiblioteker for forbrug Azure services, men ikke alle er installeret, når du installerer SDK. Hvis dit program skal et klientbibliotek, der ikke installere SDK, kan du få den [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Hvis dit program bruger en klientbibliotek, der installerer SDK, er det en god ide at opdatere den med den aktuelle version på NuGet.org.

    **Lokale kopier af klientbiblioteker.** Azure SDK til .NET blev kopieret til computeren NuGet-pakker for nogle Azure klientbiblioteker, som lager, Service Bus og cachelagring. Disse klientbiblioteker medtages automatisk i nye projekter i skyen-tjenesten, så de lokale NuGet pakker aktivere Visual Studio til at oprette projekter, selvom du ikke har forbindelse til internettet. Klientbiblioteker opdateres generelt hyppigere, end der udgives SDK versioner, så klient bibliotekerne på NuGet.org er ofte mere aktuel end hvad du får med SDK.

    **Project-skabeloner, der omfatter klientbiblioteker.** Kun [Azure skybaseret tjeneste](cloud-services/cloud-services-dotnet-get-started.md) og Azure App Service [Mobile-Apps](./app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) projektskabeloner Medtag automatisk nogle klientbiblioteker. Andre biblioteker eller andre skabeloner, skal du installere de [klient bibliotek NuGet pakker](http://go.microsoft.com/fwlink/?LinkId=510472) , du skal bruge.

* [Mobile-Apps project-skabeloner](./app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

    Mobile-Apps skabeloner er kun tilgængelig i Visual Studio 2013 opdatering 2 eller nyere. De er ikke tilgængelige i Visual Studio 2012 eller tidligere versioner og ikke i Visual Studio 2013 opdatering 1 eller tidligere, selvom du installerer Azure SDK til .NET.

##<a id="faq"></a>Ofte stillede spørgsmål

- [Mange Azure funktioner er allerede i Visual Studio. Skal jeg installere Azure SDK til .NET?](#azinvs)
- [Jeg vil en klientbibliotek. Skal jeg installere Azure SDK til .NET at få det til?](#clientlib)
- [Hvor kan jeg finde tidligere versioner af Azure SDK til .NET?](#olderversions)
- [Hvad er Livscykluspolitikken for versioner af Azure SDK til .NET?](#lifecycle)
- [Hvilke gæst OS versioner er Azure SDK til .NET kompatibelt med?](#guestos)
- [Hvordan fjerner jeg Azure SDK til .NET?](#uninstall)

###<a id="azinvs"></a>Mange Azure funktioner er allerede i Visual Studio. Skal jeg installere Azure SDK til .NET?

Det er en god ide at installere SDK, hvis du vil udvikle til Azure ved hjælp af værktøjerne seneste. Hvis du hellere vil ikke installere SDK, kan du gøre, hvis følgende betingelser er opfyldt:

* Du har installeret den nyeste [Visual Studio-opdatering](http://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5).
* Du udvikler kun for Azure websteder eller Mobile tjenester, ikke til skytjenester eller virtuelle computere.
* Dit program bruge ikke lagerplads, eller den anvender lagerplads, men du behøver ikke lagerplads emulatoren eller værktøjet AzCopy.

###<a id="clientlib"></a>Jeg vil en klientbibliotek. Skal jeg installere Azure SDK til .NET at få det til?

SDK installerer klientbiblioteker, kun, så du kan oprette skyen service projekter selvom du ikke har forbindelse til internettet. Aktuelle klientbiblioteker er tilgængelige i NuGet pakker på [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Se, [hvilke har ikke medtages, når du installerer Azure SDK til .NET](#notincluded) tidligere i dette dokument kan finde flere oplysninger.

###<a id="olderversions"></a>Hvor kan jeg finde tidligere versioner af Azure SDK til .NET?

Ældre versioner i [Azure SDK til .NET](https://azure.microsoft.com/downloads/archive-net-downloads/) downloadsiden.

###<a id="lifecycle"></a>Hvad er Livscykluspolitikken for versioner af Azure SDK til .NET?

Se [Microsoft Azure Cloud Services understøtter politik om supportlivscyklus](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq).

###<a id="guestos"></a>Hvilke gæst OS versioner er Azure SDK til .NET kompatibelt med?

Se [Azure gæst OS versioner og SDK kompatibilitet Matrix](http://msdn.microsoft.com/library/ee924680.aspx).

###<a id="uninstall"></a>Hvordan fjerner jeg Azure SDK til .NET?

Hvert element, der vises i denne artikel under [hvad der skal medtages i Azure SDK til .NET](#included) er et separat program på listen over installerede programmer i Windows Kontrolpanel **programmer og funktioner**.  Der findes ingen måde at fjerne dem som en gruppe Du skal fjerne hvert program enkeltvis.

Når du har i Azure SDK til .NET installeret i forvejen, og du installere en ny version, er der generelt ikke nødvendigt at fjerne den gamle liste. I de fleste tilfælde opdaterer SDK installationen en eksisterende program i stedet for at tilføje en ny og efterlade den gamle liste.

Hvis du vil fjerne Nej-længere-det er nødvendigt resterne af en tidligere version, fjerne kun programmer, som angiver det ældre versionsnummer, og kun fjerne dem, hvis det samme program med en nyere version er til stede. For eksempel efter opdatering fra 2,5 2.6 kan du se både 2,5 og 2.6 versioner af "Microsoft Azure værktøjer til Microsoft Visual Studio 2013", og du kan fjerne den 2,5 version. Men du kan kun se den 2,5 version af "Microsoft Azure redigering værktøjer", og det ville være sikkert at fjerne.

Bemærk, at version tal i programmet titler vises i **programmer og funktioner** kan være vildledende.  For eksempel indeholder SDK version 2.6 "Microsoft Azure Mobile App SDK version 1.0", hvis du installerer SDK til Visual Studio 2013 og "Microsoft Azure Mobile App SDK V2.0" til Visual Studio-2015; i dette tilfælde er versionen ikke SDK-version, men en indikator, hvoraf Visual Studio version programmet, der gælder for.

I denne artikel listen ikke alle programmer, der hver tidligere version af Azure SDK inkluderet; der findes andre programmer, du kan fjerne fra tidligere versioner af SDK, fordi tidligere versioner af SDK også medtages de programmer, der er udeladt i nyere versioner. For eksempel 2.5-versionen kan installeres "Azure ressource Manager værktøjer til Visual Studio", men det er ikke på listen i denne artikel, fordi den ikke længere vises som et separat program under **programmer og funktioner**.  I denne artikel vises kun de programmer, der er inkluderet i Azure SDK til .NET version 2.6.

> **Note:** Nogle af de programmer, der SDK indeholder muligvis også installeres særskilt i andre kontekster og det kan være nødvendigt selvom du ikke har brug for i fuld SDK. Den samme kan være sandt om programmer, der blev installeret af tidligere versioner af SDK, men er udeladt i SDK nyere. Når du fjerner programmer, skal du huske at undgå at fjerne noget, der stadig er nødvendigt på din computer.



##<a id="versions"></a>Versioner

Se, hvilken version er aktuelle eller hente ældre versioner, skal du på siden [Azure SDK til .NET versionshistorik](https://azure.microsoft.com/downloads/archive-net-downloads/) .

##<a id="resources"></a>Ressourcer

For at hente den aktuelle Azure SDK til .NET eller et klientbibliotek, på computeren, skal du se [Azure Downloads-siden](https://azure.microsoft.com/downloads/).

Du kan finde i Azure SDK til .NET kildekode, herunder klientbiblioteker, [GitHub.com/Azure](https://github.com/azure/).

Du kan finde Azure klient bibliotek referencedokumentation [Azure.NET Reference](https://azure.microsoft.com/documentation/api/).
