<properties
   pageTitle="Forberede dig på at publicere eller installere et Azure-program fra Visual Studio | Microsoft Azure"
   description="Få mere at vide procedurerne for at konfigurere skyen og lager Kontotjenester og konfigurere dit Azure-program."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="prepare-to-publish-or-deploy-an-azure-application-from-visual-studio"></a>Forberede dig på at publicere eller installere et Azure-program fra Visual Studio

## <a name="overview"></a>Oversigt

Før du kan udgive en skybaseret tjeneste project, skal du konfigurere følgende tjenester:

- En **skybaseret tjeneste** til at køre din roller i Azure-miljø

- En **lagerplads konto** , som giver adgang til tjenesterne Blob, kø og tabel.

Benyt følgende fremgangsmåde for at konfigurere disse tjenester og konfigurere dit program


## <a name="create-a-cloud-service"></a>Oprette en skybaseret tjeneste

Hvis du vil publicere en skybaseret tjeneste til Azure, skal du først oprette en skybaseret tjeneste, som kører dine roller i Azure-miljøet. Du kan oprette en skybaseret tjeneste i [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885), som det er beskrevet i afsnittet **oprette en skybaseret tjeneste ved hjælp af portalen Azure klassisk**, senere i dette emne. Du kan også oprette en skybaseret tjeneste i Visual Studio ved hjælp af guiden Udgivelse.

### <a name="to-create-a-cloud-service-by-using-visual-studio"></a>Oprette en skybaseret tjeneste ved hjælp af Visual Studio

1. Åbn genvejsmenuen for Azure projektet, og vælg **Publicer**.

    ![VST_PublishMenu](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/vst-publish-menu.png)

1. Hvis du ikke har logget på, logge på med dit brugernavn og din adgangskode til Microsoft-konto eller organisationskonto, der er knyttet til abonnementet Azure.

1. Klik på **Næste** for at gå til siden **Indstillinger** .

    ![Publicering guiden almindelige indstillinger](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/publish-settings-page.png)

1. På listen **Cloud Services** skal du vælge **Opret ny**. Dialogboksen **Opret Azure Services** vises.

1. Skriv navnet på din skybaseret tjeneste. Navnet på en del af URL-adressen for din tjeneste og derfor skal være globalt entydige. Der skelnes ikke navnet.

### <a name="to-create-a-cloud-service-by-using-the-azure-classic-portal"></a>Oprette en skybaseret tjeneste ved hjælp af portalen Azure klassisk

1. Log på [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkId=253103) på Microsofts websted.

1. (valgfrit) For at få vist en liste over skytjenester, som du allerede har oprettet, skal du vælge linket Cloud Services på venstre side af siden.

1. Vælg den **+** -ikonet i nederste venstre hjørne, og vælg derefter **Skybaseret tjeneste** på den viste menu. En anden skærm med to indstillinger, **Hurtig oprettelse** og **Oprette brugerdefinerede**, vises. Hvis du vælger **Hurtig oprettelse**, kan du oprette en skybaseret tjeneste blot ved at angive URL-adressen og det område, hvor den skal være fysisk hostet. Hvis du vælger **Brugerdefineret oprette**, kan du publicere en skybaseret tjeneste med det samme ved at angive en pakke (.cspkg-fil), en konfigurationsfil (.cscfg) og et certifikat. Brugerdefineret Opret er ikke påkrævet, hvis du vil publicere din skytjeneste ved hjælp af kommandoen **Udgiv** i et Azure projekt. Kommandoen **Udgiv** er tilgængelig i genvejsmenuen for et Azure projekt.

1. Vælg **Hurtig oprettelse** senere udgive din skytjeneste ved hjælp af Visual Studio.

1. Angiv et navn for din skybaseret tjeneste. Den fulde URL vises ud for navnet.

1. Vælg det område, hvor de fleste af dine brugere er placeret på listen.

1. Nederst i vinduet skal du vælge linket **Oprette skybaseret tjeneste** .

## <a name="create-a-storage-account"></a>Oprette en lagerplads-konto

En lagerplads konto giver adgang til tjenesterne Blob, kø og tabel. Du kan oprette en lagerplads konto ved hjælp af Visual Studio eller [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkId=253103).

### <a name="to-create-a-storage-account-by-using-visual-studio"></a>Oprette en lagerplads konto ved hjælp af Visual Studio

1. I **Solution Explorer**skal du åbne genvejsmenuen for noden **lager** og derefter vælge **Opret lagerplads konto**.

    ![Oprette en ny firmapost Azure-lager](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/IC744166.png)

1. Vælg eller Indtast følgende oplysninger for den nye lagerplads konto i dialogboksen **Opret lagerplads konto** .
    - Azure abonnementet, du vil tilføje kontoen lagerplads.
    - Navnet, du vil bruge til den nye konto lagerplads.
    - Det område eller den forbindelse gruppe (som Vest USA eller Østasien).
    - Hvilken type replikering, du vil bruge til kontoen lagerplads, som geografisk overflødige.

1. Når du er færdig, skal du vælge **Opret**. Den nye lagerplads konto vises på listen **lagerplads** i **Server Explorer**.

### <a name="to-create-a-storage-account-by-using-the-azure-classic-portal"></a>Oprette en lagerplads konto ved hjælp af portalen Azure klassisk

1. Log på [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkId=253103) på Microsofts websted.

1. (Valgfrit) Vælg linket **lagerplads** i panelet til venstre på siden for at få vist kontiene lagerplads.

1. I nederste venstre hjørne af siden skal du vælge den **+** ikon.

1. Vælg **lager**i den menu, der vises, og vælg derefter **Hurtig oprettelse**.

1. Giv kontoen lagerplads et navn, der medfører en entydig URL-adresse.

1. Navngive din skybaseret tjeneste. Den fulde URL vises ud for navnet.

1. Vælg et område, hvor de fleste af dine brugere er placeret på listen over områder.

