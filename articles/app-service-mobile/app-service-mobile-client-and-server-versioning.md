<properties
  pageTitle="Klient- og SDK versionsstyring i Mobile-Apps og Mobile Services | Azure App Service"
  description="Liste over klient SDK'er og kompatibilitet med server SDK versioner til Mobile-tjenester og Azure Mobile-Apps"
  services="app-service\mobile"
  documentationCenter=""
  authors="adrianhall"
  manager="erikre"
  editor=""/>

<tags
  ms.service="app-service-mobile"
  ms.workload="mobile"
  ms.tgt_pltfrm="mobile-multiple"
  ms.devlang="dotnet"
  ms.topic="article"
  ms.date="10/01/2016"
  ms.author="adrianha"/>

# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Klient- og versionsstyring i Mobile-Apps og Mobile-tjenester

Den seneste version af Azure Mobile Services er funktionen **Mobile-Apps** Azure App-tjenesten.

Mobile-Apps klienten og serveren SDK'er oprindeligt er baseret på dem i Mobile-tjenester, men de er *ikke* kompatibel med hinanden.
Det vil sige, skal du bruge en *Mobile-Apps* klient SDK, med en *Mobile-Apps* server SDK og på samme måde for *Mobile-tjenester*. Denne aftale gennemtvinges via en speciel sidehoved værdi, der bruges af klienten og serveren SDK'er, `ZUMO-API-VERSION`.

Bemærk: Når dette dokument refererer til en *Mobile-tjenester* back-end, den nødvendigvis behøver ikke at være hostes på Mobile-tjenester. Det er nu muligt at overføre en mobil tjeneste til at køre på App Service uden kodeændringer, men tjenesten vil stadig bruger *Mobile-tjenester* SDK versioner.

Hvis du vil vide mere om overførsel til App Service uden kodeændringer skal du se artiklen [overføre en Mobile Service til Azure App-tjenesten].

## <a name="header-specification"></a>Specifikation af sidehoved

Tasten `ZUMO-API-VERSION` kan angives i HTTP sidehoved eller forespørgselsstrengen. Værdien er en versionsstreng i formularen **x.y.z**.

Eksempel:

FÅ https://service.azurewebsites.net/tables/TodoItem

SIDEHOVEDER: ZUMO-API-VERSION: 2.0.0

INDLÆG https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Fravalg af versionskontrol af

Du kan fravælge version fejlkontrol ved at angive en værdi på **true** appen angive **MS_SkipVersionCheck**. Angive dette i din web.config eller i afsnittet programindstillinger i portalen Azure.

> [AZURE.NOTE] Der findes et antal funktionsmåden for funktioner ændres mellem Mobile-tjenester og Mobile-Apps, især i områderne af offlinesynkronisering, godkendelse og pushmeddelelser. Du skal kun fravælge version kontrol efter fuldstændig test for at sikre, at disse funktionsmæssige ændringer ikke bryde din app funktionalitet.

## <a name="summary-of-compatibility-for-all-versions"></a>Oversigt over kompatibilitet for alle versioner

Nedenstående diagram viser kompatibilitet mellem alle typer af klienten og serveren. En back-end er klassificeret som Mobile **tjenester** eller Mobile **Apps** baseret på serveren SDK, der bruges.

|                           | **Mobile-tjenester** Node.js eller .NET | **Mobilapps** Node.js eller .NET |
| ----------                | -----------------------             |   ----------------              |
| [Mobile Services-klienter] | Ok                                  | Fejl\*                         |
| [Mobile-Apps-klienter]     | Fejl\*                             | Ok                              |

\*Dette kan styres ved at angive **MS_SkipVersionCheck**.


<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is http://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>Mobile-tjenester klienten og serveren

Klienten SDK'er i tabellen nedenfor er kompatible med **Mobile-tjenester**.

Bemærk: Mobile-tjenester klienten SDK'er *ikke* sende en sidehoved værdi `ZUMO-API-VERSION`. Hvis tjenesten modtager denne sidehoved eller en forespørgsel strengværdi fejlen returneres, medmindre du eksplicit har valgt ud, som beskrevet ovenfor.

### <a name="MobileServicesClients"></a>*Services* mobilklient SDK'er

| Klient platform                   | Version                                                                   | Version sidehoved værdi |
| -------------------               | ------------------------                                                  | -------------------  |
| Administreret klient (Windows, Xamarin) | [1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) | i/t.                  |
| iOS                               | [2.2.2](http://aka.ms/gc6fex)                                             | i/t.                  |
| Android                           | [2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126)                   | i/t.                  |
| HTML                              | [punkt 1.2.7](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) | i/t.     |

### <a name="mobile-services-server-sdks"></a>Mobile *Services* -server SDK'er

| Server-platformen  | Version                                                                                                        | Accepteret version sidehoved |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET             | [WindowsAzure.MobileServices.Backend.* Version 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) | **Ingen version sidehoved** |
| Node.js          | (kommer snart)                        | **Ingen version sidehoved** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>Funktionsmåden for en back-end Mobile-tjenester

| ZUMO-API-VERSION | Værdien af MS_SkipVersionCheck | Svar |
| ---------------- | ---------------------------- | -------- |
| Ikke angivet    | En hvilken som helst                          | 200 - OK |
| En vilkårlig værdi        | SAND                         | 200 - OK |
| En vilkårlig værdi        | FALSK/ikke angivet          | 400 - forkert anmodning |

## <a name="2.0.0"></a>Azure Mobile-Apps klienten og serveren

### <a name="MobileAppsClients"></a>*Apps* mobilklient SDK'er

Versionskontrol af blev introduceret i følgende versioner af klient-SDK til **Azure Mobile-Apps**:

| Klient platform                   | Version                   | Version sidehoved værdi |
| -------------------               | ------------------------  | -----------------    |
| Administreret klient (Windows, Xamarin) | [2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) | 2.0.0 |
| iOS                               | [3.0.0](http://go.microsoft.com/fwlink/?LinkID=529823) | 2.0.0  |
| Android                           | [3.0.0](http://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) | 3.0.0 |

<!-- TODO: add HTML version when released -->

### <a name="mobile-apps-server-sdks"></a>Mobile *Apps* server SDK'er

Versionskontrol af er medtaget i følge server SDK versioner:

| Server-platformen  | SDK                                                                                                        | Accepteret version sidehoved |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET             | [Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) | 2.0.0 |
| Node.js          | [Azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps)                         | 2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Funktionsmåden for en back-end Mobile-Apps

| ZUMO-API-VERSION | Værdien af MS_SkipVersionCheck | Svar |
| ---------------- | ---------------------------- | -------- |
| x.y.z eller Null    | SAND                         | 200 - OK |
| Null             | FALSK/ikke angivet          | 400 - forkert anmodning |
| 1.x.y            | FALSK/ikke angivet          | 400 - forkert anmodning |
| 2.0.0-2.x.y      | FALSK/ikke angivet          | 200 - OK |
| 3.0.0-3.x.y      | FALSK/ikke angivet          | 400 - forkert anmodning |


## <a name="next-steps"></a>Næste trin

- [Overføre en Mobile Service til Azure App Service]


[Mobile Services-klienter]: #MobileServicesClients
[Mobile-Apps-klienter]: #MobileAppsClients


[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Overføre en Mobile Service til Azure App Service]: app-service-mobile-migrating-from-mobile-services.md

