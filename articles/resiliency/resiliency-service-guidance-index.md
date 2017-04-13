<properties
   pageTitle="Vedligeholde fleksibilitet vejledning | Microsoft Azure"
   description="Links til nedbrud og proaktiv fleksibilitet og tilgængelighed vejledning til Microsoft Azure-tjenester."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

# <a name="microsoft-azure-service-resiliency-guidance"></a>Microsoft Azure service fleksibilitet vejledning
Microsoft Azure er udviklet til at give dig de ressourcer, du skal bruge, når du har brug for. Du bør vide både hvordan at udvikle din brug af Azure services til at opnå høj tilgængelighed samt hvad du skal gøre, hvis dit program der påvirkes af en tjenesteforstyrrelse som en del af god design og drift fremgangsmåder. Dette dokument indeholder links til nedbrud gendannelse vejledning samt designvejledning til forskellige Azure tjenester kan være en hjælp i denne proces.

##<a name="disaster-recovery-guidance"></a>Nedbrud gendannelse vejledning
Nedbrud gendannelse vejledning linkene nedenfor er kan give dig de oplysninger, du har brug at hjælpe dig med at få dit program online igen hurtigt, hvis du påvirkes af en Azure tjenesteforstyrrelse. Disse links blev oprettet, for at hjælpe dig med at besvare spørgsmålene, "Jeg er der påvirkes af en Azure tjenesteforstyrrelse, hvad kan jeg gøre?"

##<a name="design-guidance"></a>Designvejledning
Design vejledning linkene nedenfor er design og arkitektonisk vejledning, der er oprettet for at hjælpe dig med at forstå, hvordan du bedst for at bruge hver Azure service på en måde, som maksimerer programmets oppetid. Disse links blev oprettet, for at hjælpe dig med at besvare spørgsmålene "Hvordan jeg sikre, at, en fejl, hardwarefejl, tjenesteforstyrrelse eller andre fejl ikke påvirke den overordnede tilgængeligheden af mit program?" Hvis der ikke er nogen specifikke vejledninger for den tjeneste, du aktuelt søger efter, i [høj tilgængelighed til programmer, der er bygget på Microsoft Azure](./resiliency-high-availability-azure-applications.md) -artiklen muligvis yderligere oplysninger, der kan hjælpe dig i dit design. 

##<a name="service-guidance"></a>Tjenesten vejledning
| Tjenesten  | Nedbrud gendannelse vejledning | Designvejledning |
|:---------|:--------------------------:|:------------------:|
| [Cloud Services] (https://azure.microsoft.com/services/cloud-services/ "Azure Cloud Services")       | [link] (../cloud-services/cloud-services-disaster-recovery-guidance.md "Azure Cloud Services nedbrud gendannelse vejledning")   | Ikke tilgængelig |
| [Vigtige samling] (https://azure.microsoft.com/services/key-vault/ "Azure vigtige samling")                      | [link] (../key-vault/key-vault-disaster-recovery-guidance.md "Azure nøgle samling nedbrud gendannelse vejledning")        | Ikke tilgængelig |
| [Lagerplads] (https://azure.microsoft.com/services/storage/ "Azure-lager")                            | [link] (../storage/storage-disaster-recovery-guidance.md "Azure-lager nedbrud gendannelse vejledning")          | Ikke tilgængelig |
| [SQL-databaser] (https://azure.microsoft.com/services/sql-database/ "Azure SQL-databaser")           | [link] (../sql-database/sql-database-disaster-recovery.md  "Azure SQL-Database nedbrud gendannelse vejledning")    | [link] (../sql-database/sql-database-business-continuity.md "Oversigt over Forretningskontinuitet med Azure SQL-Database") |
| [Virtuelle maskiner] (https://azure.microsoft.com/services/virtual-machines/ "Azure virtuelle maskiner") | [link] (../virtual-machines/virtual-machines-disaster-recovery-guidance.md "Virtuelle Azure-computere nedbrud gendannelse vejledning") | Ikke tilgængelig |
| [Virtuelt netværk] (https://azure.microsoft.com/services/virtual-network/ "Azure virtuelt netværk")    | [link] (../virtual-network/virtual-network-disaster-recovery-guidance.md "Azure virtuelt netværk nedbrud gendannelse vejledning")  | Ikke tilgængelig |

##<a name="next-steps"></a>Næste trin
Hvis du leder efter vejledning, der fokuserer mere alment på systemer og løsninger, skal du læser [nedbrud og høj tilgængelighed for bygget på Microsoft Azure-programmer](https://aka.ms/drtechguide).
