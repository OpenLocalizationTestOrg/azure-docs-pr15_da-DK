<properties
 pageTitle="Udvikler vejledning – forstå enhed twins | Microsoft Azure"
 description="Azure IoT Hub udvikler guide – Brug enhed twins til at synkronisere stat og konfiguration af data mellem IoT Hub og dine enheder"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>

# <a name="understand-device-twins---preview"></a>Forstå enhed twins – få vist et eksempel

## <a name="overview"></a>Oversigt

*Enhed twins* er JSON-dokumenter, der gemmer enhed tilstandsoplysninger (metadata, konfigurationer og betingelser). IoT Hub fortsætter en enhed med dobbelt for hver enhed, du opretter forbindelse til IoT-Hub. I denne artikel beskriver:

* strukturen i enhed dobbelt: *ønskede* og *mærker*, *rapporteret egenskaber*, og
* de handlinger, der kan udføre enhed apps og tilbage ender på enheden twins.

> [AZURE.NOTE] Enhed twins er kun tilgængelig fra enheder, der har forbindelse til IoT Hub ved hjælp af MQTT protokollen på nuværende tidspunkt. Se den [MQTT understøtter] [ lnk-devguide-mqtt] artikel for at få vejledning i at konvertere eksisterende enhed app til at bruge MQTT.

### <a name="when-to-use"></a>Hvornår skal jeg bruge

Brug enhed twins til:

* Gemme enhed bestemte metadata i skyen, f.eks. installation placeringen af en salgsautomat.
* Rapportere aktuelle statusoplysninger som tilgængelige funktioner og betingelser fra din enhed-app, f.eks. en enhed, du har forbindelse via mobilnetværk eller Wi-Fi.
* Synkronisere tilstanden for længerevarende arbejdsprocesser mellem enhed app og back end, f.eks. back-end angive den nye firmwareversion at installere, og enheden app rapportering de forskellige faser af opdateringsprocessen.
* Forespørge din enhed metadata, konfiguration eller tilstand.

Bruge [enhed i skyen meddelelser] [ lnk-d2c] for sekvens af tidsstemplet hændelser som tidsserie af føler data eller alarmer. Bruge [skyen-til-enhed metoder] [ lnk-methods] for interaktive kontrolelementer enheder, som aktivering af en.

## <a name="device-twins"></a>Enhed twins

Enhed twins gemme enhed-relaterede oplysninger, som:

- Enhed og tilbage ender kan bruge til at synkronisere enhed betingelser og konfiguration.
- Programmet back-end kan bruge til forespørgslen og mål længerevarende handlinger.

Livscyklus for en enhed med dobbelt er knyttet til den tilsvarende [enhed identitet][lnk-identity]. Twins oprettes implicit og slettet, når en ny enhed identitet der oprettes eller slettes i IoT Hub.

En enhed med dobbelt er et dokument, JSON, der omfatter:

* **Mærker**. Et JSON dokument læse og skrevet af back-end. Mærker er ikke synlige for enhed apps.
* **Ønsket egenskaber**. Bruges sammen med anmeldt egenskaber til at synkronisere enhedskonfiguration eller tilstand. Ønskede egenskaber kan kun angives af programmet tilbage slutningen og kan læses af appen enhed. Appen enhed kan også få besked i realtid af ændringer på de ønskede egenskaber.
* **Rapporteret egenskaber**. Bruges sammen med ønskede egenskaber til at synkronisere enhedskonfiguration eller tilstand. Rapporteret egenskaber kan kan kun angives ved appen enhed og læse og forespørges ved programmet back-end.

Desuden i roden af enhed dobbelt indeholder skrivebeskyttede egenskaber fra den tilsvarende identitet, som er indeholdt i [enhed identitet registreringsdatabasen][lnk-identity].

![][img-twin]

Her er et eksempel på en enhed med dobbelt JSON-dokument:

        {
            "deviceId": "devA",
            "generationId": "123",
            "status": "enabled",
            "statusReason": "provisioned",
            "connectionState": "connected",
            "connectionStateUpdatedTime": "2015-02-28T16:24:48.789Z",
            "lastActivityTime": "2015-02-30T16:24:48.789Z",

            "tags": {
                "$etag": "123",
                "deploymentLocation": {
                    "building": "43",
                    "floor": "1"
                }
            },
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata" : {...},
                    "$version": 1
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": 55,
                    "$metadata" : {...},
                    "$version": 4
                }
            }
        }

