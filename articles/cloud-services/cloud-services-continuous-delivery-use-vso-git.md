<properties
    pageTitle="Kontinuert levering med ciffer og Visual Studio Team Services i Azure | Microsoft Azure"
    description="Lær, hvordan du konfigurerer din Visual Studio Team Services gruppeprojekter for at bruge ciffer til automatisk at opbygge og anvende til funktionen Web App i Azure App Service eller cloud services."
    services="cloud-services"
    documentationCenter=".net"
    authors="mlearned"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="mlearned"/>

# <a name="continuous-delivery-to-azure-using-visual-studio-team-services-and-git"></a>Kontinuert levering til Azure ved hjælp af Visual Studio Team Services og ciffer

Du kan bruge Visual Studio Team Services gruppeprojekter til vært for et ciffer lager til din kildekode og automatisk opbygge og implementere til Azure webapps eller cloud services, hver gang du push en bekræftelse til lageret.

Du skal Visual Studio 2013 og Azure SDK installeret. Hvis du ikke allerede har Visual Studio 2013, kan du hente det ved at vælge linket **Introduktion gratis** på [www.visualstudio.com](http://www.visualstudio.com). Installer Azure SDK fra [her](http://go.microsoft.com/fwlink/?LinkId=239540).


> [AZURE.NOTE]Du har brug for en Visual Studio Team Services-konto til at udføre dette selvstudium: Du kan [åbne en Visual Studio Team Services-konto gratis](http://go.microsoft.com/fwlink/p/?LinkId=512979).

For at konfigurere en skybaseret tjeneste til automatisk at opbygge og anvende til Azure ved hjælp af Visual Studio Team Services skal du følge disse trin.

## <a name="1-create-a-git-repository"></a>1: oprette et ciffer lager

1. Hvis du ikke allerede har en Visual Studio Team Services-konto, kan du få en [her](http://go.microsoft.com/fwlink/?LinkId=397665). Når du opretter dit teamprojekt, kan du vælge ciffer som systemet kilde kontrolelement. Følg vejledningen for at oprette forbindelse Visual Studio til dit teamprojekt.

2. I **Team Explorer**skal du vælge linket **klone denne lager** .

    ![][3]

3. Angive placeringen af den lokale kopi, og vælg derefter knappen **Klon** .

## <a name="2-create-a-project-and-commit-it-to-the-repository"></a>2: oprette et projekt, og foretag til lageret

1. Vælg **Ny** linket til at oprette et nyt projekt i det lokale lager i **Team Explorer**, i sektionen **løsninger** .

    ![][4]

2. Du kan installere en WebApp eller en skybaseret tjeneste (Azure program) ved at følge trinnene i denne gennemgang. Oprette et nyt Azure skybaseret tjeneste projekt eller et projekt med ASP.NET MVC. Sørg for, at projektet er beregnet til .NET Framework 4 eller nyere. Hvis du opretter en skybaseret tjeneste project, kan du tilføje en ASP.NET MVC web rolle og en kollega rolle.
Hvis du vil oprette en WebApp, skal du vælge projektskabelon **ASP.NET-webprogrammet** og derefter vælge **MVC**. Se [oprette en ASP.NET WebApp i Azure App Service](../app-service-web/web-sites-dotnet-get-started.md) kan finde flere oplysninger.

3. Åbne genvejsmenuen for løsningen, og vælg **Udfør**.

    ![][7]

4. Hvis dette er første gang du har brugt ciffer i Visual Studio Team Services, skal du give nogle oplysninger til at identificere dig selv i ciffer. I området **Ventende ændringer** af **Team Explorer**skal du angive dit brugernavn og din mailadresse. Skriv en kommentar for tildelingen, og vælg derefter knappen **Udfør** .

    ![][8]

5. Bemærk indstillingerne for at medtage eller udelade bestemte ændringer, når du markerer. Hvis de ønskede ændringer er udeladt, Vælg **Medtag alle**.

6. Du har nu bekræftet ændringerne i din lokale kopi af lageret. Dernæst skal synkronisere ændringerne med serveren ved at vælge linket **synkronisering** .

## <a name="3-connect-the-project-to-azure"></a>3: forbinde projektet Azure

1. Nu hvor du har et ciffer lager i Visual Studio Team Services med nogle kildekode i den, er du klar til at forbinde dine ciffer lager til Azure.  Vælg din skybaseret tjeneste eller web app på [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885), eller Opret en ny ved at vælge den + ikonet i nederste venstre hjørne og vælge ** **Skybaseret tjeneste** eller Web App** og derefter **Hurtig oprettelse**.

    ![][9]

3. Vælg linket **konfigurere publicering med Visual Studio Team Services** til skytjenester. Web Apps, skal du vælge linket **konfigurere installation fra versionsstyring** .

    ![][10]

2. Skriv navnet på din konto, Visual Studio Team Services i tekstfeltet, og vælg linket **Godkend nu** i guiden. Du kan blive bedt om at logge på.

    ![][11]

3. Vælg **Acceptér** for at tillade Azure til at konfigurere dit teamprojekt i Visual Studio Team Services i **Forbindelse anmode om** pop op-dialogboksen.

    ![][12]

4. Når godkendelse lykkes, kan du se en rulleliste, der indeholder dit Visual Studio Team Services gruppeprojekter.  Vælg navnet på teamprojekt, du oprettede i de forrige trin, og vælg guidens markering knappen.

    ![][13]

    Næste gang du push en bekræftelse til din lager vil Visual Studio Team Services opbygge og anvende dit projekt til Azure.

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4: udløse en Genopbyg og geninstaller dit projekt

1. I Visual Studio, skal du åbne en fil og ændre den. For eksempel ændre filen `_Layout.cshtml` under visningerne\\delt mappe i en MVC web rolle.

    ![][17]

2. Redigere teksten i sidefoden på webstedet, og Gem filen.

    ![][18]

3. Åbn genvejsmenuen for noden løsning, projektnode eller den fil, du har ændret i **Solution Explorer**, og vælg derefter **Udfør**.

4. Skriv en kommentar, og vælg **Udfør**.

    ![][20]

5. Vælg **Synkroniser** linket.

    ![][38]

6. Vælg linket **opslagsnål** til at overføre din bekræftelse til lager i Visual Studio Team Services. (Du kan også bruge knappen **Synkroniser** til at kopiere dine anvendelser til lageret. Forskellen er, at **synkronisere** også trækker de seneste ændringer fra lageret.)

    ![][39]

7. Vælg knappen **Hjem** for at vende tilbage til startsiden i **Team Explorer** .

    ![][21]

8. Vælg **opbygger** til at få vist builds i gang.

    ![][22]

    **Team Explorer** viser, at et build er blevet udløst for din indtjekning.

    ![][23]

9. For at få vist en detaljeret logfil som build skrider frem, skal du dobbeltklikke på navnet på Opret i gang.

10. Mens Opret er i gang, skal du se nærmere på build definitionen, der blev oprettet, da du brugte guiden til at oprette et link til Azure.  Åbne genvejsmenuen for build definitionen og vælge **Rediger opbygge Definition**.

    ![][25]

11. I fanen **udløser** kun du se build definitionen er angivet af hver indtjekning, som standard. (En skybaseret tjeneste, Visual Studio Team Services opbygger og installerer den overordnede gren på det midlertidige miljø automatisk. Du har stadig gøre et manuelt trin for at installere på webstedet. En web App, som ikke har testmiljø, installerer det overordnede gren direkte til webstedet.

    ![][26]

1. Du kan se installationsmiljøet er indstillet til navnet på din skybaseret tjeneste eller web app i under fanen **proces** .

    ![][27]

1. Angiv værdier for egenskaberne, hvis du vil have forskellige værdier end standardindstillingerne. Egenskaber for Azure publicering er i afsnittet **installation** , og du muligvis også angive MSBuild parametre. For eksempel i en skybaseret tjeneste projekt for at angive en tjenestekonfiguration end "Skyen", indstille parametrene MSbuild til `/p:TargetProfile=[YourProfile]` hvor *[YourProfile]* svarer til en tjeneste konfigurationsfil med et navn som ServiceConfiguration. *YourProfile*.cscfg.

    I følgende tabel vises de tilgængelige egenskaber i afsnittet **installation** :

  	|Egenskaben|Standardværdi|
  	|---|---|
  	|Tillad ikke har tillid til certifikater|Hvis falsk, skal være logget SSL-certifikater ved en rodnøglecenter.|
  	|Tillad opgradering.|Giver mulighed for installation til at opdatere en eksisterende installation i stedet for at oprette en ny. Bevarer IP-adressen.|
  	|Slet ikke|Hvis sand, ikke overskriver en eksisterende ikke-relaterede installation (opgraderingen er tilladt).|
  	|Stien til installationsindstillinger|Stien til filen .pubxml til en WebApp, i forhold til rodmappen på repo. Ignoreres i efterfølgende skytjenester.|
  	|SharePoint-installation-miljø|Den samme som navnet på tjenesten.|
  	|Azure-installation miljø|Web app eller skyen navnet på tjenesten.|

1. På tidspunkt fuldføres din opbygge korrekt.

    ![][28]

1. Hvis du dobbeltklikker på build navnet, viser Visual Studio en **Opbygge oversigt**, herunder eventuelle testresultater fra tilknyttede enhed testprojekter.

    ![][29]

1. Du kan se den tilknyttede installation under fanen **installationer** på [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885), når det midlertidige miljø er markeret.

    ![][30]

1.  Gå til URL-adressen til dit websted. En web App, du blot vælge knappen **Gennemse** i portalen. Vælg URL-adressen til en skybaseret tjeneste, i sektionen **Hurtig Glance** på siden **Dashboard** , der viser det midlertidige miljø.

    Installationer fra fortløbende integration til skytjenester er udgivet på det midlertidige miljø som standard. Du kan ændre dette ved at angive egenskaben **Alternative skyen Service-miljø** til **fremstilling**. Her er, hvor webstedets URL-adresse på tjenesten skyen dashboardside.

    ![][31]

    Der åbnes en ny fane i browseren for at få vist webstedet, der kører.

    ![][32]

1.  Hvis du foretager andre ændringer i dit projekt, du udløser opbygger mere, og du vil samle flere installationer. Den seneste udgave er markeret som aktiv.

    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5: Geninstaller et tidligere build

Dette trin er valgfrit. Vælg en tidligere installation i Azure klassisk portalen, og vælg **Geninstaller** til spole dit websted til en tidligere indtjekning. Bemærk, at dette vil udløse en ny build i TFS og oprette en ny post i oversigt over dine installation.

![][34]

## <a name="6-change-the-production-deployment"></a>6: ændre installationen i et produktionsmiljø

Når du er klar, kan du opgradere det midlertidige miljø til produktionsmiljøet ved at vælge **Ombyt** i portalen Azure klassisk. Nyligt udløst midlertidige miljøet hæves til fremstilling, og den forrige produktionsmiljø, hvis der overhovedet skal bliver et midlertidige miljø. Den aktive installation kan være forskellige for fremstilling og arrangere miljøer, men installation oversigten over seneste builds er den samme uanset miljø.

![][35]

## <a name="7-deploy-from-a-working-branch"></a>7: installere fra en arbejde gren.

Når du bruger ciffer, du normalt foretager ændringer i en arbejde forgrening og integrere i den overordnede gren, når din udvikling når en færdig tilstand. I udviklingsfasen af et projekt, skal du vil opbygge og anvende den arbejde gren til Azure.

1. Vælg knappen **Hjem** i **Team Explorer**, og vælg derefter knappen **forgreninger** .

    ![][40]

2. Vælg **Ny gren** linket.

    ![][41]

3. Skriv navnet på grenen, såsom "arbejder", og vælg **Opret gren**. Dette opretter en ny lokal gren.

    ![][42]

4. Publicere grenen. Vælg navnet på gren i **ikke-udgivne forgreninger**, og vælg **Publicer**.

    ![][44]

6. Som standard udløse kun ændringer i den overordnede gren et sammenhængende build. Vælg siden **opbygger** i **Team Explorer**for at konfigurere fortløbende build for en arbejde gren skal, og vælg **Rediger opbygge Definition**.

7. Åbn fanen **Indstillinger for datakilde** . Vælg **Klik her for at tilføje en ny række**under **overvågede forgrenes til fortløbende integration og build**.

    ![][47]

8. Angiv den gren, du har oprettet, som referencer/hoveder/arbejde.

    ![][48]

9. Foretager en ændring i koden, Åbn genvejsmenuen for den ændrede fil, og vælg derefter **Udfør**.

    ![][43]

10. Vælg linket **Usynkroniserede anvendelser** , og vælg knappen **Synkroniser** eller linket **Push** for at kopiere ændringerne til kopien af den arbejde gren i Visual Studio Team Services.

    ![][45]

11. Gå til visningen **opbygger** , og find den nyeste version, som kun har udløste til arbejde gren.

## <a name="next-steps"></a>Næste trin

Du kan få flere tip til brugen af ciffer med Visual Studio Team Services, [udvikling og dele dine kode i ciffer ved hjælp af Visual Studio](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) og oplysninger om brug af et ciffer lager, der ikke styres af Visual Studio Team Services til at udgive til Azure, se [Fortløbende installation til Azure App-tjenesten](../app-service-web/app-service-continuous-deployment.md). Du kan finde flere oplysninger om Visual Studio Team Services, [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateTeamProjectInGit.PNG
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png
[3]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
[4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
[7]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
[8]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[9]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
[10]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
[11]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
[12]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
[13]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
[20]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[21]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
[22]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
[23]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
[28]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateANewAccount.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG
