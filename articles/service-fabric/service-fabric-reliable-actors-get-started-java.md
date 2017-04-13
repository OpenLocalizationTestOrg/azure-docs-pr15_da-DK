<properties
   pageTitle="Introduktion til tjenesten strukturen pålidelig aktører | Microsoft Azure"
   description="Dette selvstudium vejleder dig gennem trinnene til oprettelse af fejlfinding og implementere en enkelt Agent-baseret tjeneste, der bruger tjenesten strukturen pålidelig aktører."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="getting-started-with-reliable-actors"></a>Introduktion til pålidelig aktører

> [AZURE.SELECTOR]
- [C# i Windows](service-fabric-reliable-actors-get-started.md)
- [Java på Linux](service-fabric-reliable-actors-get-started-java.md)

I denne artikel forklares de grundlæggende elementer af Azure Service-strukturen pålidelig aktører og vejleder dig gennem oprettelse og installation af et enkelt program pålidelig Agent i Java.

## <a name="installation-and-setup"></a>Installation og konfiguration
Før du begynder, skal du sørge udviklingsmiljø Service-strukturen, konfigurere på din computer.
Hvis du vil konfigurere det, skal du gå til [komme i gang på Mac](service-fabric-get-started-mac.md) eller [Introduktion på Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Grundlæggende begreber
For at komme i gang med pålidelig aktører, skal du forstår nogle grundlæggende begreber:

 * **Agent tjeneste**. Pålidelige aktører er pakket i pålidelige tjenester, der kan være installeret i tjenesten strukturen for infrastruktur. Agent forekomster er aktiveret i en forekomst af navngivne tjenesten.
 
 * **Agent registrering**. Som med pålidelig Services skal en pålidelig Agent tjeneste være registreret hos tjenesten strukturen runtime. Desuden skal typen Agent være registreret hos Agent runtime.
 
 * **Agent interface**. Grænsefladen Agent bruges til at definere et stærkt indtastede offentlige grænseflade for en agent. I den pålidelige Agent model terminologi definerer grænsefladen Agent typerne meddelelser, som agenten kan forstå og proces. Grænsefladen Agent bruges af andre aktører og klientprogrammer til "send" (asynkront) meddelelser til agenten. Pålidelige aktører kan implementere flere grænseflader.
 
 * **ActorProxy klasse**. Klassen ActorProxy bruges af klientprogrammer til at kalde metoderne fremvises grænsefladen Agent. Klassen ActorProxy indeholder to vigtige funktioner:
    * Navneoversættelse: den er i stand til at finde agenten i klynge (find noden af den klynge, hvor det hostes).
    * Håndtering af fejl: den kan prøv igen metode starter og løse igen Agent placering efter, for eksempel en fejl, der kræver Agent til flyttes til en anden node i klyngen.

Følgende regler, der vedrører Agent grænseflader er værd at nævne:

- Agent interface metoder må ikke være overfyldt.
- Agent interface metoder ikke må have ud, ref eller valgfrie parametre.
- Generisk grænseflader understøttes ikke.

## <a name="create-an-actor-service"></a>Oprette en agent-tjeneste
Starte med at oprette et nyt Service-strukturen program. Tjenesten strukturen SDK for Linux indeholder en Yeoman generator til at levere stilladser for en-strukturen tjenesteprogram med en uden status tjeneste. Start med at køre følgende Yeoman kommando:

```bash
$ yo azuresfjava
```

Følg vejledningen for at oprette en **Pålidelig Agent Service**. Dette selvstudium skal du navngive programmet "HelloWorldActorApplication" og Agent "HelloWorldActor." Den følgende stilladser oprettes:

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="reliable-actors-basic-building-blocks"></a>Pålidelige aktører grundlæggende dokumentkomponenter

De grundlæggende begreber, der er beskrevet tidligere oversætte til grundlæggende grundpillerne i en pålidelig Agent tjeneste.

### <a name="actor-interface"></a>Agent grænseflade

Dette indeholder interfacedefinitionen for agenten. Denne grænseflade definerer den Agent kontrakt, der deles af Agent implementering og de klienter, der kalder Agent, så det giver mening at definere den på et sted, der er adskilt fra Agent implementering og kan deles af flere andre tjenester eller klientprogrammer typisk.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>Agent service 
Dette indeholder Agent implementering og Agent registreringskode. Klassen Agent implementerer grænsefladen Agent. Dette er, hvor din Agent fungerer.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActor.HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends ReliableActor implements HelloWorldActor {
    Logger logger = Logger.getLogger(this.getClass().getName());

    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>Agent registrering

Tjenesten Agent skal være registreret hos en tjenestetype i tjenesten strukturen runtime. I rækkefølge for tjenesten Agent til at køre din Agent forekomster, skal din Agent type også være registreret hos tjenesten Agent. Den `ActorRuntime` registrering metode udfører denne arbejde for aktører.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {
    
    public static void main(String[] args) throws Exception {
        
        try {
            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new ActorServiceImpl(context, actorType, ()-> new HelloWorldActorImpl()), Duration.ofSeconds(10));

            Thread.sleep(Long.MAX_VALUE);
            
        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
    }
}
```

### <a name="test-client"></a>Test klient

Dette er en simpel test-klientprogrammet kan du køre separat fra Service-strukturen program tilladelse til at teste din Agent tjeneste. Dette er et eksempel af hvor ActorProxy kan bruges til at aktivere og kommunikere med Agent forekomster. Det ikke arbejdsprocesaktiviteterne installeres med din tjeneste.

### <a name="the-application"></a>Programmet 

Til sidst skal pakker programmet tjenesten Agent og andre tjenester, som du kan tilføje i fremtiden sammen til installation. Den indeholder *ApplicationManifest.xml* og placere holdere til servicepakke Agent.

## <a name="run-the-application"></a>Køre programmet

Yeoman stilladser indeholder et gradle script for at basere programmet og bash scripts til at installere og fjerne-installere programmet. Hvis du vil køre programmet, oprettes først programmet med gradle:

```bash
$ gradle
```

Dette vil returnere en tjeneste-strukturen programpakke, der kan være installeres ved hjælp af tjenesten strukturen Azure CLI. Scriptet install.sh indeholder de nødvendige Azure CLI kommandoer til at udrulle pakken programmet på computeren. Skal du bare køre scriptet install.sh til at udrulle:

```bask
$ ./install.sh
```
