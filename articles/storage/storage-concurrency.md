<properties
    pageTitle="Administrere på Dokumentsammenfald i Microsoft Azure-lager"
    description="Sådan administreres på dokumentsammenfald for Blob, kø, tabel og filer"
    services="storage"
    documentationCenter=""
    authors="jasonnewyork"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jahogg"/>

# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Administrere på Dokumentsammenfald i Microsoft Azure-lager

## <a name="overview"></a>Oversigt

Moderne Internetbaserede programmer har normalt flere brugere, få vist og opdatere data samtidigt. Dette kræver programmerudviklere nøje om, hvordan du får en mere forudsigelige oplevelse til deres slutbrugere særligt for scenarier, hvor flere brugere kan opdatere de samme data. Der er tre primære data på dokumentsammenfald strategier udviklere vil typisk overveje:  


1.  Optimistisk på dokumentsammenfald – et program, som udfører en opdatering som en del af opdateringen kontrollere Hvis dataene er blevet ændret siden programmet sidst læst disse data. Eksempelvis hvis to brugere, der viser en wikiside foretager en opdatering til den samme side derefter wiki-platform skal sikre dig, at den anden opdatering ikke overskriver den første opdatering – og, at begge brugerne forstår om deres opdateringen blev gennemført, eller ej. Denne strategi bruges oftest i web-programmer.
2.  Pessimistisk på dokumentsammenfald – et program, der er leder for at udføre en opdatering tager en lås på et objekt, at andre brugere i at opdatere dataene, indtil låsen frigives. For eksempel i et overordnet/slave data gentagelse scenarie, hvor kun master udfører opdateringer skal masteren typisk indeholde en eksklusiv lås i længere tid på at sikre, at ingen andre kan opdatere dataene.
3.  Sidste skriver vinder – en metode, der gør det muligt for en hvilken som helst opdatering handlinger at fortsætte uden at bekræfte, hvis har andre programmer først opdateres dataene siden programmet læse dataene. Denne strategi (eller manglende af en formel strategi) bruges som regel hvor data er opdelt i en sådan måde, at der er ingen sandsynligheden for, at flere brugere skal have adgang til de samme data. Det kan også være nyttigt hvor forbigående datastreams behandles.  

Denne artikel indeholder en oversigt over, hvordan Azure-lager platformen forenkler udviklingen ved at indsende førsteklasses support for alle tre af disse på dokumentsammenfald strategier.  

## <a name="azure-storage--simplifies-cloud-development"></a>Azure-lager – forenkler udvikling i sky
Tjenesten Azure storage understøtter alle tre strategier, selvom det er karakteristisk i muligheden for at få fuld understøttelse for optimistisk og pessimistisk på dokumentsammenfald, fordi det er udviklet til at få dobbelt en stærk konsistens model, som garanterer, at når tjenesten Storage bekræftes en data Indsæt eller opdateringshandling alle yderligere har adgang til, at data kan se den seneste opdatering. Lagerplads platforme, der bruger en eventuel konsistens model har en mellemliggende tid mellem, når en skrivning er udført af én bruger, og når de opdaterede data kan ses af andre brugere, og som derfor kompliceret udvikling af klientprogrammer for at forhindre uoverensstemmelser i påvirker slutbrugere.  

Ud over at vælge en relevant på dokumentsammenfald strategi for bør udviklere også være opmærksom på, hvordan en lagerplads platform isolerer ændringer – særligt ændringer på det samme objekt på tværs af transaktioner. Tjenesten Azure storage bruger øjebliksbillede isolationsniveauet til at tillade Læs handlinger til at opstå, samtidig med skrivehandlinger i en enkelt partition. I modsætning til andre isolationsniveauet niveauer garanterer øjebliksbillede isolationsniveauet, at alle læser ser et ensartet øjebliksbillede af dataene, mens opdateringer virkelighed – grundlæggende ved at returnere det sidste anvendt værdier under en opdatering transaktion behandles.  

## <a name="managing-concurrency-in-blob-storage"></a>Administrere på Dokumentsammenfald i Blob-lager
Du kan vælge for at bruge enten på dokumentsammenfald optimistisk eller pessimistisk modeller til at administrere adgang til BLOB og beholdere i blob-tjenesten. Hvis du ikke angiver en strategi for sidste skriver wins eksplicit er standard.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Optimistisk på dokumentsammenfald for BLOB og beholdere  

