> [AZURE.SELECTOR]
- [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
- [C#](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)

## <a name="introduction"></a>Introduktion

I [Introduktion til IoT Hub twins][lnk-twin-tutorial], du har lært, hvordan du indstiller enheden meta-data fra din løsning back-end ved hjælp af *mærker*, rapporten enhed betingelser fra en enhed app *rapporterede egenskaber*og forespørge oplysningerne ved hjælp af en SQL-lignende sprog.

I dette selvstudium lærer du at bruge den sammen med *rapporterede egenskaber*, til eksternt at konfigurere enheden apps på twin, *ønskede egenskaber* . Mere specifikt dette selvstudium viser, hvordan twin's rapporteret og ønskede egenskaber aktiverer en konfiguration af flere trin i en programindstilling for enheden, og synlighed til backend løsning af status for denne handling på tværs af alle enheder.

Dette selvstudium følger *ønskede tilstand mønster* til Enhedsadministration på et højt niveau. Den grundlæggende ide i dette mønster er at have den løsning-back-end, der angiver den ønskede tilstand for styrede enheder, i stedet for at sende bestemte kommandoer. Dette placerer enheden for etablering af den bedste måde at nå frem til den ønskede tilstand (meget vigtigt IoT scenarier, hvor specifikke enhed betingelser påvirke muligheden for at udføre specifikke kommandoer straks), mens Løbende rapportering til back-end de aktuelle og potentielle fejltilstande. Mønsteret ønskede tilstand er instrumentelle til administration af store grupper af enheder, som gør det muligt back-end at have fuldt overblik over tilstanden af konfigurationsprocessen på tværs af alle enheder.
Du kan finde flere oplysninger om rollen for den ønskede tilstand mønster i Enhedshåndtering i [Oversigt over Azure IoT Hub Enhedsadministration][lnk-dm-overview].

> [AZURE.NOTE] I tilfælde, hvor enheder styres på en mere interaktiv måde (slå en ventilator fra en brugerstyret app), bør du overveje at bruge [cloud-enhed metoder][lnk-methods].

I dette selvstudium, back-end programændringer fjernmåling konfigurationen af en målenhed, og som følge af, at enheden-app følger en proces med flere trin for at anvende en konfigurationsopdatering (f.eks. krav om genstart software modul), som simulerer dette selvstudium med en simpel forsinkelse).

Backend-gemmer konfigurationen i den enhed twin ønskede egenskaber på følgende måde:

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

> [AZURE.NOTE] Da konfigurationer kan være komplekse objekter, de normalt er tildelt entydige id'er (hashes eller [GUID'er][lnk-guid]) til at forenkle deres sammenligninger.

App til enhed rapporterer den aktuelle konfiguration spejling ønskede egenskab **telemetryConfig** i de egenskaber, der er rapporteret:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

Bemærk, hvordan den rapporterede **telemetryConfig** har en ekstra egenskab **status**, anvendes til at rapportere status for opdatering konfigurationsprocessen.

Når der modtages en ny ønskede konfiguration, rapporterer enhed-app en ventende konfiguration ved at ændre oplysningerne:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "changeId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

Derefter på et senere tidspunkt rapporterer enhed-app fejl succes ved at opdatere egenskaben ovenfor.
Bemærk, hvordan back-end er i stand til enhver tid til at hente status for konfigurationsprocessen på tværs af alle enheder.

Dette selvstudium viser, hvordan til at:

- Opret en simuleret enhed, der modtager konfiguration opdateringer fra back-end og rapporterer flere opdateringer som *rapporterede egenskaber* på opdatering konfigurationsprocessen.
- Oprette en back-end-app, der opdaterer den ønskede konfiguration af en enhed og forespørger derefter opdateringen konfigurationsprocessen.

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier