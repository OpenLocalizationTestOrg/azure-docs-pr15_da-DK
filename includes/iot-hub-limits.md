I følgende tabel vises de begrænsninger, der er knyttet til de anden tjeneste niveauer (S1, S2, S3, F1). Finde oplysninger om omkostninger for hver *enhed* i hvert lag, [IoT Hub priser](https://azure.microsoft.com/pricing/details/iot-hub/).

| Ressource | S1 Standard | S2 Standard | S3 Standard | F1 gratis |
| -------- | ----------- | ----------- | ----------- | ------- |
| Meddelelser/dag | 400.000 | 6,000,000   | 300,000,000 | 8.000   |
| Maksimale antal enheder | 200    | 200         | 200         | 1       |

> [AZURE.NOTE] Hvis du forventer at skulle bruge mere end 200 enheder med en S1 eller S2 eller S3 niveau-hub, skal du kontakte Microsoft support.

I følgende tabel vises begrænsningerne, der gælder for IoT Hub ressourcer:

| Ressource | Grænse |
| -------- | ----- |
| Maksimum betalt IoT hubs per Azure-abonnement | 10 |
| Maksimale gratis IoT hubs per Azure-abonnement | 1 |
| Maksimale antal enhed identiteter<br/>  returneres i et enkelt opkald | 1000 |
| IoT Hub meddelelse maksimale opbevaring for enhed i skyen meddelelser | 7 dage |
| Maksimumstørrelsen for enhed i skyen meddelelse | 256 KB |
| Maksimumstørrelsen for enhed i skyen batchen | 256 KB |
| Maksimalt antal meddelelser i batch enhed i skyen | 500 |
| Maksimumstørrelsen for cloud-til-enhed meddelelse | 64 KB |
| Maksimale TTL til skyen-til-enhed meddelelser | 2 dage |
| Levering af maksimalt antal for cloud-til-enhed <br/> meddelelser | 100 |
| Levering af maksimalt antal for feedback meddelelser <br/> svar på en meddelelse i skyen-til-enhed | 100 |
| Maksimale TTL til feedback meddelelser i <br/> svar på en meddelelse i skyen-til-enhed | 2 dage |

> [AZURE.NOTE] Hvis du har brug for mere end 10 betalt IoT hubs i et Azure-abonnement, skal du kontakte Microsoft support.

Tjenesten IoT Hub throttles anmodninger, hvis følgende kvotaer overskrides:

| Begrænsning | Hub værdi |
| -------- | ------------- |
| Identitet registreringsdatabasen handlinger <br/> (oprette, hente, liste, opdatere, slette) <br/> individuelle eller at Importér/Eksportér | 5000/min/enhed (for S3) <br/> 100/min/enhed (for S1 og S2). |
| Forbindelser til enheder | 6000/sec/enhed (for S3) 120/sec/enhed (for S2), 12/sec/enhed (for S1). <br/>Minimum af 100/sec. |
| Sender enhed i skyen | 6000/sec/enhed (for S3) 120/sec/enhed (for S2), 12/sec/enhed (for S1). <br/>Minimum af 100/sec. |
| Cloud-til-enhed sender | 5000/min/enhed (for S3), 100/min/enhed (for S1 og S2). |
| Cloud-til-enhed modtager | 50000/min/enhed (for S3) 1000/min/enhed (for S1 og S2). |
| Overfør filhandlinger | 5000 fil overføre meddelelser/min/enhed (for S3), 100 filer Overfør meddelelser/min/enhed (for S1 og S2). <br/> 10000 SAS URI'er kan være ud for en Azure-lager-konto på én gang.<br/> 10 SAS URI'er/enhed kan være ud ad gangen. |
