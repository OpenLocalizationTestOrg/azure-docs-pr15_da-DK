<properties 
    pageTitle="Machine Learning anbefalinger: JavaScript Integration | Microsoft Azure" 
    description="Azure Machine Learning anbefalinger - Integration af JavaScript - dokumentation" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="javascript" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/>

# <a name="azure-machine-learning-recommendations---javascript-integration"></a>Azure Machine Learning anbefalinger - JavaScript-Integration

>[AZURE.NOTE]Du skal begynde at bruge tjenesten anbefalinger API Kognitiv i stedet for denne version. Tjenesten anbefalinger Kognitiv vil erstatte denne tjeneste, og alle de nye funktioner udvikles der. Der er nye funktioner som "samling" support, en bedre API Explorer, en renere API grundflade, mere ensartet tilmelding/fakturering oplevelse, osv.
> Lær mere om [overførsel til den nye Kognitiv tjeneste](http://aka.ms/recomigrate)


Dette dokument viser, hvordan du integrerer webstedet med JavaScript. JavaScript'et gør det muligt at sende dataindsamling begivenheder og forbruge anbefalinger, når du opretter en anbefaling model. Alle handlinger, der er udført via JS kan udføres også fra serveren.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="1-general-overview"></a>1. oversigt over Generelt
Integrering af dit websted med Azure ML anbefalinger bestå på 2 faser:

1.  Send begivenheder til Azure ML anbefalinger. Dette gør det muligt for at opbygge en anbefaling model.
2.  Bruge anbefalingerne. Når modellen er bygget kan du bruge anbefalingerne. (Dette dokument ikke forklarer, hvordan du opretter en model, Læs startvejledningen til at få mere at vide om, hvordan).


<ins>Fase I</ins>

I den første fase indsætter du i HTML-sider et lille JavaScript-bibliotek, der gør det muligt for siden for at sende begivenheder, efterhånden som de opstår på siden html til Azure ML anbefalinger servere (via Datamarkeret):

![Drawing1][1]

<ins>Fase II</ins>

I den anden fase, når du vil vise anbefalinger på siden vælge du en af følgende indstillinger:

1. din server (på fasen af sidegengivelse) opkald Azure ML anbefalinger Server (via Datamarkeret) for at få anbefalinger. Resultaterne indeholder en liste over elementer-id. Din server skal forbedre resultaterne med elementer metadata (fx billeder, beskrivelse) og sende den oprettede side til browseren.

![Drawing2][2]

2. anden mulighed er at bruge små JavaScript-filen fra trin et til at få en simpel liste over anbefalede elementer. De data, der er modtaget her er leaner end den i den første indstilling.

![Drawing3][3]

##<a name="2-prerequisites"></a>2. forudsætninger for

1. Oprette en ny model ved hjælp af API'erne. Se startvejledningen om, hvordan det kan gøres.
2. Kode din &lt;dataMarketUser&gt;:&lt;dataMarketKey&gt; med base64. (Dette bruges til grundlæggende godkendelse til at aktivere JS koden til at ringe til API'erne).


##<a name="3-send-data-acquisition-events-using-javascript"></a>3. sende dataindsamling hændelser ved hjælp af JavaScript
Følgende trin lette afsendelse hændelser:

1.  Medtage JQuery bibliotek i din kode. Du kan hente det fra nuget i den følgende URL-adresse.

        http://www.nuget.org/packages/jQuery/1.8.2
2.  Medtage biblioteket anbefalinger JavaScript fra følgende URL-adressen: http://aka.ms/RecoJSLib1

3.  Initialiseret Azure ML anbefalinger bibliotek med de relevante parametre.

        <script>
            AzureMLRecommendationsStart("<base64encoding of username:key>",
            "<model_id>");
        </script>

4.  Sende den relevante hændelse. Se detaljeret sektion under på alle typer hændelser (eksempel på, klik på begivenhed)

        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") {      
                        AzureMLRecommendationsEvent = [];
                    }
            AzureMLRecommendationsEvent.push({ event: "click", item: "18321116" });
        </script>


###<a name="31-limitations-and-browser-support"></a>3.1. Begrænsninger og browserunderstøttelse
Dette er en referenceimplementering og den er angivet som det er. Det skal understøtter alle større browsere.

###<a name="32-type-of-events"></a>3.2. Type af hændelser
Der findes 5 typer begivenhed, der understøtter biblioteket: Klik, anbefaling, Tilføj til butik Indkøbsvogn, fjerne fra butik indkøbskurv og køb. Der er en ekstra begivenhed, der bruges til at angive brugerkonteksten kaldet Login.

####<a name="321-click-event"></a>3.2.1. Klik på begivenhed
Begivenheden skal bruges som helst en bruger har klikket på et element. Normalt, når brugeren klikker på et element er åbnet en ny side med produktoplysningerne; Denne hændelse skal udløses på denne side.

Parametre:
- begivenhed (streng, obligatorisk) - "Klik"
- element (streng, obligatorisk) - entydigt id for elementet
- Elementnavn (streng, valgfrit) - navnet på elementet
- itemDescription (streng, valgfrit) - beskrivelsen af elementet
- itemCategory (streng, valgfrit) - kategorien i elementet
        
        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "click", item: "3111718" });
        </script>

