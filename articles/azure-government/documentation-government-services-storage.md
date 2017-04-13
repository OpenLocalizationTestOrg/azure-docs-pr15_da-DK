<properties
    pageTitle="Azure Government dokumentation | Microsoft Azure"
    description="Dette giver en sammenligning af funktioner og vejledning om udvikling af programmer til Azure Government"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/13/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-storage"></a>Azure Government lagerplads

##  <a name="azure-storage"></a>Azure-lager

Få mere at vide om denne tjeneste, og hvordan det skal bruges i dokumentationen [Azure-lager offentlige](https://azure.microsoft.com/documentation/services/storage/).

### <a name="variations"></a>Variationer

URL-adresser for lagerplads konti i Azure Government er forskellige:

Tjenestetype|Azure offentlige|Azure Government
---|---|---
BLOB-lager|*. blob.core.windows.net|*. blob.core.usgovcloudapi.net
Kø lagerplads|*. queue.core.windows.net|*. queue.core.usgovcloudapi.net
Table Storage|*. table.core.windows.net| *. table.core.usgovcloudapi.net

>[AZURE.NOTE] Alle dine scripts og kode skal tage højde for de relevante slutpunkter.  Se [konfigurere Azure lagerplads forbindelsesstrenge](../storage-configure-connection-string.md#creating-a-connection-string-to-the-explicit-storage-endpoint). 

Du kan finde flere oplysninger om API'er <a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx">Cloud Storage konto parametre</a>.

Det første eller sidste ark suffiks til brug i disse overloads er core.usgovcloudapi.net 

### <a name="considerations"></a>Overvejelser i forbindelse med

Følgende oplysninger identificerer i Azure Government rammen til Azure-lager:

| Omfattet/kontrolleres data, der er tilladt | Omfattet/kontrolleres data, der er ikke tilladt |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Data, der er angivet, gemmes, og behandles i en Azure-lager produkt kan indeholde kontrolleres Eksporter data. Statisk authenticators som adgangskoder og chipkort ben til adgang til Azure-platformskomponenter. Private nøgler af certifikater, der bruges til at administrere Azure-platformskomponenter. Andre sikkerhed oplysninger/hemmeligheder, som certifikater, kryptering taster, master nøgler og lagerplads taster, der er gemt i Azure services. | Azure lagerplads metadata er ikke tilladt til at indeholde kontrolleres Eksporter data. Disse metadata omfatter alle konfigurationsdata, der er angivet, da at oprette og vedligeholde produktet lagerplads.  Kan ikke indsætte Regulated/kontrolleres data i følgende felter: ressourcegrupper, installation navne, ressourcenavne, ressource-mærker  

##  <a name="premium-storage"></a>Premium lagerplads

Finde flere oplysninger om denne tjeneste, og hvordan det skal bruges i [Premium lagerplads: High-Performance lagerplads til Azure virtuelt arbejdsbelastninger](../storage/storage-premium-storage.md).

###  <a name="variations"></a>Variationer

Premium lagerplads er alment tilgængelig i USGov Virginia. Dette omfatter DS serie virtuelle computere. 

### <a name="considerations"></a>Overvejelser i forbindelse med

De samme lagerplads data overvejelser i forbindelse med ovennævnte gælder for premium lagerplads konti. 

##  <a name="next-steps"></a>Næste trin

Til supplerende oplysninger og opdateringer abonnere på den <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government Blog.</a>
