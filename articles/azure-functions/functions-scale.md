<properties
   pageTitle="Sådan skalere Azure funktioner | Microsoft Azure"
   description="Forstå, hvordan Azure funktioner skaleres, så den imødekommer behovet hos din hændelsesstyret arbejdsmængder."
   services="functions"
   documentationCenter="na"
   authors="dariagrigoriu"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure-funktioner, funktioner, behandling af hændelse, webhooks, dynamisk Beregn, ikke-serverbaseret arkitektur"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="reference"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/03/2016"
   ms.author="dariagrigoriu"/>

# <a name="how-to-scale-azure-functions"></a>Sådan skalere Azure funktioner

## <a name="introduction"></a>Introduktion

En fordel af Azure funktioner er, at Beregn ressourcer er kun brugt, når det er nødvendigt. Det betyder, at du ikke betale for inaktiv FOS eller har reservere kapacitet, når du muligvis har brug for den. I stedet allokerer platformen Beregn power, når din kode kører, skaleres efter behov for at håndtere indlæsning, og derefter skaleres, når kode ikke kører.

Funktionen til denne nye egenskab er den dynamiske serviceaftale.  

I denne artikel giver et overblik over hvordan den dynamiske serviceaftale fungerer, og hvordan platformen skaleres efter behov for at køre din kode.

Hvis du ikke endnu er fortrolig med Azure funktioner, skal du sørge for at se artiklen [Oversigt over Azure funktioner](functions-overview.md) for bedre at forstå dens egenskaber.

## <a name="configure-azure-functions"></a>Konfigurere Azure funktioner

To primære indstillinger er relateret til skalering:

* [Azure App-serviceaftale](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) eller dynamisk serviceaftale
* Hukommelsesstørrelse for udførelse af miljøet

Omkostninger for en funktion ændres afhængigt af den serviceaftale, som du vælger. Kostprisen er en funktion af udførelse af tid, hukommelsesstørrelse og antal udførelser med et dynamisk-serviceaftale. Gebyrer periodiseres kun, når din kode faktisk kører.

En App-serviceaftale er vært for din funktioner på eksisterende FOS, hvilket kan også bruges til at køre andre kode. Når du betale for disse FOS hver måned, der ikke gratis ekstra for udførelse af funktioner på dem.

## <a name="choose-a-service-plan"></a>Vælg en serviceaftale

Når du opretter funktioner, kan du vælge for at køre dem på en dynamisk serviceaftale eller en [App-serviceaftale](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
I App-serviceaftale kører din funktioner for en dedikeret VM, ligesom web apps arbejde i dag for Basic, Standard eller Premium lagerenheder.
Denne dedikeret VM er allokeret til dine apps og -funktioner og altid er tilgængelig om kode der udføres aktivt eller ej. Dette er en god indstilling, hvis du har eksisterende, under anvendes VM'er, der kører allerede anden kode, eller hvis du forventer at køre funktioner løbende eller næsten løbende. En VM decouples omkostninger fra både runtime og hukommelse størrelse. Som et resultat, kan du begrænse omkostningerne for mange længerevarende funktioner omkostninger for på en eller flere FOS, som de kører på.

[AZURE.INCLUDE [Dynamic Service plan](../../includes/functions-dynamic-service-plan.md)]
