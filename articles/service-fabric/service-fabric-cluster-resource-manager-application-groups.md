<properties
   pageTitle="Tjenesten strukturen klynge ressourcestyring - programmet grupper | Microsoft Azure"
   description="Oversigt over programgruppen funktionaliteten i tjenesten strukturen klynge ressourcestyring."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# <a name="introduction-to-application-groups"></a>Introduktion til programmet grupper
Tjenesten strukturen klynge ressourcestyring administrerer typisk klyngeressourcer ved at sprede afkrydsningsfeltet Indlæs (repræsenteret via målepunkter) jævnt i hele klyngen. Tjenesten strukturen håndterer også kapaciteten for knuderne i klyngen og klyngen som helhed gennem begrebet kapacitet. Denne funktion for en masse forskellige typer arbejdsbelastninger, men mønstre, som gør omfattende brug af forskellige strukturen forekomster af tjenesteprogrammer flytte nogle gange ekstra krav. Nogle ekstra krav er typisk:

- Muligheden for at reservere kapacitet for en programmet forekomst tjenester på nogle antallet af knuder
- Mulighed for at begrænse antallet af knuder, et bestemt udvalg af tjenester i et program har tilladelse til at køre på
- Definerer kapacitet på forekomsten programmet selve for at begrænse det telefonnummer eller den samlede ressourceforbrug af tjenesterne indeni

For at kunne opfylder kravene, har vi udviklet understøttelse hvad vi kalder programmet grupper.

## <a name="managing-application-capacity"></a>Administration af programmet kapacitet
Programmet kapacitet kan bruges til at begrænse antallet af knuder, der bruges af et program, samt den samlede metriske belastning, som de programmer forekomster på individuelle noder. Det kan også bruges til at reservere ressourcer i klynge for programmet.

Programmet kapacitet kan angives for nye programmer, når de oprettes. Det kan også opdateres til eksisterende programmer, der blev oprettet uden at angive programmet kapacitet.

### <a name="limiting-the-maximum-number-of-nodes"></a>Begrænse det maksimale antal noder
Den nemmeste use case for programmet kapacitet er, når et program forskellige skal være begrænset til et bestemt maksimalt antal noder. Hvis nogen programmet kapacitet ikke er angivet, Service strukturen klynge ressourcestyring der oprettes en forekomst af replikaer efter normal regler (justering af eller -defragmentering), hvilket betyder som regel, at sine tjenester fordeles på tværs af alle de tilgængelige noder i klyngen, eller hvis defragmentering er aktiveret nogle vilkårlig, men mindre antallet af knuder.

Følgende billede viser potentielle placeringen af en forekomst af programmet uden at det maksimale antal noder, der er defineret, og angiv derefter samme program med et maksimalt antal noder. Bemærk, at der er ingen garantier, der er foretaget om hvilke replikaer eller forekomster, hvor tjenester vil få placeret sammen.

![Programmet forekomst definerer maksimale antallet af knuder][Image1]

Programmet har ikke programmet kapacitet, der er angivet i eksemplet med venstre, og der er tre tjenester. CRM har foretaget et logisk beslutning om at sprede alle kopier på tværs af seks tilgængelige noder for at opnå den bedste balance i klyngen. I eksemplet højre se vi det samme program, der er begrænset på tre noder, og hvor tjenesten strukturen CRM har opnået den bedste balance for kopier af programmets tjenester.

Den parameter, der styrer denne funktionsmåde kaldes MaximumNodes. Denne parameter kan angive under oprettelse af programmet eller opdateret for en programmet forekomst, der allerede kørte, hvori der sag Service-strukturen CRM begrænser kopier af programtjenester til definerede maksimale antallet af knuder.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;

