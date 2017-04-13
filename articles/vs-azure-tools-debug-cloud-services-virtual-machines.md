<properties 
    pageTitle="Fejlfinding af en Azure skybaseret tjeneste eller virtuel maskine i Visual Studio | Microsoft Azure"
    description="Fejlfinding af en skybaseret tjeneste eller virtuel maskine i Visual Studio"
    services="visual-studio-online"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />
<tags 
    ms.service="visual-studio-online"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/15/2016"
    ms.author="tarcher" />

# <a name="debugging-an-azure-cloud-service-or-virtual-machine-in-visual-studio"></a>Fejlfinding af en Azure skybaseret tjeneste eller virtuel maskine i Visual Studio

Visual Studio giver forskellige indstillinger til fejlfinding Azure skytjenester og virtuelle computere.



## <a name="debug-your-cloud-service-on-your-local-computer"></a>Fejlfinding af din skytjeneste på din lokale computer

Du kan spare tid og penge ved hjælp af Azure beregne emulator foretage fejlfinding af din skytjeneste på en lokal computer. Ved fejlfinding en tjeneste lokalt, inden du installerer det, kan du forbedre pålideligheden og ydeevnen uden betaler for Beregn tid. Dog nogle fejl kan opstå kun, når du kører en skybaseret tjeneste i Azure sig selv. Du kan finde disse fejl, hvis du aktiverer ekstern fejlfinding, når du publicerer din tjeneste og derefter vedhæfte fejlfindingsværktøjet til en rolle forekomst.

