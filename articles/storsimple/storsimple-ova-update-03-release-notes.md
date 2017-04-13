<properties 
   pageTitle="StorSimple virtuelle matrix opdateringer produktbemærkninger | Microsoft Azure"
   description="Beskriver kritiske Åbn problemer og løsninger til StorSimple virtuelle matrixen kører opdatering 0,3."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/15/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-update-03-release-notes"></a>StorSimple virtuelle matrix opdatering 0,3 produktbemærkninger

## <a name="overview"></a>Oversigt

Følgende produktbemærkninger identificere de kritiske åbne problemer og de løste problemer med Microsoft Azure StorSimple virtuelle matrix opdateringer.

Produktbemærkninger opdateres løbende, og som er opdaget alvorlige problemer, der kræver en løsning, de er blevet tilføjet. Før du installerer din StorSimple virtuelle matrix, gennemgå omhyggeligt oplysningerne i produktbemærkninger.

Opdater 0,3 svarer til software version **10.0.10288.0**.

> [AZURE.NOTE] Opdateringer er forstyrrende og genstart enheden. Hvis I/O er i gang, påløber enheden, nedetid.


## <a name="whats-new-in-the-update-03"></a>Hvad er nyt i opdateringen 0,3

Opdater 0,3 er primært en rettelse build. I denne version er blevet sendt flere fejl, hvilket resulterer i sikkerhedskopiering fejl i den tidligere version.

## <a name="issues-fixed-in-the-update-03"></a>Problemer, der løses i Opdater 0,3

Den følgende tabel indeholder en oversigt over problemer løses i denne udgave.

| Nej.  | Funktion                              | Problem                                                                                                                                                                                                                                                                                                                           |
|------|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1    | Sikkerhedskopier                                |Et problem blev set i den tidligere udgave, hvor sikkerhedskopierne ikke vil udføre i et filshare. Hvis problemet opstod; sikkerhedskopieringen ikke vil og en vigtig besked er opløftet på tjenesten StorSimple Manager til at informere brugeren. Dette problem påvirker ikke dataene på aktier eller adgang til data. Den egentlige årsag blev identificeret og løst i denne udgave. <br></br> Fix gælder ikke tilbagevirkende for aktier, der allerede kan se dette problem. Kunder, der oplever dette problem skal først anvende opdatering 0,3 og derefter kontakte Microsoft Support for at udføre en sikkerhedskopiering af hele systemet for at løse problemet. I stedet for at kontakte Microsoft Support kan kunder også gendanne til et nyt share fra en sund sikkerhedskopi for de pågældende aktier.                                                                                                                                                                                 |
| 2    | iSCSI                         | Et problem, der blev set i den tidligere udgave, hvor enhederne ville forsvinder, når du kopierer data til en enhed på matrixen StorSimple virtuelle. Problemet er løst i denne udgave. <br></br> Rettelserne træder i kraft kun på det nye enheder. Rettelserne gælder ikke tilbagevirkende til enheder, der allerede kan se dette problem. Kunder anbefales det, at få de pågældende enheder online via Azure klassisk-portalen, opretter en sikkerhedskopi for disse enheder og derefter gendanne disse enheder til nye enheder.                                                               |


## <a name="known-issues-in-the-update-03"></a>Kendte problemer i opdateringen 0,3

I følgende tabel indeholder en oversigt over kendte problemer for matrixen StorSimple virtuelle og omfatter de problemer, der er noteret release fra tidligere versioner. 


