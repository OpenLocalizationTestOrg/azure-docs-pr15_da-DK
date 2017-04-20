> [AZURE.SELECTOR]
- [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md)
- [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-simulated-device.md)

Denne gennemgang af [simulerede enhed sky overføre eksempel] viser, hvordan du bruger [Azure IoT Gateway SDK] [ lnk-sdk] til at sende enheden til sky fjernmåling til IoT Hub fra simuleret enheder.

Denne gennemgang omfatter:

1. **Arkitektur**: Vigtige arkitekturmæssige oplysninger om den simulerede enhed sky overføre prøve.

2. **Bygge og køre**: de trin, der kræves for at bygge og køre eksemplet.

## <a name="architecture"></a>Arkitektur

Simulerede enhed sky overføre prøven viser, hvordan bruge SDK'ET til at oprette en gateway, som sender fjernmåling fra simuleret enheder til en IoT-hub. Simuleret enheder kan ikke oprette forbindelse direkte til IoT-Hub, fordi:

- Enhederne, der bruger ikke en kommunikationsprotokol, der forstås af IoT Hub.
- Enhederne er ikke smart nok til at huske identiteten tildeles dem af IoT Hub.

Gatewayen løser disse problemer for simuleret enhederne på følgende måder:

- Gatewayen forstår den protokol, som de simulerede enheder modtager enhed-sky fjernmåling fra enhederne, og videresender meddelelserne til IoT Hub ved hjælp af en protokol, der forstås af hubben.
- Gatewayen gemmer IoT Hub identiteter på vegne af simulerede enheder og fungerer som en proxy, når de simulerede enheder sender meddelelser til IoT Hub.

I følgende diagram vises hovedbestanddelene af prøveemnet, herunder gateway-moduler:

![][1]


> [AZURE.NOTE] Modulerne, der ikke sende meddelelser direkte til hinanden. Modulerne udgive meddelelser til en intern broker, der leverer meddelelser til andre moduler, der bruger en abonnement ordning, som vist i nedenstående diagram. Yderligere oplysninger finder du under [Introduktion til Gateway SDK IoT][lnk-gw-getstarted].

### <a name="protocol-ingestion-module"></a>PPP-modulet til indtagelse

Dette modul er udgangspunktet for overførsel af data fra enheder, via denne gateway og i skyen. Modulet, der udføres i stikprøven, fire opgaver:

1.  Opretter den simulerede temperatur data.
    
    Bemærk: Hvis du bruger virkelige enheder, modulet kan læse data fra de fysiske enheder.

2.  Simuleret temperatur data placeres i indholdet af en meddelelse.

3.  Den tilføjer en egenskab med en falsk MAC-adresse til den meddelelse, der indeholder dataene, simulerede temperatur.

4.  Det gør meddelelsen til den næste modul i kæden.

> [AZURE.NOTE] Modulet hedder **protokol X indtagelse** i diagrammet ovenfor kaldes **simulerede enhed** i kildekoden.

### <a name="mac-lt-gt-iot-hub-id-module"></a>MAC &lt; - &gt; IoT Hub-ID-modul

Dette modul scanner for meddelelser, der indeholder en egenskab, der indeholder MAC-adresse, der er tilføjet af protokollen indtagelse modul på den simulerede enhed. Hvis modulet, der finder en sådan egenskab, tilføjer en anden egenskab med en nøgle til IoT Hub-enhed til meddelelsen, og gør meddelelsen til den næste modul i kæden. Dette er, hvordan prøven knytter IoT Hub enhed identiteter til simulerede enheder. Udvikleren indstiller mapning mellem MAC-adresser og IoT Hub identiteter manuelt som en del af konfigurationen af modulet. 

> [AZURE.NOTE]  Dette eksempel bruger en MAC-adresse som et entydigt enheds-id og korrelerer det med en IoT Hub anordningens identitet. Du kan dog skrive dit eget modul, der bruger en anden entydig identifier. For eksempel kan du have enheder med entydige serienumre eller fjernmåling data, der har et entydigt enhedsnavn, der er integreret i den, som du kan bruge til at fastslå anordningens identitet IoT Hub.

### <a name="iot-hub-communication-module"></a>IoT Hub kommunikation modul

Dette modul tager meddelelser med en IoT Hub anordningens identitet, der er tildelt af det tidligere version af modulet og sender meddelelsen til IoT Hub ved hjælp af HTTP. HTTP er en af de tre protokoller, som forstås af IoT Hub.

I stedet for at åbne en forbindelse til IoT Hub for hver simulerede enhed, dette modul åbner en enkelt HTTP-forbindelse fra gatewayen til IoT-hub og multiplexes forbindelser fra alle de simulerede enheder via denne forbindelse. Dette gør det muligt for en enkelt gateway til at oprette forbindelse til mange flere enheder, simulerede eller på anden måde, end det ville være muligt, hvis den er åbnet en entydig forbindelse for hver enhed.

![][2]


<!-- Images -->
[1]: media/iot-hub-gateway-sdk-simulated-selector/image1.png
[2]: media/iot-hub-gateway-sdk-simulated-selector/image2.png

<!-- Links -->
[Simulerede enhed sky overføre prøven]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/sample_simulated_device_cloud_upload.md
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md