Eller med valgfrit data:

        <script>
            if (typeof AzureMLRecommendationsEvent === "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "click", item: "3111718", itemName: "Plane", itemDescription: "It is a big plane", itemCategory: "Aviation"});
        </script>


####<a name="322-recommendation-click-event"></a>3.2.2. Anbefaling skal du klikke på begivenhed
Begivenheden skal bruges som helst en bruger har klikket på et element, der blev modtaget fra Azure ML anbefalinger som en anbefalede element. Normalt, når brugeren klikker på et element er åbnet en ny side med produktoplysningerne; Denne hændelse skal udløses på denne side.

Parametre:
- begivenhed (streng, obligatorisk) - "recommendationclick"
- element (streng, obligatorisk) - entydigt id for elementet
- Elementnavn (streng, valgfrit) - navnet på elementet
- itemDescription (streng, valgfrit) - beskrivelsen af elementet
- itemCategory (streng, valgfrit) - kategorien i elementet
- forhåndsudfylder (strengmatrix, valgfrit) - frø, som genererede anbefaling forespørgslen.
- recoList (strengmatrix, valgfrit) - resultatet af anbefaling anmodningen, som genererede det element, der blev klikket på.
        
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "recommendationclick", item: "18899918" });
        </script>

Eller med valgfrit data:

        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: eventName, item: "198", itemName: "Plane2", itemDescription: "It is a big plane2", itemCategory: "Default2", seeds: ["Seed1", "Seed2"], recoList: ["199", "198", "197"]               });
        </script>


####<a name="323-add-shopping-cart-event"></a>3.2.3. Tilføje i indkøbskurv begivenhed
Begivenheden skal bruges når brugeren tilføjer et element til indkøbsvognen.
Parametre:
* begivenhed (streng, obligatorisk) - "addshopcart"
* element (streng, obligatorisk) - entydigt id for elementet
* Elementnavn (streng, valgfrit) - navnet på elementet
* itemDescription (streng, valgfrit) - beskrivelsen af elementet
* itemCategory (streng, valgfrit) - kategorien i elementet
        
        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "addshopcart", item: "13221118" });
        </script>

####<a name="324-remove-shopping-cart-event"></a>3.2.4. Fjerne indkøb indkøbskurv begivenhed
Begivenheden skal bruges, når brugeren fjerner et element til indkøbsvognen.

Parametre:
* begivenhed (streng, obligatorisk) - "removeshopcart"
* element (streng, obligatorisk) - entydigt id for elementet
* Elementnavn (streng, valgfrit) - navnet på elementet
* itemDescription (streng, valgfrit) - beskrivelsen af elementet
* itemCategory (streng, valgfrit) - kategorien i elementet
        
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "removeshopcart", item: "111118" });
        </script>

####<a name="325-purchase-event"></a>3.2.5. Køb begivenhed
Begivenheden skal bruges, når brugeren købt sin indkøbskurv.

