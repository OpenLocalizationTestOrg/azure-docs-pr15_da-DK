<properties
    pageTitle="Introduktion til Storage Explorer (Preview) | Microsoft Azure"
    description="Administrere Azure lagerplads ressourcer med lagerplads Stifinder (Preview)"
    services="storage"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />

 <tags
    ms.service="storage"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/17/2016"
    ms.author="tarcher" />

# <a name="getting-started-with-storage-explorer-preview"></a>Introduktion til Storage Explorer (Preview)

## <a name="overview"></a>Oversigt 

Microsoft Azure-lager Explorer (Preview) er en enkeltstående app, der gør det muligt at arbejde ubesværet med Azure-lager data på Windows, OS X og Linux. I denne artikel lærer du de forskellige metoder til at oprette forbindelse til og administration af kontiene Azure-lager.

![Microsoft Azure-lager Explorer (Preview)][15]

## <a name="prerequisites"></a>Forudsætninger

- [Downloade og installere Storage Explorer (preview)](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Oprette forbindelse til en lagerplads konto eller tjeneste

Storage Explorer (Preview) indeholder en et væld måder at oprette forbindelse til lagerplads konti. Dette omfatter oprette forbindelse til lagerplads konti, der er knyttet til din Azure abonnementer, oprette forbindelse til lagerplads konti og tjenester, der deles fra andre Azure abonnementer og endda oprette forbindelse til og administration af lokale lager, med Azure lagerplads emulatoren:

- [Opret forbindelse til et Azure abonnement](#connect-to-an-azure-subscription) - Administrer lagerplads ressourcer, der hører til abonnementet Azure.
- [Arbejde med lokale udvikling lagerplads](#work-with-local-development-storage) - administrere lokale lager, med Azure lagerplads emulatoren. 
- [Vedhæft til eksterne lager](#attach-or-detach-an-external-storage-account) - Administrer lagerplads ressourcer, der tilhører en anden Azure-abonnement ved at lagerplads firmaets kontonavn og nøgle.
- [Vedhæft lagerplads konto ved hjælp af SAS](#attach-storage-account-using-sas) - Administrer lagerplads ressourcer, der hører til et andet Azure abonnement via en SAS.
- [Vedhæft tjenesten ved hjælp af SAS](#attach-service-using-sas) - administrere en bestemt lagringstjeneste (blob objektbeholder, kø eller tabel) der hører til et andet Azure abonnement via en SAS.

## <a name="connect-to-an-azure-subscription"></a>Oprette forbindelse til et Azure-abonnement

> [AZURE.NOTE] Hvis du ikke har en Azure-konto, kan du [tilmelde dig en gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivere dine Visual Studio abonnement fordele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. I lagerplads Explorer (Preview) skal du vælge **Azure kontoindstillinger**. 

    ![Azure kontoindstillinger][0]

1. Ruden til venstre, vises alle de du har logget på Microsoft-konti. Vælg **Tilføj en konto**, og følg dialogboksene at logge på med en Microsoft-konto, der er knyttet til mindst én aktivt Azure abonnement for at oprette forbindelse til en anden konto.

    ![Tilføje en konto][1]

1. Når du logge på med en Microsoft-konto, udfylder ruden til venstre med de Azure abonnementer, der er knyttet til den pågældende konto. Vælg de Azure abonnementer, som du vil arbejde, og vælg derefter **Anvend**. (At markere **alle abonnementer** slår markering af alle eller ingen af de viste Azure abonnementer).

    ![Vælg Azure abonnementer][3]

1. Ruden til venstre vises nu kontiene lagerplads, der er knyttet til de markerede Azure abonnementer.

    ![Azure abonnementerne][4]

## <a name="work-with-local-development-storage"></a>Arbejde med lokale udvikling lagerplads

Storage Explorer (Preview) gør det muligt for dig at arbejde med lokale lager, med Azure lagerplads emulatoren. Dette kan du skrive programkode mod og teste lagerplads nødvendigvis uden en lagerplads-konto, der er installeret på Azure (da kontoen lagerplads er der emulerer Azure lagerplads emulatoren).

>[AZURE.NOTE] Azure-lager emulatoren understøttes i øjeblikket kun til Windows. 

1. Udvid i venstre rude af lagerplads Explorer (Preview) på **(lokal og tilknyttet** > **Lagerplads konti** > **(udvikling)** node.

    ![Lokale udvikling node][21]

1. Hvis du endnu ikke har installeret Azure lagerplads emulatoren, bliver du bedt om at gøre dette via et oplysningspanel. Hvis der vises på oplysningspanelet, klik på **Hent den nyeste version**og installere emulatoren. 

    ![Hente Azure lagerplads Emulator prompt][22]

1. Når emulatoren er installeret, har du mulighed for at oprette og arbejde med lokale blob, forespørgsler og tabeller. For at lære at arbejde med hver kontotype lagerplads, skal du vælge på det relevante link nedenfor:

    - [Administrere Azure blob storageressourcer](./vs-azure-tools-storage-explorer-blobs.md)
    - Administrere Azure fil lagerplads ressourcedeling - *kommer snart*
    - Administrere Azure kø lagerplads ressourcer - *kommer snart*
    - Administrere Azure table storageressourcer - *kommer snart*

## <a name="attach-or-detach-an-external-storage-account"></a>Tilknytte eller fjerne en ekstern storage-konto

Storage Explorer (Preview) giver mulighed for at knytte til ekstern storage konti så nemt kan deles lagerplads konti. Dette afsnit beskriver, hvordan du vedhæfte til (og afbryde forbindelsen) ekstern storage konti.

### <a name="get-the-storage-account-credentials"></a>Få kontolegitimationsoplysninger lagerplads

For at dele en ekstern storage-konto, skal ejeren af kontoen først få legitimationsoplysningerne - kontonavn og nøgle - for kontoen og derefter dele disse oplysninger med den person, som ønsker at knytte til den (eksterne) konto. Hente lagerplads kontolegitimationsoplysninger kan udføres via Azure-portalen ved at følge disse trin: 

1.  Log på [Azure-portalen](https://portal.azure.com).
1.  Vælg **Gennemse**.
1.  Vælg **lagerplads konti**.
1.  Vælg den ønskede lagerplads konto i bladet **Lagerplads konti** .
1.  Vælg **hurtigtaster**i bladet **Indstillinger** for den valgte lagerplads konto.

    ![Taster adgangsindstilling][5]
    
1.  Kopiere de **KONTONAVN lager** og **nøgle 1** værdier til brug i bladet **hurtigtaster** når vedhæfte til kontoen lagerplads. 

    ![Access-taster][6]

### <a name="attach-to-an-external-storage-account"></a>Vedhæfte en ekstern storage-konto
Hvis du vil knytte til en ekstern storage-konto, skal du firmaets navn og nøgle. Sektionen *få lagerplads konto legitimationsoplysningerne* forklarer, hvordan du få vist disse værdier fra Azure-portalen. Bemærk imidlertid, i portalen tasten konto hedder "nøglen 1" så hvor Storage Explorer (Preview) beder om at få en kontonøgle, du vil angive (eller indsætte) værdien "nøglen 1". 
 
1.  I lagerplads Explorer (Preview) skal du vælge **Opret forbindelse til Azure-lager**.

    ![Oprette forbindelse til Azure datalager][23]

1.  Angiv kontonøgle ("nøglen 1" værdi fra portalen Azure) i dialogboksen **Opret forbindelse til Azure-lager** , og klik derefter på **Næste**.

    ![Oprette forbindelse til Azure-lager dialogboks][24] 

1.  I dialogboksen **Vedhæft eksterne lagerplads** , angive kontonavn lager i boksen **kontonavn** , Angiv eventuelt andre indstillinger, og vælg **Næste** når du er færdig. 

    ![Vedhæfte ekstern storage dialogboksen][8]

1.  Bekræft oplysningerne i dialogboksen **Forbindelse oversigt** . Hvis du vil ændre noget, skal du vælge **tilbage** og angive de ønskede indstillinger igen. Når færdig, skal du vælge **Opret forbindelse**.

1.  Når forbindelse, vises kontoen ekstern storage med teksten **(ekstern)** føjet til kontonavn lager. 

    ![Resultatet af at oprette forbindelse til en ekstern storage-konto][9]

### <a name="detach-from-an-external-storage-account"></a>Fjerne fra en ekstern storage-konto

1.  Højreklik på den eksterne lagerplads-konto, du vil fjerne, og - i kontekstmenuen - Vælg **Fjern**.

    ![Afbryde forbindelsen datalager][10]

1.  Når meddelelsesfeltet bekræftelse vises, Vælg **Ja** for at bekræfte løsrives fra kontoen eksterne lagerplads.

## <a name="attach-storage-account-using-sas"></a>Vedhæfte lagerplads konto ved hjælp af SAS

En [SAS (delt Access underskrift)](storage/storage-dotnet-shared-access-signature-part-1.md) giver administratoren af et Azure abonnement muligheden for at give adgang til en konto til lagerplads på midlertidigt uden at skulle angive deres Azure abonnement legitimationsoplysninger. 

For at illustrere, Lad os sige BrugerA er administrator for et Azure-abonnement, og BrugerA ønsker at tillade UserB at få adgang til en lagerplads konto i en begrænset periode med bestemte tilladelser:

1. BrugerA genererer en SAS (bestående af forbindelsesstrengen for kontoen lagerplads) for en bestemt periode og med de ønskede tilladelser.
1. BrugerA deler Sikkerhedstilknytningerne med den person, som ønsker adgang til lagerplads konto - UserB, i vores eksempel.  
1. UserB bruger Storage Explorer (Preview) til at vedhæfte til den konto, der hører til BrugerA ved hjælp af den medfølgende SAS. 

### <a name="get-a-sas-for-the-account-you-want-to-share"></a>Få en SAS for den konto, du vil dele

1.  Lagerplads Stifinder (Preview), skal du højreklikke på lagerplads-konto, du vil dele, og - genvejsmenuen - Vælg **Få delt Access-signatur**.

    ![Få menuindstillingen SAS kontekst][13]

1. Angiv tidsramme og tilladelser, du vil bruge til kontoen i dialogboksen **Delt Access signatur** , og vælg **Opret**.

    ![Få SAS dialogboksen][14]
 
1. Dialogboks med en anden **Delt Access signatur** vises viser Sikkerhedstilknytningerne. Klik på **Kopiér** ud for **Forbindelsesstreng** at kopiere den til Udklipsholder. Vælg **Luk** for at fjerne dialogboksen.

### <a name="attach-to-the-shared-account-using-the-sas"></a>Vedhæfte til den delte konto ved hjælp af Sikkerhedstilknytningerne

1.  I lagerplads Explorer (Preview) skal du vælge **Opret forbindelse til Azure-lager**.

    ![Oprette forbindelse til Azure datalager][23]

1.  Angiv forbindelsesstrengen i dialogboksen **Opret forbindelse til Azure-lager** , og klik derefter på **Næste**.

    ![Oprette forbindelse til Azure-lager dialogboks][24] 

1.  Bekræft oplysningerne i dialogboksen **Forbindelse oversigt** . Hvis du vil ændre noget, skal du vælge **tilbage** og angive de ønskede indstillinger igen. Når færdig, skal du vælge **Opret forbindelse**.

1.  Når vedhæftet, vises kontoen lagerplads med den tekst (SAS), der er føjet til det kontonavn, du har angivet.

    ![Resultat af knyttet til en konto med SAS][17]

## <a name="attach-service-using-sas"></a>Vedhæfte tjenesten ved hjælp af SAS

Sektionen [Vedhæft lagerplads konto ved hjælp af SAS](#attach-storage-account-using-sas) illustrerer, hvordan administrator Azure-abonnement kan give midlertidig adgang til en lagerplads konto ved at generere (og deling af) en SAS for kontoen lagerplads. En SAS kan oprettes på samme måde, til en bestemt service (blob objektbeholder, kø eller tabel) i en lagerplads konto.  

### <a name="generate-a-sas-for-the-service-you-want-to-share"></a>Generere en SAS for den tjeneste, du vil dele

I denne kontekst, kan en tjeneste være en blob objektbeholder, kø eller tabel. I følgende afsnit beskrives, hvordan du kan generere SAS for tjenesten vises:

- [Få Sikkerhedstilknytningerne til en blob objektbeholder](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
- Få Sikkerhedstilknytningerne til et filshare - *kommer snart*
- Få Sikkerhedstilknytningerne for en kø - *kommer snart*
- Få Sikkerhedstilknytningerne til en tabel - *kommer snart*

### <a name="attach-to-the-shared-account-service-using-the-sas"></a>Vedhæft til tjenesten delte konto ved hjælp af Sikkerhedstilknytningerne

1.  I lagerplads Explorer (Preview) skal du vælge **Opret forbindelse til Azure-lager**.

    ![Oprette forbindelse til Azure datalager][23]

1.  Angiv den SAS URI i dialogboksen **Opret forbindelse til Azure-lager** , og klik derefter på **Næste**.

    ![Oprette forbindelse til Azure-lager dialogboks][24] 

1.  Bekræft oplysningerne i dialogboksen **Forbindelse oversigt** . Hvis du vil ændre noget, skal du vælge **tilbage** og angive de ønskede indstillinger igen. Når færdig, skal du vælge **Opret forbindelse**.

1.  Når vedhæftet, vises tjenesten lige har vedhæftet under noden **(Service SAS)** .

    ![Resultatet af vedhæfte til en delt tjeneste via SAS][20]

## <a name="search-for-storage-accounts"></a>Søge efter lagerplads konti

Hvis du har en lang liste over lagerplads konti, er en hurtig måde at finde en bestemt lagerplads konto at bruge søgefeltet øverst i ruden til venstre. 

Mens du skriver i søgefeltet, vises kun lagerplads konti, der svarer til den søgeværdi, du har angivet op til, der peger venstre rude. Følgende skærmbillede viser et eksempel, hvor jeg har søger efter alle lagerplads konti hvor kontonavn lager indeholder teksten "tarcher".

![Lagerplads konto søgning][11]
    
Vælg knappen **x** for at fjerne markeringen i feltet Søg, i søgefeltet.

## <a name="next-steps"></a>Næste trin
- [Administrere Azure blob storageressourcer med lagerplads Stifinder (Preview)](./vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png
