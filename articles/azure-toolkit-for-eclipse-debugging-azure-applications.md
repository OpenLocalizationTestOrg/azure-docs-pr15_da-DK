<properties
    pageTitle="Fejlfinding af Azure programmer i Eklipse"
    description="Få mere at vide om Azure fejlfinding programmer med Azure-værktøjskassen til Eklipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690949.aspx -->

# <a name="debugging-azure-applications-in-eclipse"></a>Fejlfinding af Azure programmer i Eklipse #

Bruger Azure-værktøjskassen til Eklipse, du kan foretage fejlfinding af dine programmer om de kører i Azure eller i Beregn emulatoren, hvis du bruger Windows som dit operativsystem. Følgende billede viser egenskaberne **fejlfinding** dialogboksen bruges til at aktivere ekstern fejlfinding:

![][ic719504]

Dette selvstudium, antages det, du allerede har et program, der er blevet oprettet, og er fortrolig med Beregn emulatoren og implementering til Azure.

Vi bruger programmet fra selvstudium [ved hjælp af biblioteket Azure Service Runtime i JSP][] som udgangspunkt for dette emne. Oprette dette program, hvis du ikke allerede har gjort det, før du fortsætter.

## <a name="to-debug-your-application-while-running-in-azure"></a>Foretage fejlfinding af dit program, mens du kører i Azure ##

>[AZURE.WARNING] Den værktøjskassen aktuelle support til fejlfinding af Java henvender sig primært til installationer, der kører i Azure Beregn emulatoren. Fordi ikke der er sikker fejlfinding forbindelsen, skal du ikke aktivere ekstern fejlfinding i fremstilling installationer. Hvis du har brug at foretage fejlfinding af et program, der kører i Azure skyen, Brug en midlertidig installation, men opdager, at uautoriseret adgang til din sessionen med fejlfinding er muligt, hvis nogen kender IP-adressen på skybaseret miljø, selvom det er en midlertidig installation.

1. Opbygge dit projekt til test i emulatoren: I Eklipses Projektstifinder, højreklik på **MyAzureProject**, klik på **Egenskaber**, skal du klikke på **Azure**, og angiv **bygge** til **installation til skyen**.
1. Genopbygge projektet: Klik på **projekt**i menuen Eklipse og derefter klikke på **Opret alle**.
1. Installere dit program tilladelse til at *arrangere* i Azure.
    >[AZURE.IMPORTANT] Som nævnt ovenfor, anbefales det, at du foretager fejlfinding i Beregn emulatoren i de fleste tilfælde, og derefter foretage fejlfinding i det midlertidige miljø kun, hvis yderligere fejlfinding skal bruges. Det anbefales at fejlfinde ikke i produktionsmiljøet.
1. Når installationen er klar i Azure, kan du hente DNS-navnet for installationen fra [Azure Management Portal][]. En midlertidig installation har et DNS-navn i form af http://*&lt;guid&gt;*. cloudapp.net, hvor * &lt;guid&gt; * er en GUID-værdi, der er tildelt af Azure.
1. Højreklik på **WorkerRole1**i Eklipses Projektstifinder, skal du klikke på **Azure**, og klik derefter på **fejlfinding**.
1. I dialogboksen **Egenskaber for WorkerRole1 fejlfinding** :
    1. Kontrollere **aktivere ekstern fejlfinding for denne rolle.**
    1. **Input slutpunkt til brug**, brug af **fejlfinding (offentlige: 8090, privat: 8090)**.
    1. Sikre, at **Starte JVM i pausetilstand, venter på fejlfindingen forbindelse** ikke er markeret.
        >[AZURE.IMPORTANT] Indstillingen **Start JVM i pausetilstand, venter på fejlfindingen forbindelse** er beregnet til avanceret fejlfinding-scenarier i Beregn emulatoren kun (ikke til skyen installationer). Hvis indstillingen **Start JVM i pausetilstand, venter på fejlfindingen forbindelse** bruges, vil det suspendere serverens startproces, indtil Eklipse fejlfindingsværktøjet er forbundet med JVM. Du kan bruge denne indstilling for en fejlfinding session ved hjælp af Beregn emulatoren, skal du ikke bruge det til en fejlfinding session i en installation i skyen. En server initialisering finder sted på en opgave i Azure Start, og Azure skyen gør ikke slutpunkter for offentligt tilgængelige før Start opgaven er afsluttet. Det vil sige, en startprocessen bliver ikke fuldført Hvis denne indstilling er aktiveret i et skybaseret miljø, da det ikke vil kunne modtage en forbindelse fra en ekstern Eklipse klient.
