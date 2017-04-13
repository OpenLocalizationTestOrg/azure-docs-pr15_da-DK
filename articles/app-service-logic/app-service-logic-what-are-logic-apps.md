<properties 
    pageTitle="Hvad er logik Apps?" 
    description="Lær mere om App Service logik Apps" 
    authors="kevinlam1" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article" 
    ms.date="10/12/2016"
    ms.author="klam"/>

# <a name="what-are-logic-apps"></a>Hvad er logik Apps?

Logik Apps ikke en metode til at forenkle og implementere SVG integrationer og arbejdsprocesser i skyen. Den indeholder en visuel designer for at modellere og automatisere processen som en række trin kendt som en arbejdsproces.  Der findes [mange forbindelser](../connectors/apis-list.md) på tværs af skyen og lokalt hurtigt integrere på tværs af tjenester og protokoller.  En logik app begynder med en udløser (like 'når der føjes en konto til Dynamics CRM') og efter brænding kan begynde at mange kombinationer handlinger, konverteringer og betingelse logik.

Fordele ved at bruge logik Apps omfatter følgende:  

- Spare tid ved at designe komplekse processer, der bruger nemt at forstå designværktøjer
- Implementere mønstre og arbejdsprocesser problemfrit, ellers ville være svært at implementere i kode
- Komme hurtigt i gang fra skabeloner
- Tilpasse din logik app med dine egne brugerdefinerede API'er, kode og handlinger
- Oprette forbindelse og synkronisere deres forskellige systemer på tværs af i det lokale miljø og skyen
- Opbygge fra BizTalk-server, administration af API, Azure funktioner og Azure Service Bus med førsteklasses integration support

Logik Apps er en fuldt administreret iPaaS (integration Platform som en tjeneste) giver mulighed for udviklere ikke behøver at bekymre dig om at bygge vært, skalerbarhed, tilgængelighed og administration.  Logik Apps skalere automatisk for at imødekomme behov.

![Flow app designer](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

Som nævnt med logik Apps, kan du automatisere forretningsprocesser. Her er et par eksempler:  
 
* Flytte filer, der er overført til en FTP-server til Azure-lager
* Proces og distribuere ordrer på tværs af i det lokale miljø og skyen systemer
* Overvåge alle tweets om et bestemt emne, analysere synspunkt og oprette beskeder og opgaver for elementer, der er brug for opfølgning.

Scenarier som disse kan konfigureres alt fra visual designer og uden at skrive en enkelt linje af kode. Få Introduktion [bygge din logik app nu][create].  Når skrevet - kan en logik app være [hurtigt installeret og konfigureret](app-service-logic-create-deploy-template.md) på tværs af flere miljøer og områder.

## <a name="why-logic-apps"></a>Hvorfor logik Apps?

Logik Apps viser hastighed og skalerbarhed til enterprise-integration plads.  Brugervenlig i designeren, forskellige tilgængelige udløsere og handlinger og effektive værktøjer til administration skal du centralisering din API'er nemmere end nogensinde før.  Når virksomheder flytter mod digitalization, logik Apps giver dig mulighed at oprette forbindelse til ældre og avancerede sammen.

Desuden med vores [Enterprise Integration konto] [ biztalk] skaleres forfaldne integrationsmuligheder med en potens af et [XML-meddelelser][xml], [handel partner administration][tpm], og meget mere.

- **Brugervenlig designværktøjer** - logik Apps kan være designet til en komplet i browseren eller med Visual Studio-værktøjer. Start med en udløser – fra en simpel tidsplan for at oprettelsen af et GitHub problem. Derefter dirigerer et vilkårligt antal handlinger ved hjælp af galleriet omfattende af forbindelser.

- **Forbinde API'er nemt** -lige sammensætning opgaver, der er nemt at beskrive er svære at implementere kode. Logik Apps gør det nemt at oprette forbindelse til forskellige systemer. Vil forbinde dine skyen marketing løsning til dine lokale fakturering system? Vil du samle meddelelser på tværs af API'er og systemer med en Enterprise Service Bus? Logik apps er den hurtigste og mest pålidelige måde til at holde løsninger for disse problemer.

- **Komme i gang hurtigt fra skabeloner** - kan hjælpe dig med at komme i gang vi har fået et [Galleri med skabeloner] [ templates] , der gør det muligt hurtigt at oprette nogle almindelige løsninger. Avancerede B2B løsninger til enkel SaaS connectivity, fra og endda et par, der er 'for sjov' - galleriet er den hurtigste måde at komme i gang med en potens af logik Apps.

- **Udvidelsesmuligheder af bagt-i** - ikke kan se den forbindelse, du har brug for? Logik Apps er udviklet til at arbejde med dine egne API'er og kode; Du kan nemt oprette dine egne API-app Hvis du vil bruge som en brugerdefineret forbindelse, eller ringe til en [Azure-funktionen](https://functions.azure.com) til at udføre kodestykker af kode efter behov. 

- **Reelle integration hestekræfter** - starte nemt og vokse, efterhånden som du har brug for. Logik Apps kan nemt udnytte fordelene ved BizTalk, Microsofts branche foranstillet integrationsløsning til at aktivere integration professionelle til at oprette de løsninger, de skal bruge. Få mere at vide om [Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md).

## <a name="logic-app-concepts"></a>Logik App begreber

Følgende er nogle af de vigtigste enheder, der omfatter logik oplevelsen. 

- **Arbejdsproces** - logik Apps giver en grafisk måde at model forretningsprocesserne som en række trin eller en arbejdsproces.
- **Administrerede forbindelser** - dine logik apps skal have adgang til data og -tjenester. Administrerede forbindelser oprettes specifikt kan være en hjælp, når du er tilsluttet og arbejde med dine data. Se en liste over forbindelser, der er nu tilgængelig i [administrerede forbindelser][managedapis].
- **Udløsere** - nogle administrerede forbindelser også kan fungere som en udløser. En udløser starter en ny forekomst af en arbejdsproces, der er baseret på en bestemt hændelse, som modtagelse af en mail eller en ændring i kontoen Azure-lager.
-  **Handlinger** - hvert trin, når udløser i en arbejdsproces, der kaldes en handling. Hver handling typisk er tilknyttet en handling på din administrerede forbindelse eller brugerdefinerede API-apps.
- **Enterprise Integration Pack** – indeholder funktioner fra BizTalk til mere avancerede integrationsmuligheder, logik Apps. BizTalk er Microsofts branche foranstillet integration platform. Enterprise Integration Pack forbindelserne kan du nemt omfatter validering, transformation og mere i til dine logik App-arbejdsprocesser.

## <a name="getting-started"></a>Kom godt i gang  

- For at komme i gang med logik Apps skal du følge, [oprette en logik App] [ create] selvstudium.  
- [Eksempler på almindelige og -scenarier](app-service-logic-examples-and-scenarios.md)
- [Du kan automatisere forretningsprocesser med logik Apps](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Lær at integrere dine systemer med logik Apps](http://channel9.msdn.com/Events/Build/2016/P462)

[biztalk]: app-service-logic-enterprise-integration-accounts.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: app-service-logic-enterprise-integration-accounts.md
[xml]: app-service-logic-enterprise-integration-b2b.md
[templates]: app-service-logic-use-logic-app-templates.md
