<properties
   pageTitle="Komme i gang med pålidelig Services | Microsoft Azure"
   description="Introduktion til oprettelse af et Microsoft Azure Service strukturen program med uden status og med høj sikkerhed."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="vturecek"/>

# <a name="get-started-with-reliable-services"></a>Komme i gang med pålidelig tjenester

> [AZURE.SELECTOR]
- [C# i Windows](service-fabric-reliable-services-quick-start.md)
- [Java på Linux](service-fabric-reliable-services-quick-start-java.md)

I denne artikel beskrives de grundlæggende regler for Azure Service strukturerede pålidelig Services og fører dig gennem oprettelse og implementere en enkelt pålidelig Service-program, der er skrevet i Java.

## <a name="installation-and-setup"></a>Installation og konfiguration
Før du begynder, skal du sørge udviklingsmiljø Service-strukturen, konfigurere på din computer.
Hvis du vil konfigurere det, skal du gå til [komme i gang på Mac](service-fabric-get-started-mac.md) eller [Introduktion på Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Grundlæggende begreber
For at komme i gang med pålidelig tjenester, skal du forstår nogle grundlæggende begreber:

 - **Tjenestetype**: Dette er implementeringen tjeneste. Det er defineret af den klasse, du skriver, der udvider `StatelessService` og eventuelle andre kode eller afhængigheder, bruges sammen med et navn og en versionsnummeret.

 - **Navngivne service forekomst**: for at køre din tjeneste skal du oprette navngivne forekomster af din tjenestetype meget måde, som du opretter objektforekomster af typen af klasse. Forekomster af tjenesten er faktisk objekt forekomster af tjenesteklasse, du skriver. 

 - **Service host**: forekomster af de navngivne tjenesten du opretter skal køre i en vært. Tjenesteværten for er bare en proces, hvor forekomster af din tjeneste kan køre.

 - **Tjenesten registrering**: registrering samler alt. Typen skal være registreret hos runtime Service-strukturen i en tjenestevært tillade Service-strukturen til at oprette forekomster af det til at køre.  

## <a name="create-a-stateless-service"></a>Oprette en uden status tjeneste

Starte med at oprette et nyt Service-strukturen program. Tjenesten strukturen SDK for Linux indeholder en Yeoman generator til at levere stilladser for en-strukturen tjenesteprogram med en uden status tjeneste. Start med at køre følgende Yeoman kommando:

```bash
$ yo azuresfjava
```

Følg vejledningen for at oprette en **Pålidelig uden status Service**. Dette selvstudium skal du navngive programmet "HelloWorldApplication" og "HelloWorld"-tjenesten. Resultatet vil indeholde mapper til den `HelloWorldApplication` og `HelloWorld`.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="implement-the-service"></a>Implementere tjenesten

Åbn **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Denne klasse definerer typen, og den kan køre vilkårlig kode. Service API indeholder to indgangspunkter til din kode:

 - Et åbent element punkt metode, kaldet `runAsync()`, hvor du kan begynde at afholde alle arbejdsbelastninger, herunder længerevarende Beregn arbejdsmængder.

```java
@Override
protected CompletableFuture<?> runAsync() {
    ...
}
```

 - Et indgangspunkt, hvor du kan Tilslut din kommunikation stak af valg som kommunikation. Dette er, hvor du kan starte modtage anmodninger fra brugere og andre tjenester.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

I dette selvstudium Vi fokuserer på den `runAsync()` posten punkt metode. Dette er, hvor du kan straks starte kører din kode.

### <a name="runasync"></a>RunAsync

Platformen kalder denne metode, når en forekomst af en tjeneste indsatte og er klar til at udføre. Åbn/luk livscyklussen for en forekomst af tjenesten kan forekomme mange gange over levetiden for tjenesten, som helhed. Dette kan ske af forskellige årsager, herunder:

- Systemet flytter din forekomster af tjenesten for justering af ressource.
- Fejl opstår i din kode.
- I programmet eller systemet er opgraderet.
- Den underliggende hardware har en afbrydelse.

Denne organisering administreres ved Service-strukturen til at holde din tjeneste, høj tilgængelighed og ikke-opgjorte korrekt.

#### <a name="cancellation"></a>Annullering

Det er vigtigt, som din kode i `runAsync()` kan stoppe udførelse af når besked fra tjenesten struktur. Den `CompletableFuture` returneres fra `runAsync()` annulleres, når tjenesten strukturen kræver din tjeneste at stoppe udførelsen. I følgende eksempel viser Sådan håndteres en annullering begivenhed: 

```java
    @Override
    protected CompletableFuture<?> runAsync() {

        CompletableFuture<?> completableFuture = new CompletableFuture<>();
        ExecutorService service = Executors.newFixedThreadPool(1);
        
        Future<?> userTask = service.submit(() -> {
            while (!Thread.currentThread().isInterrupted()) {
                try
                {
                   logger.log(Level.INFO, this.context().serviceName().toString());
                   Thread.sleep(1000);
                }
                catch (InterruptedException ex)
                {
                    logger.log(Level.INFO, this.context().serviceName().toString() + " interrupted. Exiting");
                    return;
                }
            }
         });
 
        completableFuture.handle((r, ex) -> {
            if (ex instanceof CancellationException) {
                userTask.cancel(true);
                service.shutdown();
            }
            return null;
        });
 
        return completableFuture;
   }
``` 

### <a name="service-registration"></a>Tjenesten registrering

Tjenestetyper skal være registreret hos tjenesten strukturen runtime. Tjenesten er defineret i den `ServiceManifest.xml` og tjenesteklasse, der implementerer `StatelessService`. Tjenesten registrering foretages i den primære indgangspunkt i processen. I dette eksempel er den primære proces indgangspunkt `HelloWorldServiceHost.java`:

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    } 
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration: {0}", ex.toString());
        throw ex;
    }
}
```

## <a name="run-the-application"></a>Køre programmet

Yeoman stilladser indeholder et gradle script for at basere programmet og bash scripts til at installere og fjerne-installere programmet. Hvis du vil køre programmet, oprettes først programmet med gradle:

```bash
$ gradle
```

Dette vil returnere en tjeneste-strukturen programpakke, der kan være installeres ved hjælp af tjenesten strukturen Azure CLI. Scriptet install.sh indeholder de nødvendige Azure CLI kommandoer til at udrulle pakken programmet på computeren. Skal du bare køre scriptet install.sh til at udrulle:

```bask
$ ./install.sh
```