1. Klik på **Opret fejlfinding konfigurationer**.
1. I dialogboksen **Azure fejlfinding af konfiguration** :
    1. Vælg **MyHelloWorld** projektet for **Java project til at foretage fejlfinding**.
    1. **Konfigurer fejlfinding til**, se **Azure skyen (arrangere)**.
    1. Sørg for **Azure beregne emulator** er markeret.
    1. Angiv DNS-navnet af faseinddelt installationen, men uden de foregående **http://**til **Host**. For eksempel (Brug din GUID i stedet for det GUID, der er vist her): **4e616d65-6f6e-6 d 65-6973-526f62657274.cloudapp.net**
1. Klik på **OK** for at lukke dialogboksen **Azure fejlfinding af konfiguration** .
1. Klik på **OK** for at lukke dialogboksen **Egenskaber for WorkerRole1 fejlfinding** .
1. Hvis du ikke har et pausepunkt allerede konfigureret i index.jsp, kan du konfigurere det:
    1. Udvid **MyHelloWorld**i Eklipses Projektstifinder, udvide **WebContent**, og dobbeltklik på **index.jsp**.
    1. Højreklik på den blå bjælke til venstre for Java-kode i index.jsp, og klikke på **Til/fra pausepunkter**, som vist i følgende: ![][ic551537]
1. Klik på **Kør** , og klik derefter på **Fejlfinding af konfigurationer**i Eklipses-menuen.
1. Udvid **Eksterne Java-program** i den venstre rude i dialogboksen **Foretage fejlfinding af konfigurationer** , Vælg **Azure skyen (WorkerRole1)**, og klik på **fejlfinding**.
1. I din browser skal du køre faseinddelt programmet, **http://***&lt;guid&gt;***.cloudapp.net/MyHelloWorld**, erstatter GUID fra din DNS-navn for * &lt;guid&gt;*. Hvis du bliver bedt om en **Bekræfte perspektiv skifte** dialogboks, skal du klikke på **Ja**. Sessionen fejlfinding bør nu udføre linje af kode, hvor pausepunktet blev angivet.

>[AZURE.NOTE] Hvis du forsøger at starte en remote forbindes fejlfinding af forbindelse til en installation med rolle kører flere forekomster, du kan ikke i øjeblikket styre hvilke forekomst fejlfindingsværktøjet indledningsvis til, som Azure justering af belastning Vælg tilfældigt en forekomst. Når du har forbindelse med forekomsten, dog fortsat du fejlfinding den samme forekomst. Bemærk også, hvis der er et tidsrum uden aktivitet på mere end 4 minutter (for eksempel, når du ikke er længere på et pausepunkt for længe), Azure kan afbryde forbindelsen.

## <a name="debugging-a-specific-role-instance-in-a-multi-instance-deployment"></a>Fejlfinding af en forekomst af rolle i en installation med flere forekomster ##

Når installationen kører i skyen, skal du højst sandsynligt køre den i flere Beregn, eller rolle, forekomster. Dette gør det muligt at drage fordel af Azure 99.95% tilgængelighed GARANTIBRUD samt til at skalere ud af dit program.

