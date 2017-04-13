<properties 
   pageTitle="Overvåge brugen og statistik i en Azure søgetjenesten | Microsoft Azure | Hostet skyen search-tjenesten" 
   description="Spore ressource forbrug og indeks størrelse til Azure søgning efter en hostet skyen søgetjenesten på Microsoft Azure." 
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="jhubbard" 
   editor=""
   tags="azure-portal"/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required" 
   ms.date="05/17/2016"
   ms.author="heidist"/>

# <a name="monitor-usage-and-statistics-in-an-azure-search-service"></a>Overvåge brugen og statistik i en Azure Search-tjenesten

Sporing af væksten af indeks og dokumentstørrelse kan hjælpe dig med at justere proaktiv kapacitet før nå den øvre grænse, du har oprettet for din tjeneste. 

Tæller og statistikker for din tjeneste for at overvåge Ressourceforbrug, vises nemt i [Azure-portalen](https://portal.azure.com), men du kan også få oplysninger fra et program, hvis du bygger et værktøj til administration af brugerdefinerede tjenester. I denne artikel beskrives trinnene til begge metoder.

Du kan også bruge den nye trafik analytics søgefunktion til indsigt i aktivitet på niveauet for indeks. Besøg [Søg trafik Analytics til Azure søgning](search-traffic-analytics.md) til at komme i gang.

##<a name="view-counts-and-metrics-in-the-portal"></a>Få vist tæller og målepunkter i portalen 

1. Log på [Azure-portalen](https://portal.azure.com). 

2. Åbn dashboardet tjenesten for din Azure søgetjeneste. Felter for tjenesten kan findes på startsiden, eller du kan gå til tjenesten fra Gennemse i JumpBar. Se [oprette en tjeneste](search-create-service-portal.md) for en trinvis vejledning.

Sektionen brugen omfatter en tæller, der fortæller dig, hvilken del af ressourcer, der er tilgængelig i øjeblikket er i brug.

  ![][1]

Tilbagekalde, delte tjenester har maksimalt én replika og partition hver. Desuden kan det kun understøtter 10.000 dokumenter i samlede eller 50 MB data, afhængigt af hvad der kommer først.

##<a name="get-index-statistics-using-the-rest-api"></a>Få indeks statistik ved hjælp af REST-API

Både Azure Search REST-API og .NET SDK indeholder programmeringsmæssig adgang til tjenesten målepunkter.  Hvis du bruger [indeks](https://msdn.microsoft.com/library/azure/dn946891.aspx) til at indlæse et indeks fra Azure SQL-Database eller DocumentDB, en ekstra API er tilgængelig for at få de tal, du har brug for. 

  + [Få indeks statistik](https://msdn.microsoft.com/library/azure/dn798942.aspx)
  + [Antal dokumenter](https://msdn.microsoft.com/library/azure/dn798924.aspx)
  + [Hente indekseringsprogram Status](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## <a name="next-steps"></a>Næste trin

Gennemse [begrænsninger og kapacitet](search-limits-quotas-capacity.md) for at afgøre kombinationen af partitioner og replikaer, du skal bruge, hvis eksisterende kapacitet er tilstrækkelig. 

Gå til [Administrer dit søgetjenesten på Microsoft Azure](search-manage.md) til flere oplysninger om administration af tjenesten.

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG




 
