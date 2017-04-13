<properties
    pageTitle="Synkronisering af offline Data i Azure-Mobilapps | Microsoft Azure"
    description="Grundlæggende oversigt og oversigt over synkroniseringsfunktion offline data til Azure Mobile-Apps"
    documentationCenter="windows"
    authors="adrianhall"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="offline-data-sync-in-azure-mobile-apps"></a>Synkronisering af offline Data i Azure-Mobilapps

## <a name="what-is-offline-data-sync"></a>Hvad er synkronisering af offline data?

Synkronisering af offline data er en klient og serveren SDK funktion i Azure Mobile-Apps, der gør det nemt for udviklere til at oprette apps, der kan anvendes uden en netværksforbindelse.

Når din app er i offlinetilstand, kan brugerne stadig oprette og redigere data, som bliver gemt i et lokalt lager. Når appen er igen er online, kan det synkronisere lokale ændringer med din Azure Mobile-App back end. Funktionen indeholder også understøttelse af registrering af konflikter, når den samme post ændres i både klienten og back end. Konflikter kan derefter håndteres, enten på serveren eller -klienten.

Offlinesynkronisering har et antal fordele:

* Forbedre app reaktionstid ved at cachelagre server-data lokalt på enheden
* Oprette robust apps, der forbliver nyttig, når der er netværksproblemer
* Tillad slutbrugere kan oprette og redigere data, selv når der er ingen netværksadgang, skal du understøttende scenarier med lille eller ingen forbindelse
* Synkronisere data på tværs af flere enheder og registrere konflikter, når den samme post er ændret af to enheder
* Begrænse netværksbrug på høj ventetid eller forbrugsafregnet netværk

Følgende selvstudier viser, hvordan du føjer offlinesynkronisering til din mobile klienter, der bruger Azure Mobile-Apps:

* [Android: Aktivere offlinesynkronisering]
* [iOS: aktivere offlinesynkronisering]
* [Xamarin iOS: aktivere offlinesynkronisering]
* [Xamarin Android: Aktivere offlinesynkronisering]
* [Xamarin.Forms: Aktivér offlinesynkronisering](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Universal Windows-Platform: Aktivere offlinesynkronisering]

## <a name="what-is-a-sync-table"></a>Hvad er en Synkroniser tabel?

For at få adgang til "/ tabeller" slutpunktet Azure Mobile-klient SDK'er giver grænseflader såsom `IMobileServiceTable` (.NET klient-SDK) eller `MSTable` (iOS-klienten). Disse API'er adgang til direkte Azure Mobile-App back-end og mislykkes, hvis klientenhed ikke har en netværksforbindelse.

For at offlinebrug, din app skal du i stedet bruge *Synkroniser tabel* API'er, f.eks `IMobileServiceSyncTable` (.NET klient-SDK) eller `MSSyncTable` (iOS-klienten). Alle de samme fungerer CRUD handlinger (oprette, læse, opdatere, slette) mod Synkroniser tabel API'er, bortset fra de vil nu læse fra eller skrive til en *lokal butik*. Før en hvilken som helst Synkroniser tabel handlinger kan udføres, skal være initialiseret det lokale lager.

## <a name="what-is-a-local-store"></a>Hvad er et lokalt lager?

Et lokalt lager er data brugerdata lag på klientenhed. Azure Mobile-Apps klient SDK'er giver en standard lokale store-implementering. I Windows, Xamarin og Android, er den baseret på SQLite; iOS, er den baseret på Core Data.

Hvis du vil bruge implementeringen SQLite-baseret på Windows Phone eller Windows Store 8.1, skal du installere filtypenavnet SQLite. Yderligere oplysninger finder du [Universal Windows-Platform: aktivere offlinesynkronisering]. Android- og iOS leveres med en version af SQLite i operativsystemet enhed sig selv, så det ikke er nødvendigt at referere til din egen version af SQLite.

Udviklere kan også implementere deres egen lokale lager. Hvis du vil gemme data i et krypteret format på den mobilklient, kan du f.eks kan definere en lokal butik, der bruger SQLCipher til kryptering.

## <a name="what-is-a-sync-context"></a>Hvad er en synkronisering kontekst?

*Synkroniser kontekst* er knyttet til en mobilklient objekt (såsom `IMobileServiceClient` eller `MSClient`) og registrerer ændringer, der er foretaget med synkronisering af tabeller. Konteksten synkronisering opdaterer en *handling kø* dermed bevare en sorteret liste over CUD handlinger (oprette, opdatere, slette), der sendes senere til serveren.

