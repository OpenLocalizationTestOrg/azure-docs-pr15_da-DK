<properties 
   pageTitle="Brugerdefineret logfiler i Log Analytics | Microsoft Azure"
   description="Log Analytics kan indsamle hændelser fra tekstfiler på både Windows og Linux-computere.  I denne artikel beskrives, hvordan du definerer en ny brugerdefineret log og oplysninger om de poster, de opretter i OMS-lager."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="custom-logs-in-log-analytics"></a>Brugerdefineret logge i Log Analytics

Brugerdefineret logfiler datakilden i Log Analytics giver dig mulighed at indsamle begivenheder fra tekstfiler på både Windows og Linux-computere. Mange programmer logoplysninger til tekstfiler i stedet for almindelige logføring tjenester som Windows-hændelseslog eller Syslog.  Når der indsamles, kan du fortolker hver post i loggen til individuelle felter ved hjælp af funktionen [Brugerdefinerede felter](log-analytics-custom-fields.md) i Log analyser.

![Brugerdefineret log af websteder](media/log-analytics-data-sources-custom-logs/overview.png)

Logfilerne, der indsamles skal svare til følgende kriterier.

- Loggen skal enten have en enkelt post hver linje eller bruge et tidsstempel, der svarer til en af følgende formater i starten af hvert element.

    ÅÅÅÅ-MM-DD HH:MM:SS <br>
  MM-ÅÅÅÅ TT: MM: SS AM/PM <br>
  Man DD, åååå HH:MM:SS
    
- Logfilen må ikke tillade cirkulær opdateringer, hvor filen overskrives med nye elementer. 

## <a name="defining-a-custom-log"></a>Definere en brugerdefineret logfil

Brug følgende fremgangsmåde til at definere en brugerdefineret logfil.  Rul ned til slutningen af denne artikel for at få en gennemgang af en stikprøve af at tilføje en brugerdefineret logfil.

### <a name="step-1-open-the-custom-log-wizard"></a>Trin 1. Åbn guiden brugerdefineret Log

Guiden Brugerdefineret Log kører i portalen OMS og gør det muligt at oprette en ny brugerdefineret log til at indsamle.

1.  Gå til **Indstillinger**på portalen OMS.
2.  Klik på **Data** og derefter på **brugerdefineret logfiler**.
3.  Alle ændringer i konfigurationen publiceres som standard automatisk i alle supportmedarbejdere.  For Linux supportmedarbejdere, der sendes en konfigurationsfil til Fluentd dataindsamler.  Hvis du vil ændre denne fil manuelt på hver Linux agent, derefter fjerne markeringen i afkrydsningsfeltet *Anvend under konfiguration til min Linux-computere*.
4.  Klik på **Add +** for at åbne guiden brugerdefineret Log.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Trin 2. Overføre og fortolke en logfil over eksempel

Du starter ved at uploade et eksempel på brugerdefineret logfilen.  Guiden Fortolk og få vist posterne i denne fil for dig at validere.  Log Analytics anvender det skilletegn, som du angiver for at identificere hver post.

**Ny linje** er standard afgrænser og bruges til logfiler, der har en enkelt post hver linje.  Hvis linjen starter med en dato og klokkeslæt i en af de tilgængelige formater, kan du angive et **tidsstempel** afgrænser som understøtter poster, der strækker sig over mere end én linje. 

Hvis der bruges et tidsstempel afgrænser, udfyldes egenskaben TimeGenerated for hver post, der er gemt i OMS med datatypen Dato/klokkeslæt, der er angivet for posten i logfilen.  Hvis en ny linje afgrænser bruges, udfyldes TimeGenerated med dato og klokkeslæt, Log Analytics indsamles posten. 

>[AZURE.NOTE]Log Analytics behandler aktuelt datatypen Dato/klokkeslæt, der indsamles fra en log, ved hjælp af en tidsstemplet afgrænser som UTC.  Dette vil snart ændres for at bruge tidszonen på agenten. 
 
1.  Klik på **Gennemse** og søge efter en eksempelfil.  Bemærk, at dette kan knappen kan navngives **Vælg fil** i nogle browsere.
2.  Klik på **Næste**. 
3.  Guiden Brugerdefineret Log vil overføre filen og få vist de poster, der identificerer.
4.  Ændre det skilletegn, som bruges til at identificere en ny post og vælge det tegn, der bedst identificerer posterne i din logfil.
5.  Klik på **Næste**.

### <a name="step-3-add-log-collection-paths"></a>Trin 3. Tilføje log af websteder stier

