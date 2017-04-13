<properties
    pageTitle="Integrere en lagerplads konto med CDN | Microsoft Azure"
    description="Lær at bruge Azure indhold levering netværk (CDN) til at fremvise høj båndbredde indhold ved at cachelagre BLOB fra Azure-lager."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>


# <a name="integrate-a-storage-account-with-cdn"></a>Integrere en lagerplads konto med CDN

LEVERANDØR(er) kan være aktiveret til cache indhold fra dit Azure-lager. Det giver udviklere en global løsning til at levere høj båndbredde indhold ved at cachelagre BLOB og statisk indholdet af Beregn forekomster på fysisk noder i USA, Europa, Asien, Australien og Sydamerika.


## <a name="step-1-create-a-storage-account"></a>Trin 1: Oprette en lagerplads konto

Brug følgende fremgangsmåde til at oprette en ny firmapost lagerplads til et abonnement, Azure. En lagerplads konto giver adgang til Azure lagerplads tjenester. Kontoen lagerplads repræsenterer det højeste niveau af navneområdet for at få adgang til hver af Azure lagerplads servicekomponenter: Blob services, kø tjenester og tabellen services. Yderligere oplysninger finder du referere til [Introduktion til Microsoft Azure-lager](../storage/storage-introduction.md).

Hvis du vil oprette en lagerplads-konto, skal du være enten tjenesteadministratoren eller en samtidig administrator for den tilknyttede abonnement.

> [AZURE.NOTE] Der er flere forskellige metoder, du kan bruge til at oprette en lagerplads-konto, herunder Azure portalen og Powershell.  Dette selvstudium skal bruger vi Azure-portalen.  

**Oprette en lagerplads konto for et Azure-abonnement**

