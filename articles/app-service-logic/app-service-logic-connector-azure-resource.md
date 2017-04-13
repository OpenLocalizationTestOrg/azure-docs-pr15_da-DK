<properties
   pageTitle="Ved hjælp af Azure ressource Connector i logik apps | Microsoft Azure App Service"
   description="Hvordan du kan oprette og konfigurere app'en Azure ressource forbindelse eller API og bruge det i en logik app i Azure App Service"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="stepsic-microsoft-com"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="09/01/2016"
   ms.author="stepsic"/>

# <a name="get-started-with-the-azure-resource-connector-and-add-it-to-your-logic-app"></a>Introduktion til Azure ressource forbindelsen og tildele den til din logik app
>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2014-12-01-skema prøveversionen.

Bruge Azure ressource forbindelsen til at styre Azure ressourcer inde i din logik app.

## <a name="create-the-azure-resource-connector"></a>Oprette forbindelsen Azure ressource
Hvis du vil bruge Azure ressource forbindelse API-app, skal du først oprette en forekomst af den. Dette kan gøres enten indbygget mens du opretter en logik app eller ved at vælge Azure ressource Manager forbindelse API-app fra Azure Marketplace.

For at konfigurere den, har du du brug for at angive op en tjeneste sikkerhedskonto med tilladelser til at gøre alt det du vil gøre i Azure. Alle opkald foretages på vegne af denne Service-konto, som du har konfigureret. Dette gør det muligt at indskrænke forbindelsen, så Brug kun den ønskede det til at gøre, og noget mere.

David Ebbo har skrevet [et godt blogindlæg](http://blog.davidebbo.com/2014/12/azure-service-principal.html) om, hvordan du konfigurerer dette. Følg alle vejledningen der, og få dit **Lejer-ID**, **Klient-ID** og **hemmeligt**. Disse tre felter samt **Abonnement-ID**, er, hvad der kræves til at konfigurere forbindelsen.

## <a name="using-the-azure-resource-connector-in-logic-apps-designer"></a>Ved hjælp af Azure ressource Connector i logik apps designer
### <a name="trigger"></a>Udløser
Der er to udløsere, der understøttes i forbindelsen:

Navn | Beskrivelse
---- | -----------
Indtræffer | Udløser, når en hændelse indtræffer til en ressource i dit abonnement.
Metrisk krydser grænseværdi |  Udløser, når en metrikværdi opfylder en bestemt grænse.

### <a name="action"></a>Handling

På samme måde, kan du angive et stort antal handlinger i abonnementet Azure:

For **ressourcegrupper** kan du:

Navn | Beskrivelse
---- | -----------
Liste over grupper | Liste over alle ressourcegrupperne i abonnement.
Se ressourcegruppe | Få en ressourcegruppe efter dens id.
Oprette ressourcegruppe | Oprette eller opdatere en ressourcegruppe.
Slette ressourcegruppe | Slette en ressourcegruppe.

For **de ressourcer,** kan du:

Navn | Beskrivelse
---- | -----------
Liste over ressourcer | Liste over ressourcer i dit abonnement ved forskellige typer filtre.
Få ressource | Få en enkelt ressource af dens ressource-id.
Oprette eller opdatere ressource | Opret en ressource, eller opdatere en eksisterende ressource. Du skal angive alle egenskaberne for ressourcen.
Ressource-handling |  Udføre andre handlinger for en ressource. Du skal kende navnet på handlingen og de data, som denne handling fjerner (hvis relevant).
Slette ressource | Slette en ressource.

For **Ressource udbydere** kan du:

Navn | Beskrivelse
---- | -----------
Listen ressource udbydere | Liste over alle tilgængelige ressource udbydere i abonnement.

For **Ressource gruppe installationer** kan du:

Navn | Beskrivelse
---- | -----------
Listen installationer | Liste over alle installationer i en ressourcegruppe.
Få installation | Få installation af en skabelon ud fra dets-id.
Oprette installation | Oprette en ny ressource gruppe installation i form af en skabelon.

Om **begivenheder** om ressourcer kan du:

Navn | Beskrivelse
---- | -----------
Få begivenheder | Få begivenheder i et abonnement eller for en ressource.

På **målepunkter** om ressourcer kan du:

Navn | Beskrivelse
---- | -----------
Få målepunkter | Få en metrikværdi for en ressource-id.

## <a name="do-more-with-your-connector"></a>Få mere ud af forbindelsen
Nu hvor forbindelsen er oprettet, kan du tilføje den til et business-flow ved hjælp af en logik app. Se [Hvad er logik apps?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Hvis du vil Introduktion til Azure logik apps før tilmelding til en Azure-konto, skal du gå til [prøve logik app](https://tryappservice.azure.com/?appservice=logic), hvor du straks kan oprette en forbigående starter logik app i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

Få vist Swagger REST-API reference på [forbindelser og API apps Reference](http://go.microsoft.com/fwlink/p/?LinkId=529766).

<!--References -->

<!--Links -->
[Creating a Logic app]: app-service-logic-create-a-logic-app.md
