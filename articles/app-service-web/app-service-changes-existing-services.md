<properties
    pageTitle="Azure App Service og dens indvirkning på eksisterende Azure services"
    description="Forklarer, hvordan den nye Azure App tjeneste og dens funktioner påvirke eksisterende tjenester i Azure."
    services="app-service"
    documentationCenter=""
    authors="yochay"
    manager="nirma"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/12/2016"
    ms.author="yochayk"/>


# <a name="azure-app-service-and-existing-azure-services"></a>Azure-App-tjenesten og eksisterende Azure tjenester

I denne artikel beskrives ændringerne til eksisterende Azure tjenester som en del af at samle flere Azure tjenester til [Azure App Service](https://azure.microsoft.com/services/app-service/), et nyt integreret tilbud.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="overview"></a>Oversigt

[Azure App Service](https://azure.microsoft.com/services/app-service/) er en ny og enkeltstående skybaseret tjeneste, der gør det muligt for udviklere til at oprette web og mobilapps til en hvilken som helst platform og enhver enhed. App Service er en integreret løsning, der er udviklet til at strømline gentagne koder funktioner, du kan integrere med enterprise og SaaS systemer og automatisere forretningsprocesser mens opfylder dine behov for sikkerhed, pålideligheden og skalerbarhed.

App Service samler de følgende eksisterende Azure services - [websteder](https://azure.microsoft.com/services/websites/), [Mobile-tjenester](https://azure.microsoft.com/services/mobile-services/)og [BizTalk-tjenester](https://azure.microsoft.com/services/biztalk-services/) til en enkelt kombinerede tjeneste, under tilføje kraftfulde nye funktioner.  App Service gør det muligt at være vært for følgende typer app:

-   Webapps
-   Mobilapps
-   API Apps
-   Logik Apps

Den følgende tabel beskrives det, hvordan eksisterende Azure services afbildning til App-tjenesten og de tilgængelige inden for den app typer.

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">Eksisterende Azure Service</th>
<th align="left", style="width:10%">Azure App Service</th>
<th align="left", style="width:80%">Hvad er ændret</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Azure websteder</td>
<td align="left">Webapps</td>
<td align="left"><li>App Service er begrænset til ændring af navnet websteder til Web Apps til Azure websteder.
<p><li>Alle dine eksisterende forekomster af websteder er nu Web Apps i App-tjeneste.</p>
<p><li>Du kan få adgang til dine eksisterende websteder via <a href="http://go.microsoft.com/fwlink/?LinkId=529715">Azure-portalen</a>, hvor du kan finde alle dine eksisterende websteder under <em>Web Apps</em>.</p>
<p><li><em>Web vært planlægge</em> er nu <em>App Service planlægge</em>. En <em>App Service planlægge</em> kan være vært for en hvilken som helst app typen af App-tjeneste, såsom Web, mobil, logik eller API apps.</p>
<p><li>Azure-App-tjenesten Web Apps er generelt tilgængelighed.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/web/">Få flere oplysninger om Web Apps</a>.</p></td>
</tr>
<tr class="even">
<td align="left">Azure Mobile-tjenester</td>
<td align="left">Mobilapps</td>
<td align="left"><p><li>Mobile-tjenester stadig være tilgængeligt som en enkeltstående tjeneste, og forbliver fuldt understøttet.</p>
<p><li>Mobile-Apps er af typen app i App-tjenesten, som integrerer alle funktionerne i Mobile-tjenester og meget mere.</p>
<p><li>Det er nemt at <a href="http://go.microsoft.com/fwlink/?LinkID=724279&clcid=0x409">overføre fra Mobile-tjenester til Mobile-Apps</a>.</p>
<p><li>Som en del af App Service få Mobile-Apps nye funktioner ud over Mobile-tjenester, som integration med lokale og SaaS systemer, arrangere pladser, WebJobs, bedre skaleringsindstillinger og meget mere.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/mobile/">Få flere oplysninger om Mobile-Apps</a>.</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">API Apps</td>
<td align="left">
<p><li>API Apps er en ny app typen i App-tjeneste, kan du nemt oprette og bruge API'er i skyen.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/api/">Lær mere om API Apps</a>.</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Logik Apps</td>
<td align="left">
<p><li>Logik Apps er en ny app typen i App-tjeneste, hvor du kan nemt automatisere forretningsproces.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/logic/">Lær mere om logik Apps</a>.</p></td>
</tr>
<tr class="odd">
<td align="left">Azure BizTalk-tjenester</td>
<td align="left">BizTalk-API Apps</td>
<td align="left">
<li><p>BizTalk Services stadig være tilgængeligt som en enkeltstående tjeneste, og forbliver fuldt understøttet.</p>
<li><p>Alle funktionerne i BizTalk Services er integreret i App Service som API Apps aktivere brugere til at udføre programmet virksomhedsintegration og B2B integrationsmuligheder med nogen af app typerne i App-tjeneste</p>
<li><p>Med logik Apps kan du nu automatisere forretningsprocesser ved hjælp af en visuel designoplevelse til at oprette arbejdsprocesser</p></td>
</tr>
</tbody>
</table>

Hvis du vil have mere for at vide, skal du gå til [App Service dokumentation](https://azure.microsoft.com/documentation/services/app-service/).
