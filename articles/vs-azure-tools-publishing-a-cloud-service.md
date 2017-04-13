<properties
   pageTitle="Publicere en skybaseret tjeneste, ved hjælp af værktøjerne Azure | Microsoft Azure"
   description="Få mere at vide om, hvordan du publicerer Azure skyen service projekter ved hjælp af Visual Studio."
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

# <a name="publishing-a-cloud-service-using-the-azure-tools"></a>Publicere en skybaseret tjeneste, ved hjælp af værktøjerne Azure

Ved hjælp af værktøjerne Azure til Microsoft Visual Studio, kan du publicere dit Azure program direkte fra Visual Studio. Visual Studio understøtter integreret publicere til midlertidigt eller produktionsmiljø af en skybaseret tjeneste.

Før du kan udgive en Azure-program, skal du have et Azure-abonnement. Du skal også konfigurere en skybaseret tjeneste og lager konto der skal bruges i dit program. Du kan konfigurere disse på [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885).

>[AZURE.IMPORTANT] Når du udgiver, kan du vælge eget udviklingsmiljø for skybaseret tjeneste. Du skal også vælge en lagerplads-konto, som bruges til at gemme programpakke til installation. Efter installation fjernes programpakken fra kontoen lagerplads.

Når du udvikler og tester en Azure-program, kan du bruge Web installere til at udgive ændringer trinvist for dine web roller. Når du publicerer dit program til et installation miljø, Web installere gør det muligt at implementere ændringer direkte til den virtuelle maskine, der kører rollen web. Du behøver ikke at pakke og publicere hele Azure programmet hver gang, du vil opdatere din web rolle til at teste ændringerne. Med denne metode kan du få web rolle ændringerne tilgængelige i skyen til test uden at vente have dit program, der er publiceret på en installation-miljø.

Benyt følgende fremgangsmåde til at udgive dit Azure-program og opdatere en web rolle ved hjælp af Web installere:

- Publicere eller en Azure-program fra Visual Studio-pakke

- Opdatere en web rolle som en del af den udvikling og test

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Publicere eller en Azure-program fra Visual Studio-pakke

Når du publicerer dit Azure-program, kan du gøre et af følgende opgaver:

