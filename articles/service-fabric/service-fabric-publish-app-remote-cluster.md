<properties
    pageTitle="Publicere en app til en ekstern klynge med Visual Studio | Microsoft Azure"
    description="Få mere at vide, hvordan du publicerer et program til en ekstern service-strukturen klynge ved hjælp af Visual Studio."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="07/29/2016"
    ms.author="cawa" />

# <a name="publish-an-application-to-a-remote-cluster-by-using-visual-studio"></a>Udgive et program til en ekstern klynge ved hjælp af Visual Studio

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-deploy-remove-applications.md)
- [Visual Studio](service-fabric-publish-app-remote-cluster.md)

<br/>

Filtypenavnet Azure Service-strukturen til Visual Studio er en nem, gentaget og tilgængelig for scripts måde at udgive et program til en tjeneste-strukturen klynge.

## <a name="the-artifacts-required-for-publishing"></a>De elementer, der kræves til publicering

### <a name="deploy-fabricapplicationps1"></a>Installere FabricApplication.ps1

Dette er et PowerShell-script, som bruger en Publicer profilsti som en parameter for udgivelse Service-strukturen af programmer. Da dette script er en del af dit program, er du Velkommen til Rediger dem efter behov for dit program.

### <a name="publish-profiles"></a>Publicere profiler

En mappe i tjenesten strukturen programmet projektet kaldet **PublishProfiles** indeholder XML-filer, der gemmer vigtige oplysninger til publicering af et program, f.eks.:

- Tjenesten strukturen klynge forbindelsesparametre
- Stien til en parameter programfil
- Indstillinger til opgradering

Som standard dit program omfatter to publicere profiler: Local.xml og Cloud.xml. Du kan tilføje flere profiler ved at kopiere og indsætte en af standard-filerne.

### <a name="application-parameter-files"></a>Parameter programfiler

En mappe i tjenesten strukturen programmet projektet kaldet **ApplicationParameters** indeholder XML-filer til brugerdefineret program manifest parameterværdier. Programmet manifestfiler kan være parameteriseret, så du kan bruge forskellige værdier for installationsindstillinger. Hvis du vil vide mere om at anvende parametre i dit program, kan du se [administrere flere miljøer i tjenesten struktur](service-fabric-manage-multiple-environment-app-configuration.md).

>[AZURE.NOTE] For Agent tjenester, skal du oprette det projekt, første gang før du forsøger at redigere filen i redaktør eller via dialogboksen Udgiv. Dette skyldes, at der skal oprettes en del af manifestfilerne under Opret.

## <a name="to-publish-an-application-by-using-the-publish-service-fabric-application-dialog-box"></a>Udgive et program ved hjælp af dialogboksen Publicer-strukturen tjenesteprogram

Følgende trin viser, hvordan du publicerer et program ved hjælp af dialogboksen **Publicer-strukturen tjenesteprogram** leveres af Visual Studio Service-strukturen Tools.

1. I genvejsmenuen af strukturen tjenesteprogram projektet, Vælg **Publicer...** du vil se dialogboksen **Publicer-strukturen tjenesteprogram** .

    ![Den ** publicere Service strukturen programmet ** dialogboksen][0]

    Den fil, der er valgt i rullemenuen **Target profil** er, hvor alle indstillingerne, undtagen **manifestet versioner**, gemmes. Du kan genbruge en eksisterende profil eller oprette en ny ved at vælge **<... administrere profiler >** i rullemenuen **Target profil** . Når du vælger en Publicer profil, vises indholdet i de tilsvarende felter i dialogboksen. For at gemme dine ændringer på en hvilken som helst tidspunkt skal du vælge linket **Gemme profil** .    

2. Angive en lokal eller ekstern Service-strukturen klynges publicering slutpunkt i sektionen **forbindelse slutpunkt** . Hvis du vil tilføje eller ændre slutpunktet forbindelse, skal du klikke på på rullelisten **Forbindelse slutpunkt** . Listen viser de tilgængelige Service-strukturen klynge forbindelse slutpunkter, som du kan udgive, baseret på dine Azure abonnementer. Vær opmærksom på, hvis du ikke allerede er logget til Visual Studio, du vil blive bedt om at gøre dette.

    Brug dialogboksen til valg af klynge til at vælge fra sættet af tilgængelige abonnementer og klynger.

    ![Den ** dialogboksen Vælg tjeneste strukturen klynge **][1]

    >[AZURE.NOTE] Hvis du vil publicere til en vilkårlig slutpunkt (som en part klynge), kan du se afsnittet **publicering til en vilkårlig klynge slutpunkt** nedenfor.

    Når du vælger et slutpunkt, validerer Visual Studio forbindelse til den valgte Service-strukturen klynge. Hvis klyngen ikke sikkert, Visual Studio kan oprette forbindelse til det med det samme. Men hvis klyngen er sikker, skal du installere et certifikat på din lokale computer, før du fortsætter. Se, [hvordan du konfigurerer sikre forbindelser](service-fabric-visualstudio-configure-secure-connections.md) kan finde flere oplysninger. Når du er færdig, skal du vælge knappen **OK** . Den valgte klynge vises i dialogboksen **Publicer-strukturen tjenesteprogram** .