Tjenesten Storage tildeler et id til hvert objekt, der er gemt. Dette id opdateres, hver gang en opdateringshandling udføres på et objekt. Id returneres til klienten som en del af en HTTP GET svar ved hjælp af overskriften ETag (enhed mærke), der er defineret i HTTP-protokollen. En bruger, der udfører en opdatering til sådanne et objekt kan sende i den oprindelige ETag sammen med et betinget sidehoved til at sikre, at en opdatering forekommer kun, hvis en bestemt betingelse er opfyldt – i dette tilfælde betingelsen er en "If-Match" sidehoved, som kræver tjenesten Storage at sikre, at værdien af den ETag, der er angivet i anmodning om opdatering er den samme som, gemmes i tjenesten lagerplads.  

Disposition for denne proces er som følger:  

1.  Hente en blob fra tjenesten lagerplads, skal svaret, der indeholder en HTTP ETag sidehoved-værdi, der identificerer den aktuelle version af objektet i tjenesten storage.
2.  Når du opdaterer blob, omfatte værdien ETag, du har modtaget i trin 1 i overskriften **If-Match** betinget for din anmodning, du sender til tjenesten.
3.  Tjenesten sammenligner ETag værdien i anmodningen med den aktuelle ETag værdi af blob.
4.  Hvis den aktuelle ETag værdi af blob er en anden version end ETag i **If-Match** betinget sidehovedet i anmodningen, returnerer tjenesten fejlen 412 til klienten. Her angives klienten, en anden proces er opdateret blob, da klienten blev hentet.
5.  Hvis den aktuelle ETag værdi af blob er den samme version som ETag i **If-Match** betinget sidehovedet i anmodningen, tjenesten udfører den ønskede handling og opdaterer den aktuelle ETag værdi af blob til at vise, at det har oprettet en ny version.  

De følgende C# kodestykke (ved hjælp af biblioteket klient lagerplads 4.2.0) viser et simpelt eksempel i at oprette en **If-Match AccessCondition** baseret på den ETag-værdi, der åbnes fra egenskaberne for en blob, der tidligere blev enten hentet eller indsat. Derefter bruges objektet **AccessCondition** når det ved at opdatere blob: objektet **AccessCondition** tilføjes **If-Match** brevhoved anmodningen. Hvis en anden proces har opdateret blob, returnerer tjenesten blob statusmeddelelse en HTTP 412 (betingelse mislykkedes). Du kan hente den fulde prøve: [Administrere sammenfald ved hjælp af Azure-lager](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

    // Retrieve the ETag from the newly created blob
    // Etag is already populated as UploadText should cause a PUT Blob call
    // to storage blob service which returns the etag in response.
    string orignalETag = blockBlob.Properties.ETag;

    // This code simulates an update by a third party.
    string helloText = "Blob updated by a third party.";

    // No etag, provided so orignal blob is overwritten (thus generating a new etag)
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}",
    blockBlob.Properties.ETag);

    // Now try to update the blob using the orignal ETag provided when the blob was created
    try
    {
        Console.WriteLine("Trying to update blob using orignal etag to generate if-match access condition");
        blockBlob.UploadText(helloText,accessCondition:
        AccessCondition.GenerateIfMatchCondition(orignalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine("Precondition failure as expected. Blob's orignal etag no longer matches");
            // TODO: client can decide on how it wants to handle the 3rd party updated content.
        }
        else
            throw;
    }  

Tjenesten Storage indeholder også understøttelse af flere betinget sidehoveder f.eks **Hvis ændret siden**, **Hvis uændrede siden** og **If-None-Match** samt kombinationer deraf. Finde flere oplysninger under [Angive betinget sidehoveder for Blob servicehandlinger](http://msdn.microsoft.com/library/azure/dd179371.aspx) på MSDN.  

Den følgende tabel opsummerer de objektbeholder handlinger, der accepterer betinget sidehoveder som **If-Match** i anmodningen og, der returneres en ETag værdi i svaret.  

| Handling                | Returnerer objektbeholder ETag værdi | Accepterer betinget sidehoveder |
|:-------------------------|:-----------------------------|:----------------------------|
| Oprette objektbeholder         | Ja                          | Nej                          |
| Få objektbeholder egenskaber | Ja                          | Nej                          |
| Få objektbeholder Metadata   | Ja                          | Nej                          |
| Angive objektbeholder Metadata   | Ja                          | Ja                         |
| Hent objektbeholder ACL        | Ja                          | Nej                          |
| Angive objektbeholder ACL        | Ja                          | Ja (*)                     |
| Slette objektbeholder         | Nej                           | Ja                         |
| Bortlease objektbeholder          | Ja                          | Ja                         |
| Listen BLOB               | Nej                           | Nej                          |

(*) De tilladelser, der er defineret af SetContainerACL cachelagres og opdateringer til disse tilladelser tage 30 sekunder at sprede under hvilken periode opdateringer ingen er garanti for at være konsekvente.  

Den følgende tabel opsummerer de blob handlinger, der accepterer betinget sidehoveder som **If-Match** i anmodningen og, der returneres en ETag værdi i svaret.

| Handling           | Returnerer ETag værdi | Accepterer betinget sidehoveder           |
|:--------------------|:-------------------|:--------------------------------------|
| Placere Blob            | Ja                | Ja                                   |
| Få Blob            | Ja                | Ja                                   |
| Få Blob egenskaber | Ja                | Ja                                   |
| Angive Blob egenskaber | Ja                | Ja                                   |
| Få Blob Metadata   | Ja                | Ja                                   |
| Angive Blob Metadata   | Ja                | Ja                                   |
| Bortlease Blob (*)      | Ja                | Ja                                   |
| Øjebliksbillede Blob       | Ja                | Ja                                   |
| Kopiere Blob           | Ja                | Ja (til kilde- og destinationstabellerne blob) |
| Afbryde kopi Blob     | Nej                 | Nej                                    |
| Slette Blob         | Nej                 | Ja                                   |
| Placere blok           | Nej                 | Nej                                    |
| Placere liste over blokerede afsendere      | Ja                | Ja                                   |
| Få liste over blokerede afsendere      | Ja                | Nej                                    |
| Anbringe siden            | Ja                | Ja                                   |
| Hent siden områder     | Ja                | Ja                                   |


(*) Bortlease Blob ændrer ikke ETag på en blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>Pessimistisk på dokumentsammenfald for BLOB
Hvis du vil låse en blob med udelt adgang, kan du får fat på en [bortlease](http://msdn.microsoft.com/library/azure/ee691972.aspx) på den. Når du får fat på en rettighed, du angiver, hvor længe du har brug for lånet: det kan skyldes mellem 15-60 sekunder eller uendelig som udgør en eksklusiv lås. Du kan forny en endelig rettighed at udvide den, og du kan frigive en hvilken som helst bortlease, når du er færdig med den. Tjenesten blob versioner automatisk endelig rettigheder, når de udløber.  

Rettigheder aktivere synkronisering af forskellige strategier til at være understøttet, herunder udelt skriveadgang / delt læst, begge eksklusive Skriv / eksklusiv læse og skrive har delt / eksklusiv læse. Hvis der findes en rettighed håndhæves tjenesten storage eksklusiv skriver (placere, angive og slette handlinger) dog sikre eksklusivitet for Læs handlinger kræver udvikleren mulighed for at sikre, at alle klientprogrammer bruger et bortlease-ID og kun én klient ad gangen har en gyldig bortlease-ID. Læs handlinger, som ikke du skal medtage et bortlease ID resultat i delte læser.  

Den følgende C# kodestykke viser et eksempel på henter en eksklusivt rettighed til 30 sekunder på en blob, ved at opdatere indholdet af blob og derefter slippe lease. Hvis der allerede er en gyldig rettighed blob når du forsøger at få fat på en ny rettighed returnerer tjenesten blob et "HTTP (409) konflikt" status resultat. Den nedenstående kodestykke bruger et **AccessCondition** objekt til at omfatter bortlease oplysningerne, hvornår det giver en anmodning om at opdatere blob i tjenesten lagerplads.  Du kan hente den fulde prøve: [Administrere sammenfald ved hjælp af Azure-lager](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

    // Acquire lease for 15 seconds
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation will succeed
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    //Simulate third party update to blob without lease
    try
    {
        // Below operation will fail as no valid lease provided
        Console.WriteLine("Trying to update blob without valid lease");
        blockBlob.UploadText("Update without lease, will fail");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
            Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
        else
            throw;
    }  

Hvis du forsøger Skrivehandlingen på et faste blob uden at der passerer bortlease-ID, mislykkes anmodningen med en 412 fejl. Bemærk, at hvis udløb før du kalder **UploadText** metode, men du stadig gå bortlease-ID, anmodningen også mislykkes med fejlen **412** . Du kan finde flere oplysninger om administration af bortlease udløbet gange og bortlease id'er, i dokumentationen på [Bortlease Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx) RESTEN.  

Følgende blob handlinger kan bruge rettigheder til at administrere pessimistisk på dokumentsammenfald:  


-   Placere Blob
-   Få Blob
-   Få Blob egenskaber
-   Angive Blob egenskaber
-   Få Blob Metadata
-   Angive Blob Metadata
-   Slette Blob
-   Placere blok
-   Placere liste over blokerede afsendere
-   Få liste over blokerede afsendere
-   Anbringe siden
-   Hent siden områder
-   Øjebliksbillede Blob - bortlease ID valgfrit, hvis der findes en rettighed
-   Kopiér Blob - bortlease ID kræves om en rettighed findes på destination blob
-   Afbryde kopi Blob - lease ID, der er påkrævet, hvis en uendelig bortlease findes på destination blob
-   Bortlease Blob  

### <a name="pessimistic-concurrency-for-containers"></a>Pessimistisk på dokumentsammenfald af objektbeholdere
Rettigheder på beholdere aktivere de samme synkronisering strategier til at være understøttet som i BLOB (eksklusiv skrive / delt læst, begge eksklusive Skriv / eksklusiv læse og skrive har delt / eksklusiv læse) dog i modsætning til BLOB storage tjenesten kun håndhæves eksklusivitet på sletninger. Hvis du vil slette en objektbeholder med en aktiv bortlease, skal en klient indeholde det aktive bortlease-ID med Sletteanmodningen. Alle andre objektbeholder handlinger lykkes på objektbeholderen faste uden at medtage det bortlease-ID i så fald de er delt handlinger. Hvis eksklusivitet opdatering (læg eller et sæt) eller Læs operationer er påkrævet skal derefter udviklere sikre alle klienter, der bruger et bortlease-ID og, der kun én klient ad gangen har en gyldig bortlease-ID.  

Følgende objektbeholder handlinger kan bruge rettigheder til at administrere pessimistisk på dokumentsammenfald:  

-   Slette objektbeholder
-   Få objektbeholder egenskaber
-   Få objektbeholder Metadata
-   Angive objektbeholder Metadata
-   Hent objektbeholder ACL
-   Angive objektbeholder ACL
-   Bortlease objektbeholder  

Flere oplysninger under:  

- [Angive betinget sidehoveder for Blob servicehandlinger](http://msdn.microsoft.com/library/azure/dd179371.aspx)
- [Bortlease objektbeholder](http://msdn.microsoft.com/library/azure/jj159103.aspx)
- [Bortlease Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>Administrere på Dokumentsammenfald i tabeltjenesten
Tabeltjenesten bruger optimistisk på dokumentsammenfald kontrollerer som standard, når du arbejder med enheder, i modsætning til tjenesten blob, hvor du skal eksplicit vælge til at udføre optimistisk kontrol. Andre forskellen mellem tabel og blob tjenesterne er, at du kun kan administrere på dokumentsammenfald funktionsmåden for enheder, mens du kan administrere på dokumentsammenfald både beholdere og BLOB med blob-tjenesten.  

Til at bruge optimistisk og kontrollere, om en anden proces ændret et objekt, da hentet fra tabellen lagringstjeneste, kan du bruge den ETag værdi, du modtager, når tabeltjenesten returnerer et objekt. Disposition for denne proces er som følger:  

1.  Hente et objekt fra tjenesten tabel storage, svaret omfatter en ETag-værdi, der identificerer det aktuelle id, der er knyttet til denne enhed i tjenesten lagerplads.
2.  Når du opdaterer enheden, kan du medtage den ETag-værdi, du har modtaget i trin 1 i overskriften obligatorisk **If-Match** for din anmodning, du sender til tjenesten.
3.  Tjenesten sammenligner ETag værdien i anmodningen med den aktuelle ETag værdi af objektet.
4.  Hvis den aktuelle ETag værdi af objektet er anderledes end ETag i obligatorisk **If-Match** sidehovedet i anmodningen, returnerer tjenesten fejlen 412 til klienten. Her angives klienten, en anden proces er opdateret enheden, da klienten blev hentet.
5.  Hvis den aktuelle ETag værdi af objektet er den samme som ETag i obligatorisk **If-Match** sidehovedet i anmodningen eller overskriften **If-Match** indeholder jokertegnet (*), udfører den ønskede handling i tjenesten, og opdaterer den aktuelle ETag værdi af objektet til at vise, at det er blevet opdateret.  

Bemærk, at til forskel fra tjenesten blob tabeltjenesten kræver klienten til at medtage en **If-Match** sidehovedet i update-anmodninger. Det er muligt at gennemtvinge en almindelig opdatere (sidste writer wins strategi) og igennem på dokumentsammenfald kontrol, hvis klienten angiver **If-Match** sidehovedet til jokertegnet (*) i indkaldelsen.  

Den følgende C# kodestykke viser en kunde-enhed, der tidligere blev enten oprettet eller hentet har deres mailadresse, der er opdateret. Første indsætter eller hente handlingen butikker ETag værdien i objektet kunde, og fordi udsnittet bruger den samme objektforekomst, når det afvikler handlingen Erstat, sender værdien ETag automatisk tilbage til tabel-tjenesten ved aktivering af tjenesten til at kontrollere, om overtrædelse af på dokumentsammenfald. Hvis en anden proces har opdateret objektet i tabellagring, returnerer tjenesten statusmeddelelse en HTTP 412 (betingelse mislykkedes).  Du kan hente den fulde prøve: [Administrere sammenfald ved hjælp af Azure-lager](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

    try
    {
        customer.Email = "updatedEmail@contoso.org";
        TableOperation replaceCustomer = TableOperation.Replace(customer);
        customerTable.Execute(replaceCustomer);
        Console.WriteLine("Replace operation succeeded.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == 412)
            Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
        else
            throw;
    }  

Hvis du vil deaktivere eksplicit kontrollen på dokumentsammenfald, skal du indstille egenskaben **ETag** for objektet **Medarbejder** til "*" før du udfører handlingen Erstat.  

    customer.ETag = "*";  

I følgende tabel opsummeres, hvordan handlingerne tabel objekt bruge ETag værdier:

| Handling                | Returnerer ETag værdi | Kræver If-Match anmodning sidehoved |
|:-------------------------|:-------------------|:---------------------------------|
| Forespørgsel enheder           | Ja                | Nej                               |
| Indsæt objekt            | Ja                | Nej                               |
| Opdater objekt            | Ja                | Ja                              |
| Flette enhed             | Ja                | Ja                              |
| Slet enhed            | Nej                 | Ja                              |
| Indsætte eller erstatte enhed | Ja                | Nej                               |
| Indsætte eller flette objekt   | Ja                | Nej                               |

Bemærk, at handlingerne **Insert eller erstatte enhed** , og **Indsæt eller flette enhed** kan *ikke* udføre en hvilken som helst på dokumentsammenfald kontrol, fordi de ikke sender en ETag værdi til tabeltjenesten.  

Udviklere ved hjælp af tabeller må generelt stole på optimistisk, når SVG udviklingsprogrammer. Hvis pessimistisk låsning er nødvendigt, drage én tilgang udviklere når adgang til tabeller er at tildele en dedikeret blob for hver tabel og prøve at benytte en rettighed på blob før operativsystem på tabellen. Denne fremgangsmåde kræver programmet for at sikre, at alle data access stier få bortlease før operativsystem på tabellen. Du bør også bemærke det mindste tiden er 15 sekunder, der kræver nøje overvejelser for skalerbarhed.  

Flere oplysninger under:  

- [Handlinger på enheder](http://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Administrere på Dokumentsammenfald i tjenesten kø
Et scenarie, i hvilken på dokumentsammenfald er et problem i tjenesten sætter i kø er, hvor flere klienter henter meddelelser fra en kø. Når en meddelelse er hentet fra køen, omfatter svaret meddelelsen og en pop-kvittering værdi, der er påkrævet for at slette meddelelsen. Meddelelsen slettes ikke automatisk fra køen, men når den er blevet hentet, det er ikke synlige for andre klienter til et tidsinterval, der er angivet af parameteren visibilitytimeout. Den klient, der henter meddelelsen forventes at slette meddelelsen, når den er blevet behandlet, og før den tid, der er angivet af TimeNextVisible element i svaret, som er beregnet ud fra værdien af parameteren visibilitytimeout. Værdien af visibilitytimeout føjes til den tid, som er hentet meddelelsen for at afgøre værdien af TimeNextVisible.  

Tjenesten kø har ikke understøttelse af optimistisk eller pessimistisk på dokumentsammenfald og i dette årsag klienter behandling af meddelelser, der er hentet fra en kø skal sikre meddelelser behandles i en idempotent måde. En sidste writer wins strategi bruges til opdatering handlinger som SetQueueServiceProperties, SetQueueMetaData, SetQueueACL og UpdateMessage.  

Flere oplysninger under:  

- [Kø Service REST-API](http://msdn.microsoft.com/library/azure/dd179363.aspx)
- [Få meddelelser](http://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>Administrere på Dokumentsammenfald i tjenesten fil
Tjenesten filen kan åbnes ved hjælp af to forskellige protocol slutpunkter – små og mellemstore virksomheder, og Placer MARKØREN. Tjenesten RESTEN har ikke understøttelse af enten optimistisk låsning eller pessimistisk låsning og alle opdateringer følger en sidste writer wins strategi. Små og mellemstore virksomheder klienter, der tilslutte filshares kan udnytte filer system låsning funktioner til at administrere adgang til delte filer – herunder også muligheden for at udføre fuldstændig låsning. Når en klient til små og mellemstore virksomheder åbner en fil, angiver den både adgang til filer og dele tilstand. Angive en indstilling for adgang til filer af "Skrive" eller "Læse-og skriveadgang" sammen med et filshare tilstand "Ingen" resulterer i filen bliver låst af en klient til små og mellemstore virksomheder, indtil filen er lukket. Hvis er forsøgt RESTEN handling på en fil, hvor en klient til små og mellemstore virksomheder har låst filen returnerer REST-webtjenesten statuskode 409 (konflikt) med fejlkode SharingViolation.  

Når en klient til små og mellemstore virksomheder åbner en fil til sletning, markerer fil, som afventer sletning før alle andre små og mellemstore virksomheder-klienten Åbn håndtagene på denne fil er lukket. Mens en fil er markeret som afventer sletning, returnerer nogen RESTEN handling på denne fil statuskode 409 (konflikt) med fejlkode SMBDeletePending. Statuskode 404 (ikke fundet) returneres ikke, da det er muligt for små og mellemstore virksomheder-klienten til at fjerne flaget afventer sletning før du lukker filen. Statuskode 404 (ikke fundet) forventes med andre ord kun, når filen er blevet fjernet. Bemærk, mens en fil er i en små og mellemstore virksomheder afventer sletning, den ikke medtages i listen filer resultaterne. Bemærk også, at handlingerne RESTEN Slet fil og RESTEN slette mappe er anvendt atomically og ikke medfører afventer sletning.  

Flere oplysninger under:  

- [Administration af fil låser](http://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>Oversigt og næste trin
Tjenesten Microsoft Azure-lager er udviklet til imødekommer behovet hos de mest komplekse online applikationer uden at tvinge udviklere til forringer eller Tænk nyt vigtige design antagelser som på dokumentsammenfald og data konsistens, som de er vant til at tage til ydes.  

For hele eksemplet-program, der refereres til i denne blog:  

- [Administrere på Dokumentsammenfald ved hjælp af Azure-lager - Northwind](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Kan finde flere oplysninger om Azure-lager i:  

- [Startside for Microsoft Azure-lager](https://azure.microsoft.com/services/storage/)
- [Introduktion til Azure-lager](storage-introduction.md)
- Lagerplads Introduktion til [Blob](storage-dotnet-how-to-use-blobs.md), [tabel](storage-dotnet-how-to-use-tables.md), [køer](storage-dotnet-how-to-use-queues.md)og [filer](storage-dotnet-how-to-use-files.md)
- Lagerplads arkitektur – [Azure-lager: en meget tilgængelige Skylagringstjeneste med stærke konsistens](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
