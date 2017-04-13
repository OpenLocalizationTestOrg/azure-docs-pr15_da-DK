<properties
   pageTitle="Konfigurere rollerne for en Azure skybaseret tjeneste med Visual Studio | Microsoft Azure"
   description="Lær, hvordan du konfigurerer og konfigurere roller for Azure skytjenester ved hjælp af Visual Studio."
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

# <a name="configure-the-roles-for-an-azure-cloud-service-with-visual-studio"></a>Konfigurere rollerne for en Azure skybaseret tjeneste med Visual Studio

Azure-skytjeneste kan have en eller flere arbejder eller web roller. For hver rolle skal du definere, hvordan rollen er konfigureret og også konfigurere, hvordan rollen kører. Få vist video [Introduktion til Azure Cloud Services](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services)for at lære mere om roller i skytjenester. Oplysningerne om din skybaseret tjeneste er gemt på følgende filer:

- **ServiceDefinition.csdef**

    Tjenesten definitionsfil definerer runtime indstillingerne for din skybaseret tjeneste, herunder hvilke roller, der kræves, slutpunkter og virtuelt størrelse. Ingen af de data, der er gemt i denne fil kan ikke ændres, når din rolle kører.

- **ServiceConfiguration.cscfg**

    Konfigurationsfil service konfigurerer, hvor mange forekomster af en rolle køres og værdierne af de indstillinger, der er defineret for en rolle. De data, der er gemt i denne fil kan ændres, mens der kører din rolle.

Hvis du vil kunne lagre forskellige værdier for disse indstillinger for hvordan din rolle kører, kan du få flere service konfigurationer. Du kan bruge en anden tjenestekonfiguration for hver installation-miljø. For eksempel kan du angive din lagerplads konto forbindelsesstreng til at bruge lokale Azure lagerplads emulatoren i en lokal tjenestekonfiguration og oprette en anden tjenestekonfiguration for at bruge Azure lagerplads i skyen.

Når du opretter en ny Azure skybaseret tjeneste i Visual Studio, oprettes to service konfigurationer som standard. Disse konfigurationer føjes til projektet Azure. Konfigurationerne navngives:

- ServiceConfiguration.Cloud.cscfg

- ServiceConfiguration.Local.cscfg

## <a name="configure-an-azure-cloud-service"></a>Konfigurere en Azure skybaseret tjeneste

Du kan konfigurere en Azure skybaseret tjeneste fra Solution Explorer i Visual Studio, som vist i nedenstående illustration.

![Konfigurere skybaseret tjeneste](./media/vs-azure-tools-configure-roles-for-cloud-service/IC713462.png)

### <a name="to-configure-an-azure-cloud-service"></a>Konfigurere en Azure skybaseret tjeneste

1. For at konfigurere hver rolle i projektet Azure fra **Solution Explorer**skal du åbne genvejsmenuen for rollen i Azure projektet og derefter vælge **Egenskaber**.

    En side med navnet på rollen vises i Visual Studio-editor. Siden viser felter til fanen **konfiguration** .

1. Vælg navnet på den konfiguration af tjenesten, som du vil redigere, på listen **Tjenestekonfiguration** .

    Hvis du vil foretage ændringer i alle service konfigurationerne for denne rolle, kan du vælge **Alle konfigurationer**.

    >[AZURE.IMPORTANT] Hvis du vælger en bestemt tjenestekonfiguration, deaktiveres nogle af de egenskaber, fordi de kan kun angives for alle konfigurationer. Hvis du vil redigere disse egenskaber, skal du vælge alle konfigurationer.

    Nu kan du vælge en fane til at opdatere en hvilken som helst aktiveret egenskaber på denne visning.

## <a name="change-the-number-of-role-instances"></a>Ændre antallet af forekomster af rolle

