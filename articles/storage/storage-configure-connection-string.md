<properties 
    pageTitle="Konfigurere en forbindelsesstreng til Azure-lager | Microsoft Azure"
    description="Konfigurere en forbindelsesstreng til en Azure-lager-konto. En forbindelsesstreng indeholder de oplysninger, der er behov for at godkende adgang til en konto lagerplads fra dit program på kørselstidspunktet."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="configure-azure-storage-connection-strings"></a>Konfigurere Azure lagerplads forbindelsesstrenge

## <a name="overview"></a>Oversigt

En forbindelsesstreng indeholder oplysningerne om godkendelse behov for at få adgang til data i en konto Azure lagerplads fra dit program på kørselstidspunktet. Du kan konfigurere en forbindelsesstreng til:

- Oprette forbindelse til emulatoren Azure-lager.
- Få adgang til en lagerplads konto i Azure.
- Få adgang til angivne ressourcer i Azure via en delt adgang signatur (SAS).

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>Gemme din forbindelsesstreng

Dit program skal have adgang til forbindelsesstrengen på kørselstidspunktet for at godkende anmodninger til Azure-lager. Har du nogle forskellige muligheder for at gemme din forbindelsesstreng:

- Et program, der kører på skrivebordet eller på en enhed, kan du gemme forbindelsesstrengen i en `app.config `fil eller en `web.config` fil. Tilføj forbindelsesstrengen til sektionen **AppSettings** .
- Et program, der kører i en Azure skybaseret tjeneste, kan du gemme din forbindelsesstreng i [Azure service konfiguration skemafil (.cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Føje forbindelsesstrengen til afsnittet **ConfigurationSettings** i tjenesten konfigurationsfil.
- Du kan også bruge din forbindelsesstreng direkte i din kode. For de fleste scenarier, men anbefaler vi, at du gemmer din konfiguration streng i en konfigurationsfil.

Gemme din forbindelsesstreng i en konfigurationsfil gør det nemt at opdatere forbindelsesstrengen til at skifte mellem lagerplads emulatoren og en Azure lagerplads konto i skyen. Du skal kun redigere forbindelsesstrengen, så de peger på dit mål-miljø.

Du kan bruge klassen [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) til at få adgang til din forbindelsesstreng på kørselstidspunktet uanset hvor dit program kører.

## <a name="create-a-connection-string-to-the-storage-emulator"></a>Oprette en forbindelsesstreng til lagerplads emulatoren

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Se [Brug af Azure lagerplads emulatoren for udvikling og tester](storage-use-emulator.md) du kan finde flere oplysninger om lagerplads emulatoren.

## <a name="create-a-connection-string-to-an-azure-storage-account"></a>Oprette en forbindelsesstreng til en Azure-lager-konto

Brug i formatet af forbindelsesstrengen nedenfor til at oprette en forbindelsesstreng til kontoen Azure-lager. Angiver, om du vil oprette forbindelse til kontoen lagerplads via HTTPS (anbefales) eller HTTP, erstatte `myAccountName` med navnet på din lagerplads konto, og Erstat `myAccountKey` med din kontonøgle i access:

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

For eksempel ser din forbindelsesstreng nogenlunde sådan forbindelsesstrengen følgende eksempel:

    DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>

> [AZURE.NOTE] Azure-lager understøtter både HTTP og HTTPS i en forbindelsesstreng men ved hjælp af HTTPS anbefales.

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Oprette en forbindelsesstreng, ved hjælp af en delt adgang signatur

[AZURE.INCLUDE [storage-use-sas-in-connection-string-include](../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="creating-a-connection-string-to-an-explicit-storage-endpoint"></a>Oprette en forbindelsesstreng til eksplicitte lagerplads ark

Eksplicit kan du angive service slutpunkterne i din forbindelsesstreng i stedet for ved hjælp af standard slutpunkterne. For at oprette en forbindelsesstreng, der angiver eksplicitte ark skal du angive komplet service slutpunkt for hver tjeneste, herunder specifikationen protocol (HTTPS (anbefales) eller HTTP), i følgende format:

    DefaultEndpointsProtocol=[http|https];
    BlobEndpoint=myBlobEndpoint;
    QueueEndpoint=myQueueEndpoint;
    TableEndpoint=myTableEndpoint;
    FileEndpoint=myFileEndpoint;
    AccountName=myAccountName;
    AccountKey=myAccountKey

Et scenarie, hvor du muligvis gerne skal du angive et eksplicitte slutpunkt er, hvis du har tilknyttet dit Blob storage slutpunkt til et brugerdefineret domæne. Du kan i så fald angive din brugerdefinerede slutpunkt for Blob-lager i din forbindelsesstreng, og du kan også angive standard slutpunkterne for anden tjenesten, hvis dit program bruger disse.

Her er eksempler på gyldig forbindelsesstrenge, der angiver et eksplicitte slutpunkt for tjenesten Blob:

    # Blob endpoint only
    DefaultEndpointsProtocol=https;
    BlobEndpoint=www.mydomain.com;
    AccountName=storagesample;
    AccountKey=account-key

    # All service endpoints
    DefaultEndpointsProtocol=https;
    BlobEndpoint=www.mydomain.com;
    FileEndpoint=myaccount.file.core.windows.net;
    QueueEndpoint=myaccount.queue.core.windows.net;
    TableEndpoint=myaccount;
    AccountName=storagesample;
    AccountKey=account-key

Slutpunktsværdien, der er angivet i forbindelsesstrengen bruges til at oprette anmodningen URI'er til Blob-tjenesten, og det bestemmer form af en hvilken som helst URI'er, der returneres af koden.

Vær opmærksom på, hvis du vælger at udelade et service slutpunkt fra forbindelsesstrengen, derefter du ikke vil kunne bruge denne forbindelsesstreng til at få adgang til data i den pågældende tjeneste fra din kode.

### <a name="creating-a-connection-string-with-an-endpoint-suffix"></a>Oprette en forbindelsesstreng med et slutpunkt suffiks

Brug følgende forbindelse strengformat for at oprette en forbindelsesstreng for lagringstjeneste i områder eller forekomster med forskellige slutpunkt suffikser f.eks Azure Kina eller Azure styring. Angiv, om du vil oprette forbindelse til kontoen lagerplads via HTTP eller HTTPS erstatte `myAccountName` med navnet på kontoen lagerplads, erstatte `myAccountKey` med din konto hurtigtast, og Erstat `mySuffix` URI suffikset:


    DefaultEndpointsProtocol=[http|https];
    AccountName=myAccountName;
    AccountKey=myAccountKey;
    EndpointSuffix=mySuffix;


For eksempel skal din forbindelsesstreng ligne følgende forbindelsesstreng:

    DefaultEndpointsProtocol=https;
    AccountName=storagesample;
    AccountKey=<account-key>;
    EndpointSuffix=core.chinacloudapi.cn;

## <a name="parsing-a-connection-string"></a>Parsing af en forbindelsesstreng

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]


## <a name="next-steps"></a>Næste trin

- [Bruge emulatoren Azure-lager til udvikling og afprøvning](storage-use-emulator.md)
- [Azure-lager stifindere](storage-explorers.md)
- [Brug af delte Access signaturer (SAS)](storage-dotnet-shared-access-signature-part-1.md)
