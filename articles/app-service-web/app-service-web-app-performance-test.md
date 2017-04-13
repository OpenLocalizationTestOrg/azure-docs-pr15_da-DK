<properties
   pageTitle="Teste din Azure online ydeevne | Microsoft Azure"
   description="Køre Azure web app performance-test for at kontrollere, hvordan din app håndterer bruger Indlæs. Måle svartid og finde fejl, der kan angive problemer."
   services="app-service\web"
   documentationCenter=""
   authors="ecfan"
   manager="douge"
   editor="jimbe"/>

<tags
   ms.service="app-service-web"
   ms.workload="web"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="05/25/2016"
   ms.author="estfan; manasma; ahomer"/>

# <a name="performance-test-your-azure-web-app-under-load"></a>Ydeevnen teste din Azure online belastning

Kontrollere din online går, før du starte det eller installere opdateringer til fremstilling. På den måde, du kan bedre vurdere om din app er klar til frigivelse. Funktionalitet, der er mere sikker på, at din app kan håndtere trafikken under spidsbelastning brug eller på din næste marketing opslagsnål.

Under offentlige eksempelvisning kan du performance-test din app gratis i portalen Azure.
Disse test simulere bruger belastning på din app i en bestemt periode og måle din app svar. For eksempel viser dine testresultater hvor hurtigt din app besvarer et bestemt antal brugere. De kan også vise, hvor mange anmodninger mislykkedes, som kan skyldes problemer med din app.      

![Finde problemer med ydeevnen i din online](./media/app-service-web-app-performance-test/azure-np-perf-test-overview.png)

## <a name="before-you-start"></a>Før du starter

