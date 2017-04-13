<properties
    pageTitle="Administrere et Machine Learning arbejdsområde | Microsoft Azure"
    description="Administrere adgang til Azure Machine Learning arbejdsområder og installere og administrere ML API webtjenester"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="garye"/>


# <a name="manage-an-azure-machine-learning-workspace"></a>Administrere et Azure Machine Learning arbejdsområde

>[AZURE.NOTE] Fremgangsmåderne i denne artikel er relevante for Azure Machine Learning klassisk Web-tjenester. Du kan finde oplysninger om administration af webtjenester i portalen Machine Learning webtjenester [Administrer en webtjeneste, ved hjælp af portalen Azure Machine Learning Web Services](machine-learning-manage-new-webservice.md).

Ved hjælp af portalen Azure klassisk, kan du administrere dine Machine Learning arbejdsområder til:

- Overvåge, hvordan arbejdsområdet bruges
- Konfigurere arbejdsområde for at tillade eller nægte adgang
- Administrere Web-tjenester, der er oprettet i arbejdsområdet
- Slette arbejdsområdet

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Desuden indeholder under fanen dashboard en oversigt over dit arbejdsområde brugen og en oversigt over dine oplysninger i arbejdsområdet.  

> [AZURE.TIP] I Azure Machine Learning Studio, under fanen **WEBTJENESTER** kan du tilføje, opdatere eller slette en Machine Learning-webtjeneste.

Sådan administreres et arbejdsområde:

1.  Log på [Azure klassisk portal](https://manage.windowsazure.com/) ved hjælp af kontoen Microsoft Azure - du kan bruge den konto, der er knyttet til Azure abonnementet.
2.  Klik på **Computer LEARNING**i panelet Microsoft Azure-tjenester.
3.  Klik på det arbejdsområde, du vil administrere.

Siden Arbejdsområde har tre faner:

- **DASHBOARD** - giver dig mulighed for at få vist brugen af arbejdsområdet og oplysninger
- **KONFIGURER** - tillader dig at administrere adgang til arbejdsområdet
- **WEBTJENESTER** – tillader dig at administrere Web-tjenester, der er blevet publiceret fra dette arbejdsområde

## <a name="to-monitor-how-the-workspace-is-being-used"></a>Til at overvåge hvordan arbejdsområdet bruges

Klik på fanen **DASHBOARD** .

Dashboard kan du få vist overordnede brugen af arbejdsområdet og få en oversigt over oplysninger om arbejdsområde.

- **Beregne** diagrammet viser de Beregn ressourcer, der bruges af arbejdsområdet. Du kan ændre visningen til at vise relative eller absolutte værdier, og du kan ændre det tidsrum, der vises i diagrammet.
- **Oversigt over brugen** viser Azure lagerplads, der bruges af arbejdsområdet.
- **Oversigt over** indeholder en oversigt over oplysninger om arbejdsområde og nyttige links.

> [AZURE.NOTE] Linket **Log på ML Studio** åbnes Machine Learning Studio ved hjælp af Microsoft-Account du i øjeblikket er logget på. Microsoft-Account du brugte til at logge på portalen Azure klassisk til at oprette et arbejdsområde har ikke tilladelse til at åbne det pågældende arbejdsområde automatisk. Hvis du vil åbne et arbejdsområde, skal du være logget på den Microsoft-Account, som blev defineret som ejeren af arbejdsområdet, eller du har brug at modtage en invitation fra ejeren til at deltage i arbejdsområdet.


## <a name="to-grant-or-suspend-access-for-users"></a>Til at tildele eller suspendere adgangen for brugere ##

Klik på **KONFIGURER** .

Fra fanen konfiguration kan du:

- Suspendere adgangen til arbejdsområdet Machine Learning ved at klikke på AFVIS. Brugere vil ikke længere kunne åbne arbejdsområdet i Machine Learning Studio. Hvis du vil gendanne adgang, skal du klikke på Tillad.

For at administrere flere konti, der har adgang til et arbejdsområde i Machine Learning Studio, skal du klikke på **Log på ML Studio** under fanen **DASHBOARD** (se den foregående note om **logon på ML Studio**). Dette åbner arbejdsområdet i Machine Learning Studio. Herfra kan du klikke på fanen **Indstillinger** og derefter **brugere**. Du kan klikke på **INVITER flere brugere** for at give brugere adgang til arbejdsområdet, eller Vælg en bruger og klikke på **Fjern**.


## <a name="to-manage-web-services-in-this-workspace"></a>Til at administrere webtjenester i dette arbejdsområde

Klik på fanen **WEB-tjenester** .

Dette viser en liste over webtjenester, der publiceres fra dette arbejdsområde.
Klik på navnet på listen for at åbne siden service for at administrere en webtjeneste.

En webtjeneste muligvis et eller flere slutpunkter, der er defineret.

- Du kan angive flere slutpunkter ud over "Standard" slutpunktet. Hvis du vil tilføje slutpunktet, skal du klikke på **Administrere slutpunkter** i bunden af dashboardet for at åbne portalen Azure Machine Learning Web Services.

- Slette et slutpunkt (du ikke kan slette "Standard" slutpunktet), skal du markere afkrydsningsfeltet i starten af rækken slutpunkt, og klik på **Slet**. Dette fjerner slutpunktet fra webtjenesten.

    > [AZURE.NOTE] Hvis et program bruger web tjenesteslutpunkt når slutpunktet slettes, får programmet en fejlmeddelelse næste gang den forsøger at få adgang til tjenesten.

Klik på navnet på en Web service slutpunkt for at åbne den. 

Du kan se overordnede brugen af din webtjeneste dashboard over en periode. Du kan vælge punktum til at få vist den periode rullemenuen i øverste højre hjørne af brugen diagrammer. Dashboard viser følgende oplysninger:

- **Anmodninger Over tid** viser en trin graf over antallet af anmodninger over den markerede tidsperiode. Det kan hjælpe med at identificere, om du oplever spidser i anvendelse.
- **Anmodning om svar anmodninger** viser det samlede antal anmodning om svar opkald tjenesten har modtaget over den markerede tidsperiode, og hvor mange af dem mislykkedes.
- **Gennemsnitlig anmodning om svar beregne tid** viser et gennemsnit af den tid, der er behov for at udføre de modtagne anmodninger.
- **Anmodninger om batchen** viser det samlede antal batchen anmodninger om tjenesten har modtaget over den markerede tidsperiode, og hvor mange af dem mislykkedes.
- **Forsinkelse på gennemsnitligt Job** viser et gennemsnit af den tid, der er behov for at udføre de modtagne anmodninger.
- **Fejl** vises det samlede antal fejl, der er opstået på opkald til webtjenesten.
- **Services omkostninger** viser gebyrerne for fakturering plan der er knyttet til tjenesten.

Fra siden Konfigurer, kan du opdatere følgende egenskaber:

* **Beskrivelse** kan du angive en beskrivelse til webtjenesten. Beskrivelse er et obligatorisk felt.
* **Logføring** kan du aktivere eller deaktivere på slutpunktet logføring af fejl. Se Aktivér [logføring for Machine Learning webtjenester](machine-learning-web-services-logging.md)kan finde flere oplysninger om logføring.
* **Aktivere eksempeldata** kan du give eksempeldata, som du kan bruge til at afprøve tjenesten anmodning om svar. Hvis du oprettede webtjenesten i Machine Learning Studio, hentes eksempeldataene fra dataene din bruges til at undervise din model. Hvis du oprettede tjenesten fra et program, hentes dataene fra eksempeldataene, du har angivet som en del af JSON-pakke.

[consume]: machine-learning-consume-web-services.md
[marketplace]: machine-learning-publish-web-service-to-azure-marketplace.md
