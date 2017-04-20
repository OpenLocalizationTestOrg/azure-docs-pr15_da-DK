
# <a name="azure-and-internet-of-things"></a>Azure og Internet ting

Velkommen til Microsoft Azure og Internet ting (IoT). Denne artikel introducerer en IoT løsningsarkitektur, der beskriver de fælles karakteristika for en IoT løsning, som du kan installere ved hjælp af Azure services. IoT løsninger kræver sikker torettet kommunikation mellem enheder, muligvis nummerering i millioner og back-end løsning. Back-end løsning kan for eksempel bruge automatiserede, forudseende analytics til at afdække viden fra din enhed til sky event-stream.

Azure IoT Hub er et centralt element, når du implementerer denne IoT løsningsarkitektur, ved hjælp af Azure services. IoT-pakken indeholder fuldstændige, end-to-end-implementeringer af denne arkitektur i bestemte situationer, IoT. For eksempel: 

- *Fjernovervågning* -løsningen gør det muligt at overvåge status for enheder som salgsautomater. 
- *Forudseende vedligeholdelse* -løsningen hjælper dig med at forudse behov for vedligeholdelse af enheder som pumper på remote Pumpende stationer og for at undgå ikke-planlagt nedetid.

## <a name="iot-solution-architecture"></a>IoT løsningsarkitektur

Følgende diagram viser en typisk IoT løsningsarkitektur. Diagrammet indeholder ikke navnene på de specifikke Azure tjenester, men i denne artikel beskrives de vigtige elementer i en generisk IoT løsningsarkitektur. I denne arkitektur IoT enheder ved at indsamle data, som de sender til en sky-gateway. Sky-gateway gør dataene tilgængelige for behandling af andre back-end-tjenester fra hvor dataene leveres til andre line-of-business-programmer eller til menneskers operatører via et dashboard eller en anden præsentation enhed.

![IoT løsningsarkitektur][img-solution-architecture]

> [AZURE.NOTE] En detaljeret diskussion af IoT-arkitekturen i afsnittet [Microsoft Azure IoT Reference Architecture][lnk-refarch].

### <a name="device-connectivity"></a>Tilslutning af enheden

I denne IoT løsningsarkitektur sende enheder fjernmåling som sensor aflæsninger fra en pumpe station til en sky slutpunkt for oplagring og forarbejdning. I et scenarie, forudseende vedligeholdelse, kan back-end bruge strøm af sensordata til at bestemme, hvornår en bestemt pumpen kræver vedligeholdelse. Enheder kan også modtage og svare på sky-til-enhed-kommandoer ved at læse meddelelser fra en sky-slutpunkt. For eksempel i scenariet forudseende vedligeholdelse kan backend løsning sende kommandoer til andre pumper i pumpe station til at begynde omdirigering strømme, lige før vedligeholdelse skal starte at sikre, at den vedligeholdelse tekniker kan komme i gang, når hun ankommer.

En af de største udfordringer, der vender mod IoT projekter er at tilslutte enheder til back-end løsning, pålidelig og sikker måde. IoT enheder har forskellige karakteristika i forhold til andre klienter som browsere og mobile apps. IoT enheder:

- Er ofte integrerede systemer uden menneskelig operator.
- Kan anvendes i eksterne placeringer, hvor fysisk adgang er dyre.
- Kan kun nås gennem back-end løsning. Der er ingen anden måde at kommunikere med enheden.
- Kan have begrænset effekt og behandlingsressourcer.
- Muligvis ustabil, langsom eller dyr netværksforbindelse.
- Kan være nødvendigt at bruge beskyttet, brugerdefineret eller branchespecifikke programprotokoller.
- Du kan oprette ved hjælp af en stor samling af populære hardware og software-platforme.

Ud over de ovenstående krav, skal enhver IoT løsning også levere skalering, sikkerhed og pålidelighed. Det resulterende sæt af krav til netværksforbindelser er hård og tidskrævende at gennemføre ved hjælp af traditionelle teknologier som web-beholdere og messaging mæglere. Azure IoT Hub og IoT enhed SDK'er gør det nemmere at implementere løsninger, der opfylder disse krav.

En enhed kan kommunikere direkte med en sky gateway slutpunkt, eller hvis enheden ikke kan bruge nogen af kommunikationsprotokoller, der understøtter sky-gateway, der kan oprette forbindelse via en mellemliggende gateway. For eksempel [Azure IoT protokol gateway] [ lnk-protocol-gateway] kan udføre oversættelse af protokollen, hvis enhederne ikke kan bruge nogen af de protokoller, som understøtter IoT Hub.

### <a name="data-processing-and-analytics"></a>Databehandling og analytics

I skyen er en løsning tilbage afsluttes IoT, hvor de fleste af databehandlingen sker, som filtrering og sammenlægge fjernmåling og distribuere den til andre tjenester. Afslut IoT løsningen tilbage:

- Modtager fjernmåling på skalaen fra dine enheder og bestemmer, hvordan du behandler og gemmer disse data. 
- Måske kan du sende kommandoer fra skyen til specifikke enhed.
- Indeholder funktioner til registrering af enhed, der giver dig mulighed for til bestemmelse enheder og til at styre, hvilke enheder der har tilladelse til at oprette forbindelse til din infrastruktur.
- Gør det muligt at spore status for dine enheder og overvåge deres aktiviteter.

I scenariet forudseende vedligeholdelse gemmer backend løsning fjernmåling historiske data. Back-end kan bruge disse data skal bruges til at identificere mønstre, der angiver vedligeholdelse forfalder på en bestemt pumpe.

IoT-løsninger kan indeholde automatiske feedback-sløjfer. For eksempel kan et analytics modul i back-end identificere fra fjernmåling, temperatur for en bestemt enhed, der er større end normal drift niveauer. Løsningen kan derefter sende en kommando til enheden, instruktioner om at træffe korrigerende foranstaltninger.

### <a name="presentation-and-business-connectivity"></a>Præsentation og business connectivity

Præsentation og business connectivity lag giver slutbrugere mulighed for at interagere med IoT-løsningen og enhederne. Det gør det muligt for brugerne at få vist og analysere de data, der indsamles fra deres enheder. Disse visninger kan tage form af dashboards eller BI-rapporter, som kan vise både historiske data eller i nærheden af data i realtid. For eksempel kan en operator kontrollere status for en særlig pumpe station og se påmindelser udløses af systemet. Dette lag giver også mulighed for integration af IoT løsning back-end med eksisterende line of business-applikationer bindes til enterprise forretningsprocesser eller arbejdsprocesser. For eksempel kan forudseende vedligeholdelse løsningen integreres med en planlægningssystemet, bøger ingeniør for at besøge en pumpe station, når løsningen identificerer en pumpe, der har behov for vedligeholdelse.

![IoT løsning dashboard][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
