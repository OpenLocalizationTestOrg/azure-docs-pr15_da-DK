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
    ms.date="10/12/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-security-and-identity"></a>Azure Government sikkerhed og -identitet

##  <a name="key-vault"></a>Vigtige samling
Oplysninger om denne tjeneste og hvornår det skal bruges, finder du de <a href="https://azure.microsoft.com/documentation/services/key-vault">Azure-tasten samling offentlige dokumentation.</a>

### <a name="data-considerations"></a>Overvejelser i forbindelse med data
Følgende oplysninger identificerer i Azure Government rammen til Azure-tasten samling:

| Omfattet/kontrolleres data, der er tilladt | Omfattet/kontrolleres data, der er ikke tilladt |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alle de data, der er krypteret med en Azure-tasten samling nøgle kan indeholde Regulated/kontrolleres data. | Azure-tasten samling metadata er ikke tilladt til at indeholde kontrolleres Eksporter data. Disse metadata omfatter alle konfigurationsdata, der er angivet, da at oprette og vedligeholde din nøgle samling.  Kan ikke indsætte Regulated/kontrolleres data i følgende felter: gruppe ressourcenavne, nøgle samling feltnavne, Abonnementsnavnet |

Tasten samling er alment tilgængelig i Azure Government. Som offentligheden er der uden filtypenavn, så nøgle samling er kun tilgængelig via PowerShell og CLI.

## <a name="next-steps"></a>Næste trin

Supplerende oplysninger og opdateringer, du abonnerer på den <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government Blog.</a>