3. Gå til en parameter programfil i rullemenuen **Parameter programfil** . En parameter programfil indeholder brugerdefinerede værdier for parametre i programmet manifestfilen. Tilføje eller ændre en parameter, skal du vælge knappen **Rediger** . Angive eller ændre parameterværdi i gitteret **parametre** . Når du er færdig, skal du vælge knappen **Gem** .

    ![Den ** dialogboksen Rediger parametre **][2]

4. Brug afkrydsningsfeltet **opgradere programmet** til at angive, om dette udgive handling er en opgradering. Opgradering publicere handlinger adskiller sig fra normal publicere handlinger. Se [Service strukturen programmet opgradere](service-fabric-application-upgrade.md) til en liste over forskelle. For at konfigurere opgraderingsindstillingerne skal du vælge linket **Konfigurer opgradere indstillinger** . Vinduet opgradering parameter editor vises. Se [konfigurere opgradering af en tjeneste-strukturen program](service-fabric-visualstudio-configure-upgrade.md) mere at vide om opgradering parametre.

5. Vælg den **manifestet version...** knap til at få vist dialogboksen **Rediger versioner** . Du vil opdatere programmet og service versioner til en opgradering til foregå. Se [Service-strukturen programmet opgradere selvstudium](service-fabric-application-upgrade-tutorial.md) til at få mere at vide, hvordan programmet og tjenesten manifestet versioner virkning en opgraderingsprocessen.

    ![Den ** dialogboksen Rediger versioner **][3]

    Hvis programmet og service versioner bruger semantisk versionsstyring som 1.0.0 eller numeriske værdier i formatet af 1.0.0.0, kan du vælge indstillingen **automatisk at opdatere programmet og service versioner** . Når du vælger denne indstilling, tjenesten og programmet version tal opdateres automatisk, når en kode, config eller data pakke version er opdateret. Hvis du foretrækker at redigere versionerne manuelt, kan du fjerne markeringen i afkrydsningsfeltet for at deaktivere denne funktion.

    >[AZURE.NOTE] Alle pakke værdierne skal vises for en agent projekt, først opbygge projektet for at oprette poster i dialogboksen Service manifestet filer.

6. Når du er færdig angive alle de nødvendige indstillinger, klik på **Publicer** for at publicere dit program til den valgte Service-strukturen klynge. De indstillinger, du har angivet anvendes for processen på Publicer.

## <a name="publish-to-an-arbitrary-cluster-endpoint-including-party-clusters"></a>Publicere på en vilkårlig klynge slutpunktet (herunder part klynger)

I Visual Studio publicering oplevelse er optimeret til udgivelse på remote klynger, der er knyttet til en af dine Azure abonnementer. Men det er muligt på Udgiv på vilkårlig slutpunkter (såsom Service-strukturen part klynger) ved at redigere direkte profilen Udgiv XML. Som beskrevet ovenfor, publicere to profiler der leveres af standard -**Local.xml** og **Cloud.xml**– men du er Velkommen til at oprette flere profiler for forskellige miljøer. Du kan for eksempel vil oprette en profil for at publicere til tredjepart klynger, måske med navnet **Party.xml**.

Hvis du opretter forbindelse til en usikker klynge, alle, der kræves er klynge forbindelse slutpunktet, såsom `partycluster1.eastus.cloudapp.azure.com:19000`. I så fald ser forbindelse slutpunkt i profilen Publicer nogenlunde således ud:

```XML
<ClusterConnectionParameters ConnectionEndpoint="partycluster1.eastus.cloudapp.azure.com:19000" />
```

  Hvis du opretter forbindelse til en sikker klynge, skal du også angive oplysninger om klientcertifikat fra det lokale lager skal bruges til godkendelse. Du kan finde flere detaljer, [konfiguration af sikre forbindelser til en tjeneste-strukturen klynge](service-fabric-visualstudio-configure-secure-connections.md).

  Når din Publicer profil er konfigureret, kan du referere til den i dialogboksen Udgiv som vist nedenfor.

  ![Ny publicere profil i dialogboksen Publicer][4]

  Bemærk, at i dette tilfælde den nye profil Publicer peger på en af standard-parameter programfilerne. Dette er relevant, hvis du vil publicere den samme programkonfiguration til et antal miljøer. Derimod i tilfælde, hvor du vil have forskellige konfigurationer for hvert miljø, du vil publicere til mening det at oprette en tilsvarende programmet parameterfil.

## <a name="next-steps"></a>Næste trin

For at lære at automatisere publiceringsprocessen i et sammenhængende integration miljø, skal du se [konfigurere tjenesten strukturen fortløbende integration](service-fabric-set-up-continuous-integration.md).


[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png
[4]: ./media/service-fabric-publish-app-remote-cluster/publish-to-party-cluster.png
