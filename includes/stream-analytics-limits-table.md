<properties 
   pageTitle="Streame Analytics begrænsninger for tabel"
   description="I denne artikel beskrives begrænsninger for systemet og anbefalede størrelser for Stream Analytics komponenter og forbindelser."
   services="stream-analytics"
   documentationCenter="NA"
   authors="jeffstokes72"
   manager="paulettm"
   editor="cgronlun" />
<tags 
   ms.service="stream-analytics"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-data"
   ms.date="07/25/2016"
   ms.author="jeffstok" />

| Grænse-id | Grænse       | Kommentarer |
|----------------- | ------------|--------- |
| Maksimale antal Streaming enheder per abonnement per område | 50 | En anmodning om at øge streaming enheder for dit abonnement ud over 50 kan gøres ved at kontakte [Microsoft Support](https://support.microsoft.com/en-us). |
| Maksimale antal gennemløb af en Streaming enhed | 1MB / s * | Maksimale antal gennemløb per sø afhænger af scenariet. Faktisk overførselshastighed kan være lavere og afhænger af forespørgsel kompleksitet og partitionering. Yderligere oplysninger finder du i artiklen [skala Azure Stream Analytics job at øge overførselshastighed](../articles/stream-analytics/stream-analytics-scale-jobs.md) . |
| Maksimale antal input med værdien per job | 60 | Er der en grænse på 60 input per Stream Analytics job. |
| Maksimale antal output per job | 60 | Er der en grænse på 60 output per Stream Analytics job. |
| Maksimale antal funktioner per job | 60 | Er der en grænse på 60 funktioner per Stream Analytics job. |
| Maksimalt antal job per område | 1500 | Hvert abonnement kan have op til 1500 job per geografisk område. |