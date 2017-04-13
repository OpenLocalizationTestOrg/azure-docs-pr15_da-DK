<properties
     pageTitle="Bruge Azure-portalen til at oprette en IoT Hub | Microsoft Azure"
     description="En oversigt over, hvordan du oprette og administrere Azure IoT hubs via Azure-portalen"
     services="iot-hub"
     documentationCenter=""
     authors="dominicbetts"
     manager="timlt"
     editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/30/2016"
     ms.author="dobett"/>

# <a name="create-an-iot-hub-using-the-azure-portal"></a>Oprette en IoT hub ved hjælp af portalen Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]


## <a name="introduction"></a>Introduktion

I denne artikel beskrives, hvordan du finder tjenesten IoT Hub i portalen Azure, og hvordan du kan oprette og administrere IoT hubs.

## <a name="where-to-find-iot-hubs"></a>Sådan finder du IoT hubs

Der findes forskellige steder, hvor du kan finde IoT hubs.

1. **+ Ny**: **Azure IoT Hub** er en IoT-tjeneste, og du kan finde i kategorien **Internet af ting**, under **+ Ny**, svarende til andre tjenester.

2. IoT hubs kan også åbnes gennem på Marketplace som tjenesten helt under **Internet ting**.

## <a name="create-an-iot-hub"></a>Oprette en IoT-hub

Du kan oprette en IoT hub på følgende måder:

- Oprette en IoT hub via indstillingen **+ Ny** fører til bladet vises i det næste skærmbillede. Trinnene til at oprette IoT hubben via denne metode og via marketplace er identiske.

- Oprette en IoT hub gennem på Marketplace: Klik på **Opret** åbnet en blade, der er identisk med bladet forrige til **+ Ny** oplevelsen. I næste afsnit vises på flere trin, der er involveret i oprettelse af en IoT hub.

### <a name="choose-the-name-of-the-iot-hub"></a>Vælg navnet på IoT-hub

Hvis du vil oprette en IoT hub, skal du navngive hub. Dette navn skal være entydig på tværs af hubberne. Ingen dubletter af hubs er tilladt i back-end, så det anbefales, at denne hub hedder som identificerer som muligt.

### <a name="choose-the-pricing-tier"></a>Vælg det priser niveau

Du kan vælge mellem fire lag: **ledig**, **Standard 1** og **Standard 2**og **Standard S3**. Det gratis niveau kan kun 500 enheder have forbindelse til IoT hub og op til 8.000 meddelelser om dagen.

**Standard S1**: IoT Hubs S1 edition henvender sig til IoT løsninger, der har et stort antal enheder, der opretter relativt små datamængder per enhed. Hver enhed af S1 edition giver mulighed for op til 400.000 meddelelser om dagen på tværs af alle forbundne enheder.

**Standard S2**: IoT Hub S2 edition henvender sig til IoT løsninger, hvori enheder generere store datamængder. Hver enhed af S2 edition giver mulighed for op til 6 millioner meddelelser om dagen mellem alle forbundne enheder.

**Standard S3**: IoT Hub S3 edition henvender sig til IoT løsninger, der opretter store datamængder. Hver enhed af S3 edition giver mulighed for op til 300 millioner meddelelser om dagen mellem alle forbundne enheder.

![][4]

> [AZURE.NOTE] IoT Hub kan kun én gratis hub per Azure-abonnement.

### <a name="iot-hub-units"></a>IoT hub enheder

En IoT hub enhed indeholder et bestemt antal meddelelser om dagen. Det samlede antal meddelelser, der understøttes i forbindelse med denne hub er antallet enheder ganget med antallet af beskeder om dagen i pågældende niveau. Eksempelvis hvis du vil IoT hub til at understøtte indtrængen af 700,000 meddelelser, skal vælge du to S1 niveau enheder.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Enhed til skyen partitioner og ressourcegruppe

Du kan ændre antallet af partitioner for en IoT hub. Standard partitioner er sat til 4; dog kan du vælge et andet antal partitioner fra en rulleliste.

For hver ressourcegruppe behøver du ikke eksplicit oprette en tom ressourcegruppe. Når du opretter en ressource, kan du vælge at oprette en ny ressourcegruppe eller bruge en eksisterende ressourcegruppe.

![][5]

### <a name="choose-subscriptions"></a>Vælg abonnementer

Azure IoT Hub vises automatisk på listen over Azure abonnementer, som er tilknyttet brugerkontoen. Du kan vælge en af indstillingerne her for at knytte IoT hubben til pågældende Azure abonnement.

### <a name="choose-the-location"></a>Vælg placeringen

Indstillingen placering indeholder en liste over de områder, hvor IoT Hub tilbydes. IoT Hub er tilgængelig til at udrulle på følgende placeringer: Australien Øst, Australien Sydøst, Asien Øst, Asien Sydøst, Europe Nord, Europe vest, Japan Øst Japan vest, OS Øst, OS vest.

### <a name="create-the-iot-hub"></a>Oprette IoT-hub

Når alle forrige trin er fuldført, er IoT hub klar til at blive oprettet. Klik på **Opret** til at starte back end-processen med at oprette denne IoT hub med de specifikke indstillinger og for at installere den på den angivne placering.

Det kan tage et par minutter til IoT-hub skal have oprettet, som det tager tid til back end-installation skal udføres i de korrekte placering-servere.

## <a name="change-the-settings-of-the-iot-hub"></a>Ændre indstillingerne for IoT-hub