I disse scenarier skal du fra en fjernplacering fejlfinding af Java-program i en bestemt rolle forekomst. Men hvis du aktiverer kun et almindelige input slutpunkt til fejlfinding, Azure justering af belastning vil gøre det stort set umuligt for dig at oprette forbindelse fejlfindingsværktøjet til en bestemt rolle forekomst. I stedet opretter den forbindelse du til en rolle-forekomst, som den samler tilfældigt.

Dette er typen scenarie, hvor at drage fordel af forekomst input slutpunkter vil gøre det nemmere for dig at foretage fejlfinding af en forekomst af rolle.

Antag, at du planlægger at køre op til 5 rolle forekomster af din installation. Bruger siden **slutpunkter** for egenskaben i dialogboksen Egenskaber for rolle, oprette en forekomst input slutpunkt og tildele den et område af offentlige porte i stedet for et enkelt portnummer. For eksempel i feltet **offentlige port** input angive **81 85**.

Når du installerer programmet med denne forekomst slutpunkt, tildele et entydigt portnummer Azure fra dette område til hver af de rolle forekomster. For at finde ud af, hvilken forekomst har fået tildelt hvilket portnummer, skal du derefter kan bruge miljøvariablen *InstanceEndpointName***_PUBLICPORT** (hvor *InstanceEndpointName* er det navn, du har tildelt, da du oprettede forekomst slutpunktet) automatisk konfigurerede ved værktøjerne i din installation (for eksempel ved at returnere dens værdi i sidefoden på en webside, så du kan læse den, når du navigerer til det).

Når du ved, hvilken offentlige portnummeret forekomsten har fået tildelt, du kan referere til den i din konfiguration af fejlfinding i Eklipse, ved at anbringe det til værtsnavnet på din tjeneste. Dette vil aktivere Eklipse fejlfindingsværktøjet til at oprette forbindelse til denne bestemte forekomst, og ikke nogen af de andre forekomster.

## <a name="windows-only-to-debug-your-application-while-running-in-the-compute-emulator"></a>Kun Windows: foretage fejlfinding af dit program, mens du kører i Beregn emulatoren ##

>[AZURE.NOTE] Azure emulatoren er kun tilgængelig i Windows. Ignorere dette afsnit, hvis du bruger et operativsystem end Windows. 

1. Opbygge dit projekt til test i emulatoren: I Eklipses Projektstifinder, højreklik på **MyAzureProject**, klik på **Egenskaber**, skal du klikke på **Azure**, og angiv **bygge** at **tester i emulator**.
1. Genopbygge projektet: Klik på **projekt**i menuen Eklipse og derefter klikke på **Opret alle**.
1. Højreklik på **WorkerRole1**i Eklipses Projektstifinder, skal du klikke på **Azure**, og klik derefter på **fejlfinding**.
1. I dialogboksen **Egenskaber for WorkerRole1 fejlfinding** :
    1. Kontrollere **aktivere ekstern fejlfinding for denne rolle.**
    1. Brug standardslutpunktet genereres automatisk af værktøjskassen, er angivet som **fejlfinding (offentlige: 8090, privat: 8090)**for **Input slutpunkt til brug**.
    1. Sikre, at indstillingen **Start JVM i pausetilstand, venter på fejlfindingen forbindelse** ikke er markeret.
        >[AZURE.IMPORTANT] Indstillingen **Start JVM i pausetilstand, venter på fejlfindingen forbindelse** er beregnet til avanceret fejlfinding-scenarier i Beregn emulatoren kun (ikke til skyen installationer). Hvis indstillingen **Start JVM i pausetilstand, venter på fejlfindingen forbindelse** bruges, vil det suspendere serverens startproces, indtil Eklipse fejlfindingsværktøjet er forbundet med JVM. Du kan bruge denne indstilling for en fejlfinding session ved hjælp af Beregn emulatoren, skal du ikke bruge det til en fejlfinding session i en installation i skyen. En server initialisering finder sted på en opgave i Azure Start, og Azure skyen gør ikke slutpunkter for offentligt tilgængelige før Start opgaven er afsluttet. Det vil sige, en startprocessen bliver ikke fuldført Hvis denne indstilling er aktiveret i et skybaseret miljø, da det ikke vil kunne modtage en forbindelse fra en ekstern Eklipse klient.
