<properties 
   pageTitle="StorSimple virtuelle matrix produktbemærkninger | Microsoft Azure"
   description="Beskriver kritiske Åbn problemer og løsninger til StorSimple virtuelle matrixen."
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
   ms.date="05/13/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-release-notes"></a>StorSimple virtuelle matrix produktbemærkninger

## <a name="overview"></a>Oversigt

Følgende produktbemærkninger identificere de kritiske åbne problemer for marts 2016 generelt tilgængelig (GA) version af Microsoft Azure StorSimple virtuelle matrixen (også kaldet StorSimple lokale virtuel enhed eller den virtuelle enhed StorSimple). Denne version svarer til softwareversion 10.0.10271.0.

Produktbemærkninger opdateres løbende, og som er opdaget alvorlige problemer, der kræver en løsning, de er blevet tilføjet. Før du installerer enheden StorSimple virtuelle, gennemgå omhyggeligt oplysningerne i produktbemærkninger. 

Den følgende tabel indeholder en oversigt over kendte problemer i denne udgave.


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