Du kan ændre indstillingerne for en eksisterende IoT hub, når den er oprettet fra bladet IoT Hub.

![][8]

**Delt Access politikker**: disse politikker definerer tilladelser for enheder og tjenester til at oprette forbindelse til IoT-Hub. Du kan få adgang til disse politikker ved at klikke på **Delt Access politikker** under **Generelt**. I denne blade, kan du ændre eksisterende politikker eller tilføje en ny politik.

### <a name="create-a-policy"></a>Oprette en politik

- Klik på **Tilføj** for at åbne en blade. Her kan du angive det nye politiknavn og de tilladelser, du vil knytte til denne politik, som vist i følgende figur.

    Der findes flere tilladelser, som kan knyttes til disse delte politikker. De første to politikker, **registreringsdatabasen læse** og **skrive registreringsdatabasen**, give læse og skrive-adgangsrettigheder til enhed identitet store eller registreringsdatabasen identitet. Hvis du vælger indstillingen Skriv automatisk vælger den Læs indstilling.

    Politikken **Service forbinde** giver tilladelse til at få adgang til skyen side slutpunkterne som gruppen consumer for at oprette forbindelse til IoT hub-tjenester. Politikken **enhed forbinde** tildeler tilladelser for at sende og modtage meddelelser på enheden side slutpunkterne på IoT hubben.

- Klik på **Opret** for at tilføje den nyligt oprettede politik til den eksisterende liste.

![][10]

## <a name="messaging"></a>Messaging

Klik på **meddelelser** for at få vist en liste over messaging egenskaberne for den IoT-hub, der ændres. Der er to primære typer af egenskaber, som du kan ændre eller kopiere: **skyen til enhed** og **enhed til skyen**.

- Indstillinger for **skyen til enhed** : denne indstilling har to subsettings: **skyen enhed TTL** (time-to-live) og **opbevaring tid** for meddelelser. Når IoT hubben oprettes, er begge disse indstillinger oprettet med en standardværdi på en time. Brug skyderne for at justere disse værdier, eller skriv værdierne.

- Indstillinger for **lydenhed i skyen** : denne indstilling har flere subsettings, hvoraf med navnet/tildeles når IoT hub oprettes og kan kun kopieres til andre subsettings, der kan tilpasses. Disse indstillinger er angivet i næste afsnit.

**Partitioner**: Denne værdi er angivet, når IoT hub oprettes og kan ændres via denne indstilling.

**Begivenhed Hub-kompatible navn og slutpunkt**: Når feltet IoT hub er, oprettes en begivenhed Hub internt, kan du have adgang til under visse omstændigheder. Denne hændelse Hub-kompatible navn og slutpunkt kan ikke tilpasses, men er tilgængelig til brug via knappen **Kopiér** .

**Opbevaring tid**: som standard angivet til én dag, men kan tilpasses til andre værdier ved hjælp af på rullelisten. Denne værdi er i dage for enhed i skyen og ikke i timer, som er den samme indstilling for skyen til enhed.

**Forbrugergrupper**: Consumer grupper er en indstilling, der svarer til andre SMS systemer, der kan bruges til at hente data bestemt måder at oprette forbindelse til IoT Hub andre programmer eller tjenester. Hver IoT hub er oprettet med en standardgruppe med forbrugere. Du kan tilføje eller slette consumer grupper til din IoT hubber.

> [AZURE.NOTE] Forbruger standardgruppen kan ikke redigeres eller slettes.

![][11]

## <a name="pricing-and-scale"></a>Priser og skala

Priser for en eksisterende IoT hub kan ændres via indstillingerne **priser** med følgende undtagelser:

- I den aktuelle implementering en IoT hub med en gratis SKU kan ikke ændre lag til en af de betalt lagerenheder eller omvendt.
- Der kan kun være én gratis niveau IoT hub i Azure-abonnement.

![][12]

Flytte fra et højere niveau (S2 eller S3) til nederste niveau (S1 eller S2) er tilladt, kun, når antallet af meddelelser, der sendes for den pågældende dag ikke er i konflikt. Eksempelvis hvis antallet af meddelelser om dagen overskrider 400.000, kan derefter niveauet for IoT hub ændres. Men hvis du ændrer til S1 niveau derefter hubben er begrænset for den pågældende dag.

## <a name="delete-the-iot-hub"></a>Slette IoT-hub

Du kan gå til den IoT-hub, du vil slette ved at klikke på **Gennemse**, og derefter vælge den relevante hub slette. Klik på knappen **Slet** under hub navnet til at slette hub.

## <a name="next-steps"></a>Næste trin

Følg disse links for at få flere oplysninger om administration af Azure IoT Hub:

- [Flere administrere IoT enheder][lnk-bulk]
- [Brugen målepunkter][lnk-metrics]
- [Handlinger overvågning][lnk-monitor]

Hvis du vil udforske yderligere funktionerne i IoT-Hub, i:

- [Udvikler vejledning][lnk-devguide]
- [Simulere en enhed med IoT Gateway SDK][lnk-gateway]
- [Secure din IoT løsning fra bunden op][lnk-securing]


  [4]: ./media/iot-hub-create-through-portal/create-iothub.png
  [5]: ./media/iot-hub-create-through-portal/location1.png
  [8]: ./media/iot-hub-create-through-portal/portal-settings.png
  [10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
  [11]: ./media/iot-hub-create-through-portal/messaging-settings.png
  [12]: ./media/iot-hub-create-through-portal/pricing-error.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md