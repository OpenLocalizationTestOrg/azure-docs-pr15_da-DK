<properties
 pageTitle="Aktivere administreret enheder bag en IoT gateway | Microsoft Azure"
 description="Vejledning emne ved hjælp af en IoT Gateway oprettet ved hjælp af IoT Gateway SDK sammen med enheder, der administreres af IoT Hub."
 services="iot-hub"
 documentationCenter=""
 authors="chipalost"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="cstreet"/>
 
# <a name="enable-managed-devices-behind-an-iot-gateway"></a>Aktivere administreret enheder bag en IoT gateway

## <a name="basic-device-isolation"></a>Grundlæggende enhed isolationsniveauet

Organisationer bruger ofte IoT gateways til at øge den overordnede sikkerheden for deres IoT løsninger. Visse enheder har brug at sende data til skyen, men kan ikke beskytte sig selv fra trusler på internettet. Du kan beskytte disse enheder fra eksterne tråde ved at få dem kommunikere med eksterne verden ved hjælp af en gateway.

Gatewayen er placeret på kanten mellem et sikkert miljø og åbne internettet. Enheder tale til gatewayen og gatewayen overfører meddelelser videre til den korrekte sky destination. Gatewayen er hærdet mod eksterne tråde, blokerer uautoriseret anmodninger, gør det muligt for autoriserede indgående trafik og videresender den indgående trafik til den rigtige enhed.

![][1]

Du kan også placere enheder, der kan beskytte sig bag en gateway til en ekstra sikkerhedslag. I dette scenarie skal du holde gatewayen OS installeret for at udbedre mod de seneste svagheder, i stedet for at opdatere Operativsystemet på alle enheder.

## <a name="isolation-plus-intelligence"></a>Isolationsniveauet plus intelligence

En hærdet router er en tilstrækkelige gateway til at isolere blot enheder. Men IoT løsninger ofte kræver, at en gateway giver flere intelligence end blot isolere enheder. Du kan for eksempel vil administrere dine enheder fra skyen. Er du kan bruge [LWM2M](https://github.com/OpenMobileAlliance/OMA_LwM2M_for_Developers/wiki), en standard enhed management-protokollen til skyen management del af løsningen. Enhederne send dog telemetri ved hjælp af en ikke TCP/IP aktiveret protocol. Desuden enhederne landbrugsprodukter masser af data, og du kun vil overføre et filtrerede undersæt af telemetri. Du kan oprette en løsning, der indeholder en IoT gateway stand til at håndtere to forskellige streams af data. Gatewayen skal gøre følgende:

-   Forstå **Telemetri**, filtrere dem, og Overfør det derefter til skyen gennem gatewayen. Gatewayen er ikke længere en enkel router, der blot videresender data mellem enheden og skyen.

-   Blot exchange **LWM2M enhed administration af data** mellem enhederne og skyen. Gatewayen er ikke nødvendigt at forstå dataene kommer ind i den, og kun skal at sikre, at dataene bliver sendt frem og tilbage mellem enhederne og skyen.

I følgende figur illustrerer dette scenarie:

![][2]

## <a name="the-solution-azure-iot-device-management-and-the-iot-gateway-sdk"></a>Løsningen: Azure IoT enhedsstyring og IoT Gateway SDK 

Offentligheden Gennemse version af [Azure IoT enhedsstyring] [ lnk-device-management] og betaversionen af [Azure IoT Gateway SDK] aktivere dette scenario. Gatewayen håndterer hver strøm af data på følgende måde:

-   **Telemetri**: Du kan bruge IoT Gateway SDK til at oprette en rørledning, der forstår, filtre og sender telemetridata til skyen. IoT Gateway SDK indeholder kode, der implementerer dele af denne pipeline på vegne af udvikleren. Du kan finde flere oplysninger om arkitekturen i SDK i [IoT Gateway SDK - Introduktion] [ lnk-gateway-get-started] selvstudium.

-   **Administration af enheder**: Azure Enhedshåndtering indeholder en LWM2M-klient, der kører på enheden, samt en skyen grænseflade til udstedelse management kommandoer til enheden.
    
    Du kræver ikke en særlig logik på gatewayen, fordi det ikke nødvendigt at behandle LWM2M data udvekslet mellem enheden og IoT centrum. Du kan aktivere Deling af Internetforbindelse, en funktion i mange moderne operativsystemer på gatewayen til udveksling af LWM2M data. Du kan vælge et passende operativsystem til dette scenario, fordi IoT Gateway SDK understøtter en lang række operativsystemer. Her er instruktioner til at aktivere Deling af Internetforbindelse på [Windows 10] og [Ubuntu], to af de mange understøttede operativsystemer.

Følgende illustration viser på højt niveau arkitektur, der bruges til at aktivere dette scenario, ved hjælp af [Azure IoT enhedsstyring] [ lnk-device-management] og [Azure IoT Gateway SDK].

![][3]

## <a name="next-steps"></a>Næste trin

For at lære, hvordan du bruger IoT Gateway SDK skal du se følgende selvstudier:

- [IoT Gateway SDK - Introduktion til brug af Linux][lnk-gateway-get-started]
- [IoT Gateway SDK – sende enhed i skyen meddelelser med en simuleret enhed med Linux][lnk-gateway-simulated]

Hvis du vil udforske yderligere funktionerne i IoT-Hub, i:

- [Udvikler vejledning][lnk-devguide]
- [Simulere en enhed med IoT Gateway SDK][lnk-gateway-simulated]

<!-- Images and links -->
[1]: media/iot-hub-gateway-device-management/overview.png
[2]: media/iot-hub-gateway-device-management/manage.png
[Azure IoT Gateway SDK]: https://github.com/Azure/azure-iot-gateway-sdk/
[Windows 10]: http://windows.microsoft.com/en-us/windows/using-internet-connection-sharing#1TC=windows-7
[Ubuntu]: https://help.ubuntu.com/community/Internet/ConnectionSharing
[3]: media/iot-hub-gateway-device-management/manage_2.png
[lnk-gateway-get-started]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-gateway-simulated]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-devguide]: iot-hub-devguide.md