Du skal definere en eller flere kurver på det sted, hvor den kan finde den brugerdefinerede log agent.  Du kan enten angive et bestemt sti og navn til logfilen, eller du kan angive en sti med et jokertegn for navnet.  Dette understøtter programmer, der opretter en ny fil, hver dag, eller når en fil, når en bestemt størrelse.  Du kan også angive forskellige stier til en enkelt logfil.

For eksempel en programmet kan oprette en logfil hver dag med den dato, der er inkluderet i navn som log20100316.txt. Et mønster for sådanne en logfil kan være *log\*.txt* som skal anvendes på en logfil, følge programmet navngive farveskema.

Den følgende tabel indeholder eksempler på gyldige mønstre for at angive forskellige logfiler. 

| Beskrivelse | Sti |
|:--|:--|
| Alle filer i *C:\Logs* med filtypenavnet .txt på Windows-agent | C:\Logs\\\*.txt |
| Alle filer i *C:\Logs* med et navn, der starter med logfiler og filtypenavnet .txt på Windows-agent | C:\Logs\log\*.txt |
| Alle filer i */var/log/audit* med filtypenavnet .txt på Linux agent | /var/log/audit/*.txt |
| Alle filer i */var/log/audit* med et navn, der starter med logfiler og filtypenavnet .txt på Linux agent | /var/log/audit/log\*.txt |
  

1.  Vælg Windows eller Linux til at angive, hvilken sti format, du tilføjer.
2.  Skriv stien til og klik på den **+** knappen.
3.  Gentag processen for eventuelle yderligere stier.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Trin 4. Angiv et navn og beskrivelse til logfilen

Det navn, du angiver der skal bruges til typen log, som beskrevet ovenfor.  Den slutter altid med _CL at skelne det som en brugerdefineret logfil.

1.  Skriv et navn til loggen.  Den ** \_kl** suffiks leveres automatisk.
2.  Tilføje en valgfri **Beskrivelse**.
3.  Klik på **Næste** for at gemme den brugerdefinerede log definition.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Trin 5. Validere, der indsamles loggene brugerdefineret
Der kan gå op til en time for de oprindelige data fra en ny brugerdefineret log skal vises i Log Analytics.  Det starter indsamling af poster fra loggene findes i sti, du har angivet fra det sted, du har defineret brugerdefinerede logfilen.  Den ikke vil bevare de poster, du har overført under oprettelse af brugerdefineret logfil, men det indsamler allerede eksisterende poster i de logfiler, som den finder.

Når Log Analytics begynder at indsamle fra brugerdefinerede logfilen, bliver dens poster tilgængelig med en Log søgning.  Brug det navn, du har givet brugerdefinerede loggen som **typen** i forespørgslen.

>[AZURE.NOTE] Hvis egenskaben RAADATA mangler fra søgningen, kan du har brug at lukke og genåbne din browser.

### <a name="step-6-parse-the-custom-log-entries"></a>Trin 6. Fortolke brugerdefinerede logposter

Posten i hele logfilen gemmes i en enkelt egenskab kaldet **RAADATA**.  Du vil sandsynligvis til at adskille de forskellige dele af oplysninger i hvert element i individuelle egenskaber, der er gemt i posten.  Du gør dette ved hjælp af funktionen [Brugerdefinerede felter](log-analytics-custom-fields.md) i Log analyser.

Detaljeret vejledning til parsing af posten i brugerdefineret logfilen er ikke angivet her.  Se dokumentationen til [Brugerdefinerede felter](log-analytics-custom-fields.md) til disse oplysninger.

## <a name="disabling-a-custom-log"></a>Deaktivere en brugerdefineret logfil

Du kan ikke fjerne en brugerdefineret logfil definition, når den er blevet oprettet, men du kan deaktivere det ved at fjerne alle dens samling kurver.

1.  Gå til **Indstillinger**på portalen OMS.
2.  Klik på **Data** og derefter på **brugerdefineret logfiler**.
3.  Klik på **Detaljer** ud for den brugerdefinerede log definition til at deaktivere.
4.  Fjerne hver af samling stierne til den brugerdefinerede log definition.


## <a name="data-collection"></a>Dataindsamling

Log Analytics vil indsamle nye poster fra hver brugerdefineret log cirka hver 5 minutter.  Agenten vil registrere placering i hver logfil, der indsamles fra.  Hvis agenten går offline for et bestemt tidsrum, derefter indsamler Log Analytics poster fra hvor den sidst blev afbrudt, selvom disse poster er oprettet, mens agenten var offline.

