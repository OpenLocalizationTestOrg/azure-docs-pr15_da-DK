<properties
   pageTitle="Næste trin til tjenesten strukturen project oprettelse | Microsoft Azure"
   description="I denne artikel indeholder links til et sæt core Udviklingsopgaver for tjenesten struktur"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/08/2016"
   ms.author="seanmck"/>

# <a name="your-service-fabric-application-and-next-steps"></a>Tjenesten strukturen programmet og næste trin
Azure Service strukturen programmet er blevet oprettet. I denne artikel beskrives sammensætning af projektet og nogle mulige videre trin.

## <a name="your-application"></a>Dit program
Alle nye programmerne omfatter et programprojekt. Der kan være et eller to flere projekter, afhængigt af typen tjeneste, der er valgt.

### <a name="the-application-project"></a>Programmet projektet
Programmet projektet består af:

- Et sæt af henvisninger til de tjenester, der udgør dit program.

- To publicere profiler (lokal og skyen), som du kan bruge til at vedligeholde indstillinger til at arbejde med forskellige miljøer – som indstillinger, der er relateret til et klynge slutpunkt og om du vil udføre opgradering installationer som standard.

- To parameter programfiler (lokal og skyen), som du kan bruge til at vedligeholde miljø-specifikke programmet konfigurationer, som antallet partitioner til at oprette til en tjeneste.

- En installation script, som du kan bruge til at installere programmet fra kommandolinjen eller som en del af et automatiseret fortløbende integration og implementering pipeline.

- Programmanifestet, som beskriver programmet. Du kan finde manifestet under mappen ApplicationPackageRoot.

### <a name="stateless-service"></a>Uden status service
Når du tilføjer en ny uden status tjeneste, Visual Studio føjer et service projekt til din løsning, der indeholder en type underordnede `StatelessService`. Tjenesten øger en lokal variabel i en tæller.

### <a name="stateful-service"></a>Med høj sikkerhed service
Når du tilføjer en ny med høj sikkerhed tjeneste, Visual Studio føjer et service projekt til din løsning, der indeholder en type underordnede `StatefulService`. Tjenesten øger en tæller i dets `RunAsync` metode og gemmer resultatet i en `ReliableDictionary`.

### <a name="actor-service"></a>Agent service
Når du tilføjer en ny pålidelig Agent, Visual Studio tilføjes to projekter i din løsning: en agent projekt og en brugergrænseflade projekt.

Agent project indeholder metoder til konfiguration og hentning af værdien for en tæller, som er bevaret pålideligt inden for den Agent stat. Interface project indeholder en brugergrænseflade, som andre tjenester kan bruge til at starte agenten.

### <a name="stateless-web-api"></a>Uden status Web API
Uden status Web API projektet indeholder en grundlæggende webtjeneste, der kan bruges til at åbne dit program til eksterne kunder. Du kan finde flere oplysninger om, hvordan se projektet er struktureret, [Service strukturen Web API-tjenester med OWIN selv vært](service-fabric-reliable-services-communication-webapi.md).

### <a name="aspnet-core"></a>ASP.NET core

Service-strukturen SDK indeholder det samme sæt ASP.NET Core skabeloner, som er tilgængelige for enkeltstående ASP.NET Core projekter: Tøm [Web API][aspnet-webapi], og [Webprogram][aspnet-webapp].

## <a name="next-steps"></a>Næste trin
### <a name="create-an-azure-cluster"></a>Oprette en Azure klynge
Service-strukturen SDK indeholder en lokal klynge til udvikling og afprøvning. Hvis du vil oprette en klynge i Azure, se [konfiguration af en tjeneste-strukturen klynge fra portalen Azure][create-cluster-in-portal].

### <a name="try-deploying-to-azure-for-free-with-party-clusters"></a>Prøv at installere til Azure gratis med part klynger

Hvis du vil prøve implementering og administration af programmerne i Azure uden at konfigurere dit eget klynger, kan du bruge den gratis [part klyngetjeneste](http://aka.ms/tryservicefabric).

### <a name="publish-your-application-to-azure"></a>Publicere dit program til Azure
Du kan publicere dit program direkte fra Visual Studio til en Azure klynge. Til at få mere at vide hvordan, se [publicere dit program til Azure][publish-app-to-azure].

### <a name="use-service-fabric-explorer-to-visualize-your-cluster"></a>Brug Service-strukturen Stifinder til at visualisere din klynge
Tjenesten strukturen Explorer tilbyder en nem måde at visualisere din klynge, herunder installerede programmer og fysisk layout. Hvis du vil vide mere, se [visualisere din klynge ved hjælp af tjenesten strukturen Explorer][visualize-with-sfx].

### <a name="version-and-upgrade-your-services"></a>Version og opgradere dine tjenester
Tjenesten strukturen gør det muligt for uafhængige versionsstyring og opgradering af uafhængige services i et program. Hvis du vil vide mere, se [versionsstyring og opgradere dine tjenester][app-upgrade-tutorial].

### <a name="configure-continuous-integration-with-visual-studio-team-services"></a>Konfigurere fortløbende integration med Visual Studio Team Services
Se [konfigurere fortløbende integration med Visual Studio Team Services]for at få mere for at vide, hvordan du kan konfigurere en fortløbende integration proces for dit program Service-strukturen,[ci-with-vso].


<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md
[aspnet-webapi]: https://docs.asp.net/en/latest/tutorials/first-web-api.html
[aspnet-webapp]: https://docs.asp.net/en/latest/tutorials/first-mvc-app/index.html
