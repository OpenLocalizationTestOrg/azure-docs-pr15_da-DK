<properties 
    pageTitle="Windows Phone Silverlight SDK opgradering procedurer" 
    description="Windows Phone Silverlight SDK opgradering procedurer til Azure Mobile aftale"                  
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede"
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-sdk-upgrade-procedures"></a>Windows Phone Silverlight SDK opgradering procedurer

Hvis du har allerede integreret en ældre version af vores SDK i dit program, har du overveje følgende punkter, når du opgraderer SDK.

Du kan være nødvendigt at følge flere procedurer, hvis du ikke har set flere versioner af SDK. Eksempelvis hvis du overfører fra 0.10.1 til 0.11.0, du skal først følge fremgangsmåden "fra 0.9.0 til 0.10.1" derefter fremgangsmåden "fra 0.10.1 til 0.11.0".

##<a name="from-200-to-330"></a>Fra 2.0.0 til 3,3,0

### <a name="test-logs"></a>Test logfiler

Console logfiler, der er oprettet med SDK kan nu være aktiveret/deaktiveret/filtreret. Hvis du vil tilpasse, skal du opdatere egenskaben `EngagementAgent.Instance.TestLogEnabled` til en af den værdi, der er tilgængelige fra den `EngagementTestLogLevel` optælling, for eksempel:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

##<a name="from-111-to-200"></a>Fra 1.1.1 til 2.0.0

Følgende fremgangsmåde beskriver, hvordan til at overføre en SDK-integration fra tjenesten Capptain, som Capptain SAS til en app drevet af Azure Mobile aftale. 

> [Azure.IMPORTANT] Capptain og Mobile aftale er ikke de samme tjenester, og den fremgangsmåde, der er angivet under fremhæves kun Sådan overføres klient-app. Overføre SDK i appen overføres ikke dine data fra Capptain servere til Mobile aftale servere

Hvis du overfører fra en tidligere version, skal du se webstedet Capptain for at overføre til 1.1.1 først og derefter anvende følgende procedure

### <a name="nuget-package"></a>Nuget pakke

Udskift **Capptain.WindowsPhone** ved **MicrosoftAzure.MobileEngagement** Nuget pakke.

### <a name="applying-mobile-engagement"></a>Anvende Mobile aftale

SDK bruges termen `Engagement`. Du vil opdatere dit projekt, så det svarer til denne ændring.

Du skal fjerne din aktuelle Capptain nuget pakke. Overvej, fjernes alle ændringerne i Capptain ressourcer mappe. Hvis du vil beholde filerne, og derefter oprette en kopi af dem.

Installere den nye Microsoft Azure aftale nuget pakke herefter på projektet. Du kan finde den direkte på [Nuget](http://www.nuget.org/packages/MicrosoftAzure.MobileEngagement). Denne handling erstatter alle ressourcefiler, der bruges af aftale og tilføjer nye aftale DLL-filen til dit projekt-referencer.

Du skal rydde projekt-referencer ved at slette Capptain DLL referencer. Hvis du ikke gør det, versionen af Capptain er i konflikt, og der sker fejl.

Hvis du har tilpasset Capptain ressourcer, kopiere din gamle filer indhold, og sæt dem ind i de nye aftale-filer. Vær opmærksom på, at både xaml og cs filer skal opdateres.

Når du har udført disse trin er kun nødvendigt at erstatte gamle Capptain refereres til ved de nye aftale referencer.

1. Alle navneområder Capptain skal opdateres.

    Før overførsel:
    
        using Capptain.Agent;
        using Capptain.Reach;
    
    Efter overflytningen:
    
        using Microsoft.Azure.Engagement;

2. Alle Capptain klasser, der indeholder "Capptain" skal indeholde "Aftale".

    Før overførsel:
    
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
    
    Efter overflytningen:
    
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }

3. For xaml-filerne ændre Capptain navneområde og attributter også.

    Før overførsel:
    
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
    
    Efter overflytningen:
    
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>

4. For de andre ressourcer som Capptain billeder Vær opmærksom på, at de også er blevet omdøbt for at bruge "Aftale".

### <a name="application-id--sdk-key"></a>Program-ID / SDK-tasten

Aftale anvender en forbindelsesstreng. Du ikke behøver at angive et program-ID og en SDK nøgle med Mobile aftale, skal du kun angive en forbindelsesstreng. Du kan konfigurere det på EngagementConfiguration filen.

Aftale konfigurationen kan angives din `Resources\EngagementConfiguration.xml` fil af projektet.

Redigere denne fil til at angive:

-   Dit program forbindelsesstreng mellem mærker `<connectionString>` og `<\connectionString>`.

Hvis du vil angive den på kørselstidspunktet i stedet, kan du ringe til følgende metode før aftale agent initialisering:

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        
        /* Initialize Engagement angent with above configuration. */
        EngagementAgent.Instance.Init(engagementConfiguration);

Forbindelsesstrengen for dit program vises i portalen Azure klassisk.

### <a name="items-name-change"></a>Elementer Navneændring

Alle elementer med navnet *capptain* er blevet udnævnt *aftale*. På samme måde for *Capptain* til *aftale*.

Eksempler på ofte anvendte Capptain elementer:

-   CapptainConfiguration hedder nu EngagementConfiguration
-   CapptainAgent hedder nu EngagementAgent
-   CapptainReach hedder nu EngagementReach
-   CapptainHttpConfig hedder nu EngagementHttpConfig
-   GetCapptainPageName hedder nu GetEngagementPageName

Bemærk, at omdøbe også påvirker tilsidesat metoder.



 
