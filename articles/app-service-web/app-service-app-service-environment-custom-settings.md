<properties
    pageTitle="Brugerdefinerede indstillinger for App-tjenesten miljøer"
    description="Brugerdefinerede konfigurationsindstillinger for App-tjenesten miljøer"
    services="app-service"
    documentationCenter=""
    authors="stefsch"
    manager="nirma"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="stefsch"/>

# <a name="custom-configuration-settings-for-app-service-environments"></a>Brugerdefinerede konfigurationsindstillinger for App-tjenesten miljøer

## <a name="overview"></a>Oversigt ##
Da App Service miljøer isolerede til en enkelt kunde, er der visse konfigurationsindstillinger, der kan anvendes udelukkende på App-tjenesten miljøer. I denne artikel beskrives de forskellige specifikke tilpasninger, der er tilgængelige for App-tjenesten miljøer.

Hvis du ikke har et App-tjenesten miljø, se, [hvordan du opretter en App Service-miljø](app-service-web-how-to-create-an-app-service-environment.md).

Du kan gemme App-tjenesten miljø tilpasninger ved hjælp af en matrix i den nye **clusterSettings** -attribut. Attributten findes i "Egenskaber" ordbogen for *hostingEnvironments* Azure ressourcestyring enhed.

Nedenstående forkortede ressourcestyring skabelon kodestykke viser attributten **clusterSettings** :


    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

Attributten **clusterSettings** skal indgå i en ressourcestyring skabelon for at opdatere den App Service-miljø.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Brug Azure ressource Stifinder til at opdatere en App Service-miljø
Alternativt kan du opdatere App Service-miljø ved hjælp af [Azure ressource Stifinder](https://resources.azure.com).  

1. Ressource Stifinder, gå til noden til App Service-miljø (**abonnementer** > **resourceGroups** > **udbydere** > **Micrososft.Web** > **hostingEnvironments**). Klik derefter på det specifikke App servicemiljø, du vil opdatere.

2. Klik på **Læse-og skriveadgang** i den øverste værktøjslinje tillade interaktive redigering i ressource Explorer i højre rude.  

3. Klik på den blå **redigere** knappen for at gøre skabelonen ressourcestyring redigerbar.

4. Rul ned til bunden af ruden til højre. Attributten **clusterSettings** er nederst, hvor du kan angive eller opdatere dens værdi.

5. Skriv (eller kopiere og indsætte) af en række konfigurationsværdier, du vil i attributten **clusterSettings** .  

6. Klik på grønne **PLACERE** knappen, der er placeret øverst i højre rude for at acceptere ændringen App Service-miljø.

Men du har indsendt ændringen, tager det omkring 30 minutter multipliceret med antallet af front ender i App-tjenesten miljø, før ændringen træder i kraft.
Eksempelvis hvis en App-tjenesten miljø har fire forside afslutninger, tager det omkring to timer for konfiguration af opdateringen til at afslutte. Mens konfiguration ændringen føres ud af, kan ingen andre skalering handlinger eller konfiguration Erstat finde sted i App-tjenesten miljø.

## <a name="disable-tls-10"></a>Deaktivere TLS 1.0 ##
Et tilbagevendende spørgsmål fra kunder, især kunder, der arbejder med overholdelse af PCI revision, at deaktivere eksplicit TLS 1.0 for deres programmer på computeren.

TLS 1.0 kan være deaktiveret gennem følgende **clusterSettings** post:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## <a name="change-tls-cipher-suite-order"></a>Skift TLS kryptering pakke rækkefølge ##
En anden spørgsmål fra kunder er, hvis de kan redigere listen over ciphers forhandling af deres server og det kan ske ved at ændre **clusterSettings** , som vist nedenfor. På listen over tilgængelige kryptering-pakker kan hentes fra [denne MSDN-artikel] (https://msdn.microsoft.com/library/windows/desktop/aa374757(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [AZURE.WARNING]  Hvis forkerte værdier er angivet for den kryptering pakke, der ikke kan forstå SChannel, al TLS kommunikation til din server kan stoppe med at fungere. I dette tilfælde skal du fjerne posten *FrontEndSSLCipherSuiteOrder* fra **clusterSettings** og sende den opdaterede ressourcestyring skabelon for at vende tilbage til standardindstillingerne for kryptering pakke.  Brug denne funktion med forsigtighed.

## <a name="get-started"></a>Komme i gang
Azure Hurtig start ressourcestyring skabelonen websted indeholder en skabelon med grundlæggende definitionen for at [oprette en App Service-miljø](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).


<!-- LINKS -->

<!-- IMAGES -->
