<properties 
   pageTitle="StorSimple virtuelle matrix opdateringer produktbemærkninger | Microsoft Azure"
   description="Beskriver kritiske Åbn problemer og løsninger til StorSimple virtuelle matrixen kører opdatering 0,2 og 0,1."
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
   ms.date="06/16/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>Produktbemærkninger til StorSimple virtuelle matrix opdatering 0,2 og 0,1.

## <a name="overview"></a>Oversigt

Følgende produktbemærkninger identificere de kritiske åbne problemer og de løste problemer med Microsoft Azure StorSimple virtuelle matrix opdateringer. (Microsoft Azure StorSimple virtuelle matrix er også kendt som StorSimple lokale virtuelle enheden eller den virtuelle StorSimple-enhed). 

Produktbemærkninger opdateres løbende, og som er opdaget alvorlige problemer, der kræver en løsning, de er blevet tilføjet. Før du installerer enheden StorSimple virtuelle, gennemgå omhyggeligt oplysningerne i produktbemærkninger.

Opdater 0,2 svarer til software version **10.0.10280.0**; Opdater 0,1 er version **10.0.10279.0**. I nedenstående afsnit vises ændringerne for hver opdatering. 

> [AZURE.NOTE] Opdateringer er forstyrrende og genstarter din enhed. Hvis I/O er i gang, vil enheden betale nedetid.

## <a name="issues-fixed-in-the-update-02"></a>Problemer, der løses i Opdater 0,2
Opdater 0,2 indeholder alle ændringer fra Update 0,1 ud over fix er beskrevet i følgende tabel:

Funktion                              | Problem                                                                                                                                                                                                                                                                                                                           |
--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
Opdateringer                                 | I den sidste udgave, blev ikke opdateringer fundet automatisk i Azure klassisk portal, så du skulle bruge lokale Webbrugergrænsefladen til at installere opdateringer. Problemet er løst i denne udgave. Når du har installeret opdateringen 0,2, kan du installere fremtidige opdateringer ved hjælp af portalen Azure klassisk.                       

## <a name="whats-new-in-the-update-01"></a>Hvad er nyt i opdateringen 0,1.

Opdater 0,1 indeholder følgende fejlrettelser og forbedringer. 

- **Forbedret fleksibilitet skyen afbrydelser**: denne version har flere fejlrettelser rundt om nedbrud, sikkerhedskopiering, gendanne og overgang switchmiljø i tilfælde af en sky connectivity forstyrrelse. 

- **Forbedrede gendanne ydeevne**: denne version har fejlrettelser, der har betydeligt klippet nedetid fuldførelse af gendannelsesjob.

- **Automatiseret mellemrum videreudnyttelse optimering**: Når data slettes på tyndt klargjort enheder, ubrugt lagerplads blokke skal være frigjort. Denne version er forbedret mellemrum videreudnyttelse processen fra skyen medfører ubrugt plads ved at blive tilgængeligt hurtigere i forhold til tidligere versioner.

- **Nye virtuelle diskbilleder**: ny Virtuelle, VHDX og VMDK er nu tilgængelig via Azure klassisk-portalen. Du kan hente disse billeder for at klargøre nye opdatering 0,1 enheder.

- **Forbedre nøjagtigheden af sager status på portalen**: jobstatus for rapportering i portalen er ikke findelt i den tidligere version af softwaren. Problemet er løst i denne udgave.

- **Opret forbindelse til domænet oplevelse**: fejlrettelser relateret til deltagelse i domæne og omdøbning af enheden.


## <a name="issues-fixed-in-the-update-01"></a>Problemer, der løses i Opdater 0,1.

Den følgende tabel indeholder en oversigt over problemer løses i denne udgave.

| Nej.  | Funktion                              | Problem                                                                                                                                                                                                                                                                                                                           |
|------|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1    | VMDK                                 | I nogle VMware versioner blev OS disken set som sparse forårsager beskeder og forstyrrer normal handlinger. Dette er blevet løst i denne udgave.                                                                                                                                                                                    |
| 2    | iSCSI-server                         | Brugeren i den sidste udgave, er påkrævet for at angive en gateway for hver aktiveret netværksgrænseflade af enheden StorSimple virtuelle. Denne funktionalitet er ændret i denne udgave, så brugeren har til at konfigurere mindst én gateway til alle aktiveret netværksgrænseflader.                                                                              |
| 3    | Support-pakken                      | I den tidligere version af softwaren, understøtte pakke samling mislykkedes under pakke størrelsen er større end 1 GB. Problemet er løst i denne udgave.                                                                                                                                                                               |
| 4    | Skyadgang                         |  Hvis matrixen StorSimple virtuelle ikke har netværksforbindelse og blev genstartet, i den sidste udgave være lokale Brugergrænsefladen problemer med serverforbindelsen. Problemet er løst i denne udgave.                                                                                                                            |
| 5    | Overvågning af diagrammer                    | I den tidligere udgave, følge en enhed failover, vises skyen kapacitet anvendelsen diagrammerne forkerte værdier i portalen Azure klassisk. Dette er løst i den aktuelle version.                                                                                                                          |