adUpdate.Metrics.Add(appMetric);
```

## <a name="application-metrics-load-and-capacity"></a>Programmet målepunkter, Indlæs og kapacitet
Programmet grupper også muligt at definere målepunkter, der er knyttet til en bestemt program-forekomst, samt kapaciteten af programmet med hensyn til disse målepunkter. Så kan du definere, som mange tjenester, som du vil kunne oprettes i

For hver metrikværdi er der 2 værdier, der kan konfigureres til at beskrive kapaciteten for programforekomsten:

-   Samlet programmet kapacitet – repræsenterer den samlede kapacitet af programmet for en bestemt metrikværdi. Tjenesten strukturen CRM forsøger at begrænse summen af metriske masser af dette program tjenester til den angivne værdi Hvis den programtjenester allerede forbrug Indlæs op til denne grænse, tillade Service strukturen klynge ressourcestyring desuden oprettelsen af nye tjenester eller partitioner, der kan medføre samlede Indlæs i gå over denne grænse.
-   Maksimale Node kapacitet – angiver den maksimale samlede belastning for kopier af de programmer tjenester på en enkelt node. Hvis samlede belastning på noden handler om denne kapacitet, forsøger Service-strukturen CRM at flytte replikaer til andre noder, så kapacitet begrænsningen er overholdt.

## <a name="reserving-capacity"></a>Reservere kapacitet
En anden bruges for programmet grupper normalt til at sikre, at ressourcer inden for klyngen er reserveret for et givet program-forekomst, selv hvis programmet forekomst endnu ikke er tjenester i modellen, eller også selvom de ikke forbrug ressourcerne, der endnu. Lad os se nærmere på, hvordan det virker.  

### <a name="specifying-a-minimum-number-of-nodes-and-resource-reservation"></a>Angive et minimum af knuder og ressourcereservation
At reservere ressourcer til en forekomst af programmet kræver, at angive et par ekstra parametre: *MinimumNodes* og *NodeReservationCapacity*

- MinimumNodes - ligesom angivelse af mål maksimale antallet af knuder, tjenester i et program kan køre på, du kan også angive det mindste antal noder, der skal køre et program på. Denne indstilling definerer effektivt antallet af knuder, ressourcerne, der skal være reserveret på minimum sikrer kapacitet i klynge som en del af oprettelse af programmet forekomst.
- NodeReservationCapacity - NodeReservationCapacity kan defineres for hver metrikværdi i programmet. Dette definerer mængden metriske Indlæs reserveret til programmet på en hvilken som helst node, hvor alle replikaerne eller forekomster af tjenester i modellen er placeret.

Lad os se nærmere på et eksempel på reservation af kapacitet:

![Forekomster af tjenesteprogrammer definerer reserverede kapacitet][Image2]

I eksemplet med venstre har programmer ikke en hvilken som helst program kapacitet, der er defineret. Tjenesten strukturen klynge ressourcestyring saldo programmets underordnede services replikaer og forekomster sammen med dem fra andre tjenester (uden for programmet) for at sikre balance i klyngen.

I eksemplet til højre, Antag, at programmet blev oprettet med en MinimumNodes, der er angivet til 2, MaximumNodes, der er angivet til 3, og et program, metrisk defineret med en reservation af 20, maksimal kapacitet på en node 50%, og en samlet programmet kapacitet på 100, Service-strukturen reservere kapaciteten på to noder til programmet blå og tillader ikke andre replikaer i klynge for at bruge denne egenskab. Denne reserveret programmet kapacitet anses anvendte og optalt mod rest klynge.

Når der oprettes et program med reservation, ressourcestyring klynge reservere kapacitet, der er lig med MinimumNodes * NodeReservationCapacity i klyngen, men den kan ikke reservere kapacitet på bestemte knuder, indtil kopier af den programtjenester oprettes og placeret. Dette giver fleksibilitet, da noder er valgt for nye Kopier kun, når de oprettes. Kapaciteten er reserveret på en bestemt node, når mindst én replika er placeret på den.

## <a name="obtaining-the-application-load-information"></a>Bestil Indlæs programoplysninger
For hvert program kan, er programmet kapacitet defineret du få oplysninger om den samlede belastning rapporteret af kopier af sine tjenester. Tjenesten strukturen indeholder forespørgsler PowerShell og Managed API til dette formål.

Indlæse kan for eksempel hentes, ved hjælp af følgende PowerShell-cmdlet:

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

```

Output fra denne forespørgsel skal indeholde de grundlæggende oplysninger om programmet kapacitet, der blev angivet for programmet, som Minimum noder og maksimale noder. Der vil også være oplysninger om antallet af knuder, der i øjeblikket ved hjælp af programmet. Derfor for hver indlæsning metrisk vil der være oplysninger om:
- Metriske navn: Navnet på metrikværdien.
-   Reservation kapacitet: Klynge kapacitet, der er reserveret i klyngen til dette program.
-   Programmet belastning: Samlet indlæsning af dette program underordnede replikaer.
-   Programmet kapacitet: Maksimalt tilladte værdi af programmet Indlæs.

## <a name="removing-application-capacity"></a>Fjerne programmet kapacitet
Når programmet kapacitet parametre er angivet for et program, kan de fjernes ved hjælp af Update programmet API'er eller PowerShell-cmdletter. Eksempel:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Denne kommando fjerner alle parametre for programmet kapacitet fra programmet på computeren, og tjenesten strukturen klynge ressourcestyring begynder at behandle dette program som andre programmer i den klynge, der ikke har disse parametre, der er defineret. Effekten af kommandoen sker med det samme og klynge Ressourcestyring, slettes alle parametre for programmet kapacitet til dette program. angive dem igen, kræver opdatering programmet API'er skal ringes op med de relevante parametre.

