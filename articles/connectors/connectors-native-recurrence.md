<properties
    pageTitle="Tilføje gentagelse udløser i logik apps | Microsoft Azure"
    description="Oversigt over gentagelse udløser, og hvordan du kan bruge det sammen med en Azure logik app."
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-recurrence-trigger"></a>Introduktion til gentagelse udløser

Ved hjælp af gentagelse udløser, kan du oprette effektive arbejdsprocesser i skyen.

Det kan du f.eks.:

- Planlægge en arbejdsproces til at køre en SQL-lagret procedure hver dag.
- Sende en oversigt over alle tweets inden for den sidste uge om et bestemt mærke.

For at komme i gang ved hjælp af gentagelse udløser i en logik app skal du se [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-a-recurrence-trigger"></a>Bruge en gentagelse udløser

En udløser er en begivenhed, der kan bruges til at starte arbejdsprocessen, der er defineret i en logik app. [Få mere at vide om udløsere](connectors-overview.md).

Her er et eksempel sekvens af, hvordan du konfigurerer en gentagelse udløser i en logik app:

1. Tilføje **Gentagelse** udløser som det første trin i en logik app.
2. Udfyld parametrene for intervallet for gentagelse.

Appen logik starter nu en Kør efter hver tidsinterval.

![HTTP-udløser](./media/connectors-native-recurrence/using-trigger.png)

## <a name="trigger-details"></a>Udløser detaljer

Gentagelse udløser har følgende egenskaber, som du kan konfigurere.

Den udløses en logik app efter et angivet tidsinterval.
A * betyder, at det er et obligatorisk felt.

|Vist navn|Egenskabsnavn|Beskrivelse|
|---|---|---|
|Frekvens *|frekvens|The unit of time: `Second`, `Minute`, `Hour`, `Day`, or `Year`.|
|Interval *|interval|Intervallet af den angivne hyppighed for gentagelse.|
|Tidszone|Tidszone|Hvis et starttidspunkt leveres uden en UTC-forskydning, bruges denne tidszone.|
|Starttidspunktet|Starttidspunkt|Starttidspunktet i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).|
<br>


## <a name="next-steps"></a>Næste trin

Prøv nu den platform og [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md). Du kan udforske andre tilgængelige forbindelserne i logik apps ved at kigge på vores [API'er liste](apis-list.md).
