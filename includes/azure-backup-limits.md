 (sikkerhedskopiering vaults<properties
   pageTitle = "Azure Backup limits table"
   Beskrivelse = "beskriver system grænser for Azure Backup."
   services="backup"
   documentationCenter="NA"
   authors="Jim-Parker"
   manager="jwhit"
   editor="" />
<tags
   ms.service="backup"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/05/2015"
   ms.author="trinadhk";"jimpark"; "aashishr" />


De følgende begrænsninger gælder for Azure sikkerhedskopi.

| Grænse-id | Standardgrænse |
|---|---|
|Antallet af servere/computere, der kan være registreret mod hver samling|50 for Windows Server/klient/SCDPM <br/> 200 for IaaS FOS|
|Størrelsen på en datakilde til data, der er gemt i Azure samling lagerplads|54400 GB Maks<sup>1</sup>|
|Antallet af sikkerhedskopiering vaults, der kan oprettes i hver Azure-abonnement|25 (sikkerhedskopi vaults) <br/> 25 gendannelse Services samling per område|
|Antallet af gange sikkerhedskopi kan planlægges dagen|3 om dagen til Windows Server/Client <br/> 2 om dagen i SCDPM <br/> En gang om dagen for IaaS FOS|
|Datadisce, der er knyttet til en Azure virtuelt til sikkerhedskopi|16|

- <sup>1</sup> Grænsen på 54400 GB gælder ikke for IaaS VM sikkerhedskopi.