1.  Log på [Azure-portalen](https://portal.azure.com).
2.  I det øverste venstre hjørne skal du vælge **Ny**. Vælg **Data + lagerplads**i dialogboksen **Ny** , og derefter på **lagerplads konto**.

    Bladet **Opret lagerplads konto** vises.

    ![Oprette lagerplads konto][create-new-storage-account]

4. Skriv et navn på underdomæne i feltet **navn** . Dette element kan indeholde 3-24 små bogstaver og tal.

    Denne værdi bliver værtsnavn inden for den URI, der bruges til at løse Blob, kø eller tabel ressourcer til abonnementet. For at løse en objektbeholder ressource i Blob-tjeneste, du vil bruge en URI i følgende format, hvor * &lt;StorageAccountLabel&gt; * refererer til den værdi, du har skrevet i **Angiv en URL-adresse**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt; *

    **Vigtige:** Etiketten URL-adressen underdomæne til kontoen lagerplads URI-formularer og skal være entydig mellem alle tilknyttede tjenester i Azure.

    Denne værdi bruges også som navnet på denne lagerplads konto i portalen eller, når du åbner denne konto ved hjælp af programmering.

5. Lad standardindstillingerne for **implementeringsmodel**, **type konto**, **ydeevne**og **Gentagelse**. 

6. Vælg det **abonnement** , der skal bruges kontoen lagerplads med.

7. Vælg eller Opret en **Ressourcegruppe**.  Du kan finde flere oplysninger om ressourcegrupper, [Azure ressourcestyring oversigt](azure-resource-manager/resource-group-overview.md#resource-groups).

8. Vælg en placering til kontoen lagerplads.

8. Klik på **Opret**. Processen med at oprette kontoen lagerplads kan tage flere minutter at gennemføre.


## <a name="step-2-create-a-new-cdn-profile"></a>Trin 2: Oprette en ny CDN profil

En CDN profil er en samling af CDN slutpunkter.  Hver profil indeholder en eller flere CDN slutpunkter.  Du vil bruge flere profiler til at organisere CDN slutpunkterne af internet-domæne, webprogram eller nogle andre kriterier.

> [AZURE.TIP] Hvis du allerede har en CDN profil, du vil bruge til dette selvstudium, kan du gå videre til [trin 3](#step-3-create-a-new-cdn-endpoint).

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="step-3-create-a-new-cdn-endpoint"></a>Trin 3: Oprette et nyt CDN slutpunkt

**Oprette et nyt CDN slutpunkt for kontoen lagerplads**

1. Gå til din CDN profil i [Azure Management Portal](https://portal.azure.com).  Du kan have fastgjort den til dashboardet i det forrige trin.  Hvis du ikke, kan du finde det ved at klikke på **Gennemse**og derefter **CDN profiler**og klikke på den profil, du vil tilføje dine slutpunkt til.

    Bladet CDN profil vises.

    ![LEVERANDØR(er) profil][cdn-profile-settings]

2. Klik på knappen **Tilføj slutpunkt** .

    ![Slutpunkt knappen Tilføj][cdn-new-endpoint-button]

    **Tilføj et slutpunkt** blade vises.

    ![Tilføje slutpunkt blade][cdn-add-endpoint]

3. Angiv et **navn** for dette CDN slutpunkt.  Dette navn bruges til at få adgang til dine cachelagrede ressourcer på domænet `<endpointname>.azureedge.net`.

4. Vælg *lagerplads*i rullemenuen **Origin type** .  

5. Vælg kontoen lagerplads i rullemenuen **Origin hostname** .

6. Lad standardindstillingerne for **Origin sti**, **Origin host sidehoved**og **protokol/Origin port**.  Du skal angive mindst én protocol (HTTP eller HTTPS).

    > [AZURE.NOTE] Denne konfiguration gør det muligt for alle dine offentligt synlige beholdere i kontoen lagerplads for cachelagring af i CDN.  Hvis du vil begrænse omfanget til en enkelt objektbeholder, skal du bruge **Origin sti**.  Bemærk objektbeholderen skal have synligheden sat til offentlig.

7. Klik på knappen **Tilføj** for at oprette nyt slutpunkt.

8. Når slutpunktet er oprettet, vises det på en liste med slutpunkter til profilen. Listevisningen viser URL-adressen skal bruges til at få adgang til cachelagret indhold samt origin domænet.

    ![LEVERANDØR(er) slutpunkt][cdn-endpoint-success]

    > [AZURE.NOTE] Slutpunktet bliver med det samme ikke tilgængelig til brug.  Det kan tage op til 90 minutter registrering at overføre via CDN netværket. Brugere, der forsøger at bruge domænenavnet CDN umiddelbart kan modtage statuskode 404, indtil indholdet er tilgængelig via CDN.


## <a name="step-4-access-cdn-content"></a>Trin 4: Access CDN indhold

Brug på CDN URL-adressen på portalen for at få adgang til cachelagrede indhold på CDN. Adressen for en cachelagrede blob bliver ligner følgende:

http://<*EndpointName*\>.azureedge.net/ <*myPublicContainer*\>/<*BlobName*\>

> [AZURE.NOTE] Når du aktiverer CDN adgang til en lagerplads konto eller hostet service, alle offentligt tilgængelige objekter, der kan bruges til cachelagring af CDN kant. Hvis du redigerer et objekt, der er i øjeblikket cachelagret i CDN, er det nye indhold er ikke tilgængelige via CDN, indtil CDN opdaterer dens indhold, når cachelagret indhold time to live udløber.

## <a name="step-5-remove-content-from-the-cdn"></a>Trin 5: Fjerner indhold fra CDN

Hvis du ikke længere vil cache et objekt i Azure indhold levering netværk (CDN), kan du udføre én af følgende fremgangsmåder:

-   Du kan foretage objektbeholderen privat i stedet for vise. Du kan få flere oplysninger i [Administrer anonym adgang til beholdere og BLOB](../storage/storage-manage-access-to-resources.md) .
-   Du kan deaktivere eller slette CDN slutpunktet ved hjælp af portalen administration.
-   Du kan ændre din hostet tjeneste for at besvare ikke længere anmodninger til objektet.

Et objekt, der allerede er gemt i CDN, forbliver cachelagrede, indtil på time to live til objektet er udløbet eller slutpunktet fjernes. Når den time to live periode udløber, kontrollerer CDN for at se, om CDN slutpunktet er stadig er gyldig, og det objekt, der er stadig anonym adgang. Hvis det ikke er, cachelagres den objektet ikke længere.


## <a name="additional-resources"></a>Yderligere ressourcer

-   [Hvordan du knytter CDN indhold til et brugerdefineret domæne](cdn-map-content-to-custom-domain.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

[cdn-profile-settings]: ./media/cdn-create-a-storage-account-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-a-storage-account-with-cdn/cdn-endpoint-success.png
