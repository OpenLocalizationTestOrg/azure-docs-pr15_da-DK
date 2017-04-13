<properties
    pageTitle="Introduktion til Relay Hybrid forbindelser | Microsoft Azure"
    description="Hvordan du kan skrive en Node console programmet for hybride forbindelser"
    services="service-bus"
    documentationCenter="node"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="hero-article"
    ms.tgt_pltfrm="node"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="jotaub"/>

# <a name="get-started-with-relay-hybrid-connections"></a>Introduktion til Relay Hybrid forbindelser

[AZURE.INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

## <a name="what-will-be-accomplished"></a>Hvad skal gøres

Da Hybrid forbindelser kræver både en klient og en server-komponent, skal du oprette to console-programmer i dette selvstudium. Her er trinnene:

1. Oprette et Relay-navneområde, ved hjælp af portalen Azure.

2. Oprette en Hybrid forbindelse, ved hjælp af portalen Azure.

3. Skriv en server console program tilladelse til at modtage meddelelser.

4. Skriv en klient console program til at sende meddelelser.

## <a name="prerequisites"></a>Forudsætninger

1. [Node.js](https://nodejs.org/en/) (i dette eksempel bruger Node 7.0).

2. Et Azure-abonnement.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Opret et navneområde, ved hjælp af portalen Azure

Hvis du allerede har et Relay navneområde, der er oprettet, kan du springe til afsnittet [oprette en Hybrid forbindelse ved hjælp af portalen Azure](#2-create-a-hybrid-connection-using-the-azure-portal) .

[AZURE.INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Opret en Hybrid forbindelse ved hjælp af portalen Azure

Hvis du allerede har en Hybrid forbindelse, der er oprettet, kan du gå til afsnittet [oprette et server-program](#3-create-a-server-application-listener) .

[AZURE.INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Opret et server-program (lytteren)

Hvis du vil lytte og modtage meddelelser fra Relay, skriver vi et Node.js console-program.

[AZURE.INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Opret en klientprogrammet (afsenderen)

Hvis du vil sende meddelelser til videresendelse, skriver vi et Node.js console-program.

[AZURE.INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Kør programmer

1. Kør serverprogrammet.

2. Kør klientprogrammet, og skriv noget tekst.

3. Sørg for, at konsollen server programmet skriver den tekst, der blev angivet i klientprogrammet.

![køre-programmer](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Tillykke, du har oprettet et til slut Hybrid forbindelser program.

## <a name="next-steps"></a>Næste trin:

- [Relay ofte stillede spørgsmål](relay-faq.md)
- [Oprette et navneområde](relay-create-namespace-portal.md)
- [Introduktion til .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Komme i gang med Node](relay-hybrid-connections-node-get-started.md)