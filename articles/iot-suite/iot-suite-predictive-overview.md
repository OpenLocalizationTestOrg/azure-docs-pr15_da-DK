<properties
 pageTitle="Skønnet vedligeholdelse forudkonfigureret løsning | Microsoft Azure"
 description="En beskrivelse af løsningen Azure IoT skønnet vedligeholdelse forudkonfigureret."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
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

# <a name="predictive-maintenance-preconfigured-solution-overview"></a>Skønnet vedligeholdelse forudkonfigureret løsning oversigt

*Skønnet vedligeholdelse* forudkonfigureret løsningen er et af de [løsninger, der er forudkonfigureret] [ lnk_preconfigured_solutions] udgivet som en del af [Microsoft Azure IoT Suite][lnk_iot_suite]. Denne løsning integrerer realtid enhed telemetri af websteder med en skønnet model, der er oprettet ved hjælp af [Azure Machine Learning][lnk_machine_learning].


Med Azure IoT-pakken, kan virksomheder hurtigt og nemt oprette forbindelse til og overvåge aktiver og analysere data i realtid. Skønnet vedligeholdelse forudkonfigureret løsningen tager dataene og bruger omfattende dashboards og visuelle effekter til at give virksomheder nye intelligence, som kan drive effektiviteten og forbedre omsætningsstrømme.

## <a name="the-scenario"></a>Dette Scenario

Fabrikam er en internationale flyselskaber, der fokuserer på gode kundeoplevelsen til Konkurrentanalyse priser. En årsag til flyforsinkelser er vedligeholdelse problemer og vedligeholdelse af flyet program er særligt udfordring. Program fejl under flight skal undgås på alle costs, så Fabrikam inspicerer dens programmer regelmæssigt og overholder et planlagt vedligeholdelsesprogram. Dog have fly programmer ikke altid det samme. Nogle unødvendige vedligeholdelse udføres på programmer. Vigtigere, opstår problemer, som kan formalede et fly, indtil vedligeholdelse udføres. Derved dyrt forsinkelser, især hvis et fly er på en placering, hvor ikke der findes på højre teknikere eller dele.

Programmer af Fabrikams fly er udstyret med sensorer, overvåge program betingelser under flight. Fabrikam bruge Azure IoT pakke til at indsamle de indsamlede under flight føler data. Når du ophobede år efter program funktionsdygtige og manglende data, har Fabrikam's data forskere er baseret på en metode til at forudsige den resterende nyttige levetid (RUL) af en fly-program. De har identificeret er en korrelation mellem data fra fire ud af program sensorer med slid program, der fører til eventuel fejl. Mens Fabrikam fortsætter med at udføre almindelige inspektioner for at garantere sikkerheden, kan det nu bruge modellerne til at beregne RUL af hvert program, når alle flight ved hjælp af telemetri der indsamles fra programmer under flight. Fabrikam kan nu forudsige fremtidige punkter af manglende og planlægge vedligeholdelse og reparere på forhånd.

> [AZURE.NOTE] Løsning modellen bruger faktisk program reducere data.

Ved at forudsige punktet, når vedligeholdelse er påkrævet, kan Fabrikam optimere dets handlinger for at reducere omkostninger. Vedligeholdelse koordinatorer arbejde med schedulers til at planlægge vedligeholdelse sammenfaldende med et fly stopper ved en bestemt placering og sikre tilstrækkelig tid til flyet være væk service uden at forårsage tidsplan afbrydelser. Fabrikam kan planlægge teknikere i overensstemmelse hermed; sikring af, at flyet repareret effektivt uden ventetiden. Lager kontrolelement ledere modtager vedligeholdelsesplaner, så de kan optimere deres bestilling proces og ekstra dele lager. Alt dette gør det muligt for Fabrikam at minimere fly bunden tid og for at reducere omkostninger og sikre, at sikkerhed for passagerer og personale.

At forstå, hvordan [Azure IoT pakke] [ lnk_iot_suite] indeholder funktioner kunderne skal opdager det fulde potentiale af skønnet vedligeholdelse, skal du gennemse denne [infographic][lnk_infographic].

## <a name="how-the-predictive-maintenance-solution-is-built"></a>Hvordan løsningen skønnet vedligeholdelse er oprettet

Løsningen, der har en eksisterende Azure Machine Learning-model, der er tilgængelige som en skabelon til at vise disse funktioner fungerer fra enheden telemetri, der er indsamlet via IoT pakke tjenester. Microsoft har oprettet en [regressionsmodel] [ lnk_regression_model] af en fly-program og publiceret skabelonen fuldført, data<sup>\[1\]</sup>, og en trinvis vejledning i at bruge modellen.

Azure IoT skønnet vedligeholdelse forudkonfigureret løsningen bruger regressionsmodellen oprettet ud fra denne skabelon det er installeret i abonnementet Azure og fremvises en automatisk oprettede API. Løsningen indeholder et undersæt af de test data, der repræsenterer 4 (af 100 samlede)-programmer og 4 (i 21 samlede) føler datastreams som understøtter et præcist resultat fra erfaren modellen.

*\[1\] A. Saxena og K. Goebel (2008). "Turbofanmotorer program er forringet simulering stokastisk variabel" NASA Ames Prognostics datalager (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/) NASA Ames Research Center, og Moffett felt, CA*

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om, hvordan Azure IoT aktiverer skønnet vedligeholdelse scenarier, læse, [registrere værdi fra internettet ting][lnk_capture_value].

Tage en [gennemgang] [ lnk-predictive-walkthrough] af skønnet vedligeholdelse forudkonfigureret løsning.

[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF

Du kan også udforske nogle af de andre funktioner og egenskaber de IoT pakke forudkonfigureret løsninger:

- [Ofte stillede spørgsmål om IoT pakke][lnk-faq]
- [IoT sikkerhed fra bunden][lnk-security-groundup]

[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