## <a name="restrictions-on-application-capacity"></a>Begrænsninger for programmet kapacitet
Er der flere begrænsninger på programmet kapacitet parametre, der skal overholdes. I tilfælde af valideringsfejl, blive oprettelse eller opdatering af programmet afvist med en fejl.
Alle parametre for heltal skal være ikke-negative tal.
Desuden for individuelle parametre er begrænsninger, der som følger:

-   MinimumNodes skal aldrig være større end MaximumNodes.
-   Hvis kapacitet for en metrikværdi i indlæsning er defineret, skal de følge disse regler:
  - Node Reservation kapacitet må ikke være større end maksimale Node kapacitet. Du kan for eksempel begrænse kapacitet til metrisk "CPU" under noden til 2 enheder og forsøger at reservere 3 enheder på hver node.
  - Hvis MaximumNodes er angivet, derefter være produktet af MaximumNodes og maksimale Node kapacitet ikke større end samlede programmet kapacitet. Eksempelvis hvis du angiver den maksimale Node kapacitet for indlæsning metrikværdi "CPU" 8, og du angive den maksimale noder til 10, skal derefter samlede programmet kapacitet være større end 80 for denne metrikværdi for indlæsning.

Begrænsninger, håndhæves både under oprettelse af programmet (på klientsiden), og under opdatering af programmet (på serversiden). Under oprettelsen dette er et eksempel på en Ryd overtrædelse af kravene siden MaximumNodes < MinimumNodes og kommandoen mislykkes i klienten, før anmodningen sendes endda til tjenesten strukturen klynge:

``` posh
New-ServiceFabricApplication –Name fabric:/MyApplication1 –MinimumNodes 6 –MaximumNodes 2
```

Et eksempel på ugyldig opdatering er som følger. Hvis vi tager et eksisterende program og opdatere maksimale noder med en anden værdi, overfører opdateringen:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MaximumNodes 2
```

Herefter kan vi forsøge at opdatere mindste noder:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MinimumNodes 6
```

Klienten har ikke nok kontekst om programmet, så den kan opdateringen til at videregive til tjenesten strukturen klynge. Dog i klynge, tjenesten validerer den nye parameter sammen med de eksisterende parametre og mislykkes handlingen update, fordi værdien fjenden mindste knuderne er større end værdien for maksimale noder. I dette tilfælde forbliver programmet kapacitet parametre uændret.

Disse begrænsninger, der er placeret på plads i rækkefølgen for klynge Resource Manager skal kunne give den bedste placering for kopier af programtjenester '.

## <a name="how-not-to-use-application-capacity"></a>Hvordan ikke at bruge programmet kapacitet

-   Brug ikke programmet kapacitet til at begrænse programmet til et angivet delsæt af knuder: selvom Service-strukturen sikrer, at maksimale noder er overholdt for hvert program, der indeholder programmet kapacitet angivet, brugere kan beslutte, hvilke knuder, oprettes den en forekomst på. Dette kan opnås ved hjælp af placeringen begrænsninger for tjenester.
-   Brug ikke programmet kapacitet til at sikre, at to tjenester fra det samme program altid skal placeres sammen med hinanden. Dette kan opnås ved hjælp af forbindelsen mellem processorer relation mellem tjenester, og forbindelse kan være begrænset kun til de tjenester, der faktisk skal placeres sammen.

## <a name="next-steps"></a>Næste trin
- For flere oplysninger om de andre indstillinger, der er tilgængeligt til konfiguration af tjenester udtjekning emnet på de andre klynge ressourcestyring konfigurationer tilgængelige [Få mere at vide om at konfigurere Services](service-fabric-cluster-resource-manager-configure-services.md)
- For at vide om, hvordan ressourcestyring klynge administrerer og afbalancerer Indlæs i klyngen, skal du se artiklen på [justering af belastning](service-fabric-cluster-resource-manager-balancing.md)
- Start fra begyndelsen og [få en introduktion til tjenesten strukturen klynge ressourcestyring](service-fabric-cluster-resource-manager-introduction.md)
- Yderligere oplysninger om hvordan målepunkter fungerer normalt Læs mere om [Tjenesten strukturen Indlæs målepunkter](service-fabric-cluster-resource-manager-metrics.md)
- Ressourcestyring klynge indeholder mange indstillinger til at beskrive klyngen. Hvis du vil vide Læs mere om dem denne artikel på [en beskrivelse af en tjeneste-strukturen klynge](service-fabric-cluster-resource-manager-cluster-description.md)


[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