Et lokalt lager er knyttet til konteksten synkronisering med en initialisering metode såsom `IMobileServicesSyncContext.InitializeAsync(localstore)` i [.NET klient-SDK].

## <a name="how-sync-works"></a>Hvordan offline fungerer synkronisering

Når du bruger synkronisering af tabeller, styrer din klientkode, når lokale ændringer synkroniseres med en Azure Mobile-App back end. Intet sendes til backend-version, indtil der ikke er et opkald til *opslagsnål* lokale ændringer. På samme måde, udfyldes det lokale lager med nye data, når der findes et opkald til at *hente* data.

* **Push**: Push er en handling på konteksten synkronisering og sender alle CUD ændringer siden den sidste opslagsnål. Bemærk, at det ikke er muligt at sende kun en enkelt tabel ændringer, fordi ellers handlinger kunne sendes defekt. Push udfører en række RESTEN opkald til din Azure Mobile-App backend-version, som også vil ændre din server-database.

* **Trække**: hente udføres på grundlag af hver tabel og kan tilpasses med en forespørgsel til at hente kun et undersæt af server-data. Azure Mobile client SDK'er så indsætte den resulterende data i det lokale lager.

* **Implicit skubber**: Hvis en hente køres i forhold til en tabel, der har ventende lokale opdateringer, hente først udføre en opslagsnål for konteksten synkronisering. Dette hjælper med at minimere konflikter mellem ændringer, der er allerede i kø og nye data fra serveren.

* **Delvis synkronisering**: den første parameter til handlingen hente er et *navn til forespørgslen* , der bruges kun på klienten. Hvis du bruger et ikke-null forespørgselsnavn, udfører Azure Mobile SDK en *delvis synkronisering*.
  Hver gang en handling af hente returnerer et sæt af resultater, den nyeste `updatedAt` tidsstempel fra resultatsættet er gemt i tabellerne SDK lokalt system. Efterfølgende hente handlinger vil kun hente poster efter pågældende tidsstempel.

  Hvis du vil bruge trinvis synkronisering, din server skal returnere sigende `updatedAt` værdier og skal også understøtter sortering efter dette felt. Da SDK tilføjer sin egen sortere på feltet updatedAt, kan du dog bruge en hente forespørgsel, der har sin egen `$orderBy$` delsætningen.

  Forespørgslens navn kan være en streng, du vælger, men den skal være entydige for hver logiske forespørgsel i din app.
  Ellers forskellige hente handlinger kan overskrive samme delvis synkronisering tidsstemplet og dine forespørgsler kan returnere forkerte resultater.

  Hvis forespørgslen indeholder en parameter, er en metode til at oprette et entydigt forespørgselsnavn at inkorporere værdien for parameteren.
  Hvis du filtrerer på bruger-id, skyldes din forespørgselsnavn for eksempel på følgende måde (i C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Hvis du vil fravælge delvis synkronisering, skal du overfører `null` som forespørgsel-ID. I dette tilfælde skal alle poster, der bliver hentet på hvert opkald til `PullAsync`, som er potentielt ineffektiv.

* **Purging**: Du kan rydde indholdet af den lokale lager ved hjælp af `IMobileServiceSyncTable.PurgeAsync`.
  Det kan være nødvendigt, hvis du har Fejldata i klientdatabasen, eller hvis du vil ignorere alle ventende ændringer.

  En fjern vil fjerne en tabel fra det lokale lager. Hvis der er handlinger venter på synkronisering med server-databasen, vil fjernet Udløs en undtagelse, medmindre den *gældende Fjern* parameter er angivet.

  Som et eksempel på Fejldata på klienten, forudsættes i eksemplet "opgaveliste liste" Device1 trækker kun elementer, der ikke er fuldført. Derefter en todoitem "Købe mælk" er markeret som fuldført på serveren af en anden enhed. Men Device1 stadig har "Køb mælk" todoitem i lokale lager fordi det er kun at trække elementer, der ikke er markeret som fuldført. En fjern vil fjerne forældede elementet.

## <a name="next-steps"></a>Næste trin

* [iOS: aktivere offlinesynkronisering]
* [Xamarin iOS: aktivere offlinesynkronisering]
* [Xamarin Android: Aktivere offlinesynkronisering]
* [Universal Windows-Platform: Aktivere offlinesynkronisering]

<!-- Links -->
[.NET klient-SDK]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Aktivere offlinesynkronisering]: app-service-mobile-android-get-started-offline-data.md
[iOS: aktivere offlinesynkronisering]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: aktivere offlinesynkronisering]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Aktivere offlinesynkronisering]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Universal Windows-Platform: Aktivere offlinesynkronisering]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