For at forbedre ydeevnen for din skybaseret tjeneste, kan du ændre antallet af forekomster af en rolle, der kører, baseret på antallet af brugere eller afkrydsningsfeltet Indlæs forventet for en bestemt rolle. Der oprettes en separat virtuel maskine for hver forekomst af en rolle, når skytjenesten kører i Azure. Dette vil påvirke fakturering for installation af denne skybaseret tjeneste. Se [forstå din faktura til Microsoft Azure](billing/billing-understand-your-bill.md)kan finde flere oplysninger om fakturering.

### <a name="to-change-the-number-of-instances-for-a-role"></a>Ændre antallet af forekomster til en rolle

1. Vælg fanen **konfiguration** .

1. Vælg den konfiguration af tjenesten, som du vil opdatere, på listen **Tjenestekonfiguration** .

    >[AZURE.NOTE] Du kan angive forekomst antallet for en bestemt tjenestekonfiguration eller for alle service konfigurationer.

1. Angiv antallet af forekomster, du vil starte for denne rolle i tekstfeltet **forekomst Tæl** .

    >[AZURE.NOTE] Hver forekomst køres på en separat virtuel maskine, når du publicerer skybaseret tjeneste til Azure.

1. Vælg knappen **Gem** på værktøjslinjen for at gemme ændringerne i filen service konfiguration.

## <a name="manage-connection-strings-for-storage-accounts"></a>Administrere strenge for lagerplads konti

Du kan tilføje, fjerne eller redigere strenge din tjeneste konfigurationer. Vil du muligvis anden forbindelsesstrenge anden tjeneste konfigurationer. For eksempel du måske en lokal forbindelsesstreng for en lokal tjenestekonfiguration, der indeholder en værdi på `UseDevelopmentStorage=true`. Du kan også konfigurere en skybaseret tjenestekonfiguration, som anvender en lagerplads konto i Azure.

>[AZURE.WARNING] Når du angiver Azure lagerplads konto vigtige oplysninger til en lagerplads konto forbindelsesstreng, er disse oplysninger gemt lokalt i tjenesten konfigurationsfil. Disse oplysninger gemmes i øjeblikket ikke dog som krypteret tekst.

Ved hjælp af en anden værdi for hver tjenestekonfiguration, har du ikke bruge anden forbindelsesstrenge i skybaseret tjeneste eller redigere koden, når du publicerer skybaseret tjeneste til Azure. Du kan bruge det samme navn til forbindelsesstrengen i din kode, og værdien skal være forskellige, baseret på konfigurationen af tjenesten, som du vælger, når du opretter din skybaseret tjeneste, eller når du udgiver billedet.

### <a name="to-manage-connection-strings-for-storage-accounts"></a>Til at administrere strenge for lagerplads konti

1. Vælg fanen **Indstillinger** .

1. Vælg den konfiguration af tjenesten, som du vil opdatere, på listen **Tjenestekonfiguration** .

    >[AZURE.NOTE] Du kan opdatere forbindelsesstrenge for en bestemt tjenestekonfiguration, men hvis du vil tilføje eller slette en forbindelsesstreng skal du vælge alle konfigurationer.

1. For at tilføje en forbindelsesstreng, skal du vælge knappen **Tilføj indstilling** . En ny post føjes til listen.

1. Skriv det navn, du vil bruge til forbindelsesstrengen i tekstfeltet **navn** .

1. Vælg **Forbindelsesstreng**i rullelisten **Type** .

1. For at ændre værdien for forbindelsesstrengen skal du vælge knappen ellipsen (...). Dialogboksen **Opret lagerplads forbindelsesstreng** vises.

1. Vælg alternativknappen **Microsoft Azure-lager emulator** for at bruge lokale lager konto emulatoren, og vælg derefter knappen **OK** .

1. Vælg alternativknappen **dit abonnement** for at bruge en lagerplads konto i Azure, og vælg den ønskede lagerplads konto.

