<properties
    pageTitle="Guiden Hurtig start: Machine Learning anbefalinger API | Microsoft Azure"
    description="Azure Machine Learning anbefalinger - guiden Hurtig Start"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="luisca"/>

# <a name="quick-start-guide-for-the-cognitive-services-recommendations-api"></a>Startvejledning til Kognitiv Services anbefalinger API

Dette dokument beskrives, hvordan til indbyggede din tjeneste eller program tilladelse til at bruge [Anbefalinger API](http://go.microsoft.com/fwlink/?LinkId=759710).
Du kan finde flere oplysninger om anbefalinger API og andre Kognitiv tjenester [her](http://go.microsoft.com/fwlink/?LinkId=759709). I denne vejledning, kan du også finde [Anbefalinger API Reference](http://go.microsoft.com/fwlink/?LinkId=759348) praktiske.


<a name="Overview"></a>
## <a name="general-overview"></a>Generel oversigt

Dette dokument er en trinvis vejledning. Vores mål er at vejlede dig gennem de trin, der er nødvendigt at undervise en model, og peg ressourcer, der gør det muligt at bruge modellen fra dit produktionsmiljø.

Denne øvelse tager om 30 minutter.

Hvis du vil bruge [Anbefalinger API](http://go.microsoft.com/fwlink/?LinkId=759710), skal du benytte følgende fremgangsmåde:

1. Oprette en model – en model er en objektbeholder af dine brugerdata, katalogdata og anbefaling modellen.
1. Importér katalogdata - et katalog indeholder metadataoplysninger om elementerne.
1. Importér Brugsdata - Brugsdata kan overføres i en af 2 måder (eller begge):
  -  Ved at overføre en fil, der indeholder brugerdataene.
  -  Sende data acquisition begivenheder.
  Normalt kan du overføre en brugen fil for at kunne oprette en indledende anbefaling model (bootstrap) og bruge det, indtil systemet samler nok data ved hjælp af acquisition dataformat.
1. Oprette en anbefaling model – dette er en asynkron handling, hvori anbefaling systemet tager alle Brugsdata og opretter en anbefaling model. Denne handling kan tage flere minutter eller flere timer afhængigt af størrelsen af dataene og parametrene build konfiguration. Når udløser Opret, får du et build-ID. Bruge det til at kontrollere, hvornår build processen er afsluttet før du begynder at bruge anbefalinger.
1. Bruge anbefalinger - få anbefalinger til et bestemt element eller en liste over elementer.

<a name="GetStarted"></a>
### <a name="lets-get-started"></a>Lad os komme i gang!

Du kan begynde at opbygge en anbefalinger model. Derefter skal vi guider dig om, hvordan du bruger de resultater, der genereres af modellen til power anbefalinger på en e-handel websted.

<a name="Ex1Task1"></a>
#### <a name="task-1---signing-up-for-the-recommendations-api"></a>Opgave 1 - signerende til API'EN anbefalinger ####

I denne opgave, skal du tilmelde dig tjenesten anbefalinger API og opretter en anbefalinger model.

1. Gå til **Azure-portalen** på [http://portal.azure.com/](http://portal.azure.com/) og logge på med din Azure-konto.

1.  Klik på **+ Ny**.

1. Vælg indstillingen **Intelligence** .

1. Vælg **Kognitiv Services API'er** produktet.
Dette produkt, så du kan starte et abonnement til Kognitiv tjenesterne API'er (ansigt, tekst Analytics, Computer syn osv.). I dag fokuserer vi på API'EN anbefalinger.

1. Angiv det **kontonavn** for abonnementet anbefalinger på landingssiden Kognitiv Services API. (For instace: "MyRecommendations"). Dette navn må ikke have en hvilken som helst mellemrum.

1. På **API type**skal du vælge **anbefalinger**.

1. Under **priser niveau**, kan du vælge en plan. Du kan vælge **gratis** niveau for 10.000 transaktioner/måned. Dette er en gratis plan, så det er en god måde at komme i gang med systemet. Når du går til fremstilling, anbefales det, du overvejer anmodning lydstyrken og ændre plantypen i overensstemmelse hermed. (Bemærk: Du kan have kun én gratis niveau abonnement ad gangen)

1. Vælg en **Ressourcegruppe**, eller Opret en ny, hvis du ikke allerede har et.

1. Du kan ændre andre elementer i dialogboksen Opret. Det skal understreges, provideren ressource i dag understøttes kun fra USA datacentre.

1. Klik på **Opret**, når du er færdig med en hvilken som helst valg.

1. Vent et par minutter for ressourcen indtil skal installeres.
Når den er installeret, kan du gå til afsnittet **taster** i bladet **Indstillinger** , hvor du får en primære og sekundære nøgle for at bruge API.  Kopiér den primære nøgle, som du skal bruge den, når du opretter din første model.

<a name="Ex1Task2"></a>
#### <a name="task-2---did-you-bring-your-data"></a>Opgave 2 - fik du tage dine data? ####

Anbefalinger API lærer fra dit katalog og dine transaktioner for at give godt produkt anbefalinger. Det betyder, at du har brug for til at levere med god data om dine produkter (vi kalder dette en **katalog** -fil) og et sæt af transaktioner stort nok til at finde interessante mønstre for forbrug (vi kalder denne **brugen**).

1. Normalt ville du forespørge databasen transaktioner for disse elementer af oplysninger.
Hvis du ikke har dem lige ved hånden, har vi angivet nogle eksempeldata for dig baseret på Microsoft Store transaktionsdata.

 Du kan hente data fra [her](http://aka.ms/RecoSampleData). Kopiér og Pak MsStoreData.Zip til en anden mappe på din lokale computer.

 > **Note:** På eksempelkoden, som du vil downloade og køre i opgave 3 har eksempeldata, der allerede er integreret indeni – så denne opgave er valgfri.  Dog denne opgave, kan du hente mere realistisk datasæt, og gør det muligt at forstå input til anbefalinger API bedre.

1.  Nu Lad os se nærmere på katalogfilen. Gå til det sted, hvor du kopierede data.
 Åbn katalogfilen i **Notesblok**.

 Du vil bemærke, at katalogfilen er ganske enkelt. Det har følgende format`<itemid>,<item name>,<product category>`

 >  AAA-04294, OfficeLangPack 2013 32/64 E34 Online DwnLd, Office <br>
 > AAA-04303, OfficeLangPack 2013 32/64 ET Online DwnLd, Office  <br>
 > C9F 00168, KRUSELL Kiruna spejlvendes forside til Nokia Lumia 635 - kameler, Tilbehør

 Det skal understreges, at en fil kan være meget bedre, for eksempel kan du tilføje metadata om produkter (vi kalder disse *element funktioner*). Du bør se sektionen [format](http://go.microsoft.com/fwlink/?LinkID=760716) i referencen API til få mere at vide på samme måde som katalog.

1. Lad os gør det samme med brugerdataene. Vil du bemærke, at brugen datoen er af formatet `<User Id>,<Item Id>,<Time Stamp>,<Event>`.

  > 00037FFEA61FCA16, 288186200, 2015/08/04T11:02:52, køb 0003BFFDD4C2148C 297833400, 2015/08/04T11:02:50, køb 0003BFFDD4C2118D 297833300, 2015/08/04T11:02:40, køb 00030000D16C4237 297833300, 2015/08/04T11:02:37, køb 0003BFFDD4C20B63 297833400, 2015/08/04T11:02:12, køb 00037FFEC8567FB8 297833400, 2015/08/04T11:02:04, køb

Bemærk, at de første tre elementer obligatorisk. Hændelsestypen er valgfrit. Du kan se [brugen format](http://go.microsoft.com/fwlink/?LinkID=760712) for flere oplysninger om dette emne.

 > **Hvor meget data har du brug for?**
 <p>
Lige godt, og afhænger det virkelig selve brugerdataene. Systemet lærer når brugere du køber forskellige elementer. Det er vigtigt at vide, hvilke elementer der købes i de samme transaktioner for nogle builds som FBT. (Vi kalder denne *samtidig forekomster*). Er en god ide at have de fleste elementer i 20 transaktioner eller derover, så hvis du havde 10.000 elementer i kataloget, vil vi anbefaler, at du har mindst 20 gange, antallet af transaktioner eller om 200.000 transaktioner.
Endnu engang er dette en tommelfingerregel. Du skal til at eksperimentere med dine data.
</p>

<a name="Ex1Task3"></a>
####Opgave 3 – oprette en anbefalinger model####

Nu, hvor du har en konto, og du har data, Lad os oprette din første model.

I denne opgave skal bruge du eksempelprogrammet til at oprette din første model.

1. Første skal du være opmærksom på [Anbefalinger API Reference](http://go.microsoft.com/fwlink/?LinkId=759348).

1. Hente [Northwind](http://go.microsoft.com/fwlink/?LinkID=759344) til en lokal mappe.

1. Åbn i Visual Studio **RecommendationsSample.sln** løsningen findes i mappen **C#** .

1. Åbn filen **SampleApp.cs** . Bemærk, at følgende trin i filen:
 + Oprette en Model
 + Tilføje en fil
 + Tilføje en brugen fil
 + Udløse en build til modellen
 + Få en anbefaling, der er baseret på et par elementer
<p></p>

1. Erstat værdien for feltet **AccountKey** med tasten fra opgave 1.

1. Trin til løsningen, og du vil se, hvordan en model oprettes.

1. Prøv at erstatte de katalog for og brugen filer, du netop har hentet, for at oprette en ny model for Microsoft Store eller adressekartotek anbefalinger. Du skal ændre navnet på modellen samt og de elementer, som du anmode om anbefalinger.

1. Når modellen er oprettet, skal du lægge mærke **model-ID** , som du skal bruge den, når der anmodes om anbefalinger i din produktionsmiljø.

>  Få mere at vide mere om opbygge typer, og hvordan du evaluere kvaliteten af builds [her](cognitive-services-recommendations-buildtypes.md).

<a name="Ex1Task4"></a>
### <a name="putting-your-model-in-production"></a>Hvordan man anbringer din model i fremstilling! ###

Nu hvor du ved, hvordan du opretter en model og forbruge anbefalinger, er næste trin at indsætte det i fremstilling på webstedet, mobile programmet eller integrere i dit CRM eller ERP-system.
Tydeligt, hver af disse installationer skal være forskellige. Da der anmodes om anbefalinger API som en webtjeneste, skal du kunne kald den nemt fra enhver af disse forskellige miljøer.

**Vigtige:**  Hvis du vil vise anbefalinger fra en offentlig klient (for eksempel dit websted for elektronisk handel), skal du oprette en proxyserver for at give anbefalingerne. Dette er vigtigt, så din API-nøgle ikke vises på eksterne (potentielt upålidelige) enheder.

Her er nogle ideer af steder, hvor du kan bruge anbefalinger:

### <a name="product-page"></a>Produkt side

**Element anbefalinger**
<p>Hvis modellen er accepteret på køb af data, kan det kunden at *finde produkter, der sandsynligvis er interesse for personer, der har købt kildeelementet*.</p>
<p>Klik på data, hvis modellen blev oplæring i den, så kunden at *finde produkter, der har sandsynligvis besøg på personer, der har besøgt kildeelementet*. Denne type model kan returnere lignende elementer.</p>

**Ofte købte sammen anbefalinger**
<p>A ofte købte sammen build kunne uddannelse, så du kan hente sæt af elementer, som potentielt købes sammen med elementet.</p>

### <a name="check-out-page"></a>Se siden

**Element anbefalinger**
<p>En model kan tage som anbefalinger indtastet en liste over elementer. Så du kan overføre alle elementer i din kurv som input til få anbefalinger.
I dette tilfælde give modellen anbefalinger, der interesserer givet alle elementer i din kurv.
</p>

### <a name="landing-page"></a>Landingssiden

**Bruger anbefalinger**
<p>
En model kan tage som anbefalinger Indtast bruger-id.  Dette vil bruge oversigten over transaktioner af den pågældende bruger til at levere tilpassede anbefalinger til den angivne bruger.
</p>

Se [Få element anbefalinger dokumentation](http://go.microsoft.com/fwlink/?LinkID=760719).

<a name="Ex1Task6"></a>
### <a name="whats-next"></a>Hvad skal der ske nu?
Tillykke, hvis du har nået så langt! Hvis du vil vide mere, du kan besøge den komplette [Anbefalinger API Reference](http://go.microsoft.com/fwlink/?LinkId=759348) , hvis du har spørgsmål, ikke objekterne bør kontakte os påmlapi@microsoft.com
