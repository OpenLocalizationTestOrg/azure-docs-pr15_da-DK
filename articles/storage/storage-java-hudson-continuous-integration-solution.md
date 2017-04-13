<properties
    pageTitle="Sådan bruges Hudson med Blob-lager | Microsoft Azure"
    description="Beskriver, hvordan du bruger Hudson med Azure Blob-lager som et lager til build elementer."
    services="storage"
    documentationCenter="java"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/18/2016" 
    ms.author="dinesh"/>

# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Brug af Azure-lager med en Hudson fortløbende Integration løsning

## <a name="overview"></a>Oversigt

Følgende oplysninger viser, hvordan du bruger Blob-lager som et lager af build-elementer, der er oprettet af en Hudson fortløbende Integration (CI)-løsning eller som en kilde, som du kan downloade filer til at bruges i en proces i build. Et af de scenarier, hvor du vil finde dette nyttige er, når du kodningssprog i en fleksible udviklingsmiljø (ved hjælp af Java eller andre sprog), builds forløber baseret på fortløbende integration, og du skal bruge et lager til din build elementer, så du kan, for eksempel dele dem med andre organisationen som medlemmer, dine kunder, og vedligeholde et arkiv.  Et andet scenarie er, når build tingene selve kræver, at andre filer, for eksempel afhængigheder hente som en del af den nyeste version af input.

Du skal bruge plug-in'en til Azure-lager for Hudson CI gjort tilgængelige fra Microsoft i dette selvstudium.

## <a name="introduction-to-hudson"></a>Introduktion til Hudson ##

Hudson aktiverer fortløbende integration af et projekt software idet udviklerne kan nemt integrere deres kodeændringer og har builds produceret automatisk og ofte, hvilket øger produktiviteten hos udviklerne. Builds er versionsnummer, og Opret elementer kan overføres til forskellige typer lagre. I denne artikel viser, hvordan du bruger Azure Blob-lager som lager af build-elementer. Det også viser, hvordan du henter afhængigheder fra Azure Blob-lager.

