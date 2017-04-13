<properties
   pageTitle="Trække SQL-data til Azure begivenhed Hubs | Microsoft Azure"
   description="Oversigt over begivenhed hubberne importere fra SQL-eksempel"
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor=""/>

<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="spyros;sethm" />

# <a name="pulling-data-from-sql-into-an-azure-event-hub"></a>Trække data fra SQL til en Azure begivenhed Hub

En typisk arkitektur for et program til at behandle realtidsdata skal du først trykke på den til en Azure begivenhed Hub. Det kan være en IoT scenarie, som overvågning trafik på forskellige underholdning, hvis en vej eller et scenarie, gaming, overvågning af en horde frenzied contestants eller en enterprise-scenarie, som overvågning dokumentkomponent ibrugtagning. I disse tilfælde data producenter er normalt eksterne objekter, der producerer tidsserie data, du skal bruge til at indsamle, analysere, gemme og reageres på, og du muligvis har investeret store anstrengelser for i opbygge infrastruktur for disse processer. Hvad du gøre, hvis du vil hente data fra noget som en database i stedet for en kilde til streaming data, og bruge det sammen med dine andre streaming data? Overvej den sag, hvor du vil bruge Azure Stream Analytics, eksterne Data Explorer (RDX) eller et andet værktøj til at analysere og reagere på langsomt ændre data fra Microsoft Dynamics AX eller et brugerdefineret Facilitetsstyring system? For at løse problemet, har vi skrevet og Åbn leveres et lille cloud-eksempel, der kan du redigere og installere, der henter data fra en SQL-tabel og overføre den til en Azure begivenhed Hub skal bruges som input i dine efterfølgende analytical programmer. Opdager, at dette er et scenarie med sjældne, og modsat af, hvad du normalt gør med en begivenhed Hub. Hvis du kan finde dig selv i den situation, hvor det er hvad du skal gøre, kan du finde koden i Azure eksempler galleriet, [her](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-import-from-sql/).  

Bemærk, at koden i dette eksempel er lige, en stikprøve. Det er **ikke** beregnet til at være et fremstilling program og er blevet ikke forsøgt at gøre det passer til brug i sådanne et miljø - det er stricly en DIY udvikler-fokuserede, eksempel. Du har brug at gennemse alle mulige sikkerhed, ydeevne og funktionalitet, og omkostninger faktorer inden du sætter dig på en arkitektur til slut.

## <a name="application-structure"></a>Programmet struktur

Programmet er skrevet i C#, og filen readme.md i stikprøven indeholder alle de oplysninger, du vil redigere, oprette og publicere programmet. De følgende afsnit indeholder en højt niveau oversigt over, hvad betyder det programmet.

Vi begynder med den antagelse, at du har adgang til en SQL Azure-tabel. Skal du også har konfigureret en Azure begivenhed Hub, og ved forbindelsen streng, der er behov for at få adgang til den.

Når SqlToEventHub løsningen starter, læser en konfigurationsfil (App.config) for at få en række ting, som beskrevet i filen readme.md. Dette er ret ingen forklaring, som navnet på datatabel osv, og der er ikke nødvendigt at rehash forklaringer her. 

Når programmet har læse-config-filen, det går i en løkke, læse tabellen SQL og trykke på poster til begivenhed-hub, og derefter venter på et brugerdefineret sengetider interval, før du udfører den forfra igen. Et par ting er værd at bemærke:

1. Programmet er baseret på den antagelse, at tabellen SQL opdateres af nogle eksterne proces, og du vil sende alle og kun opdateringerne til en begivenhed Hub.
2. SQL-tabellen skal have et felt, der har et entydigt og stigende tal, for eksempel en post numer. Det kan være så enkelt som et felt med navnet "-Id" eller andet, øges som det opdaterer databasen tilføjer poster som "Creation_time" eller "Sequence_number". Programmet noter og gemmer værdien i det pågældende felt i hver gentagelse. I hver efterfølgende gennemløb gennem løkken programmet grundlæggende forespørger tabellen for alle poster, når værdien i det pågældende felt overstiger værdien den fik vist sidste gang gennem løkken. Vi kalder denne sidste værdi "forskydning".
3. Programmet opretter en tabel "TableOffsets", når det startes, til at gemme forskydningerne. Tabellen er oprettet med forespørgslen "CreateOffsetTableQuery", der er defineret i filen config. 
4. Der findes flere forespørgsler, der bruges til at arbejde med den forskydning tabel, der er defineret i filen config som "OffsetQuery", "UpdateOffsetQuery" og "InsertOffsetQuery". Du skal ikke ændre disse.
5. Endelig er den forespørgsel, der er defineret "DataQuery" i filen config den forespørgsel, der skal køres for at adskille posterne fra SQL-tabel. Det er i øjeblikket er begrænset til toppen 1.000 poster i hver gang gennem løkke under optimering - Hvis for eksempel 25.000 poster er blevet føjet til databasen siden den sidste forespørgsel, det kan tage et stykke tid at udføre forespørgslen. Ved at begrænse forespørgslen til 1.000 poster hver gang, er forespørgslerne, der meget hurtigere. Vælge top 1.000 enkel flytter efterfølgende batches med 1.000 poster til begivenhed hubben.    

## <a name="next-steps"></a>Næste trin

Hvis du vil installere løsningen, klone eller hente programmet SqlToEventHub, redigere App.config-filen, opbygge den og til sidst publicere den. Når du har udgivet programmet, kan du se den kører i Azure klassisk portal under Cloud Services og overvåge begivenheder til begivenhed-hub. Bemærk, at hyppigheden afhænger af to ting: hyppigheden for opdateringer til SQL-tabel, og det sengetider interval, du har angivet i filen config for programmet.