<properties
 pageTitle="Oversigt over administration af IoT Hub enhed | Microsoft Azure"
 description="I denne artikel indeholder en oversigt over administration af enheder i Azure IoT Hub: enterprise enhed livscyklus, genstart, fabriksindstillingerne, firmware-opdatering, konfiguration, enhed twins, forespørgsler, opgaver"
 services="iot-hub"
 documentationCenter=""
 authors="bzurcher"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/03/2016"
 ms.author="bzurcher"/>

# <a name="overview-of-azure-iot-hub-device-management-preview"></a>Oversigt over administration af Azure IoT Hub (preview)

## <a name="introduction"></a>Introduktion

Azure IoT Hub indeholder funktionerne og en udvidelsesmuligheder af model, der aktiverer enhed og back end-udviklere til at oprette effektive IoT enhed management-løsninger. IoT enheder område fra begrænsede sensorer og enkelt formål mikrocontrollere til kraftfulde gateways, som distribuere kommunikation til grupper af enheder.  Desuden variere use cases og krav til IoT operatorer betydeligt på tværs af virksomheder.  På trods af denne variation indeholder Azure IoT Hub enhedsstyring muligheder, mønstre og kodebiblioteker for at imødekomme til et bredt udvalg af enheder og slutbrugere.

En vigtig del af oprettelse af en vellykket enterprise IoT løsning til at levere en strategi til hvordan operatorer håndtere de løbende administration af deres samling af enheder. IoT operatorer kræver enkle og pålidelig værktøjer og programmer, der aktiverer dem til at fokusere på mere strategiske aspekter af deres arbejde. I denne artikel indeholder:

- En kort oversigt over Azure IoT Hub tilgang til administration af enheder.
- En beskrivelse af fælles enhed management principper.
- En beskrivelse af enhed livscyklussen.
- En oversigt over almindelige enhed management mønstre.

## <a name="iot-device-management-principles"></a>IoT enhed management principper

IoT kommer med den med et entydigt sæt af enhed management udfordringer, og alle professionel løsning skal tage følgende principper:

![Azure IoT Hub enhed management principper grafik][img-dm_principles]

- **Skala og automatisering**: IoT løsninger kræver enkle værktøjer, som kan automatisere rutinemæssige opgaver og aktivere et lille handlinger personale til at administrere millioner af enheder. Daglige, operatorer forventer at håndtere enhed handlinger fra en fjernplacering, ad gangen, og kun have besked, når der opstår problemer, der kræver deres direkte opmærksomhed.

- **Åbne og kompatibilitet**: feltet IoT enhedsøkosystem er indebærer forskelligartet. Værktøjer til administration af skal tilpasses for at imødekomme en lang række enhedsklasser, platforme og protokoller. Operatorer skal kunne understøtte mange typer enheder fra de mest begrænsede integrerede single-process-chips til effektive og fuldt funktionelle computere.

- **Kontekst tilstedeværelse**: IoT miljøer er dynamiske og skiftende. Tjenesten pålidelighed er største. Enhed management handlinger skal angive SLA vedligeholdelse windows, netværk og power tilstand, i brug betingelser og enhed geolokalitet at sikre, at den vedligeholdelse nedetid ikke påvirker virksomhedens vigtige funktioner eller oprette farlig betingelser.

- **Vedligeholde mange roller**: understøttelse af entydige arbejdsprocesser og processer i IoT handlinger roller er afgørende. Personer, der skal arbejde harmonisk med de angivne begrænsninger af interne IT-afdelinger.  De skal også finde bæredygtig måder at få vist realtid enhed handlinger oplysninger til tilsynsførende og andre ledelse forretningsroller.

## <a name="iot-device-lifecycle"></a>IoT enhed livscyklus

Der er et sæt af generelle enhed management faser, der er fælles for alle IoT virksomhedsprojekter. I Azure IoT er der fem faser i IoT enhed livscyklus:

![Fem Azure IoT enhed livscyklus faser: planlægge, klargøre, konfigurere, overvåge, går på pension][img-device_lifecycle]

Inden for hver af disse fem faser er der flere enhed operator krav, der skal være opfyldt for at give en komplet løsning:

- **Planlægge**: aktivere operatorer til at oprette et farveskema til enhed metadata, der gør det nemt og præcist forespørge efter – og fokuserer på en gruppe af enheder til flere management handlinger. Du kan bruge enhed dobbelt til at gemme denne enhedsmetadata i form af mærker og egenskaber.

    *Yderligere læsning*: [Introduktion til enhed twins][lnk-twins-getstarted], [forstå enhed twins][lnk-twins-devguide], [hvordan du kan bruge to egenskaber][lnk-twin-properties]

- **Klargøring**: sikkert klargøre nye enheder til IoT Hub og aktivere operatorer til at finde enhedens funktioner med det samme.  Brug IoT Hub enhed registreringsdatabasen til at oprette fleksible enhed identiteter og legitimationsoplysninger, og udføre denne handling ad gangen ved hjælp af en sag. Opbygge enheder for at rapportere deres funktioner og betingelser via egenskaber for enheden i enhed dobbelt.

    *Yderligere læsning*: [Administrer enhed identiteter][lnk-identity-registry], [flere styring af enhed identiteter][lnk-bulk-identity], [hvordan du kan bruge to egenskaber][lnk-twin-properties]