* Hvis du ikke allerede har et, skal du bruge en [Azure-abonnement](https://account.windowsazure.com/subscriptions). Få mere at vide, hvordan du kan [åbne en Azure-konto gratis](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).

* Du skal bruge en [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) -konto for at bevare historikken for test din ydeevne. En passende konto oprettes automatisk, når du konfigurerer din performance-testen. Eller du kan oprette en ny konto eller brug en eksisterende konto, hvis du er ejeren af kontoen. 

* Installer din app til test i et ikke-produktionsmiljø. Har din app, bruge en App-serviceaftale end den plan, der anvendes fremstilling. På den måde, som du ikke påvirker alle eksisterende kunder eller langsommere din app i fremstilling. 

## <a name="set-up-and-run-your-performance-test"></a>Konfigurere og udføre performance-test

0.  Log på [Azure-portalen](https://portal.azure.com). For at bruge en Visual Studio Team Services-konto, du ejer, skal du logge på som ejeren af kontoen.

0.  Gå til din online.

    ![Gå til Gennemse alle Web Apps, din online](./media/app-service-web-app-performance-test/azure-np-web-apps.png)

0.  Gå til **Performance-testen**.

    ![Gå til funktioner, Performance-Test](./media/app-service-web-app-performance-test/azure-np-web-app-details-tools-expanded.png)
 
0. Nu kan du sammenkæde en [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) -konto for at bevare historikken for test din ydeevne.

    Hvis du har et Team Services-konto, der skal bruges, Vælg kontoen. Hvis du ikke oprette en ny konto.

    ![Vælg eksisterende Team Services-konto, eller Opret en ny konto](./media/app-service-web-app-performance-test/azure-np-no-vso-account.png)

0.  Oprette din performance-testen. Angive oplysninger, og Kør testen. 

Du kan se resultaterne i realtid, mens testen kører.

Lad os antage, at vi har en app, der har givet ud kuponer ved sidste år Feriekalender salg. Denne hændelse bestået 15 minutter med en spidsbelastning belastning på 100 samtidige kunder. Vi vil dobbeltklikke antallet af kunder år. Vi vil forbedre kundetilfredshed ved at reducere indlæsningstiden side fra 5 sekunder til to sekunder. Så kan vi teste vores opdaterede app ydeevne med 250 brugere til 15 minutter.

Vi vil simulere belastning på vores app ved at generere virtuelle brugere (kunder) der Besøg vores websted på samme tid. Dette viser os hvor mange anmodninger ned eller svarer langsomt.

  ![Oprette, konfigurere og udføre performance-test](./media/app-service-web-app-performance-test/azure-np-new-performance-test.png)

   *  URL-adressen til din online-standard føjes automatisk. 
   Du kan ændre URL-adressen for at teste andre sider (kun HTTP GET anmodninger).

   *  Vælg en placering, der er tættest på dine brugere til at oprette Indlæs for at simulere lokale forhold og reducere ventetid.

  Her er en test i gang. Vores siden indlæses langsommere end vi vil under det første minut.

  ![Performance-test i gang med data i realtid](./media/app-service-web-app-performance-test/azure-np-running-perf-test.png)

  Når en test er færdigt, skal vi få mere at vide, at siden indlæses meget hurtigere efter det første minut. Dette hjælper med at identificere, hvor vi vil kan begynde at foretage fejlfinding af problemet.

  ![Færdige performance-test viser resultaterne, herunder mislykkede anmodninger](./media/app-service-web-app-performance-test/azure-np-perf-test-done.png)

## <a name="test-multiple-urls"></a>Test flere URL-adresser

Du kan også køre ydeevne test, der omfatter flere URL-adresser, der repræsenterer et til slut brugerscenarie ved at uploade filen Visual Studio Web Test. Nogle af måder, hvorpå du kan oprette en Visual Studio Web Test fil er:

* [Registrere trafik, der bruger Fiddler og eksportere som en Visual Studio Web testfil](http://docs.telerik.com/fiddler/Save-And-Load-Traffic/Tasks/VSWebTest)
* [Opret en Indlæs testfil i Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release)

Overføre og køre en Visual Studio Web testfil:
 
0. Følg trinnene ovenfor for at åbne den **nye ydeevne teste** blade.
   Vælg indstillingen CONFIGFURE TESTE ved hjælp af til at åbne bladet **Konfigurer test ved hjælp af** i denne blade.  

    ![Åbne den Konfigurer belastning test af blade](./media/app-service-web-app-performance-test/multiple-01-authoring-blade.png)

0. Kontrollér, at den TEST TYPE er indstillet til **Visual Studio Web Test** , og vælg arkivfilen HTTP.
    Brug ikonet "mappe" til at åbne dialogboksen fil Datavælger.

    ![Overførsel af en flere URL-adressen Visual Studio Web Test-fil](./media/app-service-web-app-performance-test/multiple-01-authoring-blade2.png)

    Når filen er blevet overført, kan du se listen over URL-adresser testes i afsnittet detaljer om URL-adresse.
 
0. Angiv bruger Indlæs og teste varighed, og derefter vælge **Kør test**.

    ![Hvis du vælger bruger Indlæs og varighed](./media/app-service-web-app-performance-test/multiple-01-authoring-blade3.png)

    Når testen er færdig, skal se du resultaterne i to ruder. Viser performnace oplysninger som en serie af diagrammer i den venstre rude.

    ![Ruden ydeevne resultater](./media/app-service-web-app-performance-test/multiple-01a-results.png)

    I højre rude vises en liste over mislykkede anmodninger, med typen fejl og antallet gange, det er opstået.

    ![Ruden anmodning om fejl](./media/app-service-web-app-performance-test/multiple-01b-results.png)

0. Kør testen igen ved at vælge ikonet **Kør** øverst i højre rude.

    ![Køre en test](./media/app-service-web-app-performance-test/multiple-rerun-test.png)

##  <a name="q--a"></a>Q & A

#### <a name="q-is-there-a-limit-on-how-long-i-can-run-a-test"></a>Sp: er der en grænse på hvor længe jeg kan køre en test? 

**A**: Ja, du kan køre din test op til en time i portalen Azure.

#### <a name="q-how-much-time-do-i-get-to-run-performance-tests"></a>Sp: hvor lang tid får jeg køre performance-test? 

**A**: efter offentlige eksemplet skal du få 20.000 virtuelle bruger minutter (VUMs) gratis hver måned med kontoen Visual Studio Team Services. En VUM er antallet af virtuelle brugere ganget med antallet af minutter i din test. Hvis dine behov overskrider grænsen for gratis, kan du købe mere tid og købe kun du bruger.

#### <a name="q-where-can-i-check-how-many-vums-ive-used-so-far"></a>Sp: hvor kan jeg kontrollere, hvor mange VUMs, jeg har brugt indtil nu?

**A**: Du kan se dette beløb i portalen Azure.

![Gå til dit Team Services-konto](./media/app-service-web-app-performance-test/azure-np-vso-accounts.png)

![Kontrollere VUMs bruges](./media/app-service-web-app-performance-test/azure-np-vso-accounts-vum-summary.png)

#### <a name="q-what-is-the-default-option-and-are-my-existing-tests-impacted"></a>Sp: Hvad er standardindstillingen og påvirkes min eksisterende test?

**A**: standardindstillingen for ydeevnen Indlæs test er en manuel test - den samme som før flere URL-adressen teste indstilling blev føjet til portalen.
Eksisterende test fortsætte med at bruge den URL-adresse, der er konfigureret og fungerer som før.

#### <a name="q-what-features-not-supported-in-the-visual-studio-web-test-file"></a>Sp: hvilke funktioner understøttes ikke i Visual Studio Web Test filen?

**A**: på nuværende tidspunkt denne funktion ikke understøtter Web Test plug-ins, datakilder og udtrækning af regler. Du skal redigere filen Web Test for at fjerne disse. Vi håber på at tilføje support for disse funktioner i fremtidige opdateringer.

#### <a name="q-does-it-support-any-other-web-test-file-formats"></a>Sp: den understøtter eventuelle andre Web Test-filformater?
  
**A**: på Præsenter kun Visual Studio Web Test formatfiler understøttes.
Vi vil være glade for at høre fra dig, hvis du har brug for support til andre filformater. Sende en e-mail til [vsoloadtest@microsoft.com](mailto:vsoloadtest@microsoft.com).

#### <a name="q-what-else-can-i-do-with-a-visual-studio-team-services-account"></a>Sp: Hvad kan jeg ellers gøre med en Visual Studio Team Services-konto?

**A**: for at finde din nye konto skal du gå til ```https://{accountname}.visualstudio.com```. Dele din kode opbygge test, og registrere arbejde og Forsendelsesdato software – alt sammen i skyen ved hjælp af en hvilken som helst værktøj eller sprog. Lær mere om, hvordan [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) -funktioner og tjenester hjælpe dit team, samarbejde og installere løbende.

## <a name="see-also"></a>Se også

* [Køre enkle skyen performance-test](https://www.visualstudio.com/docs/test/performance-testing/getting-started/get-started-simple-cloud-load-test)
* [Køre Apache Jmeter performance-test](https://www.visualstudio.com/docs/test/performance-testing/getting-started/get-started-jmeter-test)
* [Optage og afspille skybaseret Indlæs test](https://www.visualstudio.com/docs/test/performance-testing/getting-started/record-and-replay-cloud-load-tests)
* [Ydeevnen teste din app i skyen](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)
