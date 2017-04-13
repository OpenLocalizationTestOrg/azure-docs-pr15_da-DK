<properties 
    pageTitle="Stream Analytics: Rotere logonnavn legitimationsoplysninger for input og output | Microsoft Azure" 
    description="Lær, hvordan du opdaterer legitimationsoplysningerne for Stream Analytics input og output."
    keywords="logonoplysninger"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

#<a name="rotate-login-credentials-for-inputs-and-outputs-in-stream-analytics-jobs"></a>Rotere logonoplysninger for input og output i Stream Analytics job

##<a name="abstract"></a>Abstrakte
Azure Stream Analytics tillader i dag ikke, at erstatte legitimationsoplysningerne på input/output, mens jobbet kører.

Mens Azure Stream Analytics understøtter genoptage et job fra sidste output, ønskede vi at dele hele processen til minimering mellemliggende mellem stopper og starter på sag og rotere logonoplysninger.

##<a name="part-1---prepare-the-new-set-of-credentials"></a>Del 1 – forberede det nye sæt legitimationsoplysninger:
Denne del gælder for følgende input/output:

* BLOB-lager
* Begivenhed Hubs
* SQL-Database
* Table Storage

Fortsæt med del 2 for andre input/output.

###<a name="blob-storagetable-storage"></a>BLOB storage/Table storage
1.  Gå til lagerplads udvidelse på Azure Management-portalen:  
![graphic1][graphic1]
2.  Find den lagerplads, der bruges af dit arbejde, og gå til det:  
![graphic2][graphic2]
3.  Klik på kommandoen administrere hurtigtaster:  
![graphic3][graphic3]
4.  Mellem den primærnøgle i Access og sekundær hurtigtast, **Vælg den, der ikke bruges af dit arbejde**.
5.  Tryk på Genopret:  
![graphic4][graphic4]
6.  Kopiere den nyligt oprettede nøgle:  
![graphic5][graphic5]
7.  Fortsætte til del 2.

###<a name="event-hubs"></a>Begivenhed hubs
1.  Gå til filtypenavnet Service Bus på Azure Management-portalen:  
![graphic6][graphic6]
2.  Find Service Bus Namespace bruges af dit arbejde, og gå til det:  
![graphic7][graphic7]
3.  Hvis din sag bruger en delt access-politik på Service Bus Namespace, gå til trin 6  
4.  Gå til fanen begivenhed Hubs:  
![graphic8][graphic8]
5.  Find begivenhed hubben bruges af dit arbejde, og gå til det:  
![graphic9][graphic9]
6.  Gå til fanen Konfigurer:  
![graphic10][graphic10]
7.  Find den delte access-politik, der bruges af tingene på rullelisten politikkens navn:  
![graphic11][graphic11]
8.  Mellem den primærnøgle og den sekundære tast, skal du **vælge den, der ikke bruges af dit arbejde**.  
9.  Tryk på Genopret:  
![graphic12][graphic12]
10. Kopiere den nyligt oprettede nøgle:  
![graphic13][graphic13]
11. Fortsætte til del 2.  

###<a name="sql-database"></a>SQL-Database

>[AZURE.NOTE] Bemærk: skal du oprette forbindelse til tjenesten SQL-Database. Vi viser, hvordan du gør dette ved hjælp af management oplevelsen på Azure Management-portalen, men du kan vælge at bruge nogle klientsiden værktøj som SQL Server Management Studio samt.

