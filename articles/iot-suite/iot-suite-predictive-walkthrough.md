<properties
 pageTitle="Skønnet vedligeholdelse gennemgang | Microsoft Azure"
 description="En gennemgang af løsningen Azure IoT skønnet vedligeholdelse forudkonfigureret."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="aguilaaj"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/17/2016"
 ms.author="araguila"/>

# <a name="predictive-maintenance-preconfigured-solution-walkthrough"></a>Skønnet vedligeholdelse forudkonfigureret løsning gennemgang

## <a name="introduction"></a>Introduktion

IoT pakke skønnet vedligeholdelse forudkonfigureret løsning er en til en komplet løsning for et business-scenarie, der beregner punktet, når der er fejl kan opstå. Du kan bruge denne forudkonfigurerede løsning proaktiv til aktiviteter som optimering af vedligeholdelse. Løsningen kombinerer vigtige Azure IoT pakke tjenesterne, herunder en [Azure Machine Learning] [ lnk_machine_learning] arbejdsområde. Dette arbejdsområde indeholder forsøg, baseret på et offentligt eksempeldatasæt, til at forudsige den resterende nyttige levetid (RUL) af en fly-program. Løsningen implementerer fuldt IoT business scenarie som udgangspunkt for dig at planlægge og implementere en løsning, der opfylder bestemte virksomhedens behov.

## <a name="logical-architecture"></a>Logiske arkitektur

I det følgende diagram viser de logiske komponenter forudkonfigurerede løsningens:

![][img-architecture]

De blå elementer er Azure tjenester, der er klargjort i den placering, du vælger, når du klargør forudkonfigurerede løsningen. Du kan klargøre forudkonfigurerede løsningen i enten af USA, nord Europe eller Østasien område.

Nogle ressourcer, der er ikke tilgængelige i de områder, hvor du klargør forudkonfigurerede løsningen. Orange elementerne i diagrammet repræsenterer Azure tjenesterne klargjort i nærmeste tilgængelige område (syd Central os, Europe Vest eller Størrelseshåndtagets Asien) givet det valgte område.

Det grønne element er en simuleret enhed, der repræsenterer en flyet program. Du kan lære mere om disse simuleret enheder i følgende afsnit.

Grå elementerne repræsenterer komponenter, der implementerer funktioner for *administration af enhed* . Den aktuelle udgave af løsningen skønnet vedligeholdelse forudkonfigureret klargøres ikke disse ressourcer. Hvis du vil vide mere om administration af enhed skal referere til [remote overvågning forudkonfigurerede løsning][lnk-remote-monitoring].

## <a name="simulated-devices"></a>Simuleret enheder

I den forudkonfigurerede løsning repræsenterer en simuleret enhed en flyet program. Løsningen er klargjort med to programmer, der er tilknyttet en enkelt flyet. Hvert program udsender fire typer telemetri: føler 9, føler 11, føler 14 og føler 15 indeholder de data, der er nødvendige for maskine Learning modellen til at beregne den resterende nyttige levetid (RUL) til programmet. Hver simuleret enhed sender følgende telemetri meddelelser til IoT Hub:

*Periodisk optælling*. En cyklus repræsenterer en færdige flight af variabel længde mellem 2-10 timer, registreres telemetridata hver halv time under flight.

*Telemetri*. Der findes fire sensorer, der repræsenterer program attributter. Sensorerne er generisk mærket føler 9, føler 11, føler 14 og føler 15. Disse 4 sensorer repræsenterer telemetri tilstrækkelige til at få egnede resultater fra Machine Learning modellen for RUL. Denne model er oprettet ud fra et offentligt datasæt, der indeholder reelle program føler data. Yderligere oplysninger om hvordan modellen blev oprettet ud fra det oprindelige datasæt, se [Cortana Intelligence galleriet skønnet vedligeholdelse skabelon][lnk-cortana-analytics].

Simuleret enheder kan håndtere de følgende kommandoer, der sendes fra en IoT hub:

| Kommandoen | Beskrivelse |
|---------|-------------|
| StartTelemetry | Kontrollerer tilstanden for simuleringen.<br/>Starter den enhed, der sender telemetri     |
| StopTelemetry  | Kontrollerer tilstanden for simuleringen.<br/>Holder op med at sende telemetri enheden |

IoT Hub indeholder enhed kommandoen bekræftelse.