1. Klik på **Opret fejlfinding konfigurationer**.
1. I dialogboksen **Azure fejlfinding af konfiguration** :
    1. Vælg **MyHelloWorld** projektet for **Java project til at foretage fejlfinding**.
    1. **Konfigurer fejlfinding til**, se **Azure beregne emulator**.
1. Klik på **OK** for at lukke dialogboksen **Azure fejlfinding af konfiguration** .
1. Klik på **OK** for at lukke dialogboksen **Egenskaber for WorkerRole1 fejlfinding** .
1. Afbrydelsespunkt index.jsp:
    1. Udvid **MyHelloWorld**i Eklipses Projektstifinder, udvide **WebContent**, og dobbeltklik på **index.jsp**.
    1. Højreklik på den blå bjælke til venstre for Java-kode i index.jsp, og klikke på **Til/fra pausepunkter**, som vist i følgende: ![][ic551537]

       En pausepunktet er angivet, hvis du ser ikonet pausepunkt i den blå bjælke til venstre for Java-kode.
1. Starte programmet i Beregn emulatoren ved at klikke på knappen **Kør i Azure Emulator** på værktøjslinjen Azure.
1. Klik på **Kør** , og klik derefter på **Fejlfinding af konfigurationer**i Eklipses-menuen.
1. Udvid **Eksterne Java-program** i den venstre rude i dialogboksen **Foretage fejlfinding af konfigurationer** , Vælg **Azure Emulator (WorkerRole1)**, og klik på **fejlfinding**.
1. Når Beregn emulatoren angiver, at dit program kører, i din browser skal du køre **http://localhost:8080/MyHelloWorld**.
    Hvis du bliver bedt om en **Bekræfte perspektiv skifte** dialogboks, skal du klikke på **Ja**.
    Sessionen fejlfinding bør nu udføre linje af kode, hvor pausepunktet blev angivet.

Dette viste du, hvordan du udfører fejlfinding i Beregn emulatoren; næste afsnit viser, hvordan du fejlfinding af et program, der er installeret på Azure.

## <a name="debugging-notes"></a>Fejlfinding af noter ##

* Efter fejlfinding, kan du skifte perspektiv fra **fejlfinding** til **Java** via menuen Eklipses, ved at klikke på **vinduet**, **Åbn perspektiv**, og vælge det perspektiv, som du vil bruge.
* Hvis du vil aktivere ekstern fejlfinding i GlassFish, Brug ikke remote fejlfinding funktionen til konfiguration af Azure-værktøjskassen til Eklipse. Konfigurere i stedet GlassFish manuelt. På grund af den måde, GlassFish behandler Java indstillinger foruddefinerede i miljøvariabler, fungerer i værktøjskassen remote fejlfinding konfigurationsfunktion ikke korrekt med GlassFish. Hvis den værktøjskassen remote fejlfinding konfigurationsfunktion er aktiveret, kan du forhindre GlassFish i at starte.

## <a name="see-also"></a>Se også ##

[Azure-værktøjskassen til Eklipse][]

[Oprette et Hej verden program til Azure i Eklipse][]

[Installation af Azure værktøjerne til Eklipse][] 

Du kan finde flere oplysninger om brug af Azure med Java, [Azure Java Developer Center][].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management-portalen]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure-værktøjskassen til Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Oprette et Hej verden program til Azure i Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installation af Azure værktøjerne til Eklipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Brug af biblioteket Azure Service Runtime i JSP]: http://go.microsoft.com/fwlink/?LinkID=699551

<!-- IMG List -->

[ic719504]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic719504.png
[ic551537]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic551537.png