1.  Gå til filtypenavnet SQL-databaser på Azure Management-portalen:  
![graphic14][graphic14]
2.  Find den SQL-Database, der bruges af dit arbejde, og **Klik på serveren** link på samme linje:  
![graphic15][graphic15]
3.  Klik på kommandoen Administrer:  
![graphic16][graphic16]
4.  Skriv Database Master:  
![graphic17][graphic17]
5.  Skriv dit brugernavn, adgangskode, og klik på Log på:  
![graphic18][graphic18]
6.  Klik på ny forespørgsel:  
![graphic19][graphic19]
7.  Skriv følgende forespørgsel udskifte < login_name > med dit brugernavn og erstatte <enterStrongPasswordHere> med den nye adgangskode:  
`CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>'`
8.  Klik på Kør:  
![graphic20][graphic20]
9.  Gå tilbage til trin 2 og denne gang Klik databasen:  
![graphic21][graphic21]
10. Klik på kommandoen Administrer:  
![graphic22][graphic22]
11. Skriv dit brugernavn, adgangskode, og klik på Logon:  
![graphic23][graphic23]
12. Klik på ny forespørgsel:  
![graphic24][graphic24]
13. Skriv i følgende forespørgsel erstatte < brugernavn > med et navn, som du vil identificere dette logon i forbindelse med denne database (du kan angive den samme værdi, du har givet til < login_name >, for eksempel) og erstatte < login_name > med dit nye brugernavn:  
`CREATE USER <user_name> FROM LOGIN <login_name>`
14. Klik på Kør:  
![graphic25][graphic25]
15. Du kan nu give din nye bruger med de samme roller og tilladelser som din oprindelige brugeren havde.
16. Fortsætte til del 2.

##<a name="part-2-stopping-the-stream-analytics-job"></a>Del 2: Stoppe kørslen Stream Analytics
1.  Gå til filtypenavnet Stream Analytics på Azure Management-portalen:  
![graphic26][graphic26]
2.  Find dit arbejde, og gå til det:  
![graphic27][graphic27]
3.  Gå til fanen input eller fanen lagrer baseret på, om du roteret legitimationsoplysninger på Input eller Output.  
![graphic28][graphic28]
4.  Klik på kommandoen Stop, og Bekræft jobbet ikke fungerer længere:  
![graphic29][graphic29] vente et job skal stoppe.
5.  Find den input/output, du vil rotere legitimationsoplysninger på og gå til det:  
![graphic30][graphic30]
6.  Fortsæt til del 3.

##<a name="part-3-editing-the-credentials-on-the-stream-analytics-job"></a>Del 3: Redigere legitimationsoplysningerne på Stream Analytics jobbet

###<a name="blob-storagetable-storage"></a>BLOB storage/Table storage
1.  Find feltet lagerplads Kontonøgle og indsætte produktnøglen nyligt oprettede i den:  
![graphic31][graphic31]
2.  Klik på kommandoen Gem, og Bekræft, at gemme dine ændringer:  
![graphic32][graphic32]
3.  En forbindelsestest starter automatisk, når du gemmer dine ændringer, Sørg for, at det vil sige har bestået.
4.  Fortsæt til del 4.

###<a name="event-hubs"></a>Begivenhed hubs
1.  Find feltet begivenhed Hub politiknøgle og indsætte produktnøglen nyligt oprettede i den:  
![graphic33][graphic33]
2.  Klik på kommandoen Gem, og Bekræft, at gemme dine ændringer:  
![graphic34][graphic34]
3.  En forbindelsestest starter automatisk, når du gemmer dine ændringer, Sørg for, at det har bestået.
4.  Fortsæt til del 4.

###<a name="power-bi"></a>Power BI
1.  Klik på Forny godkendelse:  
* ![graphic35][graphic35]
* Du får følgende bekræftelse:  
* ![graphic36][graphic36]
2.  Klik på kommandoen Gem, og Bekræft, at gemme dine ændringer:  
![graphic37][graphic37]
3.  En forbindelsestest starter automatisk, når du gemmer dine ændringer, Sørg for, at den er blevet overskredet.
4.  Fortsæt til del 4.

###<a name="sql-database"></a>SQL-Database
1.  Finde felterne brugernavn og adgangskode, og Indsæt nyoprettede sættet af legitimationsoplysninger i dem:  
![graphic38][graphic38]
2.  Klik på kommandoen Gem, og Bekræft, at gemme dine ændringer:  
![graphic39][graphic39]
3.  En forbindelsestest starter automatisk, når du gemmer dine ændringer, Sørg for, at det har bestået.  
4.  Fortsæt til del 4.

