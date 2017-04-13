<properties
   pageTitle="Sådan opgraderes projekter til den aktuelle version af Azure værktøjerne | Microsoft Azure"
   description="Lær, hvordan du opgraderer en Azure projekt i Visual Studio til den aktuelle version af Azure værktøjerne"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="how-to-upgrade-projects-to-the-current-version-of-the-azure-tools-for-visual-studio"></a>Sådan opgraderes projekter til den aktuelle version af Azure Tools til Visual Studio

## <a name="overview"></a>Oversigt

Når du har installeret den aktuelle version af Azure værktøjerne (eller en tidligere version, der er nyere end 1,6), en hvilken som helst projekter, der er oprettet ved hjælp af en Azure værktøjer slip før 1,6 (November 2011) vil blive opgraderet automatisk, når du åbner dem. Hvis du har oprettet projekter ved hjælp af den 1,6 (November 2011) version af disse værktøjer, og du stadig har den version, der er installeret, kan du åbne de pågældende projekter i den tidligere udgave og senere beslutter, om du vil opgradere dem.

## <a name="how-your-project-changes-when-you-upgrade-it"></a>Hvordan projektet ændres, når du opgraderer

Hvis et projekt er opgraderet, eller du angiver, at du vil opgradere det, dit projekt er ændret for at arbejde med aktuelle versioner af bestemte assemblies, og nogle egenskaber er også ændret i dette afsnit beskrives. Hvis dit projekt kræver andre ændringer ikke kompatibelt med den nyere version af værktøjerne, skal du foretage ændringerne manuelt.

- Filen web.config for web roller og app.config-filen til arbejder roller opdateres du henviser til en nyere version af Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitoirTraceListener.dll.

- Microsoft.WindowsAzure.StorageClient.dll, Microsoft.WindowsAzure.Diagnostics.dll og Microsoft.WindowsAzure.ServiceRuntime.dll enheder er opgraderet til de nye versioner.

- Publicer profiler, som blev gemt i Azure projektfilen (.ccproj), flyttes til en separat fil med filtypenavnet .azurePubXml, i undermappen **Publicer** .

- Nogle egenskaber i profilen Publicer er opdateret til at understøtte nye og ændrede funktioner. **AllowUpgrade** erstattes af **DeploymentReplacementMethod** , fordi du kan opdatere en udløst skybaseret tjeneste, samtidig eller trinvist.

- Egenskaben **UseIISExpressByDefault** er tilføjet og indstillet til falsk, så den webserver, der bruges til fejlfinding ikke automatisk skift fra Internet Information Services (IIS) til IIS Express. IIS Express er standardwebserver for projekter, der er oprettet med de nyere versioner af værktøjerne.

- Hvis Azure cachelagring er placeret i en eller flere af dit projekts roller, ændres nogle egenskaber i tjenestekonfiguration (.cscfg-fil) og definitionen af tjenesten (.csdef-fil), når et projekt er opgraderet. Hvis projektet bruger pakken Azure cachelagring NuGet, er projektet opgraderet til den seneste version af pakken. Du skal åbne filen web.config og bekræfte, at konfigurationen af klienten blev vedligeholdes korrekt under opgraderingen. Hvis du har tilføjet referencer til Azure cachelagring klient assemblies uden at bruge NuGet installationspakke, opdateres disse assemblies ikke; Du skal manuelt opdatere disse referencer til de nye versioner.

>[AZURE.IMPORTANT] For F # projekter, skal du manuelt opdatere referencer til Azure assemblies, så de refererer til de nyere versioner af de pågældende assemblies.

### <a name="how-to-upgrade-an-azure-project-to-the-current-release"></a>Sådan opgraderer du et Azure projekt til den aktuelle version

1. Installere den nyeste version af Azure-værktøjer til installation af Visual Studio, som du vil bruge til det opgraderede projekt, og Åbn derefter det projekt, du vil opgradere. Hvis projektet er blevet oprettet med en Azure slip før 1,6 (November 2011), projektet er automatisk opgraderet til den aktuelle version. Hvis projektet er blevet oprettet med November 2011 overgang og pågældende release stadig er installeret, åbnes projektet i denne udgave.

1. I Solution Explorer skal du åbne genvejsmenuen for projektnoden, Vælg **Egenskaber**og derefter vælge fanen **program** i den dialogboks, der vises.

    Fanen **programmet** viser den Værktøjsversion, der er knyttet til projektet. Hvis den aktuelle version af Azure værktøjer vises, vil projektet er allerede blevet opgraderet. Hvis du har installeret en nyere version af værktøjerne end hvad der vises på fanen, vises knappen **opgradere** .

1. Vælg knappen **opgradere** at opgradere et projekt til den aktuelle version af værktøjerne.

1. Opbygge projektet, og derefter adresse fejl som følge af API ændringer. Du kan finde oplysninger om, hvordan du ændrer din kode for den nye version, til bestemte API.
