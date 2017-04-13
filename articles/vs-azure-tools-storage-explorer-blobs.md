<properties
    pageTitle="Administrere Azure Blob-lager ressourcer med lagerplads Stifinder (Preview) | Microsoft Azure"
    description="Administrere Azure Blob beholdere og BLOB med lagerplads Stifinder (Preview)"
    services="storage"
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
    ms.date="08/17/2016"
    ms.author="tarcher" />

# <a name="manage-azure-blob-storage-resources-with-storage-explorer-preview"></a>Administrere Azure Blob-lager ressourcer med lagerplads Stifinder (Preview)

## <a name="overview"></a>Oversigt

[Azure Blob-lager](./storage/storage-dotnet-how-to-use-blobs.md) er en tjeneste til lagring af store mængder ustrukturerede data, som tekst eller binære data, der kan åbnes fra et vilkårligt sted i verden via HTTP eller HTTPS.
Du kan bruge Blob-lager, til at vise data offentligt for verden eller til at gemme programmet data privat. I denne artikel lærer du, hvordan du bruger lagerplads Explorer (Preview) til at arbejde med blob beholdere og BLOB.

## <a name="prerequisites"></a>Forudsætninger

For at fuldføre trinnene i denne artikel, skal du bruge følgende:

- [Downloade og installere Storage Explorer (preview)](http://www.storageexplorer.com)
- [Oprette forbindelse til en Azure lagerplads konto eller tjeneste](./vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Oprette en blob objektbeholder

Alle BLOB skal placeres i en blob beholder, som er blot en logisk gruppering af BLOB. Et firma kan indeholde et ubegrænset antal beholdere og hver beholder kan gemme et ubegrænset antal BLOB.

Følgende trin viser, hvordan du opretter en blob objektbeholder i Storage Explorer (Preview).

1.  Åbn Storage Explorer (Preview).
1.  Udvid inden for hvilken du vil oprette objektbeholderen blob storage kontoen i venstre rude.
1.  Højreklik på **Blob beholdere**, og - i kontekstmenuen - Vælg **Oprette Blob objektbeholder**.

    ![Oprette blob beholdere genvejsmenu][0]

1.  En tekstboks vises under mappen **Blob beholdere** . Skriv navnet på din blob objektbeholder. Se afsnittet [objektbeholder naming regler](./storage/storage-dotnet-how-to-use-blobs.md#create-a-container) for en liste af regler og begrænsninger på navngive blob beholdere.

    ![Oprette Blob beholdere tekstfelt][1]

1.  Tryk på **Enter** når færdig til at oprette objektbeholderen blob eller **Esc for** at annullere. Når objektbeholderen blob er blevet oprettet, vises den under mappen **Blob beholdere** for den valgte lagerplads konto.

    ![BLOB beholder oprettet][2]

## <a name="view-a-blob-containers-contents"></a>Få vist indholdet af en blob objektbeholder

BLOB beholdere indeholder BLOB og mapper (, kan også indeholde BLOB).

Følgende trin viser, hvordan du får vist indholdet af en blob objektbeholder i Storage Explorer (Preview):

1.  Åbn Storage Explorer (Preview).
1.  Udvid lagerplads-konto, der indeholder objektbeholderen blob du vil have vist i venstre rude.
1.  Udvid lagerplads firmaets **Blob beholdere**.
1.  Højreklik på den blob beholder, du vil have vist, og - i kontekstmenuen - Vælg **Åbn Blob objektbeholder Editor**.
Du kan også dobbeltklikke på objektbeholderen blob, du vil have vist.

    ![Åbn blob objektbeholder editor genvejsmenu][19]

1.  Ruden primære vises objektbeholderen blob indholdet.

    ![BLOB objektbeholder editor][3]

## <a name="delete-a-blob-container"></a>Slette en blob objektbeholder

BLOB beholdere kan nemt oprettede og slettede efter behov. (For at se, hvordan du kan slette enkelte BLOB skal referere til sektionen, [administrere BLOB i en blob objektbeholder](./#managing-blobs-in-a-blob-container).)

Følgende trin viser, hvordan du sletter en blob objektbeholder i Storage Explorer (Preview):

1.  Åbn Storage Explorer (Preview).
1.  Udvid lagerplads-konto, der indeholder objektbeholderen blob du vil have vist i venstre rude.
1.  Udvid lagerplads firmaets **Blob beholdere**.
1.  Højreklik på den blob beholder, du vil slette, og - i kontekstmenuen - Vælg **Slet**.
Du kan også trykke på **Slet** for at slette den aktuelt markerede blob beholder.

    ![Slette blob objektbeholder genvejsmenu][4]

1.  Vælg **Ja** i dialogboksen bekræftelse.

    ![Slette blob objektbeholder bekræftelse][5]

## <a name="copy-a-blob-container"></a>Kopiere en blob objektbeholder

Storage Explorer (Preview) gør det muligt at kopiere en blob objektbeholder til Udklipsholder, og derefter indsætte objektbeholderen blob i en anden lagerplads konto. (For at se, hvordan du kopierer individuelle BLOB skal referere til sektionen, [administrere BLOB i en blob beholder](./#managing-blobs-in-a-blob-container).)

Følgende trin viser, hvordan du kopierer en objektbeholder blob fra én lagerplads konto til en anden.

1.  Åbn Storage Explorer (Preview).
1.  Udvid lagerplads-konto, der indeholder blob beholderen, du vil kopiere, i venstre rude.
1.  Udvid lagerplads firmaets **Blob beholdere**.
1.  Højreklik på den blob beholder, du vil kopiere, og - i kontekstmenuen - Vælg **Kopiér Blob objektbeholder**.

    ![Kopiere blob objektbeholder genvejsmenu][6]

1.  Højreklik på den ønskede "mål" lagerplads konto, som du vil indsætte objektbeholderen blob, og - i kontekstmenuen - Vælg **Indsæt Blob objektbeholder**.

    ![Genvejsmenu for Sæt ind blob objektbeholder][7]

## <a name="get-the-sas-for-a-blob-container"></a>Få Sikkerhedstilknytningerne til en blob objektbeholder

En [delt adgang signatur (SAS)](./storage/storage-dotnet-shared-access-signature-part-1.md) giver delegeret adgang til ressourcer i kontoen lagerplads.
Det betyder, at du kan give en klient begrænsede tilladelser til objekter på din lagerplads konto i en angivet periode tid og med et bestemt sæt tilladelser, uden at dele din konto access-taster.

Følgende trin viser, hvordan du opretter en SAS for en blob objektbeholder:

1.  Åbn Storage Explorer (Preview).
1.  Udvid kontoen lagerplads med objektbeholderen blob, du vil hente en SAS i venstre rude.
1.  Udvid lagerplads firmaets **Blob beholdere**.
1.  Højreklik på den ønskede blob beholder, og - i kontekstmenuen - Vælg **Få delt Access-signatur**.

    ![Få SAS genvejsmenu][8]

1.  Angiv den ønskede politik, start- og slutdatoer og tidszone i dialogboksen **Delt Access signatur** , og få adgang til niveauer, du vil for ressourcen.

    ![Se SAS indstillinger][9]

1.  Når du er færdig med at angive indstillingerne SAS, skal du vælge **Opret**.

1.  Dialogboks med en anden **Delt Access signatur** vises derefter, der viser objektbeholderen blob sammen med URL-adresse og QueryStrings, du kan bruge til at få adgang til lagerplads ressourcen.
Klik på **Kopiér** ud for den URL-adresse, du vil kopiere til Udklipsholder.

    ![Kopiere SAS URL-adresser][10]

1.  Vælg **Luk**, når færdig.

## <a name="manage-access-policies-for-a-blob-container"></a>Administrere politikker for adgang til en blob objektbeholder

Følgende trin viser, hvordan du administrerer (tilføje og fjerne) få adgang til politikker for en blob objektbeholder:

1.  Åbn Storage Explorer (Preview).
1.  Udvid lagerplads konto, der indeholder blob beholderen, du vil administrere politikker i venstre rude.
1.  Udvid lagerplads firmaets **Blob beholdere**.
1.  Vælg den ønskede blob beholder, og - i kontekstmenuen - Vælg **Administrer Access politikker**.

    ![Administrere adgang politikker genvejsmenu][11]

1.  Dialogboksen **Access politikker** vises access politikker allerede har oprettet for objektbeholderen markerede blob.

    ![Få adgang til indstillinger for politik][12]        

1.  Følg disse trin afhængigt af opgaven med access politik for administration:

    - **Tilføj en ny politik for access** – Vælg derefter **Tilføj**. Når den er oprettet, vises dialogboksen **Access politikker** for den tilføjede access-politik (med standardindstillinger).
    - **Redigere en politik for access** - foretage de ønskede ændringer, og vælg **Gem**.
    - **Fjerne en politik for access** - Vælg **Fjern** ud for den access-politik, du vil fjerne.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Angiv niveauet for offentlig adgang til en blob objektbeholder

Alle blob objektbeholder er som standard angivet til "Ingen offentlig adgang".

I følgende trin illustreres at angive et offentligt adgangsniveau for en blob objektbeholder.

1.  Åbn Storage Explorer (Preview).
1.  Udvid lagerplads konto, der indeholder objektbeholderen blob Hvis du vil administrere politikker i venstre rude.
1.  Udvid lagerplads firmaets **Blob beholdere**.
1.  Vælg den ønskede blob beholder, og - i kontekstmenuen - Vælg **Angive offentlige adgangsniveau**.

    ![Angive offentlig adgang niveau genvejsmenu][13]

1.  Angiv det ønskede adgangsniveau i dialogboksen **Angiv objektbeholder offentlige adgangsniveau** .

    ![Angive indstillinger for offentlig adgang niveau][14]

1.  Vælg **Anvend**.

## <a name="managing-blobs-in-a-blob-container"></a>Administrere BLOB i en blob objektbeholder

Når du har oprettet en blob objektbeholder, kan du overføre en blob til blob objektbeholderen, hente en blob til din lokale computer, Åbn en blob på din lokale computer, og meget mere.

Følgende trin illustrerer, hvordan du administrerer BLOB (og mapper) i en blob objektbeholder.

1.  Åbn Storage Explorer (Preview).
1.  Udvid lagerplads-konto, der indeholder objektbeholderen blob, du vil administrere i venstre rude.
1.  Udvid lagerplads firmaets **Blob beholdere**.
1.  Dobbeltklik på den blob beholder, du vil have vist.
1.  Ruden primære vises objektbeholderen blob indholdet.

    ![Vis blob objektbeholder][3]

1.  Ruden primære vises objektbeholderen blob indholdet.

1.  Følg disse trin afhængigt af den opgave, du vil udføre:

    - **Overføre filer til en blob objektbeholder**

        1.  Vælg **Overfør**og derefter **Overføre filer** på rullemenuen på værktøjslinjen i den primære rude.

            ![Overføre filer i menuen][15]

        1.  Vælg knappen ellipse (**...**) i højre side af tekstboksen **filer** for at markere de filer, du vil overføre, i dialogboksen **overføre filer** .

            ![Overføre filer indstillinger][16]

        1.  Angiv typen af **Blob type**. I artiklen [Introduktion til Azure Blob-lager ved hjælp af .NET](./storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) forklares forskellene mellem de forskellige typer blob.

        1.  Du kan også angive en destinationsmappe, hvori de markerede filer vil blive overført. Hvis destinationsmappen ikke findes, oprettes den.

        1.  Vælg **Overfør**.

    - **Overføre en mappe til en blob objektbeholder**

        1.  Vælg **Overfør**og derefter **Overføre mappe** på rullelisten på værktøjslinjen i den primære rude.

            ![Overføre menuen mappe][17]

        1.  Vælg knappen ellipse (**...**) i højre side af tekstfeltet **mappe** for at vælge den mappe, hvis du vil overføre indhold i dialogboksen **overføre mappe** .

            ![Overføre Mappeindstillinger][18]

        1.  Angiv typen af **Blob type**. I artiklen [Introduktion til Azure Blob-lager ved hjælp af .NET](./storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) forklares forskellene mellem de forskellige typer blob.

        1.  Du kan også angive en destinationsmappe, hvor den valgte mappe indholdet vil blive overført. Hvis destinationsmappen ikke findes, oprettes den.

        1.  Vælg **Overfør**.

    - **Hente en blob til din lokale computer**

        1.  Vælg den blob, du vil hente.

        1.  Vælg **Hent**på værktøjslinjen i den primære rude.

        1.  Angiv den placering, hvor du vil blob hentes i dialogboksen **Angiv, hvor du vil gemme de hentede blob** , og navnet, du vil give den.  

        1.  Vælg **Gem**.

    - **Åbne en blob på din lokale computer**

        1.  Vælg den blob, du vil åbne.

        1.  Vælg **Åbn**på værktøjslinjen i den primære rude.

        1.  Blob, der hentes og åbnes ved hjælp af det program, der er knyttet til den blob underliggende filtype.

    - **Kopiere en blob til Udklipsholder**

        1.  Vælg den blob, du vil kopiere.

        1.  Vælg **Kopiér**på værktøjslinjen i den primære rude.

        1.  Gå til en anden blob objektbeholder i venstre rude, og dobbeltklik på den for at få den vist i ruden primære.

        1.  Vælg **Indsæt** til at oprette en kopi af blob på værktøjslinjen i den primære rude.

    - **Slette en blob**

        1.  Vælg den blob, du vil slette.

        1.  Vælg **Slet**på værktøjslinjen i den primære rude.

        1.  Vælg **Ja** i dialogboksen bekræftelse.

## <a name="next-steps"></a>Næste trin

- Få vist de [seneste Storage Explorer (Preview) produktbemærkninger og videoer](http://www.storageexplorer.com).
- Lær, hvordan du kan [oprette programmer med Azure blob, tabeller, køer og filer](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png