Emulatoren simulerer tjenesten Azure beregne og kører i din lokale miljø, så du kan teste og fejlfinde skybaseret tjeneste, inden du installerer det. Emulatoren håndterer livscyklus for din rolle forekomster og giver adgang til simuleret ressourcer, som lokale lager. Når du fejlfinding af eller køre din tjeneste fra Visual Studio, starter automatisk emulatoren som et baggrund til computeren og derefter installerer din tjeneste på emulatoren. Du kan bruge emulatoren til at få vist din tjeneste, når der køres i det lokale miljø. Du kan køre den fulde version eller den udtrykkelige version af emulatoren. (Starter med Azure 2.3, den udtrykkelige version af emulatoren er standard). Se [Brug af Emulator Express til at køre og foretage fejlfinding i en skybaseret tjeneste lokalt](https://msdn.microsoft.com/library/dn339018.aspx).

### <a name="to-debug-your-cloud-service-on-your-local-computer"></a>Foretage fejlfinding af din skytjeneste på din lokale computer

1. Vælg **fejlfinding**, **Starte fejlfinding** til at køre projektet Azure skyen service på menulinjen. Som et alternativ kan du trykke på F5. Du får vist en meddelelse, der går i gang med emulatoren beregne. Når emulatoren starter, bekræfter proceslinjeikonet system den.

    ![Azure emulator på proceslinjen](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC783828.png)

1. Få vist brugergrænsefladen for Beregn emulatoren ved at åbne genvejsmenuen for på Azure-ikonet i meddelelsesområdet, og vælg derefter **Vis beregne Emulator brugergrænseflade**.

    Venstre rude i Brugergrænsefladen viser de tjenester, der aktuelt anvendes på Beregn emulatoren og rolle forekomster, der kører hver tjeneste. Du kan vælge den tjeneste eller roller til at vise livscyklus, logføring og diagnostiske oplysninger i højre rude. Hvis du flytte fokus i den øverste margen i et vindue, der er inkluderet, udvides den til at udfylde den højre rude.

1. Gennemgå programmet ved at vælge kommandoer på menuen **fejlfinding** og indstille pausepunkter i din kode. Når du gennemgår programmet i fejlfindingen, opdateres ruderne med den aktuelle status for programmet. Når du stopper fejlfinding, slettes programmet installationen. Hvis dit program indeholder en web rolle, og du har angivet egenskaben Start handling til at starte webbrowseren, starter Visual Studio dit webprogram i browseren. Hvis du ændrer antallet af forekomster af en rolle i konfigurationen af tjenesten, skal du stoppe din skytjeneste og genstart derefter fejlfinding, så du kan foretage fejlfinding af disse nye forekomster af rollen.

    **Note:** Når du stopper kører eller foretage fejlfinding af din tjeneste, ikke er den lokale computer emulator og lagerplads emulator stoppet. Du skal forhindre dem eksplicit i meddelelsesområdet.


## <a name="debug-a-cloud-service-in-azure"></a>Fejlfinding af en skybaseret tjeneste i Azure

For at fejlfinde en skybaseret tjeneste fra en fjerncomputer, skal du aktivere denne funktionalitet eksplicit når du installerer skybaseret tjeneste, så der kræves services (for eksempel msvsmon.exe) er installeret på de virtuelle maskiner, der kører din rolle forekomster. Hvis du ikke aktiverer ekstern fejlfinding, når du har udgivet tjenesten, har du genpublicere tjenesten med ekstern fejlfinding aktiveret.

Hvis du aktiverer ekstern fejlfinding til en skybaseret tjeneste, kan det ikke have forringet ydeevne eller betale ekstra. Du skal ikke bruge ekstern fejlfinding på en fremstilling tjeneste, fordi klienter, der bruger tjenesten kan blive påvirket.

>[AZURE.NOTE] Når du publicerer en skybaseret tjeneste fra Visual Studio, kan du aktivere **IntelliTrace** til en hvilken som helst roller i pågældende tjeneste, der fokuserer på .NET Framework 4 eller .NET Framework 4.5. Ved hjælp af **IntelliTrace**, kan du undersøge hændelser, der er opstået i en forekomst af rolle i tidligere og Genskab kontekst fra tidspunkt. Se [fejlfinding af en publiceret skybaseret tjeneste med IntelliTrace og Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016) og [Ved hjælp af IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx).

### <a name="to-enable-remote-debugging-for-a-cloud-service"></a>Aktivere ekstern fejlfinding til en skybaseret tjeneste

1. Åbn genvejsmenuen for Azure projektet, og vælg derefter **Publicer**.

1. Vælg det **midlertidige** miljø og **foretage fejlfinding af** konfigurationen.

    Dette er kun en vejledning. Du kan vælge for at køre testmiljøer i et produktionsmiljø. Men du kan påvirke brugere Hvis du aktiverer ekstern fejlfinding på produktionsmiljøet. Du kan vælge Release konfiguration, men fejlfinding konfigurationen gør fejlfinding nemmere.

    ![Vælg fejlfinding konfigurationen](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746717.gif)

1. Skal du benytte følgende normalt, men markere afkrydsningsfeltet **Aktivér Remote fejlfinding for alle roller** på fanen **Avancerede indstillinger** .

    ![Fejlfinding af konfiguration](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746718.gif)

### <a name="to-attach-the-debugger-to-a-cloud-service-in-azure"></a>Vedhæfte fejlfindingsværktøjet til en skybaseret tjeneste i Azure

1. Udvid noden for skybaseret tjeneste i Server Explorer.

1. Åbne genvejsmenuen for den rolle eller rolle forekomst, du vil vedhæfte, og vælg derefter **Vedhæfte fejlfinding**.

    Hvis du foretager fejlfinding en rolle, vedhæfter Visual Studio-fejlfindingen hver forekomst af rollen. Fejlfindingsværktøjet brydes på et pausepunkt for den første forekomst af rolle, der kører pågældende linje af kode og opfylder betingelserne, der breakpoint. Hvis du foretage fejlfinding af en forekomst, fejlfindingen føjer til kun denne forekomst og sideskift på et pausepunkt kun, når denne bestemte forekomst kører pågældende linje af kode og opfylder de pausepunkt betingelser.

    ![Vedhæfte fejlfinding](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746719.gif)

1. Når fejlfindingsværktøjet vedhæfter en forekomst, kan du foretage fejlfinding på sædvanlig vis. Fejlfindingsværktøjet vedhæfter automatisk processen relevante vært for din rolle. Afhængigt af hvad rollen er, vedhæfter fejlfindingsværktøjet w3wp.exe, WaWorkerHost.exe eller WaIISHost.exe. Udvid noden forekomst i Server Explorer for at bekræfte den proces, som er tilknyttet fejlfindingsværktøjet. Du kan finde flere oplysninger om Azure processer i [Azure rolle arkitektur](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) .

    ![Kode type dialogboksen Vælg](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

1. For at identificere de processer, som er tilknyttet fejlfindingen, skal du åbne dialogboksen processer efter på menulinjen, vælge fejlfinding, Windows, processer. (Tastatur: Ctrl + Alt + Z) For at frigøre en bestemt proces, skal du åbne genvejsmenuen, og vælg derefter **Fjerne proces**. Eller Find noden forekomst i Server Explorer, find processen, åbne genvejsmenuen, og vælg derefter **Fjerne proces**.

    ![Fejlfinding af processer](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC690787.gif)

>[AZURE.WARNING] Undgå lang Stop på pausepunkter når remote fejlfinding. Azure behandler en proces, der ikke er længere i mere end et par minutter, som ikke svarer og holder op med at sende trafik til den pågældende forekomst. Hvis du stopper for længe, msvsmon.exe afbryder forbindelsen fra processen.

For at frigøre fejlfindingsværktøjet fra alle processer i din forekomst eller rolle, skal du åbne genvejsmenuen for rollen eller -forekomst, du fejlfinding, og vælg derefter **Afbryde forbindelsen til fejlfinding**.

## <a name="limitations-of-remote-debugging-in-azure"></a>Begrænsninger i forbindelse med ekstern fejlfinding i Azure

Fra Azure SDK 2.3 har ekstern fejlfinding følgende begrænsninger.

- Med ekstern fejlfinding aktiveret, kan du udgive en skybaseret tjeneste, hvor en hvilken som helst rolle har mere end 25 forekomster.

- Fejlfindingsværktøjet bruger port 30400 til 30424, 31400 til 31424 og 32400 til 32424. Hvis du forsøger at bruge en af disse porte, du vil ikke kunne publicere din tjeneste, og en af følgende fejlmeddelelser vises aktivitet Log til Azure: 

    - Fejl under godkendelsen .cscfg filen i forhold til filen .csdef. 
    Reserverede port område 'området' for slutpunkt Microsoft.WindowsAzure.Plugins.RemoteDebugger.Connector af rolle 'rolle' overlapper med en allerede defineret port eller et område.
    - Allokering mislykkedes. Prøv igen senere, kan du prøve at reducere VM størrelse eller antallet af forekomster af rolle, eller prøv at installere til et andet område.


## <a name="debugging-azure-virtual-machines"></a>Fejlfinding Azure virtuelle maskiner

Du kan foretage fejlfinding af programmer, der kører på Azure virtuelle maskiner ved hjælp af Server Explorer i Visual Studio. Når du aktiverer ekstern fejlfinding på en Azure virtuel maskine, installerer Azure filtypenavnet remote fejlfinding på den virtuelle maskine. Du kan derefter vedhæfte til processer på den virtuelle maskine og fejlfinding, som du normalt ville gøre.

>[AZURE.NOTE] Virtuelle maskiner, der er oprettet via Azure ressource manager stablen kan være fra en fjernplacering foretages fejlfinding ved hjælp af skyen Explorer i Visual Studio-2015. Se [Administrere Azure ressourcer med skyen Explorer](http://go.microsoft.com/fwlink/?LinkId=623031)kan finde flere oplysninger.

### <a name="to-debug-an-azure-virtual-machine"></a>Foretage fejlfinding af en Azure virtuelt

1. Udvid noden virtuelle maskiner Server Stifinder, og vælg noden på den virtuelle maskine, som du vil foretage fejlfinding.

1. Åbn genvejsmenuen, og vælg **Aktivér fejlfinding**. Når spurgt, om du er sikker på, om du vil aktivere fejlfinding på den virtuelle maskine, skal du vælge **Ja**.

    Azure installerer filtypenavnet remote fejlfinding på den virtuelle maskine du aktiverer fejlfinding.

    ![Virtuelt aktivere fejlfinding kommando](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Azure aktivitetslog](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

1. Når filtypenavnet remote fejlfinding er færdig med at installere, Åbn den virtuelle maskine genvejsmenuen, og vælg **Vedhæft fejlfinding**

    Azure får en liste over processerne på den virtuelle maskine og viser dem i Vedhæft til dialogboksen proces.

    ![Fejlfindingsværktøj kommandoen Vedhæft](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

1. Vælg **vælge** at begrænse resultater for at få vist kun typerne kode, du vil foretage fejlfinding i dialogboksen **Vedhæft for processen** . Du kan foretage fejlfinding af 32 - eller 64-bit administreret kode, oprindelig kode eller begge dele.

    ![Kode type dialogboksen Vælg](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

1. Vælge de processer, du vil udføre fejlfinding på den virtuelle maskine og derefter vælge **Vedhæft**. For eksempel kan du vælge processen w3wp.exe, hvis du ønsker at foretage fejlfinding af en WebApp på den virtuelle maskine. Se [fejlfinding af en eller flere processer i Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) og [Azure rolle arkitektur](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) kan finde flere oplysninger.

## <a name="create-a-web-project-and-a-virtual-machine-for-debugging"></a>Oprette et webprojekt og en virtuel maskine til fejlfinding

Før du udgiver projektet Azure, kan det være praktisk at teste den i et indeholdte miljø, der understøtter fejlfinding og test af scenarier, og hvor du kan installere test og overvåge programmer. En metode til at gøre dette er at fejlfinding ved hjælp af din app på en virtuel maskine.

Visual Studio ASP.NET-projekter mulighed for at oprette en praktiske virtuel maskine, som du kan bruge til app test. Den virtuelle maskine omfatter ofte behov slutpunkter som PowerShell, Fjernskrivebord og WebDeploy.

### <a name="to-create-a-web-project-and-a-virtual-machine-for-debugging"></a>Du kan oprette et webprojekt og en virtuel maskine til fejlfinding

1. I Visual Studio, skal du oprette et nyt ASP.NET-webprogram.

1. I dialogboksen nyt ASP.NET-projekt, i sektionen Azure skal du vælge **virtuelt** i rullemenuen. Lad afkrydsningsfeltet **Opret eksterne ressourcer** er markeret. Vælg **OK** for at fortsætte.

    Dialogboksen **Opret virtuel maskine på Azure** vises.


    ![ASP.NET web project-dialogboksen Opret](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746723.png)

    **Note:** Du bliver bedt om at logge på din Azure-konto, hvis du ikke allerede er logget.

1. Vælg de forskellige indstillinger for den virtuelle maskine, og klik derefter på **OK**. Du kan finde flere oplysninger i [virtuelle maskiner]( http://go.microsoft.com/fwlink/?LinkId=623033) .

    Det navn, du angiver for DNS-navn bliver navnet på den virtuelle maskine. 

    ![Oprette virtuelle maskine på Azure dialogboksen](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746724.png)

    Azure opretter virtuelt og derefter bestemmelser og konfigurerer slutpunkter som Fjernskrivebord og Web installere



1. Når den virtuelle maskine er fuldt konfigureret, kan du vælge den virtuelle maskine node i Server Explorer.

1. Åbn genvejsmenuen, og vælg **Aktivér fejlfinding**. Når spurgt, om du er sikker på, om du vil aktivere fejlfinding på den virtuelle maskine, skal du vælge **Ja**. 

    Azure installerer filtypenavnet remote fejlfinding til den virtuelle maskine du aktiverer fejlfinding.

    ![Virtuelt aktivere fejlfinding kommando](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Azure aktivitetslog](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

1. Publicere dit projekt, som beskrevet i [Sådan: installere en Web-projekt ved hjælp af et enkelt klik udgivelse i Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx). Fordi du vil foretage fejlfinding på den virtuelle maskine, på siden **Indstillinger** i guiden **Udgiv websted** , du Vælg **fejlfinding** som konfigurationen. Dette sikrer, at kode symboler er tilgængelige under fejlfinding.

    ![Indstillinger for publicering](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718349.png)

1. Vælg **fjerne ekstra filer til destinationen** i **Indstillinger for udgivelse af filen**, hvis projektet har du allerede er installeret på et tidligere tidspunkt.

1. Når projektet publicerer på den virtuelle maskine kontekstmenu i Server Explorer, vælge **Vedhæft fejlfinding**

    Azure får en liste over processerne på den virtuelle maskine og viser dem i Vedhæft til dialogboksen proces.

    ![Fejlfindingsværktøj kommandoen Vedhæft](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

1. Vælg **vælge** at begrænse resultater for at få vist kun typerne kode, du vil foretage fejlfinding i dialogboksen **Vedhæft for processen** . Du kan foretage fejlfinding af 32 - eller 64-bit administreret kode, oprindelig kode eller begge dele.

    ![Kode type dialogboksen Vælg](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

1. Vælge de processer, du vil udføre fejlfinding på den virtuelle maskine og derefter vælge **Vedhæft**. For eksempel kan du vælge processen w3wp.exe, hvis du ønsker at foretage fejlfinding af en WebApp på den virtuelle maskine. Se [fejlfinding af en eller flere processer i Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) kan finde flere oplysninger.

## <a name="next-steps"></a>Næste trin

- Bruge **Intellitrace** til at indsamle en logfil over opkald og begivenheder fra en release-server. Se [fejlfinding af en publiceret skybaseret tjeneste med IntelliTrace og Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016).
- Brug **Azure diagnosticering** til at logge detaljerede oplysninger fra kode, der kører i roller, uanset om rollerne, der kører i udviklingsmiljøet eller i Azure. Se [indsamling logføring data ved hjælp af Azure diagnosticering](http://go.microsoft.com/fwlink/p/?LinkId=400450).