## <a name="azure-stream-analytics-job"></a>Azure Stream Analytics-job

**Job: Telemetri** fungerer på indgående enhed telemetri strømmen ved hjælp af to sætninger. Først markerer alle telemetri fra enhederne og sender disse data til blob storage fra hvor den visualiseres i WebApp. Den anden sætning beregner gennemsnitlige føler værdier over et glidende vindue to minutter og sender disse data gennem hubben begivenhed til en **begivenhed processor**.

## <a name="event-processor"></a>Begivenhed processor

**Begivenhed processor** tager gennemsnitlige føler værdierne for en færdige cyklus. Den gennemløb disse værdier til en API, der viser maskine læring uddannelse model til at beregne RUL til et program.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Yderligere oplysninger om hvordan modellen blev oprettet ud fra det oprindelige datasæt, se [Cortana Intelligence galleriet skønnet vedligeholdelse skabelon][lnk-cortana-analytics].

## <a name="lets-start-walking"></a>Lad os starte gå

Vejleder dig gennem komponenterne i løsningen i dette afsnit beskrives tilsigtede use case, og indeholder eksempler.

### <a name="predictive-maintenance-dashboard"></a>Skønnet vedligeholdelse Dashboard

Denne side i webprogrammet bruger PowerBI JavaScript-kontrolelementer (se [PowerBI-visuelle elementer lager][lnk-powerbi]) til visualisering af:

- Outputdata fra Stream Analytics sager i blob-lager.
- RUL og cyklus antal per fly program.

### <a name="observing-the-behavior-of-the-cloud-solution"></a>Vægt funktionsmåden for løsningen skyen

Gå til ressourcegruppen med navnet på løsningen, som du vælger at få vist dine klargjort ressourcer i Azure-portalen.

![][img-resource-group]

Når du klargør forudkonfigurerede løsningen, modtager du en mail med et link til arbejdsområdet Machine Learning. Du kan også gå til arbejdsområdet Machine Learning fra [azureiotsuite.com] [ lnk-azureiotsuite] siden for din klargjort løsning, når det er i tilstanden **klar** .

![][img-machine-learning]

I portalen løsning, kan du se, at prøven er klargjort med fire simuleret enheder til at repræsentere to fly med to programmer per fly, hver med fire sensorer. Når du først gå til portalen løsning skal stoppes simuleringen.

![][img-simulation-stopped]

Klik på **Start simulering** for at starte simulering, hvori du se føler historien, RUL, skifter, og RUL historik udfylde dashboard.

![][img-simulation-running]

Når RUL er mindre end 160 (en vilkårlig grænseværdi valgt til demonstration), portalen løsning viser en advarselssymbol ud for visningen RUL og fremhæver flyet program med gult. Bemærk, hvordan værdierne RUL har en generelle nedadgående tendens overordnede, men har tendens til at hoppe op og ned. Denne funktionalitet resultater fra forskellige cyklus længderne og model nøjagtigheden.

![][img-simulation-warning]

Fuld simulering tager omkring 35 minutter for at gennemføre 148 skifter. I 160 RUL grænseværdi er opfyldt for første gang på omkring 5 minutter, og begge programmer ramme grænsen på omkring 8 minutter.

Simuleringen kører gennem fuldført datasættet for 148 skifter og udligner på endelige RUL og cyklus værdier.

Du kan stoppe simulering når som helst, men at klikke på **Starte simulering** replays simulering fra starten af datasættet.

## <a name="next-steps"></a>Næste trin

Nu har du kører skønnet vedligeholdelse forudkonfigureret løsningen du overveje at redigere den, skal du se [vejledning om tilpasning af forudkonfigurerede løsninger][lnk-customize].

Blogindlæg [IoT pakke - Under valgmuligheder - skønnet vedligeholdelse](http://social.technet.microsoft.com/wiki/contents/articles/33527.iot-suite-under-the-hood-predictive-maintenance.aspx) TechNet indeholder flere detaljer om skønnet vedligeholdelse forudkonfigureret løsningen.

Du kan også udforske nogle af de andre funktioner og egenskaber de IoT pakke forudkonfigureret løsninger:

- [Ofte stillede spørgsmål om IoT pakke][lnk-faq]
- [IoT sikkerhed fra bunden][lnk-security-groundup]


[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
