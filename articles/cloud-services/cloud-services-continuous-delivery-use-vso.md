<properties
    pageTitle="Kontinuert levering med Visual Studio Team Services i Azure | Microsoft Azure"
    description="Lær, hvordan du konfigurerer din Visual Studio Team Services gruppeprojekter for at opbygge og anvende til funktionen Web App i Azure App Service eller cloud services automatisk."
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

# <a name="continuous-delivery-to-azure-using-visual-studio-team-services"></a>Kontinuert levering til Azure ved hjælp af Visual Studio Team Services

Du kan konfigurere din Visual Studio Team Services gruppeprojekter til automatisk opbygge og anvende til Azure Onlines eller cloud services.  (Se [Fortløbende levering for Cloud Services i Azure](cloud-services-dotnet-continuous-delivery.md)for at få oplysninger om, hvordan du konfigurerer et sammenhængende build og installere system ved hjælp af en *lokal* Team Foundation Server).

Dette selvstudium forudsætter, at du har Visual Studio 2013 og Azure SDK installeret. Hvis du ikke allerede har Visual Studio 2013, kan du hente det ved at vælge linket **Introduktion gratis** på [www.visualstudio.com](http://www.visualstudio.com). Installer Azure SDK fra [her](http://go.microsoft.com/fwlink/?LinkId=239540).

> [AZURE.NOTE]Du har brug for en Visual Studio Team Services-konto til at udføre dette selvstudium: Du kan [åbne en Visual Studio Team Services-konto gratis](http://go.microsoft.com/fwlink/p/?LinkId=512979).

For at konfigurere en skybaseret tjeneste til automatisk at opbygge og anvende til Azure ved hjælp af Visual Studio Team Services skal du følge disse trin.

## <a name="1-create-a-team-project"></a>1: oprette et teamprojekt

Følg vejledningen [her](http://go.microsoft.com/fwlink/?LinkId=512980) til at oprette dit teamprojekt og knytte den til Visual Studio. Denne gennemgang antages det, du bruger Team Foundation Version kontrolelement (TFVC) som din datakilde kontrolelementet løsning. Hvis du vil bruge ciffer til versionsstyring, kan du se [ciffer versionen af denne gennemgang](http://go.microsoft.com/fwlink/p/?LinkId=397358).

## <a name="2-check-in-a-project-to-source-control"></a>2: kontrollere i et projekt til versionsstyring

1. Åbn den løsning, du vil installere i Visual Studio, eller Opret en ny.
Du kan installere en WebApp eller en skybaseret tjeneste (Azure program) ved at følge trinnene i denne gennemgang.
Hvis du vil oprette en ny løsning, oprette et nyt Azure skybaseret tjeneste projekt eller et projekt med ASP.NET MVC. Sørg for, at projektet er beregnet til .NET Framework 4 eller 4.5, og hvis du opretter en skybaseret tjeneste project, tilføje en ASP.NET MVC web rolle og en kollega rolle, og vælg Internet-programmet til rollen web. Når du bliver bedt om det, kan du vælge **Internet-program**.
Hvis du vil oprette en WebApp, skal du vælge projektskabelon ASP.NET-webprogrammet og derefter vælge MVC. Se [oprette en ASP.NET WebApp i Azure App Service](../app-service-web/web-sites-dotnet-get-started.md).

    > [AZURE.NOTE] Visual Studio Team Services understøtter kun CI-installationer med Visual Studio-webprogrammer på nuværende tidspunkt. Websted projekter er uden for området.

1. Åbne genvejsmenuen for løsningen, og vælg **Tilføj løsning til versionsstyring**.

    ![][5]

1. Acceptere eller ændre standardindstillingerne, og vælg knappen **OK** . Når processen afsluttes, vises kilde kontrolelement ikoner i **Solution Explorer**.

    ![][6]

1. Åbne genvejsmenuen for løsningen, og vælg **Tjek ind**.

    ![][7]

1. I området **Ventende ændringer** af **Team Explorer**skal du skrive en kommentar til indtjekning og vælge knappen **Tjek ind** .

    ![][8]

    Bemærk indstillingerne for at medtage eller udelade bestemte ændringer, når du markerer. Hvis der er udeladt ønskede ændringer, kan du vælge linket **Medtage alle** .

    ![][9]

## <a name="3-connect-the-project-to-azure"></a>3: forbinde projektet Azure

1. Nu hvor du har et VS Team Services teamprojekt med nogle kildekode i den, er du klar til at oprette forbindelse projektet team til Azure.  Vælg din skybaseret tjeneste eller web app på [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885), eller Opret en ny ved at vælge den **+** ikonet i nederste venstre hjørne og vælge ** **Skybaseret tjeneste** eller Web App** og derefter **Hurtig oprettelse**. Vælg linket **konfigurere publicering med Visual Studio Team Services** .

    ![][10]

1. Skriv navnet på din konto, Visual Studio Team Services i tekstfeltet, og klik på linket **Godkend nu** i guiden. Du kan blive bedt om at logge på.

    ![][11]

1. Klik på **Acceptér** for at Godkend Azure til at konfigurere dit teamprojekt i VS Team Services i **Forbindelse anmode om** pop op-dialogboksen.

    ![][12]

1. Når godkendelse lykkes, kan du se en rulleliste, der indeholder en liste over dine Visual Studio Team Services teamprojekter. Vælg navnet på teamprojekt, du oprettede i de forrige trin, og vælg derefter guidens markering knappen.

    ![][13]

1. Når projektet er sammenkædet, får du vist nogle instruktioner for at tjekke ændringer ind til projektet Visual Studio Team Services team.  På din næste indtjekning, vil Visual Studio Team Services opbygge og anvende dit projekt til Azure.  Prøv det nu ved at klikke på linket **Tjek ind fra Visual Studio** , og klik derefter linket **Start Visual Studio** (eller tilsvarende **Visual Studio** knappen nederst i skærmbilledet portalen).

    ![][14]

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4: udløse en Genopbyg og geninstaller dit projekt

1. I Visual Studio **Team Explorer**skal du vælge linket **Kilde kontrolelement Explorer** .

    ![][15]

1. Gå til løsningsfilen og åbne den.

    ![][16]

1. I **Solution Explorer**skal du åbne en fil og ændre den. For eksempel ændre filen `_Layout.cshtml` under visningerne\\delt mappe i en MVC web rolle.

    ![][17]

1. Redigere logoet for webstedet, og vælg **Ctrl + S** for at gemme filen.

    ![][18]

1. I **Team Explorer**skal du vælge linket **Ventende ændringer** .

    ![][19]

1. Skriv en kommentar, og vælg derefter knappen **Tjek ind** .

    ![][20]

1. Vælg knappen **Hjem** for at vende tilbage til startsiden i **Team Explorer** .

    ![][21]

1. Vælg linket **opbygger** for at få vist builds i gang.

    ![][22]

    **Team Explorer** viser, at et build er blevet udløst for din indtjekning.

    ![][23]

1. Dobbeltklik på navnet på Opret i gang for at få vist en detaljeret logfil som build skrider frem.

    ![][24]

1. Mens Opret er i gang, skal du se nærmere på build definitionen, der blev oprettet, da du linkede TFS til Azure ved hjælp af guiden.  Åbne genvejsmenuen for build definitionen og vælge **Rediger opbygge Definition**.

    ![][25]

    I fanen **udløser** kun du se build definitionen er angivet af hver indtjekning som standard.

    ![][26]

    Du kan se installationsmiljøet er indstillet til navnet på din skybaseret tjeneste eller web app i under fanen **proces** . Hvis du arbejder med webapps, er de egenskaber, du får vist være forskellige fra dem, der er vist her.

    ![][27]

1. Angiv værdier for egenskaberne, hvis du vil have forskellige værdier end standardindstillingerne. Egenskaberne for Azure publicering er i afsnittet **installation** .

    I følgende tabel vises de tilgængelige egenskaber i afsnittet **installation** :

  	|Egenskaben|Standardværdi|
  	|---|---|
  	|Tillad ikke har tillid til certifikater|Hvis falsk, skal være logget SSL-certifikater ved en rodnøglecenter.|
  	|Tillad opgradering.|Giver mulighed for installation til at opdatere en eksisterende installation i stedet for at oprette en ny. Bevarer IP-adressen.|
  	|Slet ikke|Hvis sand, ikke overskriver en eksisterende ikke-relaterede installation (opgraderingen er tilladt).|
  	|Stien til installationsindstillinger|Stien til filen .pubxml til en WebApp, i forhold til rodmappen på repo. Ignoreres i efterfølgende skytjenester.|
  	|SharePoint-installation-miljø|Den samme som navnet på tjenesten.|
  	|Azure-installation miljø|Web app eller skyen navnet på tjenesten.|

1. Hvis du bruger flere service konfigurationer (.cscfg filer), kan du angive den ønskede tjenestekonfiguration i indstillingen **opretter, Avanceret, MSBuild argumenter** . For eksempel for at bruge ServiceConfiguration.Test.cscfg skal angive MSBuild argumenter linjeindstilling `/p:TargetProfile=Test`.

    ![][38]

    På tidspunkt fuldføres din opbygge korrekt.

    ![][28]

1. Hvis du dobbeltklikker på build navnet, viser Visual Studio en **Opbygge oversigt**, herunder eventuelle testresultater fra tilknyttede enhed testprojekter.

    ![][29]

1. Du kan se den tilknyttede installation under fanen **installationer** på [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885), når det midlertidige miljø er markeret.

    ![][30]

1.  Gå til URL-adressen til dit websted. En WebApp blot ved at klikke på knappen **Gennemse** på kommandolinjen. Vælg URL-adressen til en skybaseret tjeneste, i sektionen **Hurtig Glance** på siden **Dashboard** , der viser det midlertidige miljø til en skybaseret tjeneste. Installationer fra fortløbende integration til skytjenester er udgivet på det midlertidige miljø som standard. Du kan ændre dette ved at angive egenskaben **Alternative skyen Service-miljø** til **fremstilling**. Dette skærmbillede viser, hvor webstedets URL-adresse er på tjenesten skyen dashboardside.

    ![][31]

    Der åbnes en ny fane i browseren for at få vist webstedet, der kører.

    ![][32]

    Til skytjenester, hvis du foretager andre ændringer i dit projekt, du udløser opbygger mere, og du vil samle flere installationer. Det seneste markeret som aktiv.

    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5: Geninstaller et tidligere build

Dette trin gælder for skytjenester og er valgfrit. Vælg en tidligere installation i Azure klassisk portalen, og vælg derefter knappen **Geninstaller** at spole dit websted til en tidligere indtjekning.  Bemærk, at dette vil udløse en ny build i TFS og oprette en ny post i oversigt over dine installation.

![][34]

## <a name="6-change-the-production-deployment"></a>6: ændre installationen i et produktionsmiljø

Dette trin gælder kun for skytjenester, ikke webapps. Når du er klar, kan du opgradere det midlertidige miljø til produktionsmiljøet ved at vælge knappen **Ombyt** i portalen Azure klassisk. Nyligt udløst midlertidige miljøet hæves til fremstilling, og den forrige produktionsmiljø eventuelt bliver et midlertidige miljø. Den aktive installation kan være forskellige for fremstilling og arrangere miljøer, men installation oversigten over seneste builds er den samme uanset miljø.

![][35]

## <a name="7-run-unit-tests"></a>7: køre test af enhed

Dette trin gælder kun for web apps, ikke cloud services. Du kan køre test af enhed for at sætte et kvalitet porte i din installation, og hvis de går ned, kan du stoppe installationen.

1.  Tilføje et enhed testprojekt i Visual Studio.

    ![][39]

1.  Føje projektreferencer til det projekt, du vil teste.

    ![][40]

1.  Tilføj nogle test af enhed. For at komme i gang skal du prøve en dummy test, der altid går.

        ```
        using System;
        using Microsoft.VisualStudio.TestTools.UnitTesting;

        namespace UnitTestProject1
        {
            [TestClass]
            public class UnitTest1
            {
                [TestMethod]
                [ExpectedException(typeof(NotImplementedException))]
                public void TestMethod1()
                {
                    throw new NotImplementedException();
                }
            }
        }
        ```

1.  Redigere definitionen for build, Vælg fanen **proces** , og udvid noden **Test** .

1.  Angiv **fejl build på test mislykkedes** til sand. Det betyder, at installationen ikke opstå, medmindre testene, der overfører.

    ![][41]

1.  Kø et nyt build.

    ![][42]

    ![][43]

1. Mens Opret procedure, se, om dens status.

    ![][44]

    ![][45]

1. Når Opret er færdig, skal du kontrollere resultaterne.

    ![][46]

    ![][47]

1.  Prøv at oprette en test, der mislykkes. Tilføje en ny test ved at kopiere den første opgave, omdøbe den og kommentere udpege linjen af kode, hvor der står NotImplementedException er en forventet undtagelse.

        ```
        [TestMethod]
        //[ExpectedException(typeof(NotImplementedException))]
        public void TestMethod2()
        {
            throw new NotImplementedException();
        }
        ```

1. Se i Ændr til kø et nyt build.

    ![][48]

1. Få vist testresultater for at se oplysninger om fejlen.

    ![][49]

    ![][50]

## <a name="next-steps"></a>Næste trin
Få mere at vide om test i Visual Studio Team Services af enheder, kan du se [køre test af enhed i din build](http://go.microsoft.com/fwlink/p/?LinkId=510474). Hvis du bruger ciffer, se [Del din kode i ciffer](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) og [fortløbende installationsvejledning til Azure App-tjenesten](../app-service-web/app-service-continuous-deployment.md).  Du kan finde flere oplysninger om Visual Studio Team Services, [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png

[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG
