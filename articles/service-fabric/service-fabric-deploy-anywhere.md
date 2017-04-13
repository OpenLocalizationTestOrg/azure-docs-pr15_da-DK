<properties
   pageTitle="Oprette Azure Service strukturen klynger på Windows Server og Linux | Microsoft Azure"
   description="Tjenesten strukturen klynger køre på Windows Server og Linux, hvilket betyder, at du vil kunne installere og overalt host Service-strukturen programmer kan du køre Windows Server eller Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="Chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/22/2016"
   ms.author="chackdan"/>

# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Oprette Service-strukturen klynger på Windows Server eller Linux

Azure Service-strukturen giver mulighed for oprettelse af tjenesten strukturen klynger på en hvilken som helst FOS eller computere, der kører Windows Server eller Linux. Det betyder, at du er i stand til at installere og køre tjenesten strukturen programmer i et miljø, hvor du har et sæt af Windows Server eller Linux-computere, der er forbundet, er det lokale miljø, Microsoft Azure eller med en hvilken som helst cloud-udbyder.

##<a name="create-service-fabric-clusters-on-azure"></a>Oprette Service-strukturen klynger på Azure

Oprette en klynge på Azure er færdig, enten via en ressource Model skabelon eller Azure portalen. Læs [Opret en tjeneste-strukturen klynge ved hjælp af en ressourcestyring skabelon](service-fabric-cluster-creation-via-arm.md) eller [oprette en tjeneste-strukturen klynge fra portalen Azure](service-fabric-cluster-creation-via-portal.md) kan finde flere oplysninger.

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Understøttede operativsystemer til klynger på Azure

Du er i stand til at oprette klynger på VM'er, der kører disse operativsystemer:

* Windows Server 2012 R2
* Windows Server 2016 (efter at den er offentliggjort som alment tilgængelig)
* Linux Ubuntu 16.04 (i offentlige preview) 


##<a name="create-service-fabric-standalone-clusters-on-premise-or-with-any-cloud-provider"></a>Oprette Service-strukturen enkeltstående klynger lokalt eller med en hvilken som helst cloud-udbyder

Service-strukturen indeholder en installationspakke, du kan oprette enkeltstående Service-strukturen klynger lokalt eller på en hvilken som helst skyen udbyder

Du kan finde flere oplysninger om oprettelse af enkeltstående service-strukturen klynger på Windows Server læse [Service-strukturen klynge oprettelse af til Windows Server](service-fabric-cluster-creation-for-windows-server.md)

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>En hvilken som helst skyen installationer kontra lokale installationer
Processen med at oprette en klynge på en hvilken som helst skyen efter eget valg med et sæt FOS ligner processen til oprettelse af en tjeneste-strukturen klynge i det lokale miljø. De første trin til at klargøre FOS er underlagt skyen provider eller lokalt miljø, du bruger. Når du har et sæt af FOS med netværksforbindelsen aktiveret mellem dem, derefter trinnene til konfiguration af pakken Service-strukturen redigere klynge indstillinger, og kør klynge oprettelse og af Administrationsscripts er identiske. Dette sikrer, at din viden og oplevelse af operativsystem og administration af tjenesten strukturen klynger er kan overføres, når du vælger at målrette nye hosting miljøer.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>Fordele ved at oprette enkeltstående Service-strukturen klynger
* Du kan frit vælge enhver skyen udbyder til at hoste din klynge.
* Tjenesten strukturen programmer, når skrevet, kan køres i flere hosting miljøer med minimale til nogen ændringer.
* Kendskab til udvikling af tjenesten strukturen programmer videreføres fra én værtsmiljø til en anden.
* Praktiske erfaringer af kørsel og administration af tjenesten strukturen klynger varetager fra et miljø til en anden.
* Generelle kunde rækkevidde er ubundet ved at placere miljø begrænsninger.
* Der findes et ekstra lag af pålidelighed og beskyttelse mod udbredte udfald, fordi du kan flytte tjenesterne til en anden installation miljø, hvis en centrere eller skyen dataprovider har en sort til eller fra.

## <a name="supported-operating-systems-for-standalone-clusters"></a>Understøttede operativsystemer til enkeltstående klynger
Du er i stand til at oprette klynger på FOS eller computere, der kører disse operativsystemer:

* Windows Server 2012 R2
* Windows Server 2016 (efter at den er offentliggjort som alment tilgængelig)
* Linux (kommer snart)

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>Fordelene ved Service-strukturen klynger på Azure over enkeltstående Service-strukturen klynger oprettet lokalt

Kører tjenesten strukturen klynger på Azure giver fordele i forhold til lokale indstilling, så hvis du ikke har specifikke behov for, hvor du kører din klynger, og klik derefter vi anbefaler, at køre dem på Azure. På Azure giver vi integration med andre Azure funktioner og tjenester, som gør handlinger og administration af klyngen nemmere og mere pålidelig.

* **Azure-portalen:** Azure portal gør det nemt at oprette og administrere klynger.

* **Azure ressourcestyring:** Brug af Azure ressourcestyring giver nem håndtering af alle de ressourcer, der anvendes af klyngen som en enhed og forenkler omkostninger registrering og fakturering.
* **Tjenesten strukturen klynge som en Azure ressource** En tjeneste-strukturen klynge er en ARM ressource, så du kan modellere den, som du gør andre ARM ressourcer i Azure.
* **Integration med Azure infrastruktur** Tjenesten strukturen koordinater med den underliggende Azure struktur for OS, Netværks- og andre til at forbedre tilgængeligheden og pålideligheden af dine programmer.  
* **Diagnosticering:** På Azure giver vi integration med Azure diagnosticering og Log analyser.
* **Automatisk skalering:** For klynger på Azure giver vi indbyggede automatisk skalering funktionalitet på grund af virtuelt skala-sæt. I i det lokale miljø og andre cloud-miljøer har du bygge din egen automatisk skalering funktion- eller -skala manuelt ved hjælp af de API'er, som viser tjenesten strukturen for skalering klynger.

## <a name="next-steps"></a>Næste trin
Oprette en klynge på FOS eller computere, der kører Windows Server: [Service-strukturen klynge oprettelse af til Windows Server](service-fabric-cluster-creation-for-windows-server.md)

Oprette en klynge på FOS eller computere, der kører Linux: [Service-strukturen på Linux](service-fabric-linux-overview.md)