| Nej. | Funktion | Problem | Løsning/kommentarer |
|-----|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1.** | Opdateringer | De virtuelle enheder, der er oprettet i preview-versionen kan ikke opdateres til en understøttet version af generelt tilgængelig. | Disse virtuelle enheder skal være failed over for ved hjælp af en arbejdsproces for nedbrud gendannelse (DR) bliver alment tilgængeligt. |
| **2.** | Klargjort datadisk | Når du har klargjort en datadisk med en bestemt angivne størrelse og oprettet den tilsvarende StorSimple virtuelle enhed, du skal ikke Udvid eller Formindsk data disken. Forsøger at gøre resultaterne i et tab af alle data i de lokale niveauer af enheden. |   |
| **3.** | Gruppepolitik | Når en enhed er medlem af et domæne, kan anvende en gruppepolitik påvirke handlingen enhed. | Sikre, at din virtuelle matrix er i sin egen organisationsenhed (OU) til Active Directory og ingen objekter til gruppepolitik (GPO) anvendes til den.|
| **4.** | Lokale web brugergrænseflade | Hvis udvidede sikkerhedsfunktioner er aktiveret i Internet Explorer (IE ESC), fungerer visse lokale Brugergrænsefladen websider som fejlfinding eller vedligeholdelse muligvis ikke korrekt. Knapper til disse sider kan også fungerer ikke. | Deaktivere udvidede sikkerhedsfunktioner i Internet Explorer.|
| **5.** | Lokale web brugergrænseflade | I en Hyper-V virtuel maskine netværksgrænseflader i webdelen Brugergrænsefladen vises som 10 Gbps grænseflader. | Denne funktionalitet er en refleksion af Hyper-V. Hyper-V viser altid 10 Gbps for virtuelle netværkskort. |
| **6.** | Lagdelte enheder eller aktier | Byte område låsning til programmer, der fungerer med StorSimple lagdelte enheder ikke understøttes. Hvis byte område låsning er aktiveret, virker StorSimple overgang switchmiljø ikke. | Anbefalede foranstaltninger omfatter: <br></br>Deaktiver byte område låsning i dit programlogik.<br></br>Vælg for at placere data for dette program i lokalt fastgjorte enheder i modsætning til lagdelte enheder.<br></br>*Advarsel*: Når ved hjælp af lokalt fastgjort enheder og byte område låsning er aktiveret, lokalt fastgjorte lydstyrken kan være online, lige før gendannelsen er fuldført. I disse tilfælde, hvis en gendannelse er i gang, skal derefter du vente til gendannelse til at fuldføre. |
| **7.** | Lagdelte aktier | Arbejde med store filer kan medføre langsom niveau ud. | Når du arbejder med store filer, anbefales det, at den største fil er mindre end 3% af del størrelsen. |
| **8.** | Bruges kapacitet til aktier | Du kan få vist del forbrug, når der er ingen data delt. Dette skyldes, at den anvendte kapacitet for aktier indeholder metadata. |   |
| **9.** | Nedbrud | Du kan kun udføre nedbrud af en filserver til det samme domæne som kilde-enhed. Nedbrud til en destination enhed i et andet domæne understøttes ikke i denne udgave. | Dette er implementeret i en nyere version. |
| **10.** | Azure PowerShell | De StorSimple virtuelle enheder kan ikke administreres via Azure PowerShell i denne udgave. | Alle for administration af de virtuelle enheder skal udføres via Azure klassisk portalen og lokale internettet brugergrænseflade. |
| **11.** | Ændring af adgangskode | Virtuel matrix enhed konsollen accepterer kun input i en-US tastatur format. |   |
| **12.** | CHAP | CHAP legitimationsoplysninger, når oprettet kan ikke fjernes. Desuden, hvis du ændrer CHAP legitimationsoplysningerne, vil du tage enhederne offline og derefter få dem online, før ændringen træder i kraft. | Dette problem er behandlet i en nyere version. |
| **13.** | iSCSI-server  | Den 'bruges lagerplads' vises for en iSCSI-enhed kan være forskellige fra tjenesten StorSimple Manager og iSCSI-vært. | Værten iSCSI har visningen filsystemet.<br></br>Enheden, se dokumentkomponenter tildeles ved lydstyrken var på den maksimale størrelse.|
| **14.** | Filserver  | Hvis en fil i en mappe har en alternativ Data Stream (REKLAMER) er knyttet til den, er REKLAMER ikke sikkerhedskopieret eller gendannet via nedbrud, Klon og gendannelse af post niveau.| |


## <a name="next-step"></a>Næste trin

[Installere opdatering 0,3](storsimple-ova-install-update-01.md) på din StorSimple virtuelle matrix.

## <a name="references"></a>Referencer

Leder du efter en ældre version note? Gå til: 

- [Produktbemærkninger til StorSimple virtuelle matrix opdatering 0,1 og 0,2](storsimple-ova-update-01-release-notes.md)

- [StorSimple virtuelle matrix generelt tilgængelighed produktbemærkninger](storsimple-ova-pp-release-notes.md)
 

