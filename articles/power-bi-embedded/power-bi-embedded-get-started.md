<properties
   pageTitle="Introduktion til Microsoft Power BI integreret"
   description="Power BI integreret, tilføje interaktive Power BI-rapporter i business intelligence-programmer"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="get-started-with-microsoft-power-bi-embedded"></a>Introduktion til Microsoft Power BI integreret

**Power BI integrerede** er en Azure tjeneste, som gør det muligt for udviklere tilføje interaktive Power BI-rapporter i deres egne programmer. **Power BI integrerede** fungerer sammen med eksisterende programmer uden brug af nyt design eller ændre måden, hvorpå brugere logger på.

Ressourcer til **Microsoft Power BI integrerede** er klargjort via [Azure ARM API'er](https://msdn.microsoft.com/library/mt712306.aspx). I dette tilfælde er ressourcen du klargøre en **Power BI arbejdsområde af websteder**.

![](media\power-bi-embedded-get-started\introduction.png)

## <a name="create-a-workspace-collection"></a>Oprette en arbejdsområde af websteder
En **Samling af arbejdsområdet** er på øverste niveau Azure ressourcen og en objektbeholder til det indhold, der vil blive integreret i dit program. En **Samling af arbejdsområde** kan oprettes på to måder:

   -    Manuelt ved hjælp af portalen Azure
   -    Automatisk med Azure ressource Manager(ARM) API'er

Lad os gennemgå trinnene til at opbygge en **Arbejdsområde af websteder** ved hjælp af portalen Azure.

   1.   Åbn, og log på **Portalen til Azure**: [http://portal.azure.com](http://portal.azure.com).

   2.   Klik på **+ Ny** på panelet øverste.

       ![](media\power-bi-embedded-get-started\create-workspace-1.png)

   3.   Klik på **Power BI integrerede**under **Data + analyser** .
   4.   Angiv de nødvendige oplysninger på **Oprettelse af Blade**. Se [Power BI integrerede priser](http://go.microsoft.com/fwlink/?LinkID=760527)for **priser**.

       ![](media\power-bi-embedded-get-started\create-workspace-2.png)

   5. Klik på **Opret**.

**Arbejdsområde samling** tage et øjeblik at blive klargjort. Når fuldført, kan du blive ført til **Arbejdsområdet samling Blade**.

   ![](media\power-bi-embedded-get-started\create-workspace-3.png)

**Oprettelse af Blade** indeholder de oplysninger, du har brug for til at ringe til de API'er, oprette arbejdsområder og installere indhold på dem.

<a name="view-access-keys"/>
## <a name="view-power-bi-api-access-keys"></a>Vis Power BI API-adgang nøgler

En af de vigtigste dele af oplysninger, der bruges til at ringe til Power BI REST API'er er **Access-taster**. Disse bruges til at generere **app tokens** , der bruges til at godkende dine API anmodninger. Klik på **Access-taster** på **Indstillinger for Blade**for at få vist dine **Access-taster**. Yderligere oplysninger om **app tokens**, i afsnittet [Authenticating og godkende med Power BI integreret](power-bi-embedded-app-token-flow.md).

   ![](media\power-bi-embedded-get-started\access-keys.png)

Du får ' meddelelse om, at du har to nøgler.

   ![](media\power-bi-embedded-get-started\access-keys-2.png)

Kopiér disse taster og gemme dem sikkert i programmet. Det er meget vigtigt, at du behandle disse taster, som du ville gøre en adgangskode, fordi de skal give adgang til alt indhold i **Arbejdsområdet af websteder**.

Mens to nøgler er angivet, bruges kun én tast på et bestemt tidspunkt. Den anden nøgle findes, så du kan med jævne mellemrum genoprette taster uden at forstyrre adgang til tjenesten.

Nu hvor du har en forekomst af Power BI til programmet og **Access-taster**, kan du importere en rapport til din egen app. Før du lære at importere en rapport, beskrives i næste afsnit oprettelse af Power BI-datasæt og rapporter for at integrere i en app.

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app"></a>Oprette Power BI-datasæt og rapporter for at integrere i en app

Nu, hvor du har oprettet en forekomst af Power BI for dit program, og har **Access-taster**, skal du oprette Power BI-datasæt og rapporter, som du vil integrere. Datasæt og rapporter kan oprettes ved hjælp af **Power BI Desktop**. Du kan hente [Power BI Desktop for ledig](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/). Eller du hurtigt kommer i gang, kan du hente den [detailsalg analyse eksempel PBIX] (http://go.microsoft.com/fwlink/?LinkID=780547). Hvis du vil vide mere om, hvordan du bruger **Power BI Desktop**, skal du se [Introduktion til Power BI Desktop](https://powerbi.microsoft.com/en-us/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

Med **Power BI Desktop**, du opretter forbindelse til datakilden ved at importere en kopi af dataene i **Power BI Desktop** eller oprette forbindelse til datakilden ved hjælp af **DirectQuery**direkte.

Her er forskelle mellem at bruge **Import** og **DirectQuery**.

|Importér | DirectQuery
|---|---
|-Tabeller, kolonner *og data* er importeret eller kopieres til **Power BI Desktop**. Når du arbejder med visuelle effekter, forespørger **Power BI Desktop** en kopi af dataene. Hvis du vil have vist eventuelle ændringer, der er indtruffet de underliggende data, skal du opdatere eller importere, fuldført, aktuelle database igen.|Kun *tabeller og kolonner* er importeret eller kopieres til **Power BI Desktop**. Når du arbejder med visuelle effekter, forespørger **Power BI Desktop** den underliggende datakilde, hvilket betyder, at du besøger altid aktuelle data.

Få mere at vide om at oprette forbindelse til en datakilde, skal du se [forbinde til en datakilde](power-bi-embedded-connect-datasource.md).

Når du gemmer dit arbejde i **Power BI Desktop**, oprettes en PBIX fil. Denne fil indeholder rapporten. Desuden, hvis du importerer data på PBIX indeholder fuldført datasættet eller hvis du bruger **DirectQuery**, PBIX blot et datasæt skema. Du installerer fra et program i PBIX i arbejdsområdet ved hjælp af [Power BI Importér API](https://msdn.microsoft.com/library/mt711504.aspx).

> [AZURE.NOTE] **Power BI integrerede** har flere API'er til at ændre den server og database, dit datasæt, der peger mod og angive en tjeneste konto legitimationsoplysninger, datasættet vil bruge til at oprette forbindelse til databasen. Se [indlæg SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) og [programrettelse Gateway datakilde](https://msdn.microsoft.com/library/mt711498.aspx).

## <a name="next-steps"></a>Næste trin
Du har oprettet en arbejdsområde af websteder og dit første rapport og datasæt i de forrige trin. Nu er det tid til at se, hvordan du skriver kode til **Power BI integreret**. For at hjælpe dig med at komme i gang, vi oprettede et eksempel online: [Introduktion til eksemplet](power-bi-embedded-get-started-sample.md). Eksemplet kan du se hvordan til:

  - Klargøring indhold
      - Oprette et arbejdsområde
      - Importere en PBIX-fil
      - Opdater forbindelsesstrenge og angive legitimationsoplysninger for din datasæt.

  - Sikker integrere en rapport

## <a name="see-also"></a>Se også
- [Komme i gang med eksempel](power-bi-embedded-get-started-sample.md)
- [Kontrollere og godkende med Power BI integreret](power-bi-embedded-app-token-flow.md)
- [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
