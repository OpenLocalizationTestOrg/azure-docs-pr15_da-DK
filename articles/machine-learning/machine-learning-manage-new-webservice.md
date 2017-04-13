<properties
    pageTitle="Administrere en webtjeneste, ved hjælp af portalen Azure Machine Learning Web Serivces | Microsoft Azure"
    description="Administrere adgang til Azure Machine Learning arbejdsområder og installere og administrere ML API webtjenester"
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="v-donglo"/>


# <a name="manage-a-web-service-using-the-azure-machine-learning-web-services-portal"></a>Administrere en webtjeneste, ved hjælp af portalen Azure Machine Learning Web Services

Du kan administrere din maskine Learning nye og klassisk webtjenester ved hjælp af portalen Microsoft Azure Machine Learning Web Services. Da klassisk webtjenester og nye webtjenester er baseret på forskellige underliggende teknologier, har du en smule anderledes administrationsfunktioner for hver af dem.

I portalen Machine Learning webtjenester kan du:

- Overvåge, hvordan webtjenesten bruges.
- Konfigurere beskrivelsen, opdatere de pågældende taster til World Wide web service (kun Nyhed), opdatere din lagerplads konto vigtige (kun Nyhed), Aktiver logføring, og aktivere eller deaktivere eksempeldata.
- Slet webtjenesten.
- Opret, Slet eller Opdater fakturering plan (kun ny).
- Tilføje og slette slutpunkter (kun klassisk)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="manage-new-web-services"></a>Administrere nye webtjenester

Til at administrere din nye webtjenester:

1.  Log på portalen [Microsoft Azure Machine Learning webtjenester](https://services.azureml.net/quickstart) ved hjælp af kontoen Microsoft Azure - du kan bruge den konto, der er knyttet til Azure abonnementet.
2.  Klik på **Web-tjenester**i menuen.

Dette viser en liste over udløst webtjenester for dit abonnement. 

Hvis du vil administrere en webtjeneste, skal du klikke på Web-tjenester. Fra siden webtjenester kan du:

- Klik på webtjenesten for at administrere den.
- Klik på den fakturering Plan for webtjenesten til at opdatere den.
- Slette en webtjeneste.
- Kopiere en webtjeneste og installerer det til et andet område.

Når du klikker på en webtjeneste, åbnes websiden for Hurtig start. Websiden for Hurtig start har to menuindstillinger, der gør det muligt at administrere din webtjeneste:

- **DASHBOARD** - kan du få vist Web-tjenester.
- **KONFIGURER** - kan du tilføje en beskrivende tekst, opdatere nøglen for kontoen lagerplads, der er knyttet til webtjenesten, og aktivere eller deaktivere eksempeldata.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Overvågning af hvordan webtjenesten bruges ###

Klik på fanen **DASHBOARD** .

Du kan se overordnede brugen af din webtjeneste dashboard over en periode. Du kan vælge punktum til at få vist den periode rullemenuen i øverste højre hjørne af brugen diagrammer. Dashboard viser følgende oplysninger:

- **Anmodninger Over tid** viser en trin graf over antallet af anmodninger over den markerede tidsperiode. Det kan hjælpe med at identificere, om du oplever spidser i anvendelse.
- **Anmodning om svar anmodninger** viser det samlede antal anmodning om svar opkald tjenesten har modtaget over den markerede tidsperiode, og hvor mange af dem mislykkedes.
- **Gennemsnitlig anmodning om svar beregne tid** viser et gennemsnit af den tid, der er behov for at udføre de modtagne anmodninger.
- **Anmodninger om batchen** viser det samlede antal batchen anmodninger om tjenesten har modtaget over den markerede tidsperiode, og hvor mange af dem mislykkedes.
- **Forsinkelse på gennemsnitligt Job** viser et gennemsnit af den tid, der er behov for at udføre de modtagne anmodninger.
- **Fejl** vises det samlede antal fejl, der er opstået på opkald til webtjenesten.
- **Services omkostninger** viser gebyrerne for fakturering plan der er knyttet til tjenesten.

### <a name="configuring-the-web-service"></a>Konfiguration af webtjenesten ###

Klik på menupunktet **KONFIGURER** .

Du kan opdatere følgende egenskaber:

* **Beskrivelse** kan du angive en beskrivelse til webtjenesten.
* **Titel** kan du angive en titel til webtjenesten
* **Taster** kan du rotere dine primære og sekundære API nøgler.
* **Lagerplads kontonøgle** kan du opdatere nøglen for kontoen lagerplads, der er knyttet til Web ændringerne. 
* **Aktivere eksempeldata** kan du give eksempeldata, som du kan bruge til at afprøve tjenesten anmodning om svar. Hvis du oprettede webtjenesten i Machine Learning Studio, hentes eksempeldataene fra dataene din bruges til at undervise din model. Hvis du oprettede tjenesten fra et program, hentes dataene fra eksempeldataene, du har angivet som en del af JSON-pakke.

### <a name="managing-billing-plans"></a>Administrere fakturering planer ###

Klik på menupunktet **planer** fra siden web services Hurtig start. Du kan også klikke på den plan, der er knyttet til bestemte webtjeneste til at administrere denne plan.

* **Ny** kan du oprette en ny plan.
* **Tilføj/fjern Plan forekomst** gør det muligt at "kan skalere" en eksisterende plan tilføje kapacitet.
* **Opgradering/Nedgraderingsrettigheder** gør det muligt at "skalere op" en eksisterende plan tilføje kapacitet.
* **Slette** gør det muligt at slette en plan.

Klik på en plan til at få vist dets dashboard. Dashboardet giver dig snapshot eller plan brugen over en valgt tidsperiode. For at vælge tidsperioden til at få vist, skal du klikke på den **periode** rullemenuen i øverste højre hjørne af dashboard. 

Dashboardet plan indeholder følgende oplysninger:

* **Beskrivelse af plan** viser oplysninger om omkostninger og kapacitet, der er knyttet til planen.
* **Planlægge brugen** viser antallet af transaktioner og Beregn timer, som er blevet debiteret mod planen.
* **Webtjenester** viser antallet af webtjenester, der bruger denne plan.
* **Toppen Web Service ved opkald** viser de øverste fire Web-tjenester, der er at foretage opkald, der betaler mod planen.
* **Toppen Web-tjenester ved at beregne timer** viser de øverste fire Web-tjenester, der bruger Beregn ressourcer, der betaler mod planen.

## <a name="manage-classic-web-services"></a>Administrere klassisk webtjenester

> [AZURE.NOTE] Fremgangsmåden i dette afsnit er relevante for administration af klassiske webtjenester via Azure Machine Learning Web Services-portalen. Du kan finde oplysninger om administration af klassiske webtjenester via Machine Learning Studio og Azure klassisk portalen [Administrer et Azure Machine Learning arbejdsområde](machine-learning-manage-workspace.md).

Til at administrere din klassisk webtjenester:

1.  Log på portalen [Microsoft Azure Machine Learning webtjenester](https://services.azureml.net/quickstart) ved hjælp af kontoen Microsoft Azure - du kan bruge den konto, der er knyttet til Azure abonnementet.
2.  Klik på **Klassisk webtjenester**i menuen.

Hvis du vil administrere en klassisk webtjeneste, skal du klikke på **Klassisk webtjenester**. Fra siden klassisk webtjenester kan du:

- Klik på webtjenesten for at få vist de tilknyttede slutpunkter.
- Slette en webtjeneste.

Når du administrerer en klassisk webtjeneste, skal administrere du hver af slutpunkterne separat. Når du klikker på en webtjeneste på siden webtjenester, åbnes liste over slutpunkter, der er knyttet til tjenesten. 

Du kan tilføje og slette slutpunkterne på tjenesten, på siden klassisk webtjeneste slutpunkt. Du kan finde flere oplysninger om tilføjelse af slutpunkter, [Oprette slutpunkter](machine-learning-create-endpoint.md).

Klik på en af slutpunkterne for at åbne websiden for Hurtig start. På siden Hurtig start er der to menuindstillinger, der gør det muligt at administrere din webtjeneste:

- **DASHBOARD** - kan du få vist Web-tjenester.
- **KONFIGURER** - kan du tilføje beskrivende tekst, skal du slå logføring af fejl ved til og fra opdatering nøglen til opbevaring konto, der er knyttet til webtjenesten, og aktivere og deaktivere eksempeldata.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Overvågning af hvordan webtjenesten bruges ###

Klik på fanen **DASHBOARD** .

Du kan se overordnede brugen af din webtjeneste dashboard over en periode. Du kan vælge punktum til at få vist den periode rullemenuen i øverste højre hjørne af brugen diagrammer. Dashboard viser følgende oplysninger:

- **Anmodninger Over tid** viser en trin graf over antallet af anmodninger over den markerede tidsperiode. Det kan hjælpe med at identificere, om du oplever spidser i anvendelse.
- **Anmodning om svar anmodninger** viser det samlede antal anmodning om svar opkald tjenesten har modtaget over den markerede tidsperiode, og hvor mange af dem mislykkedes.
- **Gennemsnitlig anmodning om svar beregne tid** viser et gennemsnit af den tid, der er behov for at udføre de modtagne anmodninger.
- **Anmodninger om batchen** viser det samlede antal batchen anmodninger om tjenesten har modtaget over den markerede tidsperiode, og hvor mange af dem mislykkedes.
- **Forsinkelse på gennemsnitligt Job** viser et gennemsnit af den tid, der er behov for at udføre de modtagne anmodninger.
- **Fejl** vises det samlede antal fejl, der er opstået på opkald til webtjenesten.
- **Services omkostninger** viser gebyrerne for fakturering plan der er knyttet til tjenesten.

### <a name="configuring-the-web-service"></a>Konfiguration af webtjenesten ###

Klik på menupunktet **KONFIGURER** .

Du kan opdatere følgende egenskaber:

* **Beskrivelse** kan du angive en beskrivelse til webtjenesten. Beskrivelse er et obligatorisk felt.
* **Logføring** kan du aktivere eller deaktivere på slutpunktet logføring af fejl. Se Aktivér [logføring for Machine Learning webtjenester](machine-learning-web-services-logging.md)kan finde flere oplysninger om logføring.
* **Aktivere eksempeldata** kan du give eksempeldata, som du kan bruge til at afprøve tjenesten anmodning om svar. Hvis du oprettede webtjenesten i Machine Learning Studio, hentes eksempeldataene fra dataene din bruges til at undervise din model. Hvis du oprettede tjenesten fra et program, hentes dataene fra eksempeldataene, du har angivet som en del af JSON-pakke.

## <a name="grant-or-suspend-access-to-web-services-for-users-in-the-portal"></a>Tildele eller suspendere adgangen til webtjenester for brugere i portalen

Ved hjælp af portalen Azure klassisk, kan du tillade eller nægte adgang til bestemte brugere.

### <a name="access-for-users-of-new-web-services"></a>Adgang til brugere af nye webtjenester

Hvis du vil give andre brugere til at arbejde med din webtjenester i portalen Azure Machine Learning Web Services, skal du tilføje dem som co-administratorer på abonnementet Azure.

Log på [Azure klassisk portal](https://manage.windowsazure.com/) ved hjælp af kontoen Microsoft Azure - du kan bruge den konto, der er knyttet til Azure abonnementet.

1. Klik på **Indstillinger**i navigationsruden og derefter skal du klikke på **Administratorer**.
2. Klik på **Tilføj**nederst i vinduet. 
3. Skriv mailadressen på den person, du vil tilføje som samtidig administrator og derefter vælge det abonnement, du vil samtidig administratoren for at få adgang til, i dialogboksen ADD A CO-ADMINISTRATOR.
4. Klik på **Gem**.

### <a name="access-for-users-of-classic-web-services"></a>Adgang til brugere af klassisk webtjenester

Sådan administreres et arbejdsområde:

Log på [Azure klassisk portal](https://manage.windowsazure.com/) ved hjælp af kontoen Microsoft Azure - du kan bruge den konto, der er knyttet til Azure abonnementet.

1. Klik på **Computer LEARNING**i panelet Microsoft Azure-tjenester.
1. Klik på det arbejdsområde, du vil administrere.
1. Klik på **KONFIGURER** .

Du kan afbryde adgang til arbejdsområdet Machine Learning fra fanen konfiguration ved at klikke på **AFVIS**. Brugere vil ikke længere kunne åbne arbejdsområdet i Machine Learning Studio. Hvis du vil gendanne adgang, skal du klikke på **Tillad**.

Til bestemte brugere:

Klik på **Log på ML Studio** under fanen **DASHBOARD** for at administrere flere konti, der har adgang til et arbejdsområde i Machine Learning Studio skal. Dette åbner arbejdsområdet i Machine Learning Studio. Herfra kan du klikke på fanen **Indstillinger** og derefter **brugere**. Du kan klikke på **INVITER flere brugere** for at give brugere adgang til arbejdsområdet, eller Vælg en bruger og klikke på **Fjern**.

> [AZURE.NOTE] Linket **Log på ML Studio** åbnes Machine Learning Studio ved hjælp af Microsoft-Account du i øjeblikket er logget på. Microsoft-Account du brugte til at logge på portalen Azure klassisk til at oprette et arbejdsområde har ikke tilladelse til at åbne det pågældende arbejdsområde automatisk. Hvis du vil åbne et arbejdsområde, skal du være logget på den Microsoft-Account, som blev defineret som ejeren af arbejdsområdet, eller du har brug at modtage en invitation fra ejeren til at deltage i arbejdsområdet.
