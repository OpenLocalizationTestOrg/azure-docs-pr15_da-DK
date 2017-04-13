<properties 
   pageTitle="Produktbemærkninger til Azure SDK til .NET 2.6" 
   description="Produktbemærkninger til Azure SDK til .NET 2.6" 
   services="app-service/web" 
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

 
# <a name="azure-sdk-for-net-26-release-notes"></a>Produktbemærkninger til Azure SDK til .NET 2.6

Dette dokument indeholder produktbemærkninger til Azure SDK til .NET 2.6 version. 

Du kan udvikle skyen tjenesteprogrammer (PaaS) målretning af .NET 4.5.2 eller .NET 4.6, forudsat, at du manuelt installere destinationen .NET Framework på rollen skyen med Azure SDK 2.6. Se [installere .NET på en skybaseret tjeneste rolle](http://go.microsoft.com/fwlink/?LinkID=309796).


##<a name="service-bus-updates"></a>Service Bus opdateringer

- Begivenhed Hubs: 

    - Nu kan målrettede adgangskontrol, når du sender hændelser ved at udsætte yderligere publisher slutpunkt for begivenhed Hubs.
    - Yderligere stabilitet og til forbedring af føjet til begivenhed Hubs funktion.
    - Tilføje understøttelse af Amqp protokollen over WebSocket til messaging og begivenhed Hubs.

##<a name="hdinsight-tools-for-visual-studio-updates"></a>HDInsight værktøjer til Visual Studio opdateringer

- **IntelliSense udvidede**: remote metadata forslag

    HDInsight værktøjer til Visual Studio understøtter nu få remote metadata, når du redigerer din Hive-script. For eksempel kan du skrive * *Vælg* fra** og alle tabelnavne vil blive vist. Kolonnenavnene vil også blive vist når du har angivet en tabel.

- **HDInsight emulator support**

    HDInsight værktøjer til Visual Studio understøtter nu oprette forbindelse til HDInsight emulator, så du kan udvikle dine Hive scripts lokalt uden Introduktion til en hvilken som helst omkostninger, derefter udføre disse scripts mod din HDInsight klynger. 

    Se [denne manual](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409)kan finde flere oplysninger.

- **HDInsight værktøjer til Visual Studio understøtter af generisk Hadoop klynger** (Preview)

    HDInsight værktøjer til Visual Studio understøtter nu generisk Hadoop klynger, så du kan bruge HDInsight Tools til Visual Studio til at gøre følgende:

    - oprette forbindelse til din klynge 
    - skrive Hive forespørgsel med forbedret understøttelse af automatisk-IntelliSense-fuldførelse 
    - få vist alle job i din klynge med en intuitiv brugergrænseflade. 

    Se [denne manual](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409)kan finde flere oplysninger.

##<a name="in-role-cache-updates"></a>I rolle Cache opdateringer

- **I rolle cachen** blev opdateret for at bruge **Microsoft Azure lagerplads SDK** version 4.3. Indtil nu brugte **i rolle Cache** Azure lagerplads SDK version 1.7.

    Kunder ved hjælp af Azure SDK 2,5 eller nedenfor skal opdatere til Azure SDK 2.6 og flytte til den nye version af Azure lagerplads SDK. 

    Azure-lager version 2011-08-18 er planlagt til at blive fjernet August 1 2016 på nuværende tidspunkt. En hvilken som helst overførsler af i rolle Cache fra Azure SDK 2,5 eller nedenfor skal til 2.6 være fuldført ved nuværende tidspunkt. Finde flere oplysninger om lukning af Azure-lager version 2011-08-18, [Version fjernelse opdatering til Microsoft Azure-lager Service: lokalnummer til 2016](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

>[AZURE.IMPORTANT]Vi præsentation af 30 November 2016 lukning af til Azure administrerede Cachetjeneste og Azure i rolle Cache. Vi anbefaler, at du overfører til Azure Redis cachen i forberedelse til denne pensionen. Finde flere oplysninger om datoer og overførsel vejledning, [som Azure Cache tilbyder er bedst for mig?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)

##<a name="azure-app-service-tools"></a>Azure App Service værktøjer

Følgende elementer er blevet opdateret i Azure SDK 2.6-versionen.

- Azure udgivelse udvides til at omfatte Azure API Apps som destination installation.
- API Apps klargøring funktionalitet aktivere brugere med API App oprettelse og klargøring af funktioner.
- Server Explorer ændres, afspejler ny App Service node til internettet, Mobile og API apps, der er grupperet efter ressourcegruppe.
- Tilføj Azure API App klient bevægelse, der er føjet til de fleste C# projekter, der gør det muligt for automatisk oprettelse af Swagger aktiverede API Apps kører i en brugers Azure abonnement.
- API Apps værktøj og App Service knuder på Server Explorer er kun tilgængelige på Visual Studio-2013. 

##<a name="azure-resource-manager-tools-updates"></a>Azure ressourcestyring værktøjer opdateringer

Værktøjerne Azure ressource manager er blevet opdateret for at medtage skabeloner til virtuelle maskiner, netværk og opbevaring. Redigere oplevelse JSON er blevet opdateret for at medtage en ny dispositionsvisning til skabeloner og muligheden for at redigere de skabeloner, ved hjælp af JSON kodestykker. Skabeloner, der installeres fra Visual Studio Brug en PowerShell-script, der følger med Microsoft project, så alle ændringer foretaget scriptet der anvendes af Visual Studio.

##<a name="diagnostics-improvements-for-cloud-services"></a>Forbedringer af diagnosticering til Cloud Services

Azure SDK 2.6 viser tilbage support til indsamling af diagnosticering logge i Azure Beregn emulatoren og overføre dem til udvikling lagerplads. En hvilken som helst diagnosticering logger (herunder programmet sporing logfiler, begivenhed sporing for Windows (ETW) logfiler, tællere i ydeevne, infrastruktur logfiler og windows-hændelseslogge) genereres når programmet kører i emulatoren kan overføres til udvikling lagerplads at bekræfte, at din logføring af diagnosticering fungerer på din lokale computer. 

Kontoen diagnosticering lagerplads kan nu angives i filen service konfiguration (.cscfg), der gør det nemmere at bruge forskellige diagnosticering lagerplads konti til forskellige miljøer. Der er nogle vigtige forskelle mellem hvordan forbindelsesstrengen arbejdet Azure SDK 2.4 og Azure SDK 2.6. Finde flere oplysninger om, hvordan du bruger diagnosticering lagerplads forbindelsen streng, og hvordan det påvirker dine projekter [Konfiguration af diagnosticering til Azure Cloud Services](http://go.microsoft.com/fwlink/?LinkID=532784).

##<a name="breaking-changes"></a>Bryd ændringer

###<a name="azure-resource-manager-tools"></a>Azure ressourcestyring værktøjer 

- Den **Skyen installation projekter** projekttype, der er tilgængelige i Azure SDK 2,5 er blevet omdøbt til **Azure ressourcegruppe**.
- Projekter, der er oprettet i Azure SDK 2,5 **Skyen installation projekter** type kan bruges i 2.6, men anvender skabelonen fra Visual Studio mislykkes. Men anvender med PowerShell-script fungerer stadig som den gjorde tidligere.  Finde oplysninger om, hvordan du bruger Læs **Skyen installation projekter** i 2.6 denne [stil](http://go.microsoft.com/fwlink/?LinkID=534086).
 
##<a name="known-issues"></a>Kendte problemer

- Indsamling af diagnosticering logge i emulatoren kræver et 64-bit-operativsystem. Diagnosticering logfiler indsamles ikke, når der benyttes et 32-bit-operativsystem. Dette påvirker ikke andre emulator-funktioner. 

- Azure SDK 2.6 udgivet 4-29-2015 havde to problemer: 

    - Universal App kan ikke indlæses i Visual Studio 2015, når Azure SDK 2.6 blev installeret på maskinen.
    - Fejlfinding af en skybaseret tjeneste project ville mislykkes i Visual Studio 2013 og Visual Studio-2015 hvor Visual Studio reagere og går ned, mens der vises en dialogboks med meddelelsen "Konfiguration af diagnosticering til emulator".
    
    En opdatering til Azure SDK 2.6 blev udgivet 5-18-2015. Den opdaterede version er 2.6.30508.1601; den indeholder løsninger til to problemer, der er beskrevet ovenfor. Du kan identificere den nyeste version til SDK fra Kontrolpanel -> programmer og funktioner -> Microsoft Azure-værktøjer til Microsoft Visual Studio 2013 – v 2.6. Kolonnen Version vises build-nummeret.

    Hvis du stadig står over ovenstående problemer, kan du installere den nyeste version af Azure 2.6 SDK for [VS 2012](http://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409), [VS 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) eller [VS 2015](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).
 
##<a name="see-also"></a>Se også

[Support og lukning af oplysninger til Azure SDK til .NET og API'er](https://msdn.microsoft.com/library/azure/dn479282.aspx/)