## <a name="known-issues-in-the-update-01"></a>Kendte problemer i opdateringen 0,1.

I følgende tabel indeholder en oversigt over kendte problemer for matrixen StorSimple virtuelle og omfatter de problemer, der er noteret release fra tidligere versioner. **Problemer med udgivelsen noteret i denne version er markeret med en stjerne. Næsten alle problemer på denne liste har overføres fra GA udgave af StorSimple virtuelle matrix.**


| Nej. | Funktion | Problem | Løsning/kommentarer |
|-----|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1.** | Opdateringer | De virtuelle enheder, der er oprettet i preview-versionen kan ikke opdateres til en understøttet version af generelt tilgængelig. | Disse virtuelle enheder skal være failed over for ved hjælp af en arbejdsproces for nedbrud gendannelse (DR) bliver alment tilgængeligt. |
| **2.** | Klargjort datadisk | Når du har klargjort en datadisk med en bestemt angivne størrelse og oprettet den tilsvarende virtuelle StorSimple-enhed, du skal ikke Udvid eller Formindsk data disken. Forsøger at gøre det resulterer i et tab af alle data i de lokale niveauer af enheden. |   |
| **3.** | Gruppepolitik | Når en enhed er medlem af et domæne, kan anvende en gruppepolitik påvirke handlingen enhed. | Sikre, at din virtuelle matrix er i sin egen organisationsenhed (OU) til Active Directory og ingen objekter til gruppepolitik (GPO) anvendes til den.|
| **4.** | Lokale web brugergrænseflade | Hvis udvidede sikkerhedsfunktioner er aktiveret i Internet Explorer (IE ESC), fungerer visse lokale Brugergrænsefladen websider som fejlfinding eller vedligeholdelse muligvis ikke korrekt. Knapper til disse sider kan også fungerer ikke. | Deaktivere udvidede sikkerhedsfunktioner i Internet Explorer.|
| **5.** | Lokale web brugergrænseflade | I en Hyper-V virtuel maskine netværksgrænseflader i webdelen Brugergrænsefladen vises som 10 Gbps grænseflader. | Denne funktionalitet er en refleksion Hyper-V. Hyper-V viser altid 10 Gbps for virtuelle netværkskort. |
| **6.** | Lagdelte enheder eller aktier | Byte område låsning til programmer, der fungerer med StorSimple lagdelte enheder ikke understøttes. Hvis byte område låsning er aktiveret, fungerer StorSimple overgang switchmiljø ikke. | Anbefalede målinger omfatter: <br></br>Deaktiver byte område låsning i dit programlogik.<br></br>Vælg for at placere data for dette program i lokalt fastgjorte enheder i modsætning til lagdelte enheder.<br></br>*Advarsel*: Hvis ved hjælp af lokalt fastgjort enheder og byte område låsning er aktiveret, skal være opmærksom på, at lokalt fastgjorte lydstyrken kan være online, lige før gendannelsen er fuldført. I disse tilfælde, hvis en gendannelse er i gang, skal derefter du vente til gendannelse til at fuldføre. |
| **7.** | Lagdelte aktier | Arbejde med store filer kan medføre langsom niveau ud. | Når du arbejder med store filer, anbefales det, at filen største er mindre end 3% af del størrelsen. |
| **8.** | Bruges kapacitet til aktier | Du kan få vist dele forbrug i fravær af alle data på dialogboksen del. Dette skyldes, at den anvendte kapacitet for aktier indeholder metadata. |   |
| **9.** | Nedbrud | Du kan kun udføre nedbrud af en filserver til det samme domæne som kilde-enhed. Nedbrud til en destination enhed i et andet domæne understøttes ikke i denne udgave. | Dette vil blive gennemført i en nyere version. |
| **10.** | Azure PowerShell | De StorSimple virtuelle enheder kan ikke administreres via Azure PowerShell i denne udgave. | Alle for administration af de virtuelle enheder skal udføres via Azure klassisk portalen og lokale internettet brugergrænseflade. |
| **11.** | Ændring af adgangskode | Virtuel matrix enhed konsollen accepterer kun input i en-US tastatur format. |   |
| **12.** | CHAP | CHAP legitimationsoplysninger, når oprettet kan ikke fjernes. Desuden, hvis du ændrer CHAP legitimationsoplysningerne, skal du tage enhederne offline og derefter få dem online, før ændringen træder i kraft. | Dette vil blive behandlet i en nyere version. |
| **13.** | iSCSI-server  | Den 'bruges lagerplads' vises for en iSCSI-enhed kan være forskellige fra tjenesten StorSimple Manager og iSCSI-vært. | Værten iSCSI har visningen filsystemet.<br></br>Enheden, se dokumentkomponenter tildeles ved lydstyrken var på den maksimale størrelse.|
| **14.** | Fil server *  | Hvis en fil i en mappe har en alternativ Data Stream (REKLAMER) er knyttet til den, er REKLAMER ikke sikkerhedskopieret eller gendannet via nedbrud, Klon og gendannelse af post niveau.| |


## <a name="next-step"></a>Næste trin

[Installere opdateringer](storsimple-ova-install-update-01.md) på din StorSimple virtuelle matrix.
