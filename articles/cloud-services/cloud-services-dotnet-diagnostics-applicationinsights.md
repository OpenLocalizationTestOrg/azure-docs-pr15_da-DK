<properties
   pageTitle="Fejlfinding i forbindelse med Cloud Services ved hjælp af programmet indsigt | Microsoft Azure"
   description="Lær, hvordan du udfører fejlfinding af problemer med tjenesten til skyen ved hjælp af programmet indsigt til proces data fra Azure diagnosticering."
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager="timlt"
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
   ms.author="saurabh" />


# <a name="troubleshoot-cloud-services-using-application-insights"></a>Fejlfinding i forbindelse med Cloud Services ved hjælp af programmet indsigt

Med [Azure SDK 2,8](https://azure.microsoft.com/downloads/) og Azure diagnosticering lokalnummer sende 1,5 du nu dataene Azure diagnosticering til din skytjeneste direkte til programmet indsigt. De forskellige typer af logfiler, der indsamles via Azure diagnosticering herunder programmet logfiler, windows-hændelseslogge ETW logger og tællere i ydeevne kan nu sendes til programmet indsigt og visualized i portalen programmet indsigt brugergrænseflade. Du kan nu få indsigt i logfiler, der kommer fra dit program samt system og infrastruktur niveau dataene kommer fra Azure diagnosticering og målepunkter, når den bruges sammen med programmet indsigt SDK.

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Konfigurere Azure diagnosticering for at sende data til programmet indsigt

Følg disse trin for at konfigurere projektet skyen service for at sende Azure diagnosticering data til programmet indsigt.

1) I Visual Studio Solution Explorer skal du højreklikke på en rolle, og vælg **Egenskaber** for at åbne den rolle designer

![Løsning Explorer rolleegenskaber][1]

2) Markér afkrydsningsfeltet for at **sende diagnosticering data til programmet indsigt** i rolle designer i afsnittet diagnosticering

![Rolle designer sende diagnosticering data til programmet indsigt][2]

3) Vælg den programmet indsigt ressource, du vil sende Azure diagnosticering dataene til i den dialogboks, der vises. Dialogboksen kan du vælge en eksisterende programmet indsigt ressource fra dit abonnement eller manuelt angive en instrumentation nøgle for en ressource programmet indsigt. Hvis du ikke har en eksisterende programmet indsigt ressource kan du oprette på ved at klikke på hyperlinket **Opret en ny ressource** som vil åbne et browservindue på Azure klassisk portalen, hvor du kan oprette en programmet indsigt ressource. Se [oprette en ny programmet indsigt ressource](../application-insights/app-insights-create-new-resource.md) kan finde flere oplysninger om oprettelse af en programmet indsigt ressource

![Vælg programmet indsigt ressource][3]

4) Når du har tilføjet programmet indsigt ressourcen, er tasten instrumentation for den pågældende ressource gemt som en tjeneste denne indstilling med navnet **APPINSIGHTS_INSTRUMENTATIONKEY**. Du kan ændre denne indstilling for hver tjenestekonfiguration eller -miljø ved at vælge en anden konfiguration på rullelisten Service konfiguration ned og angive en ny instrumentation nøgle for konfigurationen.

![Vælg tjenestekonfiguration][4]

Indstillingen **APPINSIGHTS_INSTRUMENTATIONKEY** konfiguration bruges af Visual Studio til at konfigurere filtypenavnet diagnosticering med de korrekte programmet indsigt ressourceoplysninger under udgivelsen. Konfigurationsindstillingen er en praktisk metode til at definere forskellige instrumentation taster til anden tjeneste konfigurationer. Visual Studio skal oversætte indstillingen og indsætte den i offentlige konfigurationen af diagnosticering lokalnummer, når du udgiver. For at forenkle processen med at konfigurere filtypenavnet diagnosticering med PowerShell, pakken output fra Visual Studio også indeholder offentlige konfigurationen XML med den relevante programmet indsigt instrumentation nøgle, der er inkluderet. Offentlige config-filer er oprettet i mappen filtypenavne og følge mønsteret PaaSDiagnostics. <RoleName>. PubConfig.xml. En hvilken som helst PowerShell baseret installationer kan bruge denne mønster tilknytte hver konfiguration til en rolle.

5) Aktivere **Send diagnosticering data til programmet indsigt** konfigureres automatisk Azure diagnosticering for at sende alle tællere i ydeevne og en niveau fejllog, der indsamles af Azure diagnosticering agent til programmet indsigt. Hvis du vil konfigurere yderligere hvilke data der sendes til programmet viden, og derefter skal du manuelt redigere filen *diagnostics.wadcfgx* for hver rolle. Se [Konfigurere Azure diagnosticering til at sende data til programmet indsigt](../azure-diagnostics-configure-applicationinsights.md) mere at vide om opdatering af konfiguration manuelt.

Når Skytjenesten er konfigureret til at sende Azure diagnosticering data til programmet viden, du kan installere det i Azure, som du normalt ville gøre er at sikre, at filtypenavnet Azure diagnosticering aktiveret. Se [publicere en skybaseret tjeneste, ved hjælp af Visual Studio](../vs-azure-tools-publishing-a-cloud-service.md).  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>Få vist Azure diagnosticering data i programmet indsigt
Azure diagnosticering telemetri vises i programmet indsigt ressourcen konfigureret til skybaseret tjeneste.

Følgende er, hvordan forskellige Azure diagnosticering logge typer kort programmet indsigt begreber:  

-  Tællere i ydeevne vises som brugerdefineret målepunkter i programmet indsigt
-  Windows hændelseslogfiler vises som sporinger og brugerdefinerede hændelser i programmet indsigt
-  Program logfiler, ETW logfiler og en hvilken som helst diagnosticering infrastruktur logfiler vises som sporinger i programmet indsigt.

Få vist Azure diagnosticering data i programmet indsigt:

- Brug [målepunkter Stifinder](../application-insights/app-insights-metrics-explorer.md) til at visualisere eventuelle brugerdefinerede ydeevne tællere eller optællinger for de forskellige typer windows hændelseslog begivenheder.

![Brugerdefineret målepunkter i målepunkter Explorer][5]

- Brug [Søg](../application-insights/app-insights-diagnostic-search.md) til at søge på tværs af forskellige sporing loggene sendes af Azure diagnosticering. For eksempel, hvis du har en ikke-afviklet undtagelse i en rolle som forårsagede rollen for at gå ned og Papirkurv oplysningerne skal vises i salgskanalen *programmet* af *Windows-hændelsesloggen*. Du kan bruge søgefunktionen til at se på Windows-hændelsesloggen fejlen og få fuld Stakspor for de undtagelser, så du kan finde den egentlige årsag problemet.

![Søg sporinger][6]

## <a name="next-steps"></a>Næste trin

- [Tilføj programmet indsigt SDK til skybaseret tjeneste](../application-insights/app-insights-cloudservices.md) til at sende data om anmodninger, undtagelser, afhængigheder og eventuelle brugerdefinerede telemetri fra dit program. Kombineret med Azure diagnosticering data, du kan få en komplet oversigt over dit program- og alle i den samme ressource programmet indsigt.  


<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png
