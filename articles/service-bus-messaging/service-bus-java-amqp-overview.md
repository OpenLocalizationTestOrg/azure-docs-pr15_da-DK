<properties 
    pageTitle="Oversigt over Service Bus AMQP med Java | Microsoft Azure" 
    description="Få mere at vide om brug af Java med de avancerede meddelelse Queuing Protocol (AMQP) 1.0 i Azure." 
    services="service-bus" 
    documentationCenter="java" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>


# <a name="amqp-10-support-in-service-bus"></a>Understøttelse af AMQP 1.0 i Service Bus

Både Azure Service Bus skybaseret tjeneste og lokalt [Service Bus til Windows Server (Service Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) understøtter de avancerede meddelelse sætter i kø Protocol (AMQP) 1.0. AMQP gør det muligt at opbygge på tværs af platforme, hybrid programmer ved hjælp af en åben standard protokol. Du kan oprette programmer, der bruger komponenter, der er oprettet ved hjælp af forskellige sprog og rammer og, der kører på forskellige operativsystemer. Alle disse komponenter kan oprette forbindelse til tjenesten Bus og problemfrit exchange strukturerede business meddelelser effektivt og på samme måde.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Introduktion: Hvad er AMQP 1.0 og hvorfor er det vigtigt?

Traditionelt har meddelelse rundt middlewareprodukter brugt beskyttede protokoller af kommunikation mellem klientprogrammer og mæglere. Det betyder, at når du har valgt en bestemt leverandør SMS broker, skal du bruge den leverandør biblioteker til forbinde dine klientprogrammer at pågældende broker. Dette resulterer i en graden afhængighed af leverandøren, da konvertere et program til et andet produkt kræver kodeændringer i alle programmer. 

Desuden er forbindelse SMS mæglere fra forskellige leverandører svære. Dette kræver typisk programmet niveau broer til at flytte meddelelser fra et system til en anden og til at oversætte på deres beskyttede meddelelsesformater. Dette er et almindelige krav for eksempel når du skal give en ny samlet grænseflade til ældre forskellige systemer eller integrere IT-systemer efter fusion.

Producenter af software er en hurtige virksomhed. ny programmeringssprog og -programmet rammer introduceres i en nogle gange bewildering tempo. På samme måde, IT-systemer krav udvikler sig med tiden og udviklere vil drage fordel af de nyeste funktioner platform. Dog understøtter nogle gange den valgte SMS leverandør ikke disse platforme. Det er ikke muligt for andre til at levere biblioteker til disse nye platforme, fordi SMS protokoller er beskyttet. Derfor skal du bruge fremgangsmåder som udvikler gateways eller broer, der gør det muligt at fortsætte med at bruge SMS produktet.

Udvikling af de avancerede meddelelse Queuing Protocol (AMQP) 1.0 blev begrundet disse problemer. Den stammer fra JP Morgan Chase, der som mest finansielle tjenester virksomheder, er tunge brugere af meddelelse – et overblik over programmer. Målet var enkle: oprette en SMS Åbn-standard-protokollen, der gør det muligt at opbygge meddelelse-baserede programmer med komponenter, der er oprettet ved hjælp af forskellige sprog, strukturer og operativsystemer, alt med bedste af bedste komponenter fra et område med leverandører.

## <a name="amqp-10-technical-features"></a>AMQP 1.0 tekniske funktioner

AMQP 1.0 er en effektiv, pålidelig og tråd niveau SMS protokol, du kan bruge til at oprette robust, på tværs af platforme, messaging-programmer. Protokollen har en enkelt mål: definere funktionen af sikkert, pålideligt og effektivt overførsel af meddelelser mellem to parter indstillingen. Selve meddelelserne er kodet ved hjælp af en bærbar data repræsentation, som gør det muligt for forskellige afsendere og modtagere til at udveksle strukturerede business meddelelser på samme måde. Følgende er en oversigt over de vigtigste funktioner:

*    **Effektivt**: AMQP 1.0 er en forbindelse rundt protokol, der bruger en binær kodning for protocol vejledningen og meddelelserne, business overført via den. Den indeholder avancerede flow-objekter, der skal maksimere anvendelsen af netværket og forbundne komponenterne. Dog protokollen er udviklet til at finde den rigtige balance mellem effektivitet, fleksibilitet og interoperabilitet.
*    **Pålidelig**: feltet AMQP 1.0-protokollen giver mulighed for meddelelser, der er udvekslet med et udvalg af pålidelighed garantier fra brand og Glem til pålidelige, præcis-én gang bekræftet levering.
*    **Fleksibel**: AMQP 1.0 er en fleksibel protokol, der kan bruges til at understøtte forskellige topologier. Den samme protokol kan bruges til klient-klient, klient-broker og broker-broker kommunikation.
*    **Uafhængige Broker-model**: feltet AMQP 1.0-specifikationen ikke gøre en hvilken som helst krav på SMS modellen bruges af en mægler. Det betyder, at det er muligt at føje nemt AMQP 1.0 support til eksisterende SMS mæglere.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 er et Standard (med et stort ")

AMQP 1.0 har været under udvikling siden 2008 ved en central gruppe af mere end 20 firmaer, både teknologi leverandører og slutbrugere virksomheder. I denne periode bruger virksomheder har bidraget til deres virkelige virksomhedens behov og teknologileverandører har udviklet protokollen, så det opfylder kravene. I hele processen, har leverandører deltaget i workshops, hvor de har samarbejdet for at validere interoperabilitet mellem deres installationer.

I oktober 2011, blev det udviklingsarbejde til et teknisk udvalg inden for organisationen for den overflytning af strukturerede oplysninger standarder (OASIS) og OASIS AMQP 1.0 Standard udgivet i oktober 2012. De følgende virksomheder deltaget i det tekniske udvalg under udviklingen af standarden:

*    **Teknologileverandører**: Axway Software, Huawei teknologier, IIT Software, INETCO systemer, Kaazing, Microsoft, Mitre Corporation, Primeton teknologier, fremdrift Software, rød Hat, SITA, Software AG, Solace systemer, VMware, WSO2, Zenika.
*    **Bruger virksomheder**: Bank Nordamerika, kredit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Nogle af ofte gældende fordelene ved at åbne standarder omfatter:

*    Mindre sandsynlighed for leverandør lås-ind
*    Interoperabilitet
*    Generelle tilgængeligheden af biblioteker og værktøj
*    Beskyttelse mod forældelse
*    Tilgængeligheden af erfaren personale
*    Nederste og administreres risikoen

## <a name="amqp-10-and-service-bus"></a>AMQP 1,0 og Service Bus

Understøttelse af AMQP 1.0 i Azure Service Bus betyder, at du kan nu udnytte Service Bus queuing og Udgiv/Abonner formidlet SMS funktioner fra et område af platforme ved hjælp af en effektiv binære protokol. Desuden kan du oprette programmer, der består af komponenter, der er oprettet ved hjælp af en blanding af sprog, strukturer og operativsystemer.

I følgende figur illustreres en eksempel-installation, hvor Java-klienter, der kører på Linux, skrevet ved hjælp af de almindelige Java meddelelse Service (JMS) API og .NET-klienter, der kører på Windows, udveksling af meddelelser via Service Bus bruger AMQP 1.0.

![][0]

**Figur 1: Eksempel installation scenarie med på tværs af platforme ved hjælp af Service Bus og AMQP 1.0-beskeder**

På nuværende tidspunkt er blevet konstateret følgende klientbiblioteker til at fungere sammen med tjenesten Bus:

| Sprog | Bibliotek                                                                       |
|----------|-------------------------------------------------------------------------------|
| Java     | Apache Qpid Java meddelelse Service (JMS) klient<br/>IIT Software SwiftMQ Java-klient |
| C        | Apache Qpid Proton-C                                                          |
| PHP      | Apache Qpid Proton-PHP                                                        |
| Python   | Apache Qpid Proton-Python                                                     |


**Figur 2: Tabel over AMQP 1.0 klientbiblioteker**

Du kan finde flere oplysninger om, hvordan du får og bruger disse biblioteker med Service Bus [Service Bus AMQP Developer Guide][]. Se afsnittet [Næste trin](service-bus-java-amqp-overview.md#next-steps) for links til yderligere oplysninger.

## <a name="summary"></a>Oversigt

*    AMQP 1.0 er en åben, pålidelig SMS protokol, du kan bruge til at oprette på tværs af platforme, hybrid-programmer. AMQP 1.0 er en OASIS standard.
*    Understøttelse af AMQP 1.0 er nu tilgængelig på Azure Service Bus samt Service Bus til Windows Server (Service Bus 1.1). Priser, er den samme som for de eksisterende protokoller.

## <a name="next-steps"></a>Næste trin

Besøg følgende links for at få mere at vide om understøttelse af AMQP i Service Bus.

*    [Sådan bruges AMQP 1.0 med Service Bus .NET API](service-bus-dotnet-advanced-message-queuing.md)
*    [Sådan bruges de Java meddelelse Service (JMS) API med Service Bus & AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Service Bus AMQP Developer Guide][]
*    [Specifikation af OASIS avancerede meddelelse Queuing Protocol (AMQP) Version 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)

[0]: ./media/service-bus-java-amqp-overview/Example1.png
[Service Bus AMQP Developer Guide]: service-bus-amqp-dotnet.md

 