Kan finde flere oplysninger om Hudson på [Opfylder Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>Fordelene ved hjælp af tjenesten Blob ##

Fordelene ved hjælp af tjenesten Blob til at hoste din fleksible udvikling build elementer omfatter:

- Høj tilgængelighed af din build elementer og/eller kan downloades afhængigheder.
- Ydeevnen, når din Hudson CI-løsning overfører din build elementer.
- Ydeevnen, når dine kunder og partnere henter dine build elementer.
- Styre adgang brugerpolitikker, med et udvalg mellem anonym adgang, udløb-baserede delt adgang signatur access, privat, adgang osv.

## <a name="prerequisites"></a>Forudsætninger ##

Du skal bruge følgende for at bruge tjenesten Blob sammen med din Hudson CI-løsning:

- En løsning, Hudson fortløbende Integration.

    Hvis du ikke aktuelt har en Hudson CI-løsning, kan du køre en Hudson CI-løsning med følgende metode:

    1. Download Hudson KRIG fra <http://hudson-ci.org/>på en Java-aktiverede maskine.
    2. Kør Hudson KRIG ved en kommandoprompt, der er åbnet til den mappe, der indeholder den Hudson KRIG. Hvis du har hentet version 3.1.2 f.eks.

        `java -jar hudson-3.1.2.war`

    3. I din browser skal du åbne `http://localhost:8080/`. Dette åbner Hudson dashboard.

    4. Fuldføre indledende konfigurationen på ved første brug af Hudson `http://localhost:8080/`.

    5. Efter du har fuldført den indledende installation, annullere den igangværende forekomst af den Hudson KRIG, start Hudson KRIG igen og genåbnet dashboardet Hudson `http://localhost:8080/`, som du skal bruge til at installere og konfigurere plug-in'en til Azure-lager.

        Mens en typisk Hudson CI-løsning skal konfigureres til at køre som en tjeneste, kører Hudson krig på kommandolinjen bliver tilstrækkelige til dette selvstudium.

- En Azure-konto. Du kan tilmelde dig en Azure-konto på <http://www.azure.com>.

- En konto, Azure-lager. Hvis du ikke allerede har en lagerplads-konto, kan du oprette én ved hjælp af trin på [Opret en lagerplads konto](storage-create-storage-account.md#create-a-storage-account).

- Kendskab til Hudson CI-løsningen er anbefales, men ikke påkrævet, da følgende indhold skal bruge et eksempel på grundlæggende til at vise de trin, der er påkrævet ved brug af tjenesten Blob som et lager til Hudson CI opbygge elementer.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Sådan bruges tjenesten Blob sammen med Hudson CI ##

Hvis du vil bruge tjenesten Blob med Hudson, skal du installere plug-in'en til Azure-lager, konfigurere plug-in'et for at bruge din lagerplads konto og derefter oprette en efter build handling, der overfører din build elementer til kontoen lagerplads. Disse trin er beskrevet i følgende afsnit.

## <a name="how-to-install-the-azure-storage-plugin"></a>Hvordan du installerer Plug-in'en til Azure-lager ##

1. Klik på **Administrer Hudson**i dashboardet Hudson.
2. Klik på **Administrer plug-ins**på siden **Administrer Hudson** .
3. Klik på fanen **tilgængelig** .
4. Klik på **andre**.
5. Vælg **Microsoft Azure-lager-plug-in**i sektionen **Genstand Uploaders** .
6. Klik på **Installer**.
7. Når installationen er fuldført, skal du genstarte Hudson.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Sådan konfigureres Azure-lager-plug-in for at bruge din lagerplads konto ##

1. Klik på **Administrer Hudson**i dashboardet Hudson.
2. Klik på **Konfigurer System**på siden **Administrer Hudson** .
3. I afsnittet **Konfiguration af Microsoft Azure lagerplads konto** :

    en. Angive dit kontonavn lager, som du kan få fra [Azure-portalen](https://portal.azure.com).

    b. Angiv din lagerplads kontonøgle, også opnås fra [Azure-portalen](https://portal.azure.com).

    c. Brug standardværdien for **Blob Service slutpunkt URL-adresse** , hvis du bruger offentlige Azure skyen. Hvis du bruger en anden Azure sky, kan du bruge slutpunktet som angivet i [Azure Portal](https://portal.azure.com) for kontoen lagerplads.

    d. Klik på **Valider lagerplads legitimationsoplysninger** for at validere kontoen lagerplads.

    e. [Valgfrit] Hvis du har ekstra lagerplads konti, du vil stilles til rådighed for din Hudson CI, kan du klikke på **tilføje yderligere lagerplads konti**.

    f. Klik på **Gem** for at gemme indstillingerne.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Sådan oprettes en efter build handling, der overfører din build elementer til kontoen lagerplads ##

Med henblik på instruktion først skal vi oprette et job, der skal oprette flere filer, og Tilføj derefter i handlingen efter build til at overføre filerne til kontoen lagerplads.

1. Klik på **Nyt Job**i dashboardet Hudson.
2. Navngiv jobbet **MyJob**, klik på **Generer et job - typografi software**, og klik derefter på **OK**.
3. Klik på **Tilføj Generer trin** i afsnittet **oprette** i jobbet konfigurationen og vælg **udføre Windows batchnummer-kommando**.
4. Brug følgende kommandoer i **kommandoen**:

        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt

5. Klik på **Overfør elementer til Microsoft Azure Blob-lager**i afsnittet **Efter build handlinger** i jobkonfigurationen.
6. Vælg kontoen lagerplads til brug for **Kontonavn-lager**.
7. Angiv objektbeholdernavnet for **Navnet på objektbeholderen**. (Objektbeholderen, oprettes, hvis den ikke allerede findes når build elementer er blevet overført.) Du kan bruge miljøvariabler, så Indtast **${JOB_NAME}** som objektbeholdernavnet i dette eksempel.

    **Tip!**

    Under **kommandoen** er sektion, hvor du har angivet et script til at **udføre Windows batchnummer kommandoen** et link til de miljøvariabler, som genkendes af Hudson. Klik på hyperlinket for at lære miljø variable navne og beskrivelser. Bemærk, at miljøvariabler, der indeholder specialtegn, som miljøvariablen **BUILD_URL** , ikke er tilladt som en objektbeholder navn eller almindelige virtuel sti.

8. Klik på **Udgiv ny objektbeholder som standard** i dette eksempel. (Hvis du vil bruge en privat objektbeholder, du skal oprette en delt adgang signatur til at give adgang. Det er ikke medtaget i denne artikel. Du kan få mere at vide om delt adgang signaturer på [Ved hjælp af delte Access signaturer (SAS)](storage-dotnet-shared-access-signature-part-1.md).)
9. [Valgfrit] Klik på **ren objektbeholder før du overfører** , hvis du vil objektbeholderen der skal ryddes af indholdet, før der overføres build elementer (Lad den være umarkerede Hvis du ikke vil rydde indholdet af beholderen).
10. **Liste over elementer til at overføre**, Angiv * *tekst /*.txt**.
11. **Almindelige virtuelle sti for overførte elementer**, Angiv **${OPBYGGE\_ID} / ${OPBYGGE\_tal}**.
12. Klik på **Gem** for at gemme indstillingerne.
13. Klik på **Opret nu** for at køre **MyJob**i dashboardet Hudson. Undersøge console output til status. Statusmeddelelser for Azure-lager medtages i output fra konsollen, når handlingen efter build begynder at overføre build elementer.
14. Du kan undersøge build elementer ved at åbne den offentlige blob efter vellykket fuldførelse af jobbet.

    en. Log på [Azure-portalen](https://portal.azure.com).

    b. Klik på **lagerplads**.

    c. Klik på kontonavnet lagerplads, som du har brugt til Hudson.

    d. Klik på **beholdere**.

    e. Klik på beholderen med navnet **myjob**, som er den små version af det jobnavn, du har tildelt, da du oprettede Hudson jobbet. Navne på objektbeholder og blob er små bogstaver (og store og små bogstaver) i Azure-lager. I listen over BLOB for objektbeholderen med navnet **myjob** skal du se afsnittet **hello.txt** og **date.txt**. Kopiere URL-adressen til en af disse elementer og åbne den i din browser. Du får vist den tekstfil, der blev overført som en build genstand.

Kun én efter build handling, der overfører elementer til Azure Blob-lager kan oprettes per job. Bemærk, at handlingen enkelt efter build til at overføre elementer til Azure Blob-lager kan angive andre filer (herunder jokertegn) og stier til filer i **Listen over elementer til at overføre** ved hjælp af et semikolon som separator. Eksempelvis hvis din Hudson build giver glas filer og TXT-filer i dit arbejdsområde **opbygge** mappe, og du vil overføre begge til Azure Blob-lager, bruge følgende til **Listen over elementer til at overføre** værdien: **opbygge /\*.jar; build /\*.txt**. Du kan også bruge dobbelt kolon syntaks til at angive en sti til brug i blob-navn. For eksempel krukker til at få overført bruge **binære filer** i blob stien og TXT-filer til at få overført ved hjælp af **meddelelser** i blob stien skal du bruge følgende for værdien af **Listen over elementer til at overføre** : **opbygge /\*. jar::binaries; build /\*. txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Sådan oprettes et build trin, der er hentet fra Azure Blob-lager ##

Følgende trin viser, hvordan du konfigurerer et build trin for at hente elementer fra Azure Blob-lager. Dette er nyttigt, hvis du vil medtage elementer i din build, for eksempel krukker, der findes i Azure Blob-lager.

1. Klik på **Tilføj Generer trin** i afsnittet **oprette** i jobbet konfigurationen og vælg **Download fra Azure Blob-lager**.
2. Vælg kontoen lagerplads til brug for **kontonavn-lager**.
3. Angiv navnet på objektbeholderen, der indeholder de blob, du vil hente for **navnet på objektbeholderen**. Du kan bruge miljøvariabler.
4. Angiv navnet blob for **Blob navn**. Du kan bruge miljøvariabler. Du kan også bruge en stjerne, som et jokertegn, når du har angivet de første bogstaver af blob navn. For eksempel **project\* ** ville angive alle blob, hvis navne starter med **project**.
5. [Valgfrit] For at **hente stien**, skal du angive stien på den Hudson maskine, hvor du vil hente filer fra Azure Blob-lager. Miljøvariabler kan også bruges. (Hvis du ikke angiver en værdi for at **hente sti**, filerne fra Azure Blob-lager overføres til det job arbejdsområde.)

Hvis du har flere elementer, der skal hentes fra Azure Blob-lager, kan du oprette ekstra build trin.

Når du kører en build, kan du kontrollere build historik console output, eller se på din placeringen af overførslen, at se, om BLOB du forventede blev hentet.

## <a name="components-used-by-the-blob-service"></a>Komponenter, der bruges af tjenesten Blob ##

Følgende indeholder en oversigt over Blob service komponenterne.

- **Lagerplads konto**: alle adgang til Azure-lager sker via en lagerplads konto. Dette er det højeste niveau af navneområdet for at få adgang til BLOB. Et firma kan indeholde et ubegrænset antal beholdere, som deres samlede størrelse er under 100 TB.
- **Objektbeholder**: en objektbeholder indeholder en gruppering af et sæt af BLOB. Alle BLOB skal være i en beholder. Et firma kan indeholde et ubegrænset antal beholdere. En objektbeholder kan gemme et ubegrænset antal BLOB.
- **BLOB**: en fil med en hvilken som helst type og størrelse. Der findes to typer blob, der kan være gemt i Azure-lager: Bloker og siden BLOB. De fleste filer er Bloker BLOB. En enkelt blok blob kan være op til 200 GB. Dette selvstudium bruger Bloker BLOB. Siden blob, en anden blob type kan være op til 1 TB i størrelse, og som mere effektivt, når områder af byte i en fil er blevet redigeret ofte. Se [forstå Bloker blob, tilføje BLOB og siden BLOB](http://msdn.microsoft.com/library/azure/ee691964.aspx)kan finde flere oplysninger om BLOB.
- **URL-format**: BLOB er tilgængelige i følgende URL-format:

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`

    (Formatet ovenstående gælder for offentlige Azure skyen. Hvis du bruger en anden Azure sky, Brug slutpunktet i [Azure Portal](https://portal.azure.com) til at bestemme URL-adressen slutpunktet.)

    I formatet ovenfor, `storageaccount` repræsenterer navnet på kontoen lagerplads `container_name` repræsenterer navnet på din objektbeholder og `blob_name` repræsenterer navnet på din blob henholdsvis. Du kan have forskellige stier, adskilt med en skråstreg i objektbeholdernavnet på **/**. Objektbeholder eksempelnavn i dette selvstudium blev **MyJob**og **${OPBYGGE\_ID} / ${OPBYGGE\_tal}** blev brugt til den almindelige virtuelle sti, hvilket resulterer i blob har en URL-adresse i formularen følgende:

    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Næste trin

- [Opfylde Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
- [Azure-lager SDK til Java](https://github.com/azure/azure-storage-java)
- [Azure-lager klient SDK Reference](http://dl.windowsazure.com/storage/javadoc/)
- [Azure-lager Services REST-API](https://msdn.microsoft.com/library/azure/dd179355.aspx )
- [Azure-lager-teamets Blog](http://blogs.msdn.com/b/windowsazurestorage/)

Yderligere oplysninger finder du også se [Java Developer Center](https://azure.microsoft.com/develop/java/).