- **Konfigurer**: lette flere ændringer i konfigurationen og firmwareopdateringer til enheder og samtidig bevare både tilstand og sikkerhed. Udføre disse enhed management handlinger ad gangen, ved hjælp af ønskede egenskaber eller med direkte metoder og broadcast sager.

    *Yderligere læsning*: [bruge direkte metoder][lnk-c2d-methods], [Aktiver en direkte metode på en enhed][lnk-methods-devguide], [hvordan du kan bruge to egenskaber][lnk-twin-properties], [tidsplan og broadcast job][lnk-jobs], [planlægge opgaver på flere enheder][lnk-jobs-devguide]

- **Skærm**: overvåge overordnede enhed samling af tjenestetilstand, status for igangværende handlinger, og Giv besked operatorer til problemer, der muligvis kræver deres opmærksomhed.  Anvende enhed med dobbelt for at tillade enheder til betingelser for rapport realtid driften og status for opdatering handlinger. Oprette effektive dashboardrapporter, overflade de største problemer ved hjælp af enhed dobbelt forespørgsler.

    *Yderligere læsning*: [hvordan du kan bruge to egenskaber][lnk-twin-properties], [forespørgselssprog til twins og -job][lnk-query-language]

- **Retire**: Erstat eller afvikler enheder efter en fejl, skal du opgradere cyklus, eller i slutningen af tjenesten levetid.  Bruge enhed dobbelt til at bevare din mobiltelefon, hvis den fysiske enhed er ved at blive erstattet eller arkiveres, hvis der går på pension. Bruge IoT Hub enhed registreringsdatabasen til sikker tilbagekalde enhed identiteter og legitimationsoplysninger.

    *Yderligere læsning*: [hvordan du kan bruge to egenskaber][lnk-twin-properties], [Administrer enhed identiteter][lnk-identity-registry]

## <a name="iot-hub-device-management-patterns"></a>IoT Hub enhed management mønstre

IoT Hub gør det muligt for følgende sæt af enhed management mønstre.  [Enhed management selvstudier] [ lnk-get-started] viser dig mere detaljerede oplysninger om, hvordan du udvider disse mønstre for at passe scenariet nøjagtige og hvordan du opretter nye mønstre, der er baseret på disse grundlæggende skabeloner.

- **Genstart** - back-end-program oplyser enheden via en direkte metode, at den er startet en genstart.  Enheden bruger enheden dobbelt rapporteret egenskaber for at opdatere genstartstatus på enheden.

    ![Azure IoT Hub enhedsstyring genstart mønster grafik][img-reboot_pattern]

- **Nulstille factory** - back-end-program oplyser enheden via en direkte metode, at den er startet en fabriksindstillingerne.  Enheden bruger enhed dobbelt anmeldt egenskaber for at opdatere factory Nulstil status for enheden.

    ![Azure IoT Hub enhed management factory nulstille mønster grafik][img-facreset_pattern]

- **Konfiguration** - back-end-program bruger enhed med dobbelt beskedteksten egenskaber til at konfigurere software, der kører på enheden.  Enheden bruger enheden dobbelt rapporteret egenskaber for at opdatere konfiguration status på enheden.

    ![Azure IoT Hub enhed management konfiguration mønster grafik][img-config_pattern]

- **Opdatere firmware** - back-end-program oplyser enheden via en direkte metode, at den er startet en firmware-opdatering.  Enheden starter en forbindelse med resultatmønstre processen for at hente pakken firmware, anvende pakken firmware og til sidst genoprette forbindelse til tjenesten IoT Hub.  Under hele processen mult trin enheden bruger enheden dobbelt rapporteret egenskaber for at opdatere status og status på enheden.

    ![Azure IoT Hub enhed management firmware opdatere mønster grafik][img-fwupdate_pattern]

- **Rapportere fremdrift og status** – programmet back-end kører enhed dobbelt forespørgsler på tværs af et sæt enheder, vil rapportere om status og fremdrift handlinger, der kører på enheder.

    ![Azure IoT Hub enhedsstyring rapportering status og status mønster grafik][img-report_progress_pattern]

## <a name="next-steps"></a>Næste trin

Du kan bruge de funktioner, mønstre og kodebiblioteker, som indeholder et Azure IoT Hub enhedsstyring, hvis du vil oprette IoT programmer, der opfylder enterprise IoT operator i i de enkelte enhed livscyklus faser.

Hvis du vil fortsætte med at lære om funktioner for administration af Azure IoT Hub enhed, se [komme i gang med administration af enheder Azure IoT Hub] [ lnk-get-started] selvstudium.

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-twins-devguide]: iot-hub-devguide-device-twins.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-query-language]: iot-hub-devguide-query-language.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-methods-devguide]: iot-hub-devguide-direct-methods.md
[lnk-jobs]: iot-hub-schedule-jobs.md
[lnk-jobs-devguide]: iot-hub-devguide-jobs.md