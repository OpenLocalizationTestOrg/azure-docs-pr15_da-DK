
<properties 
   pageTitle="Produktbemærkninger til Azure SDK til .NET 2.7 og .NET 2.7.1" 
   description="Produktbemærkninger til Azure SDK til .NET 2.7 og .NET 2.7.1" 
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

# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Produktbemærkninger til Azure SDK til .NET 2.7 og .NET 2.7.1

##<a name="overview"></a>Oversigt

Dette dokument indeholder produktbemærkninger til Azure SDK til .NET 2.7 version. 

Dokumentet indeholder også produktbemærkninger til Azure SDK til .NET 2.7.1 slip.

Azure SDK 2.7 understøttes kun i Visual Studio-2015 og Visual Studio-2013. [Azure SDK 2.6](https://azure.microsoft.com/downloads/) er sidst understøttes SDK til Visual Studio 2012.

Se [Azure SDK 2.7 meddelelse stil](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) og [Azure SDK 2.7.1 meddelelse stil](http://go.microsoft.com/fwlink/?LinkId=623850), kan finde detaljerede oplysninger om denne version.

##<a name="azure-sdk-for-net-27"></a>Azure SDK til .NET 2.7

###<a name="sign-in-improvements-for-visual-studio-2015"></a>Log på forbedringer til Visual Studio 2015

Azure SDK 2.7 for Visual Studio 2015 understøtter funktioner for administration af nye identitet i Visual Studio-2015.  Dette omfatter support for at få adgang til Azure via rolle baseret adgangskontrol, skyen løsningsudbydere, DreamSpark og andre typer konto og abonnement-konti.

Log på forbedringerne inkluderet med Azure SDK 2.7 er kun tilgængelige i Visual Studio-2015. Understøttelse af Visual Studio 2013 er inkluderet i Azure SDK 2.7.1.


###<a name="mobile-sdk"></a>Mobile SDK

Opdateret **Mobile-Apps** skabeloner til at afspejle nyeste [NuGet pakke](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) og konfiguration af processen.

###<a name="service-bus"></a>Service Bus 

Generelle fejlrettelser og forbedringer. Se produktbemærkninger af den seneste [Service Bus NuGet](http://www.nuget.org/packages/WindowsAzure.ServiceBus/)for detaljer om opdateringer, og funktioner.

###<a name="hdinsight-tools"></a>HDInsight værktøjer 

I denne udgave foretaget følgende opdateringer. Disse opdateringer er i Vis udskrift. Du kan finde yderligere oplysninger finder [denne blog](http://go.microsoft.com/fwlink/?LinkId=619108).

- Hive grafer til Hive på Tez job
- Fuld understøttelse af Hive DML IntelliSense
- Gris skabelon support
- Storm skabeloner til Azure tjenester

####<a name="breaking-changes"></a>Bryd ændringer

- Gamle **Storm** project skal opgraderes, når du bruger denne version af værktøjerne. Du kan finde yderligere oplysninger finder [denne blog](http://go.microsoft.com/fwlink/?LinkId=619108).
- Visual Studio Web Express understøttes ikke længere. Du kan finde yderligere oplysninger finder [denne blog](http://go.microsoft.com/fwlink/?LinkId=619108).

###<a name="azure-app-service-tools"></a>Azure App Service værktøjer

I denne udgave foretaget følgende opdateringer til Web værktøjer Extensions. Finde flere oplysninger i [denne](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) blog. 

- Understøttelse af DreamSpark konti, der er tilføjet
- Ændre fulde til Azure-funktioner, der er foretaget til at understøtte det nye Azure ressource Management API'er
- Understøttelse af Azure App tjeneste, der er føjet til [Skyen Explorer](#cloud_explorer)

####<a name="known-issues"></a>Kendte problemer

Web App-installation slot noder vises ikke under noden pladser i Server Explorer, og Web App-installation slot underordnede hierarkinoder indlæses ikke under skyen Explorer. Problemet er løst og forberedt på den næste SDK-version. 


###<a name="cloud_explorer"></a>Skyen Explorer til Visual Studio 2015

Azure SDK 2.7 indeholder skyen Explorer til Visual Studio-2015 som gør det muligt at få vist dine Azure ressourcer, undersøge deres egenskaber og udfører vigtige udvikler handlinger fra i Visual Studio. 

Skyen explorer understøtter følgende:

- Ressourcegruppe og ressourcetype visninger af dine Azure ressourcer 
- Søge efter ressourcer efter navn (tilgængelig i ressourcetype visning)
- Understøttelse af abonnementer og ressourcer, der har rolle baseret Access kontrolelement (RBAC) anvendt 
- Integreret handling panel som viser henvender sig til udviklere handlinger, der er specifikke for valgte ressourcer. For eksempel: vedhæfte remote fejlfindingsværktøj til virtuelle maskiner, der er oprettet ved hjælp af stablen ressourcestyring vist diagnosticering data til virtuelle maskiner osv.
- Integreret panelet af dokumentegenskaber, som viser henvender sig til udviklere egenskaber, der ofte behov under Udviklingscenter/test 
- Hurtig Skift konto skal bruges, når optælling af ressourcer (Brug kommandoen indstillinger på værktøjslinjen) 
- Filtrering af abonnementer skal bruges, når optælling af ressourcer (Brug kommandoen indstillinger på værktøjslinjen) 
- Deep links til portalen Azure til styring af ressourcer og grupper 
 
 
###<a name="azure-resource-manager-tools"></a>Azure ressourcestyring værktøjer 

Azure ressourcestyring værktøjerne er blevet opdateret for at arbejde med rolle baseret Access kontrolelement (RBAC) og nye abonnementstyper.  Inkluderet med disse ændringer er muligheden for at bruge nye lagerplads konti, ud over klassisk lagerplads til at gemme elementer under installationen.  

Hvis du bruger et Azure ressourcegruppe projekt fra en tidligere version af SDK med SDK 2.7, bruges et nyt installation script til at installere ved hjælp af en ny lagerplads konto i stedet for klassisk lagerplads.  Du bliver bedt om før der foretages ændringer til dit projekt til at føje det nye script.  Gamle scriptet omdøbes, og du skal foretage eventuelle ændringer i det nye script manuelt.
 
 
###<a name="storage-explorer-tools"></a>Lagerplads Explorer værktøjer 

- Understøttelse af visning af Føj BLOB. Flere oplysninger i [denne blogindlæg](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
- Understøttelse af visning af Premium lagerplads konti via Server Explorer. Server Explorer vises kun siden BLOB premium lagerplads-konto, som de er den eneste understøttede type premium lagerplads-konto.

###<a name="azure-data-factory-tools-for-visual-studio"></a>Azure Data Factory Tools til Visual Studio 

Introduktion til **Azure Data Factory Tools** til Visual Studio. Nedenfor finder du funktionerne aktiveret. Se [denne blog](http://go.microsoft.com/fwlink/?LinkId=617530) for at få flere oplysninger.

- **Skabelon baseret redigering**: Vælg Brug familiemedlemmernes baseret skabeloner, data bevægelse skabeloner eller databehandling skabeloner til at installere en integration til slut dataløsning og komme i gang praktiske hurtigt med Data Factory. 
- **Integration med Solution Explorer til redigering og implementere Data Factory enheder**: oprette og installere rørledninger og relaterede objekter som Visual Studio-projekter. 
- **Integration med Diagram se for visuelle interaktion samtidig redigering**: visuelt Skab rørledninger og datasæt med støtte fra diagramvisning. 
- **Integration med Server explorer til gennemsyn og interaktion med allerede installeret enheder**: udnytte Server Explorer til at gennemse, har du allerede installeret data fabrikker og tilsvarende enheder. Importere en udløst data fabrik eller enheder (Pipeline, sammenkædede Service datasæt) til projektet. 
- **JSON redigering med validering af skema og omfattende intellisense**: effektivt at konfigurere og redigere JSON dokumenter af Data Factory enheder med omfattende intellisense og -Skemafiler validering 
- **Flere miljø udgivelse**: publicere skrevet rørledninger til Udviklingscenter, test eller Prod.ordre-miljø ved at oprette separate config filer for hver-miljø.
- **Gris, Hive og .net baseret databehandling Support**: understøttelse af gris og Hive Scripts i Data Factory projekt. Understøttelse af refererer til C# Project til administration af .net aktivitet.

##<a name="azure-sdk-for-net-271"></a>Azure SDK til .NET 2.7.1

Følgende afsnit indeholder opdateringer, der blev introduceret med Azure SDK til .NET 2.7.1 slip.
###<a name="hdinsight-tools"></a>HDInsight værktøjer 

Du kan finde mere detaljeret beskrivelse af HDInsight værktøjer opdateringer [denne blog](http://go.microsoft.com/fwlink/?LinkId=623831).

- Hive Job Operator visning (en ny funktion)

    For at hjælpe dig med at forstå forespørgslen Hive bedre, blev funktionen Hive Operator tilføjet. Hvis du vil se alle operatorer i et knudepunkt, dobbeltklik på knudepunkter for jobbet diagrammet. Hold markøren over operatoren for at få vist flere oplysninger om en bestemt operator.
- Hive fejl markøren (en ny funktion)

    Hvis du vil gør det muligt at få vist de grammatiske fejl med det samme, blev funktionen Hive fejl markøren tilføjet. Desuden fejlmeddelelser blev udvidet, og du kan nu se detaljerede grammatiske fejl med det samme (indtil denne version, du havde til at sende et Hive script til klyngen og vente lidt tid, før du går oplysninger om din fejlmeddelelse).  
- Storm topologi Graph (en ny funktion)

    Det er meget vigtigt at visualisere, når du vil se, om din topologi fungerer som forventet. I denne udgave tilføjet vi visualisering for Storm grafer. Du kan visualisere vigtige målene for topologien (for eksempel en farve angiver vejrudsigt en bestemt Bolt "optaget" eller ej). Du kan også dobbeltklikke klikke Bolt/tud for at få vist flere detaljer.

- Understøttelse af HDInsight klynger, der er oprettet i portalen Azure (en rettelse)

    Du kan nu bruge Visual Studio til at få vist og sende job til alle dine HDInsight klynger uanset hvor klyngen blev oprettet.

- Mere understøttelse af IntelliSense & hurtigere Hive Metadata indlæsning (en forbedring)

    Vi har forbedret IntelliSense ved at tilføje mere brugervenligt forslag. For eksempel kan tabelalias nu også foreslås i IntelliSense så du nemmere kan skrive din forespørgsel. Vi har også forbedret Hive metadataene indlæsning, så det kun tager flere sekunder at få vist alle databaser, tabeller og kolonner med dine Hive metastore.

Du kan finde mere detaljeret beskrivelse af HDInsight værktøjer opdateringer [denne blog](http://go.microsoft.com/fwlink/?LinkId=623831).

###<a name="improvements-in-visual-studio-2013"></a>Forbedringer i Visual Studio 2013

- Azure SDK 2.7.1 aktiverer Visual Studio 2013 for at få adgang til Azure konti og abonnementer via rolle baseret adgangskontrol, skyen løsningsudbydere og Dreamspark.
- Med Azure SDK 2.7.1 findes ny skyen værktøjet stifindervinduet nu også i Visual Studio-2013.

###<a name="known-issues"></a>Kendte problemer

Installation af Azure SDK 2.6 eller 2.7.1 til Visual Studio Community 2013 på en ikke - engelsk OS vises en advarsel, kan uoverensstemmelser mellem engelsk og ikke-engelsk ressourcer af Visual Studio. En advarsel muligvis afvist sikkert. Det forekommer kun, hvis computeren ikke indeholder en tidligere installation af Visual Studio Community 2013, og du installerer SDK på en ikke - engelsk OS. Advarslen vises efter sprogpakken gælder RTM ressourcer til Visual Studio, men før den gælder opdatering 4. Afviser advarslen, så sprogpakken vil fortsætte og fuldføre anvende opdatering 4-version af language pack indhold.

LightSwitch projekter er ikke compatibile med denne version. Problemet bliver løst med den næste SDK-version.

##<a name="also-see"></a>Se også
[Azure SDK 2.7.1 meddelelse indlæg](http://go.microsoft.com/fwlink/?LinkId=623850)

[Azure SDK 2.7 meddelelse indlæg](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Support og lukning af oplysninger til Azure SDK til .NET og API'er](https://msdn.microsoft.com/library/azure/dn479282.aspx/)
