<properties
    pageTitle="Vælge mellem Flow, logik Apps, funktioner og WebJobs | Microsoft Azure"
    description="Sammenligne den til skyen integration tjenester fra Microsoft, og beslutte, hvilke tjenester, du skal bruge."
    services="functions,app-service\logic"
    documentationCenter="na"
    authors="cephalin"
    manager="wpickett"
    tags=""
    keywords="Microsoft flow flow logik apps, og azure-funktioner, funktioner, azure webjobs, webjobs, begivenhed behandling, dynamisk Beregn, ikke-serverbaseret arkitektur"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="09/08/2016"
    ms.author="chrande; glenga"/>

# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Vælge mellem Flow, logik Apps, funktioner og WebJobs

I denne artikel sammenlignes og står i kontrast i skyen Microsoft, som kan alle løse problemer med at integration og automatisering af forretningsprocesser følgende tjenester:

- [Microsoft Flow](https://flow.microsoft.com/)
- [Azure logik Apps](https://azure.microsoft.com/services/logic-apps/)
- [Azure-funktioner](https://azure.microsoft.com/services/functions/)
- [Azure App Service WebJobs](../app-service-web/web-sites-create-web-jobs.md)

Alle disse tjenester er praktiske, når "fastklæbe" sammen forskellige systemer. De kan alle definerer, input, handlinger, betingelser og output. Du kan køre hver af dem på en tidsplan eller udløser. Dog hver tjeneste tilføjer et entydigt sæt af værdi og sammenligne dem ikke er et spørgsmål i "hvilken tjeneste der er mest"? men et af "hvilken tjeneste er bedst egnet til denne situation?" En kombination af disse tjenester er ofte den bedste måde til hurtigt at opbygge en SVG, fuld udvalgt integrationsløsning.

<a name="flow"></a>
## <a name="flow-vs-logic-apps"></a>Flow kontra logik Apps

Vi kan diskutere Microsoft Flow og Azure logik Apps sammen fordi de er begge *konfiguration første* integrationsservices, som gør det nemt at opbygge processer og arbejdsprocesser og integrere med forskellige SaaS og enterprise-programmer. 

- Flow er bygget oven på logik Apps
- De har samme workflow designer
- [Forbindelser](../connectors/apis-list.md) , der arbejder i den ene kan også arbejde i den anden

Flyder gør det muligt for en hvilken som helst office arbejder kan udføre simple integrationer (fx få SMS til vigtige mails) uden at gå gennem udviklere eller IT. På den anden side kan logik Apps aktivere avancerede eller vigtige integrationer (fx B2B processer) hvor enterprise-niveau DevOps og sikkerhed fremgangsmåder er påkrævet. Det er typisk for en arbejdsproces for business til at opnå vækst i kompleksitet overarbejde. Du kan derfor starte med et flow ved første og derefter konvertere den til en logik app efter behov.

Tabellen nedenfor hjælper dig med at afgøre, om forløb eller logik Apps er bedst for en given integration.

|               | Flow                                                                             | Logik Apps                                                                                          |
|---------------|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| Målgruppe      | Office-medarbejdere, business-brugere                                                   | IT-fagfolk, udviklere                                                                                 |
| Scenarier     | Selvbetjening                                                                     | Vigtige                                                                                    |
| Værktøj til design af   | I-webbrowser, kun brugergrænseflade                                                              | I browseren og [Visual Studio](../app-service/logic/app-service-logic-deploy-from-vs.md), [kodevisning](../app-service-logic/app-service-logic-author-definitions.md) tilgængelige |
| DevOps        | Ad hoc-, udvikle fremstilling                                                    | versionsstyring, test, support, og automatisering og administration i [Azure ressourcestyring](../app-service-logic/app-service-logic-arm-provision.md)|
| Administrator-oplevelse| [https://flow.Microsoft.com](https://flow.microsoft.com)                       | [https://Portal.Azure.com](https://portal.azure.com)                                                |
| Sikkerhed      | Standard praksis: [datasuverænitet](https://wikipedia.org/wiki/Technological_Sovereignty), [kryptering på resten](https://wikipedia.org/wiki/Data_at_rest#Encryption) for følsomme data osv. | Sikkerhed assurance af Azure: [Azure sikkerhed](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Sikkerhedscenter](https://azure.microsoft.com/services/security-center/), [overvågningslogge](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/)og mere. |

<a name="function"></a>
## <a name="functions-vs-webjobs"></a>Funktioner kontra WebJobs

Vi kan diskutere Azure funktioner og Azure App Service WebJobs sammen fordi de er begge *kode første* integrationsservices og udviklet for udviklere. De gør det muligt at køre et script eller en del af en kode som svar på forskellige hændelser, som [Nye lagerplads BLOB](functions-bindings-storage.md) eller [en WebHook anmode om](functions-bindings-http-webhook.md). Her er deres ligheder: 

- Begge er bygget på [Azure App Service](../app-service/app-service-value-prop-what-is.md) og få glæde af funktioner som [versionsstyring](../app-service-web/app-service-continuous-deployment.md), [godkendelse](../app-service/app-service-authentication-overview.md)og [overvågning](../app-service-web/web-sites-monitor.md).
- Begge er henvender sig til udviklere tjenester.
- Både understøtter standard scripting og programmeringssprog.
- Begge har NuGet og NPM understøtter.

Funktioner er neutralt udviklingen af WebJobs, det tager de bedste ting ved WebJobs og forbedrer dem. Forbedringerne omfatter: 

- Strømlinet Udviklingscenter test, og køre kode direkte i browseren.
- Indbyggede integration med mere Azure og 3 tredjepart tjenester som [GitHub WebHooks](https://developer.github.com/webhooks/creating/).
- Løn per-brug, ingen grund til at købe en [App Service planlægge](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
- Automatisk, [dynamisk skalering](functions-scale.md).
- For eksisterende kunder App-tjenesten, kører på App-serviceaftale stadig mulige (for at kunne udnytte henhold anvendes ressourcer).
- Integration med logik Apps.

Den følgende tabel opsummerer forskellene mellem funktioner og WebJobs:

|                        | Funktioner                                                                                                                                                                | WebJobs                            |
|------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------|
| Skalering                | Configurationless skalering                                                                                                                                                | skalere med App-serviceaftale        |
| Priser                | Løn i brug eller en del af App-serviceaftale                                                                                                                                  | En del af App-serviceaftale           |
| Kør-type               | udløses, planlagt (af timer udløser)                                                                                                                                  | udløste, fortløbende, planlagt   |
| Udløser begivenheder         | [timer](functions-bindings-timer.md), [Azure DocumentDB](functions-bindings-documentdb.md) [Azure begivenhed Hubs](functions-bindings-event-hubs) [HTTP/WebHook (GitHub, slæk)](functions-bindings-http-webhook.md), [Azure App Service Mobile-Apps](functions-bindings-mobile-apps.md), [Azure meddelelse Hubs](functions-bindings-notification-hubs.md), [Azure Service Bus](functions-bindings-service-bus.md), og [Azure-lager](articles/functions-bindings-storage.md) | [Azure-lager](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), [Azure Service Bus](websites-dotnet-webjobs-sdk-service-bus.md)         |
| Udvikling i browseren | x                                                                                                                                                                        |                                    |
| Vinduet scripting       | forsøg                                                                                                                                                             | x                                  |
| PowerShell             | forsøg                                                                                                                                                             | x                                  |
| C#                     | x                                                                                                                                                                        | x                                  |
| F#                     | x                                                                                                                                                                        |                                    |
| Fest                   | forsøg                                                                                                                                                             | x                                  |
| PHP                    | forsøg                                                                                                                                                             | x                                  |
| Python                 | forsøg                                                                                                                                                             | x                                  |
| JavaScript             | x                                                                                                                                                                        | x                                  |
| Java                   | forsøg                                                                                                                                                             | x                                  |

Om du vil bruge funktioner eller WebJobs i sidste ende afhænger af hvad du allerede laver med App-tjenesten. Hvis du har en App Service-app, du vil køre kodestykker, og du vil administrere dem sammen i det samme DevOps-miljø, skal du bruge WebJobs. Hvis du vil køre kodestykker til andre Azure tjenester eller endda 3 tredjepart apps, eller hvis du vil administrere dine integration kodestykker uafhængigt af din App Service-apps, eller hvis du vil ringe til din kodestykker fra en logik app, skal du drage fordel af forbedringerne i funktioner.  

<a name="together"></a>
## <a name="flow-logic-apps-and-functions-together"></a>Dataflow, logik Apps og funktioner sammen

Som tidligere nævnt afhænger hvilken tjeneste der er bedst egnet til dig af din situation. 

- Enkel business optimering derefter bruge forløb.
- Hvis scenariet integration for avancerede for Flow, eller du skal DevOps funktioner og sikkerhed overensstemmelse, skal du bruge logik Apps.
- Hvis et trin i scenariet integration kræver meget brugerdefinerede transformation eller specialiserede kode, skriv derefter en funktionen app, og derefter udløse en funktion som en handling i din logik app.

Du kan ringe til en logik app i et flow. Du kan også ringe til en funktion i en logik app og en logik app i en funktion. Integrationen mellem Flow, logik Apps og funktioner fortsætte med at forbedre overtid. Du kan oprette noget i en tjeneste og bruge det i andre tjenester. En investering, du foretager i disse tre teknologier er derfor betale sig.

## <a name="next-steps"></a>Næste trin

Introduktion til hver af tjenesterne ved at oprette din første flow, logik app, funktionen app eller WebJob. Klik på et af følgende links:

- [Introduktion til Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
- [Oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md)
- [Oprette din første Azure-funktion](../azure-functions/functions-create-first-azure-function.md)
- [Installere WebJobs ved hjælp af Visual Studio](../app-service-web/websites-dotnet-deploy-webjobs.md)

Eller du kan få mere at vide om disse integrationsservices med følgende links:

- [Udnytte Azure funktioner og Azure App Service for integrationsmuligheder ved Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
- [Integrationer foretaget Simple af Christian Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
- [Logik Apps Live Webcast](http://aka.ms/logicappslive)
- [Microsoft dataflow ofte stillede spørgsmål](https://flow.microsoft.com/documentation/frequently-asked-questions/)
- [Azure WebJobs dokumentationsressourcer](../app-service-web/websites-webjobs-resources.md)