##<a name="part-4-starting-your-job-from-last-stopped-time"></a>Del 4: Begyndende tingene fra holdt op med at sidst
1.  Gå væk fra Input/Output:  
![graphic40][graphic40]
2.  Klik på kommandoen Start:  
![graphic41][graphic41]
3.  Vælg den sidste ikke længere tid, og klik på OK:  
 ![graphic42][graphic42]
4.  Fortsæt til del 5.  

##<a name="part-5-removing-the-old-set-of-credentials"></a>Del 5: Fjerne det gamle sæt legitimationsoplysninger
Denne del gælder for følgende input/output:
* BLOB-lager
* Begivenhed Hubs
* SQL-Database
* Table Storage

###<a name="blob-storagetable-storage"></a>BLOB storage/Table storage
Gentag del 1 for den hurtigtast, der tidligere blev brugt af tingene til at forny nu ubrugte hurtigtast.

###<a name="event-hubs"></a>Begivenhed hubs
Gentag del 1 for den nøgle, der tidligere blev brugt af tingene til at forny tasten nu ubrugt.

###<a name="sql-database"></a>SQL-Database
1.  Gå tilbage til forespørgselsvinduet fra del 1 trin 7, og indtast følgende forespørgsel udskifte < previous_login_name > med navnet på den bruger, der tidligere blev brugt af dit arbejde:  
`DROP LOGIN <previous_login_name>`  
2.  Klik på Kør:  
    ![graphic43][graphic43]  

Du skal have følgende bekræftelse: 

    Command(s) completed successfully.