- Oprette en servicepakke: Du kan bruge denne pakke og konfigurationsfil tjeneste til at udgive dit program til et installation miljø fra [Azure klassisk Portal](http://go.microsoft.com/fwlink/?LinkID=213885).

- Publicere projektet Azure fra Visual Studio: Hvis du vil publicere dit program direkte til Azure, skal du bruge guiden publicere. Du kan finde oplysninger [Publicere Azure-guiden](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Sådan oprettes en servicepakke fra Visual Studio

1. Når du er klar til at udgive dit program, Åbn Solution Explorer, åbne genvejsmenuen for Azure projektet, der indeholder dine roller, og vælg Publicer.

1. Hvis du vil oprette en tjeneste pakke, skal du følge disse trin:  

  1. Vælg **pakke**i genvejsmenuen for Azure projektet.

  1. Vælg tjenestekonfiguration, som du vil oprette en pakke i dialogboksen **Pakke Azure-program** , og vælg derefter Opret konfigurationen.

  1. (valgfrit) For at slå på Fjernskrivebord for skytjenesten, når du udgiver billedet skal du markere afkrydsningsfeltet **Aktivér Fjernskrivebord for alle roller** og vælg derefter **Indstillinger** til at konfigurere Fjernskrivebord. Hvis du vil foretage fejlfinding skybaseret tjeneste, når du udgiver billedet, aktivere ekstern fejlfinding ved at vælge **Aktivér Remote fejlfinding for alle roller**.

      Du kan finde flere oplysninger [Ved hjælp af Remote Desktop med Azure roller](vs-azure-tools-remote-desktop-roles.md).

  1. For at oprette pakken, skal du vælge linket **pakke** .

      Åbn Stifinder viser placeringen af den nyoprettede pakke. Du kan kopiere denne placering, så du kan bruge den fra [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885).

  1. Hvis du vil publicere denne pakke til et installation miljø, skal du bruge denne placering som pakke placering, når du opretter en skybaseret tjeneste og implementere denne pakke i et miljø ved hjælp af [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Valgfrit) Vælg **Annuller, og fjern**for at annullere installationsprocessen i genvejsmenuen for elementet linje log aktivitet. Dette stopper installationsprocessen og sletter eget udviklingsmiljø fra Azure.

    >[AZURE.NOTE] Hvis du vil fjerne denne installation-miljø, når den er blevet installeret, skal du bruge [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Valgfrit) Når din rolle forekomster har startet, viser Visual Studio automatisk installationsmiljøet i noden **Cloud Services** i Server Explorer. Her kan du se status for de enkelte rolle forekomster. Se [administrere Azure ressourcer i skyen Stifinder](vs-azure-tools-resources-managing-with-cloud-explorer.md). Følgende illustration viser de rolle forekomster, mens de er stadig i tilstanden initialiserer:

    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>Opdatere en Web rolle som en del af den udvikling og test

Hvis din app back end-infrastruktur er stabilt, men rollerne, der web har brug for mere opdateres regelmæssigt, kan du bruge Web installere for at opdatere en web rolle i projektet. Dette er praktisk, hvis du ikke ønsker at genopbygge og geninstaller back end-arbejder roller, eller hvis du har flere web roller, og du vil opdatere kun én af rollerne, der web.

### <a name="requirements"></a>Krav

Her er kravene bruge Web installere til at opdatere din web rolle:

- **Til udvikling og test formål kun:** Ændringerne er foretaget direkte til den virtuelle maskine, hvor rollen web kører. Hvis denne virtuelt har genbruges, mistes ændringerne, fordi den oprindelige pakke, du publicerede bruges til at genskabe virtuelt for rollen. Du skal genudgive dit program for at hente de seneste ændringer til rollen web.

- **Kun web roller kan blive opdateret:** Arbejder roller kan ikke opdateres. Desuden kan du opdatere RoleEntryPoint i web role.cs.

- **Understøtter kun en enkelt forekomst af en web rolle:** Du kan ikke have flere forekomster af enhver web rolle i dit eget udviklingsmiljø. Dog understøttes flere web roller hver med kun én forekomst.

- **Skal du aktivere forbindelser til Fjernskrivebord:** Dette er påkrævet, så Web installere kan bruge brugeren og adgangskoden til at oprette forbindelse til den virtuelle maskine for at installere ændringerne på den server, der kører Internet Information Services (IIS). Desuden kan du muligvis oprette forbindelse til den virtuelle maskine til at føje et pålideligt certifikat til IIS på denne virtuelle maskine. (Dette sikrer, at forbindelsen til fjerncomputeren for IIS, der bruges af Web installere sikker.)

Følgende fremgangsmåde forudsætter, at du bruger guiden **Publicere Azure-program** .

### <a name="to-enable-web-deploy-when-you-publish-your-application"></a>Hvis du vil aktivere Web distribuere, når du publicerer dit program

1. Hvis du vil aktivere den **Aktivere Web installere** for alle web roller afkrydsningsfeltet, skal du først konfigurere forbindelser til Fjernskrivebord. Vælg **Aktivér Fjernskrivebord** for alle roller og derefter angive de legitimationsoplysninger, der skal bruges til at oprette forbindelse fra en fjernplacering i feltet **Remote Desktop konfiguration** , der vises. Du kan få flere oplysninger i [Brug af Fjernskrivebord med Azure roller](vs-azure-tools-remote-desktop-roles.md) .

1. For at aktivere Web installere for alle web rollerne i programmet, kan du vælge **Aktivér Web installere for alle web roller**.

    Der vises en gul advarselstrekant. Web Implementer bruger et selvsigneret certifikat, der ikke har tillid til som standard, som ikke kan anbefales til overførsel følsomme data. Hvis du har brug at sikre denne proces fortrolige data, kan du tilføje et SSL-certifikat, der skal bruges til Web installere forbindelser. Dette certifikat skal være et certifikat, der er tillid til. Finde oplysninger om, hvordan du gør dette, i afsnittet **Til, gøre Web installere Secure** senere i dette emne.

1. Vælg **Næste** til at vise **Oversigt over** skærmen, og vælg derefter **Publicer** for at installere skytjenesten.

    Skytjenesten er blevet publiceret. Den virtuelle maskine, som oprettes, har remote forbindelser, der er aktiveret for IIS, så Web installere kan bruges til at opdatere dine web roller uden genudgive dem.

    >[AZURE.NOTE] Hvis du har mere end én forekomst, der er konfigureret til en web rolle, vises en advarsel om, at hver web rolle er begrænset til én forekomst kun i pakken, som oprettes, hvis du vil publicere dit program. Vælg **OK** for at fortsætte. Som angivet i afsnittet krav, kan du har mere end én web rolle, men kun én forekomst af hver rolle.

### <a name="to-update-your-web-role-by-using-web-deploy"></a>Hvis du vil opdatere din Web rolle ved hjælp af Web installere

1. Hvis du vil bruge Web installere, ændre kode projektet på grund af din web roller i Visual Studio, som du vil publicere, og derefter skal du højreklikke på denne projektnode i din løsning og pege på **Publicer**. Dialogboksen **Udgiv websted** vises.

1. (Valgfrit) Hvis du har tilføjet en der er tillid til SSL-certifikat skal bruges til remote forbindelser til IIS, kan du fjerne markeringen i afkrydsningsfeltet **Tillad ikke har tillid til certifikatet** . Oplysninger om, hvordan du tilføjer et certifikat for at sikre Web installere, i afsnittet **Til at foretage Web installere sikker** senere i dette emne.

1. Hvis du vil bruge Web installere, skal Publicer ordning brugernavn og adgangskode, som du fastsætter til din forbindelse til Fjernskrivebord, når du først udgivet pakken.

  1. Skriv brugernavnet i **brugernavn**.

  1. Skriv adgangskoden i feltet **adgangskode**.

  1. (Valgfrit) Hvis du vil gemme denne adgangskode i denne profil, skal du vælge **Gem adgangskode**.

1. For at publicere ændringerne til din web rolle, skal du vælge **Publicer**.

    Statuslinjen viser **Udgiv i gang**. Når publicering er fuldført, vises der **Publicer lykkedes** . Ændringerne, der nu er installeret på rollen web på din virtuelle maskine. Nu kan du starte dit Azure-program i Azure-miljøet til at teste ændringerne.

### <a name="to-make-web-deploy-secure"></a>Gøre Web installere sikker

1. Web Implementer bruger et selvsigneret certifikat, der ikke har tillid til som standard, som ikke kan anbefales til overførsel følsomme data. Hvis du har brug at sikre denne proces fortrolige data, kan du tilføje et SSL-certifikat, der skal bruges til Web installere forbindelser. Dette certifikat skal være en der er tillid til certifikatet, som du få fra et nøglecenter (CA).

    For at gøre Web installere sikre for hver virtuelt for hver af dine web roller, skal du overføre der er tillid til certifikatet, du vil bruge til web installeres til [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885). Dette sikrer, at certifikatet, der er føjet til den virtuelle maskine, der er oprettet for rollen web, når du publicerer dit program.

1. Hvis du vil tilføje et SSL-certifikat, der er tillid til i IIS skal bruges til remote forbindelser, skal du følge disse trin:

  1. Hvis du vil oprette forbindelse til den virtuelle maskine, der kører rollen web, markere forekomsten af rollen web i **Skyen Explorer** eller **Server Explorer**, og vælg derefter kommandoen **Opret forbindelse ved hjælp af Fjernskrivebord** . Have en detaljeret vejledning om, hvordan du opretter forbindelse til den virtuelle maskine, skal du se [Brug af Fjernskrivebord med Azure roller](vs-azure-tools-remote-desktop-roles.md).

      Browseren beder dig om at hente en. RDP-fil.

  1. Hvis du vil tilføje et SSL-certifikat, Åbn management-tjenesten i IIS Manager. I IIS Manager: aktivere SSL ved at åbne linket **bindinger** i ruden **Handlinger** . Dialogboksen **Tilføj websted Binding** af vises. Vælg **Tilføj**, og vælg derefter HTTPS på rullelisten **Type** . Vælg SSL-certifikat, du var logget af et Nøglecenter og, at du har overført til [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885)på listen **SSL-certifikat** . Se [Konfigurere forbindelsesindstillingerne for Management-tjenesten](http://go.microsoft.com/fwlink/?LinkId=215824)kan finde flere oplysninger.

      >[AZURE.NOTE] Hvis du tilføjer et SSL-certifikat, der er tillid til, vises den gul advarselstrekant ikke længere i **Guiden Udgivelse**.

## <a name="include-files-in-the-service-package"></a>Medtage filer i pakken Service

Det være nødvendigt at inkludere bestemte filer i din servicepakke, så de er tilgængelige på den virtuelle maskine, der oprettes til en rolle. For eksempel kan du tilføje en .exe eller en .msi-fil, der bruges af et script til start til din servicepakke. Eller det være nødvendigt at tilføje en samling, der kræver et web rolle eller arbejder rolle projekt. Hvis du vil medtage filer skal de føjes til løsning til Azure-program.

### <a name="to-include-files-in-the-service-package"></a>Til at medtage filer i pakken service

1. Hvis du vil føje en samling til en tjeneste-pakke, skal du følge nedenstående trin:

  1. Åbn projektnode for det projekt, der mangler den refererede samling i **Solution Explorer** .

  1. Åbne genvejsmenuen for mappen **referencer** for at føje samlingen til projektet, og vælg derefter **Tilføj Reference**. Dialogboksen Tilføj Reference vises.

  1. Vælg den reference, du vil tilføje, og vælg knappen **OK** .

      Referencen er føjet til listen under mappen **referencer** .

  1. Åbne genvejsmenuen til samlingen, du har tilføjet, og vælg **Egenskaber**. Vinduet **Egenskaber** åbnes.

      Hvis du vil medtage Vælg denne samling i pakken service på **lokale kopi liste** **Sand**.

1. Åbn projektnode for det projekt, der mangler den refererede samling i **Solution Explorer** .

1. Åbne genvejsmenuen for mappen **referencer** for at føje samlingen til projektet, og vælg derefter **Tilføj Reference**. Dialogboksen **Tilføj Reference** vises.

1. Vælg den reference, du vil tilføje, og vælg knappen **OK** .

    Referencen er føjet til listen under mappen **referencer** .

1. Åbne genvejsmenuen til samlingen, du har tilføjet, og vælg **Egenskaber**. Vinduet Egenskaber åbnes.

1. Vælg **Sand**for at medtage denne samling i servicepakke, på listen **Lokale kopi** .

1. Hvis du vil medtage filer i pakken tjeneste, der er føjet til projektet web rolle, åbne genvejsmenuen for fil, og vælg derefter **Egenskaber**. Vælg **indhold** fra listen **Opbygge handling** fra vinduet **Egenskaber** .

1. Hvis du vil medtage filer i pakken tjeneste, der er føjet til projektet arbejder rolle, åbne genvejsmenuen for fil, og vælg derefter **Egenskaber**. Vælg **Kopiér Hvis nyere** fra feltet **Kopiér til output directory** liste fra vinduet **Egenskaber** .

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om publicering til Azure fra Visual Studio, se [Publicere Azure-guiden](vs-azure-tools-publish-azure-application-wizard.md).