1. For at bruge brugerdefinerede legitimationsoplysninger skal du vælge knappen **manuelt angivet legitimationsoplysninger** indstillinger. Angiv kontonavn lager og den primære eller anden nøgle. Du kan finde oplysninger om, hvordan du opretter en lagerplads konto og hvordan du kan angive oplysninger for kontoen lagerplads i dialogboksen **Opret lagerplads forbindelsesstreng** , [forberede til at udgive eller installere et Azure-program fra Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Vælg forbindelsesstrengen for at slette en forbindelsesstreng, og vælg derefter knappen **Fjern indstilling** .

1. Vælg ikonet **Gem** på værktøjslinjen for at gemme ændringerne i filen service konfiguration.

1. For at få adgang til forbindelsesstrengen i konfigurationsfil service, skal du hente værdien af konfigurationsindstillingen. Følgende kode viser et eksempel, hvor der er oprettet blob-lager og data, der er overført ved hjælp af en forbindelsesstreng `MyConnectionString` fra konfigurationsfil service, når en bruger vælger **Button1** på siden Default.aspx i web-rolle til en Azure skybaseret tjeneste. Tilføj følgende ved hjælp af sætninger til Default.aspx.cs:

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Åbn Default.aspx.cs i designvisning, og tilføje en knap fra værktøjskassen. Tilføj følgende kode i den `Button1_Click` metode. Denne kode bruger `GetConfigurationSettingValue` til Hent værdien fra service konfigurationsfil til forbindelsesstrengen. Vælg derefter en blob er oprettet i kontoen lagerplads, der refereres til i forbindelsesstrengen `MyConnectionString` og til sidst programmet tilføjer tekst til blob.

    ```
    protected void Button1_Click(object sender, EventArgs e)
    {
        // Setup the connection to Azure Storage
        var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("MyConnectionString"));
        var blobClient = storageAccount.CreateCloudBlobClient();
        // Get and create the container
        var blobContainer = blobClient.GetContainerReference("quicklap");
        blobContainer.CreateIfNotExists();
        // upload a text blob
        var blob = blobContainer.GetBlockBlobReference(Guid.NewGuid().ToString());
        blob.UploadText("Hello Azure");

    }
    ```

## <a name="add-custom-settings-to-use-in-your-azure-cloud-service"></a>Tilføje brugerdefinerede indstillinger til at bruge i dine Azure skybaseret tjeneste

Brugerdefinerede indstillinger i konfigurationsfil service kan du tilføje et navn og en værdi for en streng til en bestemt tjenestekonfiguration. Du kan vælge at bruge denne indstilling til at konfigurere en funktion i skybaseret tjeneste ved at læse værdien af indstillingen og bruge denne værdi til at styre logik i din kode. Du kan ændre disse værdier til konfiguration af tjenesten uden at skulle genopbygge din servicepakke eller når skybaseret tjeneste kører. Din kode kan søge efter meddelelser om, når en indstilling ændres. Se [RoleEnvironment.Changing hændelse](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

Du kan tilføje, fjerne eller redigere brugerdefinerede indstillinger for din tjeneste konfigurationer. Vil du muligvis forskellige værdier for disse strenge anden tjeneste konfigurationer.

Ved hjælp af en anden værdi for hver tjenestekonfiguration, behøver du ikke at bruge forskellige strenge i skybaseret tjeneste eller redigere din kode, når du publicerer skybaseret tjeneste til Azure. Du kan bruge det samme navn til strengen i din kode, og værdien skal være forskellige, baseret på konfigurationen af tjenesten, som du vælger, når du opretter din skybaseret tjeneste, eller når du udgiver billedet.

### <a name="to-add-custom-settings-to-use-in-your-azure-cloud-service"></a>Tilføje brugerdefinerede indstillinger til at bruge i dine Azure skybaseret tjeneste

1. Vælg fanen **Indstillinger** .

1. Vælg den konfiguration af tjenesten, som du vil opdatere, på listen **Tjenestekonfiguration** .

    >[AZURE.NOTE] Du kan opdatere strenge for en bestemt tjenestekonfiguration, men hvis du har brug at tilføje eller slette en streng, skal du vælge **Alle konfigurationer**.

1. For at tilføje en streng, skal du vælge knappen **Tilføj indstilling** . En ny post føjes til listen.

1. Skriv det navn, du vil bruge til strengen i tekstboksen **navn** .

1. Vælg **streng**i rullelisten **Type** .

1. Skriv den nye værdi for at tilføje eller ændre værdien for strengen, i tekstfeltet **værdi** .

1. Hvis du vil slette en streng, Vælg strengen, og vælg derefter knappen **Fjern indstilling** .

1. Vælg knappen **Gem** på værktøjslinjen for at gemme ændringerne i filen service konfiguration.

1. For at få adgang til strengen i konfigurationsfil service, skal du hente værdien af konfigurationsindstillingen.

    Du har brug at sikre, at følgende ved hjælp af sætningerne allerede er føjet til Default.aspx.cs, ligesom du gjorde i den foregående fremgangsmåde.

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Tilføj følgende kode i den `Button1_Click` metode til at få adgang til denne streng på samme måde, du få adgang til en forbindelsesstreng. Din kode kan udføre nogle bestemte kode, der er baseret på værdien af strengen indstillinger for tjenestens konfigurationsfil, der bruges.

    ```
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("MySetting");
    if (settingValue == “ThisValue”)
    {
    // Perform these lines of code
    }
    ```

## <a name="manage-local-storage-for-each-role-instance"></a>Administrere lokale lager for hver forekomst af rolle

Du kan tilføje lokale system fillagring for hver forekomst af en rolle. Du kan gemme lokale data her, der ikke behøver at åbnes af andre roller. De data, der du ikke behøver at gemme i tabellen, blob eller SQL-Database lagerplads kan være gemt på her. For eksempel kan du bruge denne lokale lager til cachedata, der kan være nødvendigt at bruges igen. Denne lagrede data kan ikke åbnes af andre forekomster af en rolle. 

Indstillinger for lokale lager gælder for alle service konfigurationer. Du kan kun tilføje, fjerne eller redigere lokale lager for alle service konfigurationer.

### <a name="to-manage-local-storage-for-each-role-instance"></a>Administrere lokale lager for hver forekomst af rolle

1. Vælg fanen **Lokale lager** .

1. Vælg **Alle konfigurationer**på listen **Tjenestekonfiguration** .

1. For at tilføje et lokalt lager post skal du vælge knappen **Tilføje lokale lager** . En ny post føjes til listen.

1. Skriv det navn, du vil bruge til denne lokale lager i tekstboksen **navn** .

1. Skriv størrelsen i MB, som du har brug for denne lokale lager i feltet **størrelse** .

1. Hvis du vil fjerne dataene i denne lokale lager, når den virtuelle maskine for denne rolle er flyttet til papirkurven, skal du markere afkrydsningsfeltet **rydde på rolle Papirkurv** .

1. For at redigere en eksisterende indtastning lokale lager skal du vælge den række, du vil opdatere. Du kan redigere felterne, som beskrevet i de forrige trin.

1. For at slette et lokalt lager element skal du vælge posten lagerplads på listen og derefter vælge knappen **Fjern lokale lager** .

1. For at gemme ændringerne til tjenesten konfigurationsfiler skal du vælge ikonet **Gem** på værktøjslinjen.

1. For at få adgang til det lokale lager, du har tilføjet i konfigurationsfil service, skal du hente værdien af indstillingen lokal ressource konfiguration. Brug de følgende kodelinjer til at få adgang til denne værdi og oprette en fil kaldet **MyStorageTest.txt** og skrive en linje med testdata i filen. Du kan tilføje denne kode i den `Button_Click` afskrivningsmetode, som du har brugt i ovenstående fremgangsmåder:

1. Du har brug at sikre, at følgende ved hjælp af sætningerne føjes til Default.aspx.cs:

    ```
    using System.IO;
    using System.Text;
    ```

1. Tilføj følgende kode i den `Button1_Click` metode. Dette opretter filen i det lokale lager og skriver testdata til filen.

    ```
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("LocalStorage1");

    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyStorageTest.txt" };
    String filePath = Path.Combine(paths);

    using (FileStream writeStream = File.Create(filePath))
    {
          Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
          writeStream.Write(textToWrite, 0, textToWrite.Length);
    }
    ```

1. (Valgfrit) For at se denne fil, du har oprettet, når du kører din skytjeneste lokalt, skal du følge nedenstående trin:

  1. Kør rollen web, og vælg **Button1** Sørg for, at koden i `Button1_Click` bliver kaldet.

  1. Åbne genvejsmenuen for ikonet Azure i meddelelsesområdet, og vælg **Vis beregne Emulator brugergrænseflade**. Dialogboksen **Azure beregne Emulator** vises.

  1. Vælg rollen, web.

  1. Vælg **værktøjer**, **Åbn lokale lager**på menulinjen. Der vises en Windows Stifinder-vindue.

  1. Angiv **MyStorageTest.txt** i tekstfeltet **Søg** på menulinjen, og vælg derefter **Enter** for at starte søgningen.

    Filen vises i søgeresultaterne.

  1. Åbne genvejsmenuen for filen for at få vist indholdet af fil, og vælg **Åbn**.

## <a name="collect-cloud-service-diagnostics"></a>Indsamle skyen service diagnosticering

Du kan indsamle diagnosticering data til din Azure skybaseret tjeneste. Disse data er føjet til en lagerplads konto. Vil du muligvis anden forbindelsesstrenge anden tjeneste konfigurationer. For eksempel du måske et lokalt lager konto til en lokal tjenestekonfiguration, der indeholder en værdi på UseDevelopmentStorage = true. Du kan også konfigurere en skybaseret tjenestekonfiguration, som anvender en lagerplads konto i Azure. Se Indsaml logføring Data ved hjælp af Azure diagnosticering kan finde flere oplysninger om Azure diagnosticering.

>[AZURE.NOTE] Lokal tjenestekonfigurationen er allerede konfigureret til at bruge lokale ressourcer. Hvis du bruger skyen tjenestekonfiguration publicere din Azure skybaseret tjeneste, bruges forbindelsesstrengen, du angiver, når du publicerer også til diagnosticering forbindelsesstrengen, medmindre du har angivet en forbindelsesstreng. Hvis du pakker dine skybaseret tjeneste, ved hjælp af Visual Studio, ændres ikke forbindelsesstrengen i konfigurationen af tjenesten.

### <a name="to-collect-cloud-service-diagnostics"></a>At indsamle skyen service diagnosticering

1. Vælg fanen **konfiguration** .

1. Vælg den konfiguration af tjenesten, du vil opdatere eller vælge **Alle konfigurationer**på listen **Tjenestekonfiguration** .

    >[AZURE.NOTE] Du kan opdatere lagerplads kontoen for en bestemt tjenestekonfiguration, men hvis du vil aktivere eller deaktivere diagnosticering skal du vælge alle konfigurationer.

1. For at aktivere diagnosticering skal du markere afkrydsningsfeltet **Aktivér diagnosticering** .

1. For at ændre værdien for kontoen lagerplads, skal du vælge knappen ellipsen (...).

    Dialogboksen **Opret lagerplads forbindelsesstreng** vises.

1. Hvis du vil bruge en lokal forbindelsesstreng, Vælg Azure emulator datalager, og vælg derefter knappen **OK** .

1. For at bruge en lagerplads-konto, der er knyttet til abonnementet Azure skal du vælge indstillingen **dit abonnement** .

1. Vælg indstillingen **manuelt angivet legitimationsoplysninger** for at bruge en lagerplads konto til den lokale forbindelsesstreng.

    Se [forberede til at udgive eller installere et Azure-program fra Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md), du kan finde flere oplysninger om, hvordan du opretter en lagerplads konto og hvordan du kan angive oplysninger for kontoen lagerplads i dialogboksen **Opret lagerplads forbindelsesstreng** .

1. Vælg kontoen lagerplads, du vil bruge i **kontonavn**.

    Hvis du er manuelt at angive dine lagerplads kontolegitimationsoplysninger, Indsæt eller skriv din primære nøgle i **kontonøgle**. Denne tast kan kopieres fra [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885). Sådan kopieres denne tast, skal du følge disse trin fra visningen **Lagerplads konti** i [Azure klassisk portalen](http://go.microsoft.com/fwlink/?LinkID=213885):
    
  1. Vælg kontoen lagerplads, du vil bruge til din skybaseret tjeneste.

  1. Vælg knappen **Administrer hurtigtaster** placeret nederst på skærmen. Dialogboksen **Administrer hurtigtaster** vises.

  1. For at kopiere hurtigtast, skal du vælge knappen **Kopiér til Udklipsholder** . Du kan nu indsætte denne tast i feltet **kontonøgle** .

1. Bruge kontoen lagerplads, som du angiver, som forbindelsesstrengen til diagnosticering (og cachelagring) når du publicerer skybaseret tjeneste til Azure, Markér afkrydsningsfeltet **Opdater udvikling lagerplads forbindelsesstrenge til diagnosticering og cachelagring med Azure lagerplads legitimationsoplysninger, når du udgiver til Azure** .

1. Vælg knappen **Gem** på værktøjslinjen for at gemme ændringerne i filen service konfiguration.

## <a name="change-the-size-of-the-virtual-machine-used-for-each-role"></a>Ændre størrelsen på den virtuelle maskine, der bruges til hver rolle

Du kan indstille størrelsen på virtuelt til hver rolle. Du kan kun angive denne størrelse for alle service konfigurationer. Hvis du vælger en mindre maskine, er derefter mindre CPU kerner, hukommelse og lokal disk lagerplads allokeret. Den tildelte båndbredde er også mindre. Du kan finde flere oplysninger om disse størrelser og de ressourcer, der er allokeret [størrelser for Cloud Services](cloud-services/cloud-services-sizes-specs.md).

De ressourcer, der kræves for hver virtuel maskine i Azure påvirker omkostningerne ved at køre skybaseret tjeneste i Azure. Se [forstå din faktura til Microsoft Azure](billing/billing-understand-your-bill.md)kan finde flere oplysninger om Azure fakturering.

### <a name="to-change-the-size-of-the-virtual-machine"></a>Ændre størrelsen på den virtuelle maskine

1. Vælg fanen **konfiguration** .

1. Vælg **Alle konfigurationer**på listen **Tjenestekonfiguration** .

1. For at markere størrelsen for den virtuelle maskine for denne rolle, skal du vælge den relevante størrelse fra listen **VM størrelse** .

1. Vælg knappen **Gem** på værktøjslinjen for at gemme ændringerne i filen service konfiguration.

## <a name="manage-endpoints-and-certificates-for-your-roles"></a>Administrere slutpunkter og certifikater til dine roller

Du kan konfigurere netværk slutpunkter, ved at angive protokollen, portnummeret, og til HTTPS, SSL-certifikatoplysninger. Tidligere juni 2012 versioner understøtter HTTP, HTTPS og TCP. Juni 2012 udgave understøtter disse protokoller og UDP. Du kan ikke bruge UDP for input slutpunkter i Beregn emulatoren. Du kan bruge denne protokol kun for interne slutpunkter.

For at forbedre sikkerheden for dine Azure skybaseret tjeneste, kan du oprette slutpunkter, der bruger HTTPS-protokollen. Eksempelvis hvis du har en skybaseret tjeneste, der bruges af kunder til indkøbsordrer, vil du kontrollere, at deres oplysninger er sikre ved hjælp af SSL.

Du kan også tilføje slutpunkter, der kan bruges internt eller eksternt. Eksterne slutpunkter kaldes input slutpunkter. Indtast ark gør det muligt for en anden adgangspunkt at brugere til din skybaseret tjeneste. Hvis du har en WCF-tjeneste, kan du få vist et internt slutpunkt for en web rolle kan bruge til at få adgang til denne tjeneste.

>[AZURE.IMPORTANT] Du kan kun opdatere slutpunkter for alle service konfigurationer.

Hvis du tilføjer HTTPS slutpunkter, skal du bruge et SSL-certifikat. Du kan knytte certifikater til din rolle for alle service konfigurationer og bruge disse oplysninger til slutpunkterne for at gøre dette.

>[AZURE.IMPORTANT] Certifikaterne er ikke pakket med din tjeneste. Du skal overføre dine egne certifikater separat til Azure via [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885).

En hvilken som helst management certifikater, der tilknyttes din tjeneste konfigurationer gælder kun, når din skytjeneste kører i Azure. Når din skytjeneste kører i det lokale udviklingsmiljø, bruges et standard-certifikat, der administreres af Azure Beregn emulatoren.

### <a name="to-add-a-certificate-to-a-role"></a>Føje et certifikat til en rolle

1. Klik på fanen **certifikater** .

1. Vælg **Alle konfigurationer**på listen **Tjenestekonfiguration** .

    >[AZURE.NOTE] Hvis du vil tilføje eller fjerne certifikater, skal du vælge alle konfigurationer. Du kan opdatere navnet og miniature til en bestemt tjenestekonfiguration, hvis det er nødvendigt.

1. For at tilføje et certifikat til denne rolle, skal du vælge knappen **Tilføj certifikat** . En ny post føjes til listen.

1. Angiv et navn til certifikatet i tekstboksen **navn** .

1. Vælg en placering til det certifikat, du vil tilføje på listen **Placering** .

1. Vælg den butik, som du vil bruge til at vælge certifikatet i **Gemme navn** på listen.

1. For at tilføje certifikatet, skal du vælge knappen ellipsen (...). Dialogboksen **Sikkerhed i Windows** vises.

1. Vælg det certifikat, du vil bruge, på listen, og vælg knappen **OK** .

    >[AZURE.NOTE] Når du tilføjer et certifikat fra certifikat store, føjes der automatisk en mellemliggende certifikater til konfigurationsindstillinger for dig. Certifikaterne mellemliggende skal også overføres til Azure for at konfigurere din tjeneste til SSL korrekt.

1. Hvis du vil slette et certifikat, Vælg certifikatet, og vælg derefter knappen **Fjern certifikat** .

1. Vælg ikonet **Gem** på værktøjslinjen for at gemme disse ændringer til tjenesten konfigurationsfiler.

### <a name="to-manage-endpoints-for-a-role"></a>Til at administrere slutpunkter for en rolle

1. Vælg fanen **slutpunkter** .

1. Vælg **Alle konfigurationer**på listen **Tjenestekonfiguration** .

1. For at tilføje et slutpunkt skal du vælge knappen **Tilføj slutpunkt** . En ny post føjes til listen.

1. Skriv det navn, du vil bruge til dette slutpunkt i tekstboksen **navn** .

1. Vælg typen af slutpunkt, skal du på listen **Type** .

1. Vælg protokollen, til den første eller sidste ark, du skal bruge på listen **Protocol** .

1. Hvis det er et input slutpunkt, i tekstfeltet **Offentlige Port** , skal du angive den offentlige port du skal bruge.

1. Skriv den private port til brug i tekstfeltet **Privat Port** .

1. Hvis slutpunktet kræver https-protokollen, på listen **Navn for SSL-certifikat** , skal du vælge et certifikat, du vil bruge.

    >[AZURE.NOTE] Denne liste viser de certifikater, som du har tilføjet til denne rolle i fanen **certifikater** .

1. Vælg knappen **Gem** på værktøjslinjen for at gemme disse ændringer til tjenesten konfigurationsfiler.

## <a name="next-steps"></a>Næste trin
Lær mere om Azure projekter i Visual Studio ved at læse [konfigurere en Azure-projekt](vs-azure-tools-configuring-an-azure-project.md). Lær mere om skyen service skemaet ved at læse [Skemareference](https://msdn.microsoft.com/library/azure/dd179398).
