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
    ms.date="09/29/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-compute"></a>Azure Government Beregn

##  <a name="virtual-machines"></a>Virtuelle maskiner

Du kan finde oplysninger om denne tjeneste og hvornår det skal bruges, [Azure virtuelle maskiner størrelser](../virtual-machines/virtual-machines-windows-sizes.md).

### <a name="variations"></a>Variationer

Den følgende VM lagerenheder er alment tilgængelig (GA) i Azure Government:

VM SKU|USA Gov VA|USA Gov IA|Noter
---|---|---|---
A|GA|GA|Ingen
Dv1|GA|-|Ingen
DSv1|GA|-|Ingen
Dv2|GA|GA|15 kommer snart
F|GA|GA|Ingen
G|Planlagt|-|Ingen

###  <a name="data-considerations"></a>Overvejelser i forbindelse med data

Følgende oplysninger identificerer Azure Government grænsen for virtuelle Azure-computere:

| Omfattet/kontrolleres data, der er tilladt | Omfattet/kontrolleres data, der er ikke tilladt |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Data har angivet, gemmes og behandles i en VM kan indeholde kontrolleres Eksporter data. Binære filer kører i virtuelle Azure-computere. Statisk authenticators som adgangskoder og chipkort ben til adgang til Azure-platformskomponenter. Private nøgler af certifikater, der bruges til at administrere Azure-platformskomponenter. SQL-forbindelsesstrenge.  Andre sikkerhed oplysninger/hemmeligheder, som certifikater, kryptering taster, master nøgler og lagerplads taster, der er gemt i Azure services.  | Metadata er ikke tilladt til at indeholde kontrolleres Eksporter data. Disse metadata omfatter alle konfigurationsdata, der er angivet, da at oprette og vedligeholde din Azure virtuelle maskine.  Kan ikke indsætte Regulated/kontrolleres data i følgende felter: lejer rolle navne, grupper, installation navne, ressourcenavne, ressource-mærker  

## <a name="next-steps"></a>Næste trin

Supplerende oplysninger og opdateringer, du abonnerer på den <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government Blog.</a>