Hele indholdet af posten i logfilen skrives til en enkelt egenskab kaldet **RAADATA**.  Du kan oversætte dette i flere egenskaber, der kan analysere og søgte separat ved at definere [Brugerdefinerede felter](log-analytics-custom-fields.md) , når du har oprettet brugerdefinerede logfilen.


## <a name="custom-log-record-properties"></a>Brugerdefinerede egenskaber for post

Brugerdefineret logføringsposter har en type med navn, du angiver, og egenskaberne, i den følgende tabel.

| Egenskaben | Beskrivelse |
|:--|:--|
| TimeGenerated | Dato og klokkeslæt, der er indsamlet i en post ved Log analyser.  Hvis loggen bruger en afgrænser til klokkeslæt, baseret er dette den tid, der indsamles fra den pågældende post. |
| SourceSystem  | Type af agent i en post er indsamlet fra. <br> Oprette forbindelse OpsManager – Windows agent, enten direkte eller SCOM <br> Linux – alle Linux supportmedarbejdere  |
| RAADATA             | Hele teksten for indtastningen i indsamlede. |
| ManagementGroupName | Navnet på gruppen management for SCOM supportmedarbejdere.  I forbindelse med andre supportmedarbejdere er AOI -\<arbejdsområde-ID\> |


## <a name="log-searches-with-custom-log-records"></a>Log søgninger med brugerdefinerede logposter

Poster fra brugerdefinerede logfiler gemmes i OMS lager ligesom poster fra en hvilken som helst anden datakilde.  De har en type, der stemmer overens med navnet, du angiver, når du definerer loggen, så du kan bruge egenskaben Type i din søgning til at hente poster, der indsamles fra en bestemt log.

Den følgende tabel indeholder forskellige eksempler på log søgninger, som henter poster fra brugerdefinerede logfiler.

| Forespørgsel | Beskrivelse |
|:--|:--|
| Skriv = MyApp_CL | Alle begivenheder fra en brugerdefineret log navngivne MyApp_CL. |
| Skriv = MyApp_CL Severity_CF = fejl | Alle begivenheder fra en brugerdefineret log med navnet MyApp_CL med en værdi på *fejl* i et brugerdefineret felt med navnet *Severity_CF*. |




## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Eksempel gennemgang af at tilføje en brugerdefineret logfil

Følgende afsnit indeholder en gennemgang et eksempel på oprettelse af en brugerdefineret logfil.  Eksempel loggen indsamles har en enkelt post på hver linje skal starte med en dato og klokkeslæt og derefter komma afgrænset felter for kode, status og meddelelse.  Flere eksempel poster er vist nedenfor.

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Overføre og fortolke en logfil over eksempel

Vi giver en af logfilerne og kan se de hændelser, det vil indsamle.  I dette tilfælde er ny linje en tilstrækkelige afgrænser.  Hvis en enkelt post i logfilen kan indeholde flere linjer via, skal en tidsstemplet afgrænser skal bruges.

![Overføre og fortolke en logfil over eksempel](media/log-analytics-data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Tilføje log af websteder stier

Logfilerne vil være placeret i *C:\MyApp\Logs*.  En ny fil oprettes hver dag med et navn, som indeholder datoen i mønster *appYYYYMMDD.log*.  Et tilstrækkelige mønster til denne log ville være *C:\MyApp\Logs\\\*.log*.

![Sti til logfil af websteder](media/log-analytics-data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Angiv et navn og beskrivelse til logfilen

Vi bruger et navn på *MyApp_CL* , og skriv en **Beskrivelse**.

![Lognavnet](media/log-analytics-data-sources-custom-logs/log-name.png)


### <a name="validate-that-the-custom-logs-are-being-collected"></a>Validere, der indsamles loggene brugerdefineret

Vi bruger en forespørgsel med *Type = MyApp_CL* til at returnere alle poster fra indsamlede logfilen.

![Log forespørgsel med nogen brugerdefinerede felter](media/log-analytics-data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>Fortolke brugerdefinerede logposter

Vi bruger brugerdefinerede felter til at definere *EventTime*, *kode*, *Status*, og *meddelelsen* felter og vi kan se forskellen i de poster, der returneres af forespørgslen.

![Log forespørgsel med brugerdefinerede felter](media/log-analytics-data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>Næste trin

- Bruge [Brugerdefinerede felter](log-analytics-custom-fields.md) til at fortolke poster i brugerdefineret loggen til individuelle felter.
- Få mere at vide om [log søgninger](log-analytics-log-searches.md) til at analysere de data, der indsamles fra datakilder og løsninger. 