# <a name="using-cdn-for-azure"></a>Ved hjælp af CDN til Azure

Azure indhold levering netværk (CDN) giver udviklere en global løsning til at levere høj båndbredde indhold ved at cachelagre BLOB og statisk indholdet af Beregn forekomster på fysisk noder i USA, Europa, Asien, Australien og Sydamerika. Du kan finde den aktuelle liste over CDN node placeringer [Azure CDN Node placeringer].

Denne opgave omfatter følgende trin:

* [Trin 1: Oprette en lagerplads konto](#Step1)
* [Trin 2: Oprette et nyt CDN slutpunkt for kontoen lagerplads](#Step2)
* [Trin 3: Få adgang til din CDN indhold](#Step3)
* [Trin 4: Fjerne dit CDN indhold](#Step4)

Fordelene ved at bruge CDN til cachen Azure data omfatter:

-   Bedre ydeevne og bruger oplevelse for slutbrugere, der er langt fra en indholdskilde, og bruger programmer hvor mange 'internet rejser' der kræves for at indlæse indhold
-   Store fordelt kapacitet til at håndtere bedre omgående høj belastning sig i starten af en begivenhed som en produktlancering

Eksisterende CDN kunder kan nu bruge Azure CDN [Azure klassisk portal]. CDN er et tilføjelsesprogram til dit abonnement og har en separat [Fakturering plan].

<a id="Step1"> </a>
<h2>Trin 1: Oprette en lagerplads konto</h2>

Brug følgende fremgangsmåde til at oprette en ny firmapost lagerplads til et abonnement, Azure. En lagerplads konto giver adgang til Azure lagerplads tjenester. Kontoen lagerplads repræsenterer det højeste niveau af navneområdet for at få adgang til hver af Azure lagerplads servicekomponenter: Blob services, kø tjenester og tabellen services. Under [Brug af tjenesterne Azure lagerplads](http://msdn.microsoft.com/library/azure/gg433040.aspx)kan finde flere oplysninger om tjenesterne Azure-lager.

Hvis du vil oprette en lagerplads-konto, skal du være enten tjenesteadministratoren eller en samtidig administrator for den tilknyttede abonnement.

> [AZURE.NOTE] Finde oplysninger om denne handling ved hjælp af Azure Service Management API, emnet [Opret lagerplads konto](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx) reference.

**Oprette en lagerplads konto for et Azure-abonnement**

1.  Log på [Azure klassisk portal].
2.  Klik på **Ny**i det nederste venstre hjørne. Markere **Data Services**i dialogboksen **Ny** , og derefter klikke på **lagerplads**, derefter **Hurtig oprettelse**.

    Dialogboksen **Opret lagerplads konto** vises.

    ![Oprette lagerplads konto][create-new-storage-account]

4. Skriv et navn på underdomæne i feltet **URL-adresse** . Dette element kan indeholde fra 3-24 små bogstaver og tal.

    Denne værdi bliver værtsnavn inden for den URI, der bruges til at løse Blob, kø eller tabel ressourcer til abonnementet. For at løse en objektbeholder ressource i Blob-tjeneste, du vil bruge en URI i følgende format, hvor * &lt;StorageAccountLabel&gt; * refererer til den værdi, du har skrevet i **Angiv en URL-adresse**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt; *

    **Vigtige:** Etiketten URL-adressen underdomæne til kontoen lagerplads URI-formularer og skal være entydig mellem alle tilknyttede tjenester i Azure.

    Denne værdi bruges også som navnet på denne lagerplads konto i portalen eller, når du åbner denne konto ved hjælp af programmering.

5.  Vælg et område eller en gruppe af forbindelse til kontoen lagerplads fra rullelisten **Område/forbindelse gruppe** . Vælg en forbindelse gruppe i stedet for et område, hvis du vil lagerplads tjenester skal være i samme datacenteret med andre Windows Azure-tjenester, du bruger. Dette kan forbedre ydeevnen, og ingen gebyrer, der er påløbet for udgangspunkt.  

    **Note:** Åbn området **Indstillinger** i portalen Management, skal du klikke på **forbindelsen mellem processorer grupper**, og klik derefter på **Tilføj en forbindelse gruppe** eller **Tilføj**for at oprette en forbindelse gruppe skal. Du kan også oprette og administrere forbindelse grupper ved hjælp af Windows Azure Service Management API. Du kan finde yderligere oplysninger finder [handlinger på forbindelsen mellem processorer grupper].

6. Vælg det abonnement, der skal bruges kontoen lagerplads med fra rullelisten **abonnement** .
7.  Klik på **Opret lagerplads konto**. Processen med at oprette kontoen lagerplads kan tage flere minutter at gennemføre.
8.  Bekræft, at kontoen, der vises i de elementer, der er angivet for **lagerplads** med statussen **Online**for at bekræfte, at kontoen lagerplads blev oprettet.

<a id="Step2"> </a>
<h2>Trin 2: Oprette et nyt CDN slutpunkt for kontoen lagerplads</h2>

Når du aktiverer CDN adgang til en lagerplads konto eller hostet service, alle offentligt tilgængelige objekter, der kan bruges til cachelagring af CDN kant. Hvis du redigerer et objekt, der er i øjeblikket cachelagret i CDN, er det nye indhold er ikke tilgængelige via CDN, indtil CDN opdaterer dens indhold, når cachelagret indhold time to live udløber.

**Oprette et nyt CDN slutpunkt for kontoen lagerplads**

1. Klik på **CDN**i [Azure klassisk portal], i navigationsruden.

2. Klik på **Ny**på båndet. Vælg **App tjenester**, og klik derefter **CDN**og derefter **Hurtig oprettelse**i dialogboksen **Ny** .

3. Vælg kontoen lagerplads, du oprettede i forrige afsnit på listen over dine tilgængelige lagerplads konti i rullemenuen **Origin domæne** . 

4. Klik på knappen **Opret** for at oprette nyt slutpunkt.

5. Når slutpunktet er oprettet, vises det på en liste med slutpunkter for abonnementet. Listevisningen viser URL-adressen skal bruges til at få adgang til cachelagret indhold samt origin domænet. 

    Origin domænet er den placering, hvorfra CDN gemmer indhold. Origin domænet kan være enten en lagerplads firma eller en skybaseret tjeneste; en lagerplads konto bruges til at udføre dette eksempel. Lagerplads indhold er cachelagrede til edge-servere i henhold til en cache-control-indstilling, du angiver eller til standard heuristik af cachelagring netværket. 


    > [AZURE.NOTE] Konfigurationen oprettet for slutpunktet bliver med det samme ikke tilgængelige. Det kan tage op til 60 minutter, før registreringen at overføre via CDN netværket. Brugere, der forsøger at bruge domænenavnet CDN umiddelbart kan modtage statuskode 400 (forkert anmodning), indtil indholdet er tilgængelig via CDN.

<a id="Step3"> </a>
<h2>Trin 3: Access CDN indhold</h2> 

Brug på CDN URL-adressen på portalen for at få adgang til cachelagrede indhold på CDN. Adressen for en cachelagrede blob bliver ligner følgende:

http://<*CDNNamespace*\>.vo.msecnd.net/ <*myPublicContainer*\>/<*BlobName*\>

<a id="Step4"> </a>
<h2>Trin 4: Fjerne indhold fra CDN</h2>

Hvis du ikke længere vil cache et objekt i Azure indhold levering netværk (CDN), kan du udføre én af følgende fremgangsmåder:

-   Du kan slette blob fra offentlige beholderen til en Azure blob.
-   Du kan foretage objektbeholderen privat i stedet for vise. Du kan få flere oplysninger i [Begræns adgang til beholdere og BLOB](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/#restrict-access-to-containers-and-blobs) .
-   Du kan deaktivere eller slette CDN slutpunktet ved hjælp af portalen administration.
-   Du kan ændre din hostet tjeneste for at besvare ikke længere anmodninger til objektet.

Et objekt, der allerede er gemt i CDN forbliver cachelagrede, indtil på time to live til objektet er udløbet. Når den time to live periode udløber, kontrollerer CDN for at se, om CDN slutpunktet er stadig er gyldig, og det objekt, der er stadig anonym adgang. Hvis det ikke er, cachelagres den objektet ikke længere.

Muligheden for at tømme med det samme indhold understøttes ikke i øjeblikket på Azure Management-portalen. Kontakt [support Azure](https://azure.microsoft.com/support/options/) Hvis du har brug at tømme med det samme indhold. 

## <a name="additional-resources"></a>Yderligere ressourcer

-   [Sådan oprettes en forbindelse gruppe i Azure]
-   [Sådan: Administrer lagerplads konti for et Azure abonnement]
-   [Om Service Management API]
-   [Hvordan du knytter CDN indhold til et brugerdefineret domæne]

  [Create Storage Account]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/
  [Azure CDN Node placeringer]: http://msdn.microsoft.com/library/windowsazure/gg680302.aspx
  [Azure klassisk portal]: https://manage.windowsazure.com/
  [fakturering plan]: /pricing/calculator/?scenario=full
  [Sådan oprettes en forbindelse gruppe i Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [Overview of the Azure CDN]: http://msdn.microsoft.com/library/windowsazure/ff919703.aspx
  [Om Service Management API]: http://msdn.microsoft.com/library/windowsazure/ee460807.aspx
  [Hvordan du knytter CDN indhold til et brugerdefineret domæne]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Previous Management Portal]: ../../Shared/Media/previous-portal.png