## <a name="get-help"></a>Få hjælp
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-login-credentials-inputs-outputs/1-stream-analytics-login-credentials-inputs-outputs.png
[graphic2]: ./media/stream-analytics-login-credentials-inputs-outputs/2-stream-analytics-login-credentials-inputs-outputs.png
[graphic3]: ./media/stream-analytics-login-credentials-inputs-outputs/3-stream-analytics-login-credentials-inputs-outputs.png
[graphic4]: ./media/stream-analytics-login-credentials-inputs-outputs/4-stream-analytics-login-credentials-inputs-outputs.png
[graphic5]: ./media/stream-analytics-login-credentials-inputs-outputs/5-stream-analytics-login-credentials-inputs-outputs.png
[graphic6]: ./media/stream-analytics-login-credentials-inputs-outputs/6-stream-analytics-login-credentials-inputs-outputs.png
[graphic7]: ./media/stream-analytics-login-credentials-inputs-outputs/7-stream-analytics-login-credentials-inputs-outputs.png
[graphic8]: ./media/stream-analytics-login-credentials-inputs-outputs/8-stream-analytics-login-credentials-inputs-outputs.png
[graphic9]: ./media/stream-analytics-login-credentials-inputs-outputs/9-stream-analytics-login-credentials-inputs-outputs.png
[graphic10]: ./media/stream-analytics-login-credentials-inputs-outputs/10-stream-analytics-login-credentials-inputs-outputs.png
[graphic11]: ./media/stream-analytics-login-credentials-inputs-outputs/11-stream-analytics-login-credentials-inputs-outputs.png
[graphic12]: ./media/stream-analytics-login-credentials-inputs-outputs/12-stream-analytics-login-credentials-inputs-outputs.png
[graphic13]: ./media/stream-analytics-login-credentials-inputs-outputs/13-stream-analytics-login-credentials-inputs-outputs.png
[graphic14]: ./media/stream-analytics-login-credentials-inputs-outputs/14-stream-analytics-login-credentials-inputs-outputs.png
[graphic15]: ./media/stream-analytics-login-credentials-inputs-outputs/15-stream-analytics-login-credentials-inputs-outputs.png
[graphic16]: ./media/stream-analytics-login-credentials-inputs-outputs/16-stream-analytics-login-credentials-inputs-outputs.png
[graphic17]: ./media/stream-analytics-login-credentials-inputs-outputs/17-stream-analytics-login-credentials-inputs-outputs.png
[graphic18]: ./media/stream-analytics-login-credentials-inputs-outputs/18-stream-analytics-login-credentials-inputs-outputs.png
[graphic19]: ./media/stream-analytics-login-credentials-inputs-outputs/19-stream-analytics-login-credentials-inputs-outputs.png
[graphic20]: ./media/stream-analytics-login-credentials-inputs-outputs/20-stream-analytics-login-credentials-inputs-outputs.png
[graphic21]: ./media/stream-analytics-login-credentials-inputs-outputs/21-stream-analytics-login-credentials-inputs-outputs.png
[graphic22]: ./media/stream-analytics-login-credentials-inputs-outputs/22-stream-analytics-login-credentials-inputs-outputs.png
[graphic23]: ./media/stream-analytics-login-credentials-inputs-outputs/23-stream-analytics-login-credentials-inputs-outputs.png
[graphic24]: ./media/stream-analytics-login-credentials-inputs-outputs/24-stream-analytics-login-credentials-inputs-outputs.png
[graphic25]: ./media/stream-analytics-login-credentials-inputs-outputs/25-stream-analytics-login-credentials-inputs-outputs.png
[graphic26]: ./media/stream-analytics-login-credentials-inputs-outputs/26-stream-analytics-login-credentials-inputs-outputs.png
[graphic27]: ./media/stream-analytics-login-credentials-inputs-outputs/27-stream-analytics-login-credentials-inputs-outputs.png
[graphic28]: ./media/stream-analytics-login-credentials-inputs-outputs/28-stream-analytics-login-credentials-inputs-outputs.png
[graphic29]: ./media/stream-analytics-login-credentials-inputs-outputs/29-stream-analytics-login-credentials-inputs-outputs.png
[graphic30]: ./media/stream-analytics-login-credentials-inputs-outputs/30-stream-analytics-login-credentials-inputs-outputs.png
[graphic31]: ./media/stream-analytics-login-credentials-inputs-outputs/31-stream-analytics-login-credentials-inputs-outputs.png
[graphic32]: ./media/stream-analytics-login-credentials-inputs-outputs/32-stream-analytics-login-credentials-inputs-outputs.png
[graphic33]: ./media/stream-analytics-login-credentials-inputs-outputs/33-stream-analytics-login-credentials-inputs-outputs.png
[graphic34]: ./media/stream-analytics-login-credentials-inputs-outputs/34-stream-analytics-login-credentials-inputs-outputs.png
[graphic35]: ./media/stream-analytics-login-credentials-inputs-outputs/35-stream-analytics-login-credentials-inputs-outputs.png
[graphic36]: ./media/stream-analytics-login-credentials-inputs-outputs/36-stream-analytics-login-credentials-inputs-outputs.png
[graphic37]: ./media/stream-analytics-login-credentials-inputs-outputs/37-stream-analytics-login-credentials-inputs-outputs.png
[graphic38]: ./media/stream-analytics-login-credentials-inputs-outputs/38-stream-analytics-login-credentials-inputs-outputs.png
[graphic39]: ./media/stream-analytics-login-credentials-inputs-outputs/39-stream-analytics-login-credentials-inputs-outputs.png
[graphic40]: ./media/stream-analytics-login-credentials-inputs-outputs/40-stream-analytics-login-credentials-inputs-outputs.png
[graphic41]: ./media/stream-analytics-login-credentials-inputs-outputs/41-stream-analytics-login-credentials-inputs-outputs.png
[graphic42]: ./media/stream-analytics-login-credentials-inputs-outputs/42-stream-analytics-login-credentials-inputs-outputs.png
[graphic43]: ./media/stream-analytics-login-credentials-inputs-outputs/43-stream-analytics-login-credentials-inputs-outputs.png
 
