
<properties
    pageTitle="Azure-ensartet lagerplads: forskelle og overvejelser i forbindelse med | Microsoft Azure"
    description="Forstå forskellen fra Azure-lager og andre Azure-ensartet overvejelser om installation lagerplads."
    services="azure-stack"
    documentationCenter=""
    authors="MChadalapaka"
    manager="siroy"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="mchad"/>

# <a name="azure-consistent-storage-differences-and-considerations"></a>Azure-ensartet lagerplads: forskelle og overvejelser i forbindelse med

Azure-ensartet lagerplads er mængde lagerplads skytjenester Microsoft Azure stablede. Azure-ensartet lagerplads indeholder blob, tabel, kø og konto-funktionen til dokumentstyring med Azure-ensartet semantik. I denne artikel indeholder en oversigt over kendte Azure-ensartet lagerplads forskellene med Azure-lager. Det kan du også se andre overvejelser i forbindelse med at huske på, når du installerer den aktuelt offentligt tilgængelige foreløbige version af Microsoft Azure stak.

<span id="Concepts" class="anchor"><span id="_Toc386544169" class="anchor"><span id="_Toc389466742" class="anchor"><span id="_Ref428966996" class="anchor"><span id="_Toc433223853" class="anchor"></span></span></span></span></span>
## <a name="known-differences"></a>Kendte forskelle

Denne Technical Preview version af Azure-ensartet lagerplads har ikke 100% funktion fungerer ensartet med Azure-lager til API-versioner, der understøttes. Kendte forskel i funktioner omfatter følgende:

-   Visse lagerplads kontotyper endnu ikke er tilgængelige, for eksempel Standard\_RAGRS og Standard\_GRS.

-   Premium\_LRS lagerplads konti kan klargøres. Men der er i øjeblikket ingen begrænsninger for ydeevnen eller garantier.

-   Azure filer funktionalitet er ikke tilgængelig.

-   Få siden intervaller API understøtter ikke hentning af sider, der er forskellige øjebliksbillede af siden BLOB.

-   Få siden intervaller API returnerer sider, der indeholder 4 KB af granularitet.

-   Partitionsnøgle og række nøgle i Azure-ensartet lagerplads tabel implementering er hver begrænset til 400 tegn (det vil sige, 800 byte) i størrelse.

-   BLOB navne i Azure-ensartet lagerplads Blob service gennemførelse er begrænset til 880 tegn (det vil sige, 1760 byte) i størrelse.

-   Azure-ensartet lagerplads implementeringen af lejer lagerplads Brugsdata rapportering indeholder lagerplads brugen målere, der er identisk med dem i Azure, med de samme semantik og enheder. I øjeblikket er dog lagerplads transaktioner brugen dokumentmåler omfatter ikke IaaS transaktioner og dataoverførsel brugen tælleren skelner ikke båndbredde brugen af interne og eksterne netværkstrafik til en Azure stak område.

-   Visse forskelle i omfanget af funktionalitet til administration af lagerplads. For eksempel kan du ændre kontotypen eller have brugerdefinerede domæner. Desuden kun API-niveau konsistens tilbydes for Premium\_LRS lagerplads kontotype.

## <a name="deployment-considerations"></a>Overvejelser om installation

-   **Kun test.** Azure-ensartet lageret i fremstilling miljøer, der bruger den aktuelle udgave af Microsoft Azure stak Technical Preview kan ikke installere. Denne version er meningen, kun til evalueringsformål i et testmiljø.

-   **Ydeevnen**. Microsoft Azure stak Technical Preview version af Azure-ensartet lagerplads er ikke fuldt optimeret for ydeevnen.

-   **Skalerbarhed.** Microsoft Azure stak Technical Preview version af Azure-ensartet lagerplads er ikke optimeret til skalerbarhed.

## <a name="version-support-considerations"></a>Overvejelser i forbindelse med version support

Følgende versioner understøttes i denne preview version af Azure-ensartet lagerplads:

> Azure-lager klientbibliotek: [Microsoft Azure-lager 6 .NET SDK](http://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)
>
> Azure lagerplads datatjenester: [2015-04-05 REST-API version](https://msdn.microsoft.com/library/azure/mt705637.aspx)
>
> Azure lagerplads management services: [2015-05-01-preview](https://msdn.microsoft.com/library/azure/mt163683.aspx)
> [2015-06-15](https://msdn.microsoft.com/library/azure/mt163683.aspx)
## <a name="next-steps"></a>Næste trin

-   [Introduktion til Azure-ensartet lagerplads](azure-stack-storage-overview.md)
