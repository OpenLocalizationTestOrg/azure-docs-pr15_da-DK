<properties
   pageTitle="Azure objektbeholder tjenesten objektbeholder administration ved hjælp af internettet Brugergrænsefladen | Microsoft Azure"
   description="Installere beholdere til en Azure objektbeholder tjenesten klyngetjenesten ved hjælp af webdelen Marathon brugergrænseflade."
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, objektbeholdere, Micro-tjenester, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/19/2016"
   ms.author="timlt"/>

# <a name="container-management-through-the-web-ui"></a>Objektbeholder administration ved hjælp af internettet brugergrænseflade

DC/OS indeholder et miljø til implementering og skalering grupperet arbejdsbelastninger, mens du abstracting den underliggende hardware. Oven på DC/OS er der en struktur, der administrerer planlægge og afholde Beregn arbejdsmængder.

Mens strukturer er tilgængelige for mange populære arbejdsbelastninger, som dette dokument beskriver, hvordan du kan oprette og tilpasse objektbeholder-installationer med Marathon. Før du gennemgår eksemplerne skal du en DC/OS klynge, der er konfigureret i Azure objektbeholder tjenesten. Du skal også have remote connectivity til denne klynge. Du kan finde flere oplysninger om disse elementer, i følgende artikler:

- [Installere en Azure objektbeholder tjenesten klynge](container-service-deployment.md)
- [Oprette forbindelse til en Azure objektbeholder tjenesten klynge](container-service-connect.md)

## <a name="explore-the-dcos-ui"></a>Udforske DC/OS brugergrænseflade

Gå til http://localhost/ med en sikker Shell (SSH) tunnel etableret. Dette indlæser DC/OS internettet Brugergrænsefladen og viser oplysninger om klynge, som bruges ressourcer, aktive supportmedarbejdere og tjenester, der kører.

![DC/OS BRUGERGRÆNSEFLADE](media/dcos/dcos2.png)

## <a name="explore-the-marathon-ui"></a>Udforske Marathon brugergrænseflade

For at se Marathon Brugergrænsefladen skal du gå til http://localhost/Marathon. Du kan starte en ny objektbeholder eller et andet program på Azure objektbeholder tjenesten DC/OS klynge fra dette skærmbillede. Du kan også få vist oplysninger om at køre beholdere og -programmer.  

![Marathon brugergrænseflade](media/dcos/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>Installere en formateret som Docker objektbeholder

Klik på knappen **Opret program** , du skal installere en ny objektbeholder ved hjælp af Marathon, og Angiv følgende oplysninger i formularen:

Felt           | Værdi
----------------|-----------
ID              | nginx
Billede af           | nginx
Netværk         | Forbindes
Vært Port       | 80
Protokol        | TCP

![Nyt program brugergrænseflade – Generelt](media/dcos/dcos4.png)

![Nyt program brugergrænseflade – Docker objektbeholder](media/dcos/dcos5.png)

![Nyt program brugergrænseflade - porte og Service Discovery](media/dcos/dcos6.png)

Hvis du vil tilknytte statisk objektbeholder port til en port på bruger, skal du bruge JSON-tilstand. Gør du ved at skifte guiden nyt program til **JSON tilstand** ved hjælp af indstillingen. Angiv følgende under den `portMappings` sektion af programdefinitionen. I dette eksempel binder port 80 af beholderen til port 80 af DC/OS agent. Du kan skifte denne guide af JSON-tilstand, når du har foretaget denne ændring.

```none
"hostPort": 80,
```

![Nyt program brugergrænseflade – port 80 eksempel](media/dcos/dcos13.png)

DC/OS klynge er installeret med sæt af private og offentlige supportmedarbejdere. For klynge skal kunne få adgang til programmer fra internettet, skal du installere programmerne til en offentlig agent. Vælg fanen **valgfrit** i guiden nyt program for at gøre det, og angiv **slave_public** for **Accepteret ressource roller**.

![Nyt program brugergrænseflade – offentlige agent konfiguration](media/dcos/dcos14.png)

Du kan se installation status for objektbeholderen på siden Marathon primære igen.

![Marathon hovedsiden brugergrænseflade – objektbeholder installation status](media/dcos/dcos7.png)

Når du skifter tilbage til webstedet DC/OS Brugergrænsefladen (http://localhost/), får du vist, en opgave (i dette tilfælde en objektbeholder, der er formateret som Docker) kører på DC/OS klynge.

![DC/OS web brugergrænseflade – opgaver, der kører på klyngen](media/dcos/dcos8.png)

Du kan også få vist Klyngenoden, opgaven, der kører på.

![DC/OS web brugergrænseflade – opgave klyngenode](media/dcos/dcos9.png)

## <a name="scale-your-containers"></a>Tilpasse dine objektbeholdere

Du kan bruge Marathon Brugergrænsefladen skalere forekomst antallet af en objektbeholder. Gå til siden **Marathon** gør du ved, Vælg den beholder, du vil skalere, og klik på knappen **skala** . Angiv antallet objektbeholder forekomster, du vil i dialogboksen **Skala programmet** , og vælg **Skala program**.

![Marathon brugergrænseflade – dialogboksen skala program](media/dcos/dcos10.png)

Når handlingen skala er fuldført, vises der flere forekomster af den samme opgave udbrede DC/OS supportmedarbejdere.

![DC/OS web Brugergrænsefladen dashboard – opgave opslag på tværs af supportmedarbejdere](media/dcos/dcos11.png)

![DC/OS web brugergrænseflade – noder](media/dcos/dcos12.png)

## <a name="next-steps"></a>Næste trin

- [Arbejde med DC/OS og Marathon API](container-service-mesos-marathon-rest.md)

Undersøgelse på Azure objektbeholder tjenesten med Mesos

> [AZURE. VIDEO] azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]
