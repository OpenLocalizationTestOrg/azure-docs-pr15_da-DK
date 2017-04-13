<properties
    pageTitle="Importere data til Machine Learning Studio fra en lokal fil | Microsoft Azure"
    description="Sådan importeres dataene kursus Azure Machine Learning Studio fra en lokal fil."
    keywords="importere data, dataformat, datatyper, datakilder, kursus data"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="garye;bradsev" />


# <a name="import-your-training-data-into-azure-machine-learning-studio-from-a-local-file"></a>Importere dataene kursus til Azure Machine Learning Studio fra en lokal fil

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]


Hvis du vil bruge dine egne data i Machine Learning Studio, kan du overføre en datafil forvejen fra din lokale harddisk til at oprette et datasæt modul i arbejdsområdet. 


## <a name="import-data-from-a-local-file"></a>Importere data fra en lokal fil

Du kan importere data fra en lokal harddisk ved at gøre følgende:

1. Klik på **+ Ny** nederst i vinduet Machine Learning Studio.
2. Vælg **DATASÆT** og **fra lokale filer**.
3. I dialogboksen **overføre et nyt datasæt** , gå til den fil, du vil overføre
4. Angiv et navn, identificere datatypen, og skriv eventuelt en beskrivelse. En beskrivelse anbefales – det gør det muligt at optage en hvilken som helst egenskaber om de data, skal du huske, når du bruger dataene i fremtiden.
5. Afkrydsningsfeltet **dette er den nye version af et eksisterende datasæt** kan du opdatere et eksisterende datasæt med nye data. Klik på dette afkrydsningsfelt og derefter skrive navnet på et eksisterende datasæt.

Under overførslen vises en meddelelse, at din fil overføres. Overføre tid, afhænger af størrelsen på dine data og hastigheden på din forbindelse til tjenesten.
Hvis du kender filen tager lang tid, kan du gøre andre ting inde Machine Learning Studio, mens du venter. Lukke browseren kan dog medføre data overførsel mislykkes.

Når dataene er overført, er gemt i et datasæt modul og findes på en hvilken som helst forsøg i arbejdsområdet.
Når du redigerer et forsøg, kan du finde de datasæt, du har oprettet på listen **Mine datasæt** under listen **Gemt datasæt** i paletten modul. Du kan trække og slippe datasættet over på lærredet forsøg, når du vil anvende datasættet på yderligere analyser og machine learning.