1. Angiv, om du vil aktivere geografisk gentagelse. Hvis du aktiverer geografisk gentagelse, gemmes dine data i flere fysisk for at reducere risikoen for tab. Denne funktion gør det mere kostbar lagerplads, men du kan reducere omkostningerne ved at aktivere geografisk placering, når du opretter kontoen lager i stedet for at tilføje funktionen senere. Du kan finde yderligere oplysninger finder [geografisk gentagelse](http://go.microsoft.com/fwlink/?LinkId=253108).

1. Nederst i vinduet skal du vælge linket **Opret lagerplads konto** .

Når du opretter kontoen lagerplads, vil du se de URL-adresser, som du kan bruge til at få adgang til ressourcer i hver af tjenesterne Azure-lager, samt primære og sekundære hurtigtasterne for din konto. Du kan bruge disse taster til at godkende anmodninger mod lagerplads tjenesterne.

>[AZURE.NOTE] Sekundær hurtigtast giver samme adgang til kontoen lagerplads som primær hurtigtast og genereres som en sikkerhedskopi, skal din primære hurtigtast være kompromitteret. Desuden, anbefales det, at du genoprette din access-taster jævnligt. Du kan redigere en forbindelse streng indstilling til at bruge en sekundær nøgle, mens du genoprette den primære nøgle, og derefter kan du ændre det til at bruge den gendannede primære nøgle, mens du genoprette nøglen sekundær.

## <a name="configure-your-app-to-use-services-provided-by-the-storage-account"></a>Konfigurere din app for at bruge tjenesterne kontoen lagerplads

Du skal konfigurere en rolle, der har adgang til lagerplads services til at bruge tjenesterne Azure-lager, du har oprettet. For at gøre dette, kan du bruge flere service konfigurationer for projektet Azure. Som standard oprettes to i projektet Azure. Ved hjælp af flere service konfigurationer, kan du bruge den samme forbindelsesstreng i din kode, men har en anden værdi for en forbindelsesstreng i hver tjenestekonfiguration. For eksempel kan du bruge en konfiguration til at køre og fejlfinde dit program lokalt ved hjælp af emulatoren Azure-lager og en anden tjenestekonfiguration til at udgive dit program til Azure. Du kan finde flere oplysninger om tjenesten konfigurationer, [Konfiguration af din Azure Project ved hjælp af flere Service konfigurationer](vs-azure-tools-multiple-services-project-configurations.md).

### <a name="to-configure-your-application-to-use-services-that-the-storage-account-provides"></a>Konfigurere dit program tilladelse til at bruge tjenester, der indeholder kontoen lagerplads

1. Åbn din Azure løsning i Visual Studio. I Solution Explorer skal du åbne genvejsmenuen for hver rolle i projektet Azure, der har adgang til lagerplads tjenester og vælg **Egenskaber**. En side med navnet på rollen vises i Visual Studio-editor. Siden viser felter til fanen **konfiguration** .

1. Vælg **Indstillinger**på siden med egenskaber for rollen.

1. Vælg navnet på den konfiguration af tjenesten, som du vil redigere, på listen **Tjenestekonfiguration** . Hvis du vil foretage ændringer i alle service konfigurationerne for denne rolle, kan du vælge **Alle konfigurationer**.  Du kan finde flere oplysninger om, hvordan du opdaterer tjenesten konfigurationer, i afsnittet **Administrere strenge for lagerplads konti** i emnet [konfigurere rollerne for en Azure skybaseret tjeneste med Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

1. Hvis du vil ændre en hvilken som helst streng forbindelsesindstillinger, Vælg **…** knappen ved siden af indstillingen. Dialogboksen **Opret lagerplads forbindelsesstreng** vises.

1. Under **Opret forbindelse via**, skal du vælge indstillingen **dit abonnement** .

1. På listen **abonnement** skal du vælge dit abonnement. Hvis listen over de abonnementer ikke indeholder den tastaturgenvej, du vil, kan du vælge linket **Hente publiceringsindstillinger** .

1. Vælg dit kontonavn lager på listen **kontonavn** . Azure værktøjer henter lagerplads kontolegitimationsoplysninger automatisk ved hjælp af .publishsettings-filen. For at angive din lagerplads kontolegitimationsoplysninger manuelt, skal du vælge indstillingen **manuelt angivet legitimationsoplysninger** og derefter fortsætte med denne procedure. Du kan få dit kontonavn til lager og primære nøgle fra [Azure klassisk portal](http://go.microsoft.com/fwlink/p/?LinkID=213885). Hvis du ikke vil angive din lagerplads kontoindstillinger manuelt, klik på **OK** for at lukke dialogboksen.

1. Vælg linket **Enter lagerplads konto** legitimationsoplysninger.

1. Angiv navnet på din konto, lagerplads i boksen **kontonavn** .

    >[AZURE.NOTE] Log på [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885), og vælg derefter knappen **lagerplads** . På portalen viser en liste over lagerplads konti. Hvis du vælger en konto, åbnes en side til den. Du kan kopiere navnet på kontoen, lagerplads fra denne side. Hvis du bruger en tidligere version af portalen klassisk navnet på din lagerplads konto vises i visningen **Lagerplads konti** . Hvis du vil kopiere dette navn, markere den i vinduet **Egenskaber** i denne visning, og vælg derefter tasterne Ctrl + C. Vælg tekstfeltet **kontonavn** for at indsætte navnet i Visual Studio, og vælg derefter tasterne Ctrl + V.

1. I feltet **kontonøgle** , angive den primære nøgle, eller kopiere og indsætte den fra [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885).
    Sådan kopieres denne nøgle:

    1. Vælg knappen **Administrer nøgler** nederst på siden for den relevante lagerplads konto.

    1. Markér teksten i den primære hurtigtast på siden **Administrer nøgler Access** , og vælg derefter tasterne Ctrl + C.

    1. Indsæt tasten i feltet **kontonøgle** i Azure værktøjer.

    1. Du skal vælge en af følgende indstillinger for at finde ud af, hvordan tjenesten kan få adgang til kontoen lagerplads:
        - **Brug HTTP**. Dette er indstillingen standard. For eksempel `http://<account name>.blob.core.windows.net`.
        - **Brug HTTPS** til en sikker forbindelse. For eksempel `https://<accountname>.blob.core.windows.net`.
        - **Angiv brugerdefinerede slutpunkter** for hver af de tre tjenester. Derefter kan du skrive disse slutpunkter til feltet for den specifikke tjeneste.

        >[AZURE.NOTE] Hvis du opretter brugerdefinerede slutpunkter, kan du oprette en mere kompleks forbindelsesstreng. Når du bruger denne strengformat, kan du angive lagerplads service slutpunkter, der indeholder et brugerdefineret domænenavn, du har tilmeldt kontoen lagerplads med Blob-tjenesten. Du kan også give kun adgang til blob ressourcer i en enkelt objektbeholderen gennem en delt adgang signatur. Du kan finde flere oplysninger om, hvordan du opretter brugerdefinerede slutpunkter i [Konfigurere Azure lagerplads forbindelsesstrenge](storage-configure-connection-string.md).

1. Vælg knappen **OK** for at gemme ændringerne forbindelse streng, og vælg derefter knappen **Gem** på værktøjslinjen. Når du gemmer ændringerne, kan du få værdien af forbindelsesstrengen i din kode ved hjælp af [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx). Når du publicerer dit program til Azure, skal du vælge konfigurationen af tjenesten, der indeholder kontoen Azure-lager til forbindelsesstrengen. Efter dit program er blevet publiceret, kan du kontrollere, at programmet fungerer som forventet mod tjenesterne Azure-lager

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om at publicere apps til Azure fra Visual Studio, kan du se [publicere en skybaseret tjeneste, ved hjælp af værktøjerne Azure](vs-azure-tools-publishing-a-cloud-service.md).