Parametre:
* begivenhed (streng) - "køb"
* elementer (købt []) - matrix, der holder en post for hvert element har købt.<br><br>
Købte format:
    * element (streng) - entydigt id for elementet.
    * tælle (int eller streng) - antallet af elementer, som er købt.
    * pris (flydende eller streng) - valgfrit felt – prisen på elementet.

Eksemplet herunder viser køb af 3 elementer (33, 34, 35), to med alle felter udfyldt (element, antal, pris) og et (element 34) uden en pris.

        <script>
            if ( typeof AzureMLRecommendationsEvent == "undefined"){ AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "purchase", items: [{ item: "33", count: "1", price: "10" }, { item: "34", count: "2" }, { item: "35", count: "1", price: "210" }] });
        </script>

####<a name="326-user-login-event"></a>3.2.6. Bruger Login begivenhed
Azure ML anbefalinger begivenhed bibliotek opretter og bruger en cookie for at identificere hændelser, der stammer fra den samme browser. For at forbedre model resultaterne Azure ML anbefalinger, gør det muligt for at angive et entydigt bruger-id, der tilsidesætter cookie brugen af.

Begivenheden skal bruges efter brugerens logon til dit websted.

Parametre:
* begivenhed (streng) - "userlogin"
* bruger (streng) - entydig identifikation af brugeren.
        <script>
  Hvis (typeof AzureMLRecommendationsEvent == "Udefineret") {AzureMLRecommendationsEvent = [];} AzureMLRecommendationsEvent.push ({begivenhed: "userlogin" bruger: "ABCD10AA"});      </script>

##<a name="4-consume-recommendations-via-javascript"></a>4. forbruge anbefalinger via JavaScript
Den kode, der bruger anbefalingen udløses af nogle JavaScript hændelse af klientens webside. Anbefaling svaret indeholder id'erne anbefalede elementer, deres navne og deres bedømmelser. Det er bedst at bruge denne indstilling, kun til en liste over visning af de anbefalede elementer – mere komplekse håndtering af (såsom tilføje elementets metadata) skal udføres på serveren side integration.

###<a name="41-consume-recommendations"></a>4.1 forbruge anbefalinger
Anbefalinger, skal du medtage for at bruge de nødvendige JavaScript-biblioteker på siden og til at ringe til AzureMLRecommendationsStart. Se afsnit 2.

Mulighed for at forbruge anbefalinger til et eller flere elementer, du har brug for til at ringe til en metode med navnet: AzureMLRecommendationsGetI2IRecommendation.

Parametre:
* elementer (matrix af strenge -) et eller flere elementer for at få anbefalinger til. Hvis du optager et Fbt build, og du kan angive her kun ét element.
* numberOfResults (int) - antal nødvendige resultater.
* includeMetadata (boolesk, valgfrit) – Hvis er angivet til "true" Angiver, at feltet metadata skal udfyldes i resultatet.
* Behandling af funktionen - en funktion, der håndterer anbefalinger returneres. Der returneres dataene som en matrix med:
    * Element - element entydigt id
    * Name - element navn (Hvis der findes i kataloget)
    * vurdering af ansøger - anbefaling bedømmelse
    * metadata - en streng, der repræsenterer metadata for elementet

Eksempel: Følgende kode, som 8 anbefalinger til element "64f6eb0d-947a-4c18-a16c-888da9e228ba" (og ved at angive ikke includeMetadata - implicit står der kræves ingen metadata), derefter sammenkæde resultaterne i bufferen.

        <script>
            var reco = AzureMLRecommendationsGetI2IRecommendation(["64f6eb0d-947a-4c18-a16c-888da9e228ba"], 8, false, function (reco) {
                var buff = "";
                for (var ii = 0; ii < reco.length; ii++) {
                    buff += reco[ii].item + "," + reco[ii].name + "," + reco[ii].rating + "\n";
                }
                alert(buff);
            });
        </script>


[1]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing1.png
[2]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing2.png
[3]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing3.png
 
