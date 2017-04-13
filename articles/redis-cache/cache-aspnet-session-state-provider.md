<properties
    pageTitle="Cache ASP.NET Session tilstand udbyder | Microsoft Azure"
    description="Lær at gemme ASP.NET Session stat ved hjælp af Azure Redis Cache"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="09/01/2016"
    ms.author="sdanie" />

# <a name="aspnet-session-state-provider-for-azure-redis-cache"></a>ASP.NET Session tilstand-Provider til Azure Redis Cache

Azure Redis Cache giver en session tilstand provider, du kan bruge til at gemme din sessionstilstand i en cache i stedet for i hukommelsen eller i en SQL Server-database. Hvis du vil bruge cachelagring session tilstand udbyder, først konfigurere din cache og derefter konfigurere ASP.NET-program til ved hjælp af pakken Redis Cache Session tilstand NuGet-cache.

Det er ofte ikke praktiske i en reale skyen app til at undgå at gemme andre former for tilstand for en brugersession, men nogle fremgangsmåder påvirke ydeevne og skalerbarhed mere end andre. Hvis du skal gemme stat, er den bedste løsning at beholde mængden tilstand small og gemme den i cookies. Hvis det ikke er muligt, er den næste bedste løsning at bruge ASP.NET sessionstilstand med en udbyder til fordelt, i hukommelsen cache. Værste løsning fra en ydeevne og skalerbarhed synspunkt er at bruge en database sikkerhedskopier session tilstand udbyder. Dette emne indeholder vejledning om ved hjælp af ASP.NET Session tilstand-Provider til Azure Redis Cache. Finde oplysninger om andre session tilstand indstillinger, mappeindstillingerne [ASP.NET Session tilstand](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Gemme ASP.NET sessionstilstand i cachen

Konfigurere en klientprogrammet i Visual Studio ved hjælp af pakken Redis Cache Session tilstand NuGet, højreklik på projektet i **Solution Explorer** , og vælg **Administrer NuGet pakker**.

![Azure Redis Cache administrere NuGet-pakker](./media/cache-aspnet-session-state-provider/redis-cache-manage-nuget-menu.png)

Skriv **RedisSessionStateProvider** i søgetekstfeltet, vælge den fra resultaterne, og klik på **Installer**.

>[AZURE.IMPORTANT] Hvis du bruger funktionen klyngedannelse fra premium niveauet, skal du bruge [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 eller højere eller en undtagelse er opstået. Dette er en seneste ændring; Du kan finde flere oplysninger [v2.0.0 Bryd ændre oplysningerne](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

![Azure Redis Cache Session tilstand udbyder](./media/cache-aspnet-session-state-provider/redis-cache-session-state-provider.png)

Pakken Redis Session tilstand udbyder NuGet har en afhængighed på StackExchange.Redis.StrongName-pakke. Hvis pakken StackExchange.Redis.StrongName ikke findes i dit projekt, der skal installeres. Bemærk, at ud over pakken stærke med navnet StackExchange.Redis.StrongName der også StackExchange.Redis ikke-sikkert navn version. Hvis dit projekt bruger StackExchange.Redis ikke-sikkert navn version skal du fjerne den, enten før eller efter installation af Redis Session tilstand udbyder NuGet-pakken, kan ellers du få navngivning af konflikter i projektet. Du kan finde flere oplysninger om disse pakker, [konfigurere .NET cache klienter](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Pakken NuGet-overførsler og tilføjer samlingen nødvendige referencer og tilføjer følgende tilføjer følgende afsnit i filen web.config, der indeholder den nødvendige konfiguration for ASP.NET-program at bruge Redis Cache Session tilstand Provider.

    <sessionState mode="Custom" customProvider="MySessionStateStore">
        <providers>
        <!--
        <add name="MySessionStateStore"
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
        />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false"/>
        </providers>
    </sessionState>

Afsnittet kommenterede indeholder et eksempel på attributter og eksempler på Indstillinger for hver attribut.

Konfigurere attributterne med værdierne fra din cache blade i Microsoft Azure-portalen, og Konfigurer de andre værdier efter behov. Du kan finde instruktioner til at få adgang til din cache egenskaber, [konfigurere Redis cacheindstillinger](cache-configure.md#configure-redis-cache-settings).

-   **host** – angive din cache slutpunkt.
-   **port** – Brug en ikke-SSL-port eller SSL port, afhængigt af indstillingerne for ssl.
-   **accessKey** – Brug den primære eller sekundære nøgle til din cache.
-   **ssl** -SAND, hvis du vil sikre cache/klienten kommunikation med ssl; Ellers false. Sørg for at angive den korrekte port.
    -   Ikke-SSL-porten er deaktiveret som standard for nye cache. Angiv sand for denne indstilling til at bruge SSL-port. Du kan finde flere oplysninger om aktivering af ikke-SSL-porten, i afsnittet [Access porte](cache-configure.md#access-ports) i emnet [konfigurere en cache](cache-configure.md) .
-   **throwOnError** – SAND, hvis du vil have en undtagelse til at være, i tilfælde af en manglende eller FALSK, hvis du vil handlingen mislykkes automatisk. Du kan søge efter en fejl ved at markere egenskaben statisk Microsoft.Web.Redis.RedisSessionStateProvider.LastException. Standard er sand.
-   **retryTimeoutInMilliseconds** – handlinger, der ikke er forsøgt igen under dette interval, der er angivet i millisekunder. Det første forsøg er placeret efter 20 millisekunder, og derefter forsøg forekommer hvert sekund, indtil intervallet, der retryTimeoutInMilliseconds udløber. Umiddelbart efter dette interval, skal handlingen gentages en sidste gang. Hvis handlingen stadig ikke, er undtagelsen udløst tilbage til kalderen, afhængigt af indstillingen throwOnError. Standardværdien er 0, hvilket betyder, at ingen yderligere forsøg.
-   **databaseId** – angiver, hvilken database, der skal bruges til cachelagre eksportere data. Hvis ikke angives, bruges af standardværdien for 0.
-   **applicationName** -taster er gemt i redis som `{<Application Name>_<Session ID>}_Data`. Dette gør det muligt for flere programmer til at dele den samme nøgle. Denne parameter er valgfri, og hvis du ikke angiver den bruges en standardværdi.
-   **connectionTimeoutInMilliseconds** – denne indstilling giver dig mulighed at tilsidesætte indstillingen connectTimeout i StackExchange.Redis-klienten. Hvis ikke angives, bruges standardindstillingen for connectTimeout 5000. Du kan finde yderligere oplysninger finder [StackExchange.Redis konfiguration model](http://go.microsoft.com/fwlink/?LinkId=398705).
-   **operationTimeoutInMilliseconds** – denne indstilling giver dig mulighed at tilsidesætte indstillingen syncTimeout i StackExchange.Redis-klienten. Hvis ikke angives, bruges standardindstillingen for syncTimeout 1000. Du kan finde yderligere oplysninger finder [StackExchange.Redis konfiguration model](http://go.microsoft.com/fwlink/?LinkId=398705).

Du kan finde flere oplysninger om disse egenskaber, oprindelige blog indlæg meddelelsen [præsentation af ASP.NET Session tilstand for](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)hos Redis.

Glem ikke at kommentere standard InProc session tilstand udbyder sektionen i din web.config.

    <!-- <sessionState mode="InProc"
         customProvider="DefaultSessionProvider">
         <providers>
            <add name="DefaultSessionProvider"
                  type="System.Web.Providers.DefaultSessionStateProvider,
                        System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                        PublicKeyToken=31bf3856ad364e35"
                  connectionStringName="DefaultConnection" />
          </providers>
    </sessionState> -->

Når disse trin udføres, er dit program konfigureret til at bruge Redis Cache Session tilstand udbyder. Når du bruger sessionstilstand i dit program, gemmes den i en Azure Redis Cache forekomst.

>[AZURE.NOTE] Bemærk, at data, der gemmes i cachen skal kunne serialiseres, i modsætning til de data, der kan være gemt i standard i hukommelsen ASP.NET Session tilstand udbyder. Når Session tilstand-Provider til Redis bruges, skal du sikre, at de datatyper, der gemmes i sessionstilstand er serialiserbar.

## <a name="aspnet-session-state-options"></a>ASP.NET Session tilstand indstillinger

- I hukommelse Session tilstand udbyder - gemmer denne provider tilstanden Session i hukommelsen. Fordelen ved at bruge denne provider er det er nemt og hurtigt. Du kan dog skalere Web Apps, hvis du bruger i hukommelse udbyder, fordi den ikke leveres.

- SQL Server-Session tilstanden udbyder - denne provider gemmer tilstanden Session i Sql Server. Hvis du vil fastholde tilstanden Session i en fast lagerplads, skal du bruge denne provider. Du kan tilpasse din online, men ved hjælp af Sql Server for Session skal være en ydeevne indvirkning på din online.

- Distribueret i hukommelse Session tilstand udbyderen som Redis Cache Session tilstand udbyder - denne provider giver dig bedst fra begge verdens. Din online kan have en enkelt, fast og SVG Session tilstand udbyder. Men du skal huske på overvejelser, at denne provider gemmer tilstanden Session i en Cache og din app der skal tage i betragtning alle de egenskaber, der er knyttet når taler med en distribueret i hukommelsescache som midlertidige netværk mislykkede forsøg. Du kan finde bedste fremgangsmåder til brug af Cache [cachelagring vejledning](../best-practices-caching.md) fra Microsoft Patterns og fremgangsmåder [Azure skyen programmet Design og implementering vejledning](https://github.com/mspnp/azure-guidance).

Se [Web Development bedste fremgangsmåder (dokumentkomponent reale skyen Apps med Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices)kan finde flere oplysninger om session stat og andre bedste fremgangsmåder.

## <a name="next-steps"></a>Næste trin

Se [ASP.NET Output Cache-Provider til Azure Redis Cache](cache-aspnet-output-cache-provider.md).
