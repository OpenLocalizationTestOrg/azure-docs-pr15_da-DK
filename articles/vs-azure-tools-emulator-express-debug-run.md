<properties
   pageTitle="Brug af Emulator Express til at køre og foretage fejlfinding i en skybaseret tjeneste på en lokal maskine | Microsoft Azure"
   description="Brug af Emulator Express til at køre og foretage fejlfinding i en skybaseret tjeneste på en lokal computer"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />


# <a name="using-emulator-express-to-run-and-debug-a-cloud-service-on-a-local-machine"></a>Brug af Emulator Express til at køre og foretage fejlfinding i en skybaseret tjeneste på en lokal computer

Ved hjælp af Emulator Express, kan du teste og fejlfinde en skybaseret tjeneste uden at køre Visual Studio som administrator. Du kan angive dine indstillinger for project til at bruge Emulator Express eller fuld emulatoren, afhængigt af din skytjeneste krav. Se [kører en Azure-programmet i emulatoren beregne](./storage/storage-use-emulator.md)kan finde flere oplysninger om fuld emulatoren. Emulator Express blev først inkluderet i Azure SDK 2.1, og og Azure SDK 2.3, det er standard emulatoren.

## <a name="using-emulator-express-in-the-visual-studio-ide"></a>Brug af Emulator Express i Visual Studio IDE

Når du opretter et nyt projekt i Azure SDK 2.3 eller nyere, Emulator Express er allerede er valgt. Følg disse trin for at vælge Emulator Express for eksisterende projekter, der er oprettet med en tidligere version af SDK.

### <a name="to-configure-a-project-to-use-emulator-express"></a>Konfigurere et projekt for at bruge Emulator Express

1. Vælg **Egenskaber**i genvejsmenuen for Azure projektet, og vælg derefter fanen **Web** .

1. Vælg alternativknappen **Brug IIS Express** under **Lokale Development-Server**. Emulator Express er ikke kompatibelt med IIS webserver.

1. Vælg alternativknappen **Brug Emulator Express** under **Emulator**.

    ![Emulator Express](./media/vs-azure-tools-emulator-express-debug-run/IC673363.gif)

## <a name="launching-emulator-express-at-a-command-prompt"></a>Start Emulator Express kommandoprompt

Ved en kommandoprompt, kan du starte den udtrykkelige version af Azure beregne emulatoren, csrun.exe, ved hjælp af indstillingen /useemulatorexpress.

## <a name="limitations"></a>Begrænsninger

Før du bruger Emulator Express, skal du være opmærksom på visse begrænsninger:

- Emulator Express er ikke kompatibelt med IIS webserver.

- Skybaseret tjeneste kan indeholde flere roller, men hver rolle er begrænset til én forekomst.

- Du kan ikke få adgang til portnumre under 1000. Eksempelvis hvis du bruger en udbyder af godkendelse, der normalt bruger en port under 1000, du muligvis ændre denne værdi til et portnummer, der er større end 1000.

- Begrænsninger, der gælder for Azure beregne emulatoren gælder også for Emulator Express. Du kan for eksempel har mere end 50 rolle forekomster hver installation. Se [køre en Azure-program i Beregn emulatoren](http://go.microsoft.com/fwlink/p/?LinkId=623050)

## <a name="next-steps"></a>Næste trin

[Fejlfinding Cloud Services](https://msdn.microsoft.com/library/azure/ee405479.aspx)
