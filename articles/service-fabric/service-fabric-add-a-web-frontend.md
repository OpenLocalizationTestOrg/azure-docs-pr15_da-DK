<properties
   pageTitle="Oprette en web front-end for dit program ved hjælp af ASP.NET Core | Microsoft Azure"
   description="Få vist dit Service-strukturen program på internettet ved hjælp af et ASP.NET Core Web API projekt- og mellem service kommunikation via ServiceProxy."
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
   ms.date="08/11/2016"
   ms.author="seanmck"/>


# <a name="build-a-web-service-front-end-for-your-application-using-aspnet-core"></a>Oprette en web service front-end for dit program ved hjælp af ASP.NET Core

Som standard Azure Service strukturerede services, indeholder ikke en offentlig brugergrænseflade på internettet. Hvis du vil vise programmets funktionalitet til HTTP-klienter, skal du oprette en web project til at fungere som et indgangspunkt og derefter kommunikere derfra til dine individuelle tjenester.

I dette selvstudium skal vi vælge hvor vi slap i selvstudiet [oprette dit første program i Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md) og tilføje en webtjeneste foran tjenesten med høj sikkerhed tæller. Hvis du ikke allerede har gjort det, skal du gå tilbage og gennemgå selvstudiet først.

## <a name="add-an-aspnet-core-service-to-your-application"></a>Tilføje en ASP.NET Core-tjeneste til dit program

ASP.NET Core er en lette, på tværs af platforme web development struktur, som du kan bruge til at oprette moderne web Brugergrænsefladen og web API'er. Lad os tilføje et ASP.NET Web API-projekt til vores eksisterende program.

>[AZURE.NOTE] For at fuldføre dette selvstudium, skal du [installere .NET Core 1.0][dotnetcore-install].

1. I Solution Explorer, skal du højreklikke på **tjenester** i programmet projektet og vælge **Tilføj > ny tjeneste strukturen**.

    ![Tilføje en ny tjeneste til et eksisterende program][vs-add-new-service]

2. Vælg **ASP.NET Core** og give den et navn på siden **Opret en tjeneste** .

    ![Vælge en ASP.NET-webtjeneste i dialogboksen ny tjeneste][vs-new-service-dialog]

3. Den næste side indeholder et sæt af ASP.NET Core project-skabeloner. Bemærk, at dette er de samme skabeloner, som du vil se, hvis du har oprettet en ASP.NET Core project uden for et Service-strukturen til computeren. Dette selvstudium skal vælger vi **Web API**. Du kan dog anvende de samme begreber til at lave en fuld webprogram.

    ![Vælge ASP.NET projekttype][vs-new-aspnet-project-dialog]

    Når projektet Web API er oprettet, har du to tjenester i programmet. Når du fortsætter med at opbygge dit program, skal du føje flere tjenester på nøjagtigt samme måde. Hver kan være bekræftet af uafhængig versionsnummer og opgraderet.

>[AZURE.TIP] Hvis du vil vide mere om at bygge ASP.NET Core services, skal du se [ASP.NET Core dokumentation](https://docs.asp.net).

## <a name="run-the-application"></a>Køre programmet

For at få en ide om, hvad vi har udført, Lad os installere det nye program og se nærmere på standardfunktionsmåden, som indeholder et ASP.NET Core Web API-skabelon.

1. Tryk på F5 i Visual Studio til at foretage fejlfinding af appen.

2. Når installationen er fuldført, starter Visual Studio browseren i roden af tjenesten ASP.NET Web API - stil http://localhost:33003. Portnummeret tildeles tilfældigt og kan være forskellige på din computer. Skabelonen ASP.NET Core Web API indeholder ikke standardfunktionsmåden for roden, så du får en fejl i browseren.

3. Tilføje `/api/values` til placeringen i browseren. Dette aktiverer den `Get` metode på ValuesController i skabelonen Web API. Det vil returnere afkrydsningsfeltet standardsvar, som leveres af skabelonen – en JSON-matrix, der indeholder to strenge:

    ![Standardværdier, der returneres fra ASP.NET Core Web API-skabelon][browser-aspnet-template-values]

    Ved slutningen af selvstudiet, der har vi erstattet disse standardværdier med den seneste Tællerværdien fra vores med høj sikkerhed tjeneste.


## <a name="connect-the-services"></a>Oprette forbindelse tjenesterne

Tjenesten strukturen indeholder fuldstændig fleksibilitet i, hvordan du kommunikerer med pålidelig tjenester. I et enkelt program, du skal muligvis tjenester, der er tilgængelige via TCP, andre tjenester, der er tilgængelige via en HTTP REST-API og stadig andre tjenester, der er tilgængelige via internettet sockets. Få baggrundsoplysninger om de tilgængelige indstillinger og kompromiserne til at se [Communicating med tjenester](service-fabric-connect-and-communicate-with-services.md). I dette selvstudium vi vil følge en af enklere fremgangsmåder og bruge den `ServiceProxy` / `ServiceRemotingListener` klasser, der kan bruges i SDK.

I den `ServiceProxy` tilgang (baseret på fjernprocedurekald eller fjernprocedurekald (RPC)), du definerer en grænseflade skal fungere som den offentlige kontrakt til tjenesten. Derefter skal du bruge denne grænseflade til at oprette en proxy-klasse for interaktion med tjenesten.


### <a name="create-the-interface"></a>Oprette grænsefladen

Vi starter ved at oprette grænsefladen skal fungere som kontrakten mellem tjenesten med høj sikkerhed og dens klienter, herunder ASP.NET Core projektet.

1. Højreklik på din løsning i Solution Explorer, og vælg **Tilføj** > **Nyt projekt**.

2. Vælg **Visual C#** -posten i den venstre navigationsrude, og vælg derefter skabelonen **Klassebibliotek** . Sørg for, at versionen af .NET Framework er indstillet til **4.5.2**.

    ![Oprette en grænseflade projekt til din med høj sikkerhed tjeneste][vs-add-class-library-project]

3. I rækkefølge for en grænseflade til bruges af `ServiceProxy`, den skal være afledt af grænsefladen IService. Denne grænseflade er inkluderet i en af Service-strukturen NuGet pakkerne. Føje pakken, højreklik på det nye klasse bibliotek projekt, og vælg **Administrer NuGet pakker**.

4. Søg efter **Microsoft.ServiceFabric.Services** pakken og installere den.

    ![Tilføje tjenester NuGet pakke][vs-services-nuget-package]

5. Oprette en grænseflade i klassebibliotek, med en enkelt metode `GetCountAsync`, og udvide grænsefladen fra IService.

    ```c#
    namespace MyStatefulService.Interfaces
    {
        using Microsoft.ServiceFabric.Services.Remoting;

        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```


### <a name="implement-the-interface-in-your-stateful-service"></a>Implementere grænsefladen i din med høj sikkerhed tjeneste

Nu hvor vi har defineret grænsefladen, har vi brug at implementere i tjenesten med høj sikkerhed.

1. Tilføj en reference til biblioteket klasseprojekt, der indeholder brugergrænsefladen i din med høj sikkerhed tjeneste.

    ![Tilføje en reference til biblioteket klasseprojekt i tjenesten med høj sikkerhed][vs-add-class-library-reference]

2. Find den klasse, der nedarver fra `StatefulService`, såsom `MyStatefulService`, og udvide det til at implementere den `ICounter` interface.

    ```c#
    using MyStatefulService.Interfaces;

    ...

    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. Nu implementere enkelt metode, der er defineret i den `ICounter` -grænsefladen, `GetCountAsync`.

    ```c#
    public async Task<long> GetCountAsync()
    {
      var myDictionary =
        await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```


### <a name="expose-the-stateful-service-using-a-service-remoting-listener"></a>Afsløre ved hjælp af en tjeneste remoting lytter med høj sikkerhed.

Med den `ICounter` brugergrænseflade, der implementeres, er det sidste trin at aktivere tjenesten med høj sikkerhed skal være kan kaldes fra andre tjenester til at åbne en kommunikationskanal. For med høj sikkerhed tjenester Service-strukturen giver en kan tilsidesættes metode kaldet `CreateServiceReplicaListeners`. Med denne metode kan du angive en eller flere kommunikation lyttefunktioner, baseret på typen elementer, du vil aktivere din tjeneste.

>[AZURE.NOTE] Den tilsvarende metode til at åbne en kommunikationskanal til uden status services kaldes `CreateServiceInstanceListeners`.

I dette tilfælde vi erstatter den nuværende `CreateServiceReplicaListeners` metode og angive en forekomst af `ServiceRemotingListener`, hvor der oprettes et RPC-slutpunkt, som er kan kaldes fra klienter via `ServiceProxy`.  

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```


### <a name="use-the-serviceproxy-class-to-interact-with-the-service"></a>Brug klassen ServiceProxy til at interagere med tjenesten

Vores med høj sikkerhed tjenesten er nu klar til at modtage trafik fra andre tjenester. Så alt, forbliver føjer i koden for at kommunikere med dem fra ASP.NET-webtjenesten.

1. I projektet ASP.NET, tilføje en reference til klassebibliotek, der indeholder den `ICounter` interface.

2. Åbn **Configuration Manager**fra menuen **Build** . Du bør se nogenlunde sådan ud:

    ![Configuration manager viser klassebibliotek som AnyCPU][vs-configuration-manager]

    Bemærk, at bibliotek klasseprojekt, **MyStatefulService.Interface**, er konfigureret til at opbygge for alle CPU'EN. Hvis du vil arbejde korrekt med tjenesten strukturen, skal den være eksplicit rettet mod x64. Klik på rullelisten Platform og vælge **Ny**og derefter oprette en x64 platformskonfiguration.

    ![Oprette nye platform til klassebibliotek][vs-create-platform]

3. Føje pakken Microsoft.ServiceFabric.Services til projekt ASP.NET, som du foretog tidligere for klasse bibliotek projektet. Dette giver de `ServiceProxy` klasse.

4. I mappen **enheder** skal du åbne den `ValuesController` klasse. Bemærk, at den `Get` metode returnerer i øjeblikket kun en hårdt kodet strengmatrix af "værdi1" og "værdi2" – som svarer til hvad vi har set tidligere i browseren. Erstat denne implementering med følgende kode:

    ```c#
    using MyStatefulService.Interfaces;
    using Microsoft.ServiceFabric.Services.Remoting.Client;

    ...

    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));

        long count = await counter.GetCountAsync();

        return new string[] { count.ToString() };
    }
    ```

    Den første linje af kode er den nøgle. Hvis du vil oprette ICounter proxy til tjenesten med høj sikkerhed, skal du angive to dele af oplysninger: et program-ID og navnet på tjenesten.

    Du kan bruge partitionering til skala med høj sikkerhed tjenester ved at opdele op deres tilstand i forskellige filsæt, baseret på en tast, som du definerer, som en kunde-ID eller postnummer. I vores trivial programmet har tjenesten med høj sikkerhed kun én partition, så tasten er ligegyldig. En tast, du angiver føre til samme partition. Hvis du vil vide mere om partitionering services, se, [hvordan du partition Service strukturerede pålidelig Services](service-fabric-concepts-partitioning.md).

    Navnet på tjenesten er en URI formular-strukturen: /&lt;application_name&gt;/&lt;navn_på_tjeneste&gt;.

    Med disse to filer oplysninger, kan tjenesten strukturen entydigt identificerer den maskine, anmodninger skal sendes til. Den `ServiceProxy` håndterer klassen også problemfrit den sag, hvor den maskine, der er vært med høj sikkerhed service partition mislykkes, og en anden computer skal opgraderes til at tage placering. Denne fremstilling gør skrive klient-kode til at løse med andre tjenester betydeligt nemmere.

    Når vi har proxyen, vi blot kalde den `GetCountAsync` metode og dens resultat.

5. Tryk på F5 igen for at køre programmet ændret. Som åbne før, Visual Studio automatisk browseren i roden af webprojektet. Tilføje "api/værdier" stien, og du bør se den aktuelle tællerværdi, returneres.

    ![Tællerværdien med høj sikkerhed vises i browseren][browser-aspnet-counter-value]

    Opdatere browseren med jævne mellemrum for at få vist Tællerværdien opdatere.


>[AZURE.WARNING] ASP.NET Core webserveren angivet på den skabelon, kaldet Kestrel, er [understøttes ikke i øjeblikket til håndtering af direkte internettrafik](https://docs.asp.net/en/latest/fundamentals/servers.html#kestrel). Fremstilling scenarier, kan du overveje at vært ASP.NET Core slutpunkterne bag [API Management] [ api-management-landing-page] eller en anden internettet gateway. Bemærk, at Service-strukturen ikke understøttes for installation i IIS.


## <a name="what-about-actors"></a>Hvad med aktører?

Dette selvstudium fokuseret på at tilføje en web front-end, der har kommunikeret med en med høj sikkerhed tjeneste. Dog kan du følge en minder meget om model til at tale med aktører. Det er faktisk lidt nemmere.

Når du opretter en agent projekt, genererer Visual Studio automatisk et interface projekt for dig. Du kan bruge denne grænseflade til at oprette en agent proxy i webprojektet til at kommunikere med agenten. Kommunikationskanalen angives automatisk. Så du ikke behøver at gøre noget, der svarer til oprettelse af en `ServiceRemotingListener` som du gjorde for tjenesten med høj sikkerhed i dette selvstudium.

## <a name="how-web-services-work-on-your-local-cluster"></a>Hvordan webtjenester fungerer på din lokale klynge

Generelt, kan du installere præcis det samme Service-strukturen program til en flere maskiner klynge, du har installeret på din lokale klynge og være meget sikker på, at linket virker som ventet. Dette skyldes, at din lokale klynge er blot en fem-node konfiguration, som er skjult i en enkelt maskine.

Når det drejer sig om web-tjenester, er der dog én vigtige nuance. Når din klynge bagved belastningsjustering, på samme måde som i Azure, skal du sikre dig, at din webtjenester er installeret på hver enkelt computer, da justering af belastning skal blot round robin-trafik på tværs af maskiner. Du kan gøre dette ved at angive den `InstanceCount` for tjenesten til særlige værdien "-1".

Tjenesten kører derimod, når du kører en webtjeneste lokalt, du har brug at sikre, at kun én forekomst af. Ellers kan der opstår konflikter fra flere processer, der lytter på den samme sti og port. Som et resultat web service forekomst antallet skal du vælge "1" til lokale installationer.

For at lære at konfigurere forskellige værdier for andet miljø, skal du se [administrere programmets parametre for flere miljøer](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="next-steps"></a>Næste trin

- [Oprette en klynge i Azure til implementering af dit program til skyen](service-fabric-cluster-creation-via-portal.md)
- [Få mere at vide mere om at kommunikere med tjenester](service-fabric-connect-and-communicate-with-services.md)
- [Få flere oplysninger om partitionering med høj sikkerhed tjenester](service-fabric-concepts-partitioning.md)

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png
[vs-configuration-manager]: ./media/service-fabric-add-a-web-frontend/vs-configuration-manager.png
[vs-create-platform]: ./media/service-fabric-add-a-web-frontend/vs-create-platform.png


<!-- external links -->
[dotnetcore-install]: https://www.microsoft.com/net/core#windows
[api-management-landing-page]: https://azure.microsoft.com/en-us/services/api-management/
