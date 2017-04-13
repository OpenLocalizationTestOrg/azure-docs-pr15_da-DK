<properties 
   pageTitle="Konfigurere og bruge lagerplads emulatoren med Visual Studio | Microsoft Azure"
   description="Konfigurere og bruge lagerplads emulatoren med Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="tarcher" />

# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Konfigurere og bruge lagerplads emulatoren med Visual Studio

[AZURE.INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Oversigt
Azure SDK udviklingsmiljø indeholder lagerplads emulatoren, et program, der simulerer lagerplads Blob, kø og tabel tjenester tilgængelige i Azure på din lokale computer. Hvis du er oprettelse af en skybaseret tjeneste, der anvender tjenesterne Azure-lager, eller ved at skrive alle eksterne programmer, der kalder lagerplads tjenester, du kan teste din kode lokalt mod emulatoren lagerplads. Azure-værktøjer til Microsoft Visual Studio integrere styring af lager emulatoren i Visual Studio. Værktøjerne Azure initialiseret lagerplads emulator databasen på første gang det bruges, starter tjenesten storage emulator, når du kører eller foretage fejlfinding i din kode fra Visual Studio og giver skrivebeskyttet adgang til lagerplads emulator data via Azure Storage Explorer.

Se [Brug af Azure lagerplads emulatoren for udvikling og tester](./storage/storage-use-emulator.md)finde detaljerede oplysninger om lagerplads emulatoren, herunder systemkrav og brugerdefinerede konfigurationsvejledning.

>[AZURE.NOTE] Der er nogle forskelle i funktionalitet mellem lagerplads emulator simulering og tjenesterne, Azure-lager. Se [forskelle mellem feltet lagerplads Emulator og Azure lagerplads Services](./storage/storage-use-emulator.md) i dokumentationen til Azure SDK oplysninger om bestemte forskelle.

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Konfigurere en forbindelsesstreng for lagerplads emulatoren

For at få adgang til lagerplads emulatoren fra kode i en rolle, vil du konfigurere en forbindelsesstreng, der peger på lagerplads emulatoren og, der senere kan ændres til at pege på en Azure-lager-konto. En forbindelsesstreng er en indstilling, der kan læse din rolle på kørselstidspunktet til at oprette forbindelse til en lagerplads konto. Finde flere oplysninger om, hvordan du opretter forbindelsesstrenge, [konfigurerer Azure-programmet](https://msdn.microsoft.com/library/azure/2da5d6ce-f74d-45a9-bf6b-b3a60c5ef74e#BK_SettingsPage).

>[AZURE.NOTE] Du kan returnere en reference til kontoen lagerplads emulator fra din kode ved hjælp af egenskaben **DevelopmentStorageAccount** . Denne metode fungerer korrekt, hvis du vil have adgang til lagerplads emulatoren fra din kode, men hvis du planlægger at udgive dit program til Azure, skal du oprette en forbindelsesstreng for at få adgang til kontoen Azure-lager og redigere koden for at benytte denne forbindelsesstreng, før du udgiver billedet. Hvis du skifter mellem lagerplads emulator konto og en Azure lagerplads konto ofte, forenkle en forbindelsesstreng denne proces.

## <a name="initializing-and-running-the-storage-emulator"></a>Initialisering og kører lagerplads emulatoren

Du kan angive, at når du kører eller foretage fejlfinding af din tjeneste i Visual Studio, Visual Studio automatisk startes emulatoren lagerplads. I Solution Explorer skal du åbne genvejsmenuen for projektet **Azure** og vælg **Egenskaber**. På fanen **udvikling** , på listen **Start Azure lagerplads Emulator** vælge **Sand** (hvis det ikke allerede er angivet til den pågældende værdi).

Første gang du kører eller foretage fejlfinding af din tjeneste fra Visual Studio åbner lagerplads emulatoren en initialiseringen. Denne proces forbeholder sig lokale porte for lagerplads emulatoren og opretter lagerplads emulator databasen. Når dette er færdigt, behøver ikke denne proces til at køre igen, medmindre lagerplads emulator databasen slettes.

>[AZURE.NOTE] Start med juni 2012 version af Azure-værktøjer, og kører emulatoren lagerplads, som standard i SQL Express LocalDB. I tidligere versioner af værktøjerne Azure, lagerplads emulatoren kører mod en standardforekomst af SQL Express 2005 eller 2008, som du skal installere før du kan installere Azure SDK. Du kan også køre lagerplads emulatoren mod en navngivet forekomst af SQL Express eller et navngivet eller standardforekomst af Microsoft SQL Server. Hvis du vil konfigurere lagerplads emulatoren til at køre mod en forekomst end standardforekomst, skal du se [Brug af Azure lagerplads emulatoren for udvikling og tester](./storage/storage-use-emulator.md).

Lagerplads emulatoren giver en brugergrænseflade for at få vist status for de lokale lager tjenester, og du vil starte, stoppe og nulstille dem. Når tjenesten storage emulator er startet, kan du få vist brugergrænsefladen eller starte eller stoppe tjenesten ved at højreklikke på ikonet for meddelelsesområdet for Microsoft Azure emulatoren i proceslinjen i Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Få vist lagerplads emulator data i Server Explorer

Noden Azure-lager i Server Explorer gør det muligt at få vist data og ændre indstillingerne for blob og tabel data i kontiene lagerplads, herunder emulatoren lagerplads. Se [gennemsyn og administrere lagerplads ressourcer med Server Explorer](https://msdn.microsoft.com/library/azure/ff683677.aspx) kan finde flere oplysninger.
