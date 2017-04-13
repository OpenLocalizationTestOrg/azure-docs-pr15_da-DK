<properties
   pageTitle="Azure fungerer oversigt | Microsoft Azure"
   description="Forstå, hvordan du bruger Azure funktioner til at optimere asynkron arbejdsmængder i minutter."
   services="functions"
   documentationCenter="na"
   authors="mattchenderson"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure-funktioner, funktioner, behandling af hændelse, webhooks, dynamisk Beregn, ikke-serverbaseret arkitektur"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/29/2016"
   ms.author="cfowler;mahender;glenga"/>
   
   
# <a name="azure-functions-overview"></a>Oversigt over Azure funktioner

Azure-funktioner er en løsning til nemt kørsel af små kode eller "funktioner" i skyen. Du kan skrive lige den kode, du har brug for til problemet ved hånden, uden at bekymre dig om et hele programmet eller infrastrukturen for at køre den. Det kan gøre udvikling endnu mere produktiv, og du kan bruge din udvikling sprog, som C#, F #, Node.js, Python eller PHP. Betal kun for den tid, der kører i din kode og sikkerhed og rettighedsadministration Azure skalere efter behov.

Dette emne indeholder en overordnet oversigt over Azure-funktioner. Hvis du vil hoppe direkte i og komme i gang med Azure funktioner, skal du starte med [Opret dit første Azure-funktionen](functions-create-first-azure-function.md). Hvis du leder efter flere tekniske oplysninger om funktioner, kan du se [Udviklerreference](functions-reference.md).

## <a name="features"></a>Funktioner

Her er nogle vigtige funktioner i Azure funktioner:
    
* **Valg af sprog** - skrive funktioner ved hjælp af C#, F #, Node.js, Python, PHP, batchen, Fest, Java eller en hvilken som helst eksekverbar fil.
* **Løn per Brug priser model** - løn kun for den tid, der kører din kode. Se indstillingen dynamiske App Service planlægge i [priser sektion](#pricing) nedenfor.  
* **Få dine egne afhængigheder** - funktioner understøtter NuGet og NPM, så du kan bruge dine foretrukne biblioteker.  
* **Integreret sikkerhed** - beskytte HTTP-udløste funktioner med OAuth udbydere som Azure Active Directory, Facebook, Google, Twitter og Microsoft-Account.  
* **Forenklet integration** - nemt udnytte Azure tjenester og software som-en-tjenesten (SaaS) tilbud. Se [integrationer sektion](#integrations) under nogle eksempler.  
* **Fleksible development** - kode din funktioner højre på portalen eller konfigurere fortløbende integration og installere din kode via GitHub, Visual Studio Team Services og andre [understøttede værktøjer til udvikling af](../app-service-web/web-sites-deploy.md#deploy-using-an-ide).  
* **Open source** - feltet funktioner runtime er open source- og [tilgængelig på GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>Hvad kan jeg gøre med funktioner?

Azure-funktioner er en god løsning til behandling af data, integration systemer, arbejde med internet-for-tingene (IoT), og udvikling af enkel API'er og microservices. Overvej funktioner til opgaver som billede eller rækkefølge behandling, filvedligeholdelse, længerevarende opgaver, du vil køre i en baggrundstråd eller for alle opgaver, du vil køre en tidsplan. 

Funktioner indeholder skabeloner til at komme i gang med nøglescenarier, herunder følgende:

* **BlobTrigger** - proces Azure-lager blob, når de føjes til beholdere. Du kan bruge dette til tilpasning af billeder.
* **EventHubTrigger** - svar til begivenheder leveret til en Azure begivenhed Hub. Særligt nyttige i programmet instrumentation, bruger oplevelse eller arbejdsproces behandling og Internet af ting (IoT) scenarier.
* **Generisk webhook** - proces webhook HTTP-anmodninger fra en tjeneste, der understøtter webhooks.
* **GitHub webhook** - svar på hændelser, der forekommer i din GitHub typer lagre. Se et eksempel, [Opret en webhook eller API-funktion](functions-create-a-web-hook-or-api-function.md).
* **HTTPTrigger** - udløser kørsel af din kode ved hjælp af en HTTP-anmodning.
* **QueueTrigger** - svare på meddelelser, når de modtages i kø en Azure-lager. Et eksempel, skal du se [oprette en Azure-funktion, som binder til en Azure service](functions-create-an-azure-connected-function.md).
* **ServiceBusQueueTrigger** - Kontakt din kode til andre Azure tjenester eller lokalt tjenester ved at lytte til meddelelseskøer. 
* **ServiceBusTopicTrigger** - Kontakt din kode til andre Azure tjenester eller lokalt tjenester ved at abonnere på emner. 
* **TimerTrigger** - udføre oprydning eller andre batchopgaver på en foruddefineret tidsplan. Et eksempel, kan du se [oprette en begivenhed, behandler funktionen](functions-create-an-event-processing-function.md).

Azure funktioner understøtter *Udløsere*, som er måder at starte udførelse af din kode, og *bindinger*, som er måder at forenkle koder for input- og outputområder data. Se [Azure funktioner udløsere og bindinger Udviklerreference](functions-triggers-bindings.md)have en detaljeret beskrivelse af de udløsere og bindinger, som indeholder et Azure funktioner.


## <a name="integrations"></a>Integrationer

Azure funktioner integreres med en række forskellige Azure og tjenester 3 tredjepart. Du kan bruge disse til at udløse din funktionen og starte udførelse af eller til at fungere som input og output til din kode. Følgende service integration understøttes af Azure funktioner. 

* Azure DocumentDB
* Azure begivenhed Hubs 
* Azure Mobile-Apps (tabeller)
* Azure meddelelse Hubs
* Azure Service Bus (køer og emner)
* Azure-lager (blob, forespørgsler og tabeller) 
* GitHub (webhooks)
* Lokal (ved hjælp af Service Bus)

## <a name="pricing"></a>Hvor meget koster fungerer omkostninger?

Azure funktioner har to typer af priser planer skal du vælge en, der passer bedst tilnærmer dine behov: 

* **Dynamisk vært plan** - når din funktionen køres, Azure, giver alle de nødvendige computational ressourcer. Du behøver ikke at bekymre dig om Ressourcestyring, og du betaler kun for den tid, der kører i din kode. Fuld prisoplysninger findes på [siden funktioner priser](/pricing/details/functions). 

* **App-serviceaftale** - som køre din funktioner ligesom din web, mobile og API apps. Når du allerede bruger App Service til dine andre programmer, kan du køre din funktioner på den samme plan gratis. Detaljerede oplysninger kan findes på [App servicepriser side](/pricing/details/app-service/).

Se, [hvordan skalere Azure funktioner](functions-scale.md)kan finde flere oplysninger om skalering din funktioner.

##<a name="next-steps"></a>Næste trin

+ [Oprette din første Azure-funktion](functions-create-first-azure-function.md)  
Hoppe direkte i, og oprette din første funktion ved hjælp af Azure funktioner Hurtig start. 
+ [Azure funktioner Udviklerreference](functions-reference.md)  
Indeholder flere tekniske oplysninger om Azure funktioner runtime og en reference til kodningssprog funktioner og definere udløsere og bindinger.
+ [Test Azure funktioner](functions-test-a-function.md)  
I denne artikel beskrives forskellige værktøjer og teknikker til test din funktioner.
+ [Sådan skalere Azure funktioner](functions-scale.md)  
I denne artikel beskrives tjenesteplanerne, der er tilgængelige med Azure-funktioner, herunder den dynamiske serviceaftale, og hvordan du vælger den rigtige plan. 
+ [Lær mere om Azure App Service](../app-service/app-service-value-prop-what-is.md)  
Azure-funktioner, der har Azure App Service platformen til kernefunktioner som installationer, miljøvariabler og diagnosticering. 