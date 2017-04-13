<properties 
   pageTitle="Produktbemærkninger til Azure SDK til .NET 2.9" 
   description="Produktbemærkninger til Azure SDK til .NET 2.9" 
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

# <a name="azure-sdk-for-net-29-release-notes"></a>Produktbemærkninger til Azure SDK til .NET 2.9

##<a name="overview"></a>Oversigt

Dette dokument indeholder produktbemærkninger til Azure SDK til .NET 2.9 version. 

Du kan finde detaljerede oplysninger om opdateringer i denne udgave, [Azure SDK 2.9 meddelelse skrive](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Azure SDK 2.9 til Visual Studio 2015 opdatering 2 og Visual Studio "15" få vist et eksempel
 
Denne opdatering indeholder følgende fejlrettelser:

- Problem, der er relateret til de øvrige API klient generering af i hvor strengen "Ukendt Type" skal vises som navnet på mappen kode-kasse og/eller navnet på navneområdet sluppet i den genererede kode.
- Problem, der er relateret til planlagt WebJobs, hvor oplysningerne om godkendelse blev ned, der skal overføres til planlæggeren klargøring af processen.

Denne opdatering omfatter følgende nye funktioner:

- Understøttelse af sekundære App tjenester i fanen "Tjenester" af dialogboksen App Service klargøring. 

##<a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Azure Data sø Tools til Visual Studio 2015 opdatering 2
 
Denne opdateringer omfatter følgende:

- **Azure Data sø Tools** til Visual Studio er nu flettet ind i Azure SDK til .NET version. Værktøjet installeres automatisk, når du installerer Azure SDK. 

    Værktøjet opdateres ofte, gå [her](http://aka.ms/datalaketool) at få opdateringerne.

- **Server Explorer** nu giver dig mulighed at få vist alle og oprette nogle U-SQL metadata objekter. Du kan finde yderligere oplysninger finder [denne](https://azure.microsoft.com/documentation/services/data-lake-analytics/) blog.


##<a name="hdinsight-tools"></a>HDInsight værktøjer 

**HDInsight værktøjer** til Visual Studio understøtter nu HDInsight version 3.3, herunder viser Tez grafer og andre sprog løsninger.


##<a name="azure-resource-manager"></a>Azure ressourcestyring 

Denne version tilføjer [KeyVault](../resource-manager-keyvault-parameter.md) understøttelse af ARM skabeloner.

##<a name="see-also"></a>Se også

[Azure SDK 2.9 meddelelse indlæg](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)