I Rodobjektet er dialogboksen Egenskaber for system, og beholder objekter til `tags` og begge `reported` og `desired properties`. Den `properties` objektbeholder indeholder nogle skrivebeskyttet elementer (`$metadata`, `$etag`, og `$version`), der er beskrevet henholdsvis i [dobbelt metadata] [ lnk-twin-metadata] og [optimistisk] [ lnk-concurrency] sektioner.

### <a name="reported-property-example"></a>Eksempel på anmeldt egenskaben

I eksemplet ovenfor enhed dobbelt indeholder en `batteryLevel` egenskab, der rapporteres af appen enhed. Denne egenskab gør det muligt at forespørge om og arbejde på enheder, der er baseret på den sidste anmeldt niveau. Et andet eksempel ville have enhed app rapport enhedens funktioner eller indstillinger for forbindelse.

Bemærk hvordan rapporteret egenskaber forenkle scenarier, hvor back-end er interesseret i den sidste kendte værdi på en egenskab. Bruge [enhed i skyen meddelelser] [ lnk-d2c] om back-end skal behandle enhed telemetri i form af sekvens af tidsstemplet hændelser, som tidsserie.

### <a name="desired-configuration-example"></a>Eksempel på ønskede konfiguration

I eksemplet ovenfor den `telemetryConfig` ønskede og anmeldt egenskaber bruges af back end og enhed appen til at synkronisere telemetri konfigurationen for denne enhed. Eksempel:

1. App back-end egenskaben ønskede med den ønskede konfigurationsværdi. Her er del af dokumentet med den ønskede egenskab:

        ...
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            ...
        },
        ...
        
2. Appen enhed får besked om ændringen med det samme, hvis forbindelse eller i den første forbindelsen. Appen enhed rapporterer derefter den opdaterede konfiguration (eller en fejl betingelse ved hjælp af den `status` egenskaben). Her er del af anmeldt egenskaber:

        ...
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            ...
        }
        ...

3. App back-end holder spore resultaterne af konfigurationshandlingen på tværs af mange enheder ved at [forespørge] [ lnk-query] twins.

> [AZURE.NOTE] De ovenstående kodestykker er eksempler, der er optimeret til læsbarheden af en mulig måde til at kode en enhedskonfiguration og dens status. IoT Hub angive ikke et bestemt skema for de ønskede og anmeldt egenskaber i enhed twins.

I mange tilfælde bruges twins til at synkronisere længerevarende handlinger som firmware-opdateringer. Se bruge [ønskede egenskaber for at konfigurere enheder] [ lnk-twin-properties] kan finde flere oplysninger om, hvordan du bruger egenskaber til at synkronisere og spore længe kører handlinger på tværs af enheder.

## <a name="back-end-operations"></a>Back end-handlinger

Back-end fungerer på to ved hjælp af følgende atomisk handlinger, fremvises HTTP:

1. **Hente dobbelt efter id**. Denne handling returnerer indholdet af de to dokumentet, hvis du bruger mærkning og beskedteksten, rapporteret og egenskaber for system.
2. **Delvist opdatere dobbelt**. Denne handling kan back-end delvist opdatere to mærker eller ønskede egenskaber. Delvis opdatering er udtrykt som et JSON-dokument, der tilføjer eller opdaterer en egenskab, der er nævnt. Egenskaber, der er angivet til `null` fjernes. For eksempel følgende opretter en ny ønskede egenskab med værdi `{"newProperty": "newValue"}`, overskriver den eksisterende værdi af `existingProperty` med `"otherNewValue"`, og fjerner helt `otherOldProperty`. Ingen ændringer sker der så med mærker eller andre eksisterende ønskede egenskaber:

        {
            "properties": {
                "desired": {
                    "newProperty": {
                        "nestedProperty": "newValue"
                    },
                    "existingProperty": "otherNewValue",
                    "otherOldProperty": null
                }
            }
        }

3. **Erstatter de ønskede egenskaber**. Denne handling aktiverer back-end helt overskrive alle eksisterende ønskede egenskaber og erstatte et nyt JSON-dokument til `properties/desired`.
4. **Erstatte mærker**. Denne handlinger kan analogously for at erstatte ønskede egenskaber, back-end helt overskrive alle eksisterende mærker og erstatte et nyt JSON-dokument til `tags`.

Alle ovenstående handlinger understøtter [optimistisk] [ lnk-concurrency] og kræver tilladelsen **ServiceConnect** , som defineret i [sikkerhed] [ lnk-security] artikel.

Ud over disse handlinger kan back-end forespørge twins ved hjælp af en SQL-lignende [forespørgsel sprog][lnk-query], og udføre handlinger på store datasæt i twins ved hjælp af [sager][lnk-jobs].

## <a name="device-operations"></a>Enhed handlinger

Appen enhed fungerer på to ved hjælp af følgende atomisk handlinger:

1. **Hente dobbelt**. Denne handling returnerer indholdet af de to dokument (herunder mærker og beskedteksten, rapporteret og egenskaber for system) for den aktuelt tilsluttede enhed.
2. **Delvist opdatering rapporteret egenskaber**. Denne handling kan delvis opdatering af rapporteret egenskaberne for den aktuelt tilsluttede enhed. Dette bruger de samme JSON opdatering formater som back-end modstående delvis opdatering af ønskede egenskaber.
3. **Observe beskedteksten egenskaber**. Den aktuelt tilsluttede enhed kan vælge at få besked om opdateringer til de ønskede egenskaber, så snart de opstår. Enheden modtager formularen samme af opdatering (fuld eller delvis udskiftning) udføres af back-end.

Alle ovenstående handlinger kræver tilladelsen **DeviceConnect** , som defineret i [sikkerhed] [ lnk-security] artikel.

[Azure IoT enhed SDK'er] [ lnk-sdks] gør det nemt at bruge de ovenstående handlinger fra mange sprog og platforme. Flere oplysninger om oplysninger om IoT Hub primitiver til ønskede egenskaber synkronisering kan findes i [enhed genoprettelse flow][lnk-reconnection].

> [AZURE.NOTE] Enhed twins er i øjeblikket kun tilgængelig fra enheder, der har forbindelse til IoT Hub ved hjælp af MQTT-protokollen.

## <a name="reference-topics"></a>Referenceemner i:

De følgende referenceemner giver dig flere oplysninger om kontrol af adgang til din IoT hub.

## <a name="tags-and-properties-format"></a>Mærker og egenskaber format

Mærker, ønskede og anmeldt egenskaber er JSON objekter med følgende begrænsninger:

* Alle nøgler i JSON objekter er store og små bogstaver 128 tegn Unicode-strenge. Tilladt tegn udelade Unicode-kontroltegn (målgrupper C0 og C1) og `'.'`, `' '`, og `'$'`.
* Alle værdier i JSON objekt kan være en af følgende JSON typer: boolesk, tal, streng, objekt. Matrixer, er ikke tilladt.

## <a name="twin-size"></a>Dobbelt størrelse

IoT Hub gennemtvinger et 8KB størrelsesbegrænsning på værdierne i `tags`, `properties/desired`, og `properties/reported`, med undtagelse af elementer i skrivebeskyttet tilstand.
Størrelsen beregnes ved at tælle alle tegn med undtagelse af UNICODE styre tegn (målgrupper C0 og C1) og mellemrum `' '` når det vises uden for en strengkonstant.
IoT Hub afviser alle handlinger, der vil øge størrelsen af disse dokumenter over grænsen med fejl.

## <a name="twin-metadata"></a>Dobbelt metadata

IoT Hub fører tidsstempel for den seneste opdatering til de enkelte JSON-objekter i ønskede og anmeldt egenskaber. Tidsstempler er i UTC og kodet i formatet [ISO8601] `YYYY-MM-DDTHH:MM:SS.mmmZ`.
Eksempel:

        {
            ...
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": {
                                "$lastUpdated": "2016-03-30T16:24:48.789Z"
                            },
                            "$lastUpdated": "2016-03-30T16:24:48.789Z"
                        },
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$version": 23
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": "55%",
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": "5m",
                            "status": {
                                "$lastUpdated": "2016-03-31T16:35:48.789Z"
                            },
                            "$lastUpdated": "2016-03-31T16:35:48.789Z"
                        }
                        "batteryLevel": {
                            "$lastUpdated": "2016-04-01T16:35:48.789Z"
                        },
                        "$lastUpdated": "2016-04-01T16:24:48.789Z"
                    },
                    "$version": 123
                }
            }
            ...
        }

