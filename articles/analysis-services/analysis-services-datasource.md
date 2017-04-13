<properties
   pageTitle="Datakilden forbindelser | Microsoft Azure"
   description="I denne artikel beskrives forbindelser til datakilder for datamodeller i Azure Analysis Services."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="owend"/>

# <a name="datasource-connections"></a>Datakilden forbindelser

Datamodeller i Azure Analysis Services kan kræve forskellige dataprovidere, når du opretter forbindelse til visse datakilder. I nogle tilfælde kan oprette forbindelse til datakilder ved hjælp af Oprindelig udbydere såsom SQL Server Native Client (SQLNCLI11) tabular-modeller returnere en fejl.

Eksempel Hvis du har en hukommelse, eller direkte forespørgsel data model, der opretter forbindelse til en datakilde til skyen som Azure SQL-Database, hvis du bruger oprindelige udbydere end SQLOLEDB, kan du se fejlmeddelelse: **"Provideren 'SQLNCLI11.1' ikke er registreret"**.

Eller, hvis du har en DirectQuery-model, oprette forbindelse til lokale datakilder, hvis du bruger oprindelige udbydere kan du se fejlmeddelelse: **"fejl ved oprettelse af OLE DB række sæt. Forkert syntaksen i nærheden af 'GRÆNSE' "**.

## <a name="data-source-providers"></a>Dataprovidere kilde

Udbyderne følgende datakilde understøttes til i hukommelsen eller direkte forespørgsel datamodeller, når du opretter forbindelse til en lokal installation eller datakilder i skyen:

|               | **Datakilde**                     | **I hukommelsen**                            |  **Direkte forespørgsel**                                           |
|---------------------------|-------------------------------|---------------------------------------------|---------------------------------------------|
| **Skyen**                     | Azure SQL datawarehouse      | .NET framework Data-Provider til SQL Server | .NET framework Data-Provider til SQL Server |
|                           | Azure SQL-Database            | .NET framework Data-Provider til SQL Server | .NET framework Data-Provider til SQL Server |
| **Lokalt** (via Gateway) | SQL Server                    | SQL Server Native Client 11.0               | .NET framework Data-Provider til SQL Server |
|                           |  SQL Server                             | Microsoft OLE DB-Provider til SQL Server    |   .NET framework Data-Provider til SQL Server                                          |
|                           |  SQL Server                             | .NET framework Data-Provider til SQL Server |  .NET framework Data-Provider til SQL Server                                           |
|                           | Oracle                        | Microsoft OLE DB-Provider til Oracle        | Oracle-dataprovider til .NET               |
|                           |  Oracle                             | Oracle-dataprovider til .NET               | Oracle-dataprovider til .NET                                            |
|                           | Teradata                      | OLE DB-Provider til Teradata                | Teradata-dataprovider til .NET             |
|                           |  Teradata                             | Teradata-dataprovider til .NET             |  Teradata-dataprovider til .NET                                            |
|                           | Analysesystem Platform | .NET framework Data-Provider til SQL Server | .NET framework Data-Provider til SQL Server |


> [AZURE.NOTE] Sørg for 64-bit udbydere er installeret, når du bruger lokale Gateway.

Når du overfører en lokal SQL Server Analysis Services tabular model til Azure Analysis Services, kan det være nødvendigt at ændre provider.

**Sådan angives en datakilde**

1. I SSDT > **Tabelformat Modelstifinder** > **Datakilder**, skal du højreklikke på en forbindelse til datakilde, og klik derefter på **Rediger datakilde**.

2. Klik på **Avanceret** for at åbne vinduet Avancerede egenskaber i **Rediger forbindelse**.

3. Under **Angiv avancerede egenskaber**i > **udbydere**, og derefter vælge den relevante udbyder.

## <a name="impersonation"></a>Repræsentation
I nogle tilfælde kan det være nødvendigt at angive en anden repræsentation konto. Repræsentation konto kan angives i SSDT eller SSMS.

Til lokale datakilder:

- Hvis bruger SQL-godkendelse, skal være repræsentation tjenestekonto.
- Hvis bruger Windows-godkendelse, skal du angive bruger/adgangskode til Windows. Windows-godkendelse med en bestemt repræsentation konto understøttes kun til i hukommelsen datamodeller til SQL Server.

Til skyen datakilder:

- Hvis bruger SQL-godkendelse, skal være repræsentation tjenestekonto.


## <a name="next-steps"></a>Næste trin

Hvis du har lokale datakilder, skal du sørge for at installere den [lokale gateway](analysis-services-gateway.md). Hvis du vil vide mere om administration af din server i SSDT eller SSMS, skal du se [administrere din server](analysis-services-manage.md).