Disse oplysninger gemmes på alle niveauer (ikke kun blade af JSON strukturen) for at bevare opdateringer, der fjerner objekt taster.

## <a name="optimistic-concurrency"></a>Optimistisk

Mærker, ønskede og anmeldt egenskaber alle understøtter optimistisk.
Mærker har en etag ud fra [RFC7232], der repræsenterer det mærke JSON repræsentation. Du kan bruge dette i betinget opdatering handlinger fra back-end for at sikre konsistens.

Ønskede og anmeldt egenskaber ikke har etags, men har en `$version` værdi, der er garanti for, at være stigende. Analogously til en etag kan versionen bruges af den opdatere part som (en enhed app for en anmeldt egenskab) eller back-end for en ønskede egenskab til at gennemtvinge konsistens opdateringer.

Versioner er også nyttig, når en observing agent (såsom, enhed appen vægt de ønskede egenskaber) har afstemme 2.Kontrolpunktræs mellem resultatet af en hentningen og en besked om opdatering. Sektionen [enhed genoprettelse flow] [ lnk-reconnection] indeholder flere oplysninger.

## <a name="device-reconnection-flow"></a>Enhed genoprettelse flow

IoT Hub bevarer ikke ønskede egenskaber meddelelser om opdateringer til afbrudt enheder. Det følger, en enhed, der opretter forbindelse skal hente ønskede egenskaber for hele dokumentet, ud over at abonnere for meddelelser om opdateringer. Have mulighed for 2.Kontrolpunktræs mellem meddelelser om opdateringer og fuld hentning, skal følgende strømmen sikres:

1. Enhed app opretter forbindelse til en IoT hub.
2. Enhed app abonnerer ønskede egenskaber for meddelelser om opdateringer.
3. Enhed app henter hele dokumentet i de ønskede egenskaber.

Appen enhed kan ignorere alle meddelelser med `$version` mindre end eller lig end versionen af det fulde hentede dokument. Dette er muligt, fordi IoT Hub garanterer, at versioner altid stiger.

> [AZURE.NOTE] Denne logik allerede er implementeret i [Azure IoT enhed SDK'er][lnk-sdks]. Denne beskrivelse er nyttig, kun, hvis enhed app kan ikke bruge en af Azure IoT enhed SDK'er og programmere grænsefladen MQTT direkte.

## <a name="additional-reference-material"></a>Yderligere referencemateriale

Andre referenceemner i i udvikler vejledning omfatter:

- [IoT Hub slutpunkter] [ lnk-endpoints] beskrives de forskellige slutpunkter, hver IoT hub fremviser for runtime og styring handlinger.
- [(Throttling) og kvoter for] [ lnk-quotas] beskrives de kvoter, der gælder for tjenesten IoT Hub og variere den benyttede funktionsmåden kan forvente, når du bruger tjenesten.
- [IoT Hub enheder og tjenester SDK'er] [ lnk-sdks] viser en liste over de forskellige sprog SDK'er du en bruges, når du udvikler både enhed og service-programmer, der arbejder sammen med IoT Hub.
- [Forespørge sprog efter twins, metoder og job] [ lnk-query] beskrives forespørgselssprog, du kan bruge til at hente oplysninger fra IoT Hub om din enhed twins, metoder og -job.
- [Understøttelse af IoT Hub MQTT] [ lnk-devguide-mqtt] indeholder flere oplysninger om understøttelse af IoT Hub for MQTT-protokollen.

## <a name="next-steps"></a>Næste trin

Nu har du lært enhed twins, du måske har din interesse Developer Guide følgende emner:

- [Aktivere en direkte metode på en enhed][lnk-methods]
- [Planlægge opgaver på flere enheder][lnk-jobs]

Hvis du gerne vil prøve nogle af de begreber, der er beskrevet i denne artikel, kan du måske interesseret i følgende IoT Hub selvstudier:

- [Sådan bruger du enhed dobbelt][lnk-twin-tutorial]
- [Sådan bruger du dobbelt egenskaber][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png