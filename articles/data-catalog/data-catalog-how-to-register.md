<properties
   pageTitle="Sådan registreres datakilder | Microsoft Azure"
   description="Vejledning til artikel fremhævning af hvordan du registrerer datakilder med Azure datakatalog, herunder de metadatafelter, der er hentet ved tilmeldingen."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/04/2016"
   ms.author="maroche"/>


# <a name="how-to-register-data-sources"></a>Sådan registreres datakilder

## <a name="introduction"></a>Introduktion
**Microsoft Azure datakatalog** er en fuldt administreret skybaseret tjeneste, der fungerer som et system af registrering og system af registrering til enterprise-datakilder. Med andre ord handler **Azure datakatalog** om hjælpe personer opdage, forstå og bruge datakilder, og hjælp organisationer til at få flere værdi fra deres eksisterende data. Og det første trin til at gøre en datakilde synlig via **Azure datakatalog** kan du registrere datakilden.
## <a name="registering-data-sources"></a>Registrering af datakilder
Registrering er processen med at hente metadata fra datakilden og kopiere disse data til tjenesten **Azure datakatalog** . Dataene forbliver hvor den aktuelt er placeret, og forbliver under kontrol af administratorer og politikker i det aktuelle system.

Du kan registrere en datakilde ved blot Start værktøjet **Azure datakatalog** datakilde registrering fra portalen **Azure datakatalog** . Log på med dit arbejde eller skolekonto (de samme Azure Active Directory legitimationsoplysninger du kan bruge til at logge på portalen) og derefter vælge den datakilde, du vil registrere.
Se [Introduktion til Azure datakatalog](data-catalog-get-started.md) selvstudiet yderligere trinvise oplysninger.

Når først datakilden er blevet registreret, sporer dens placering i kataloget, og indekserer dets metadata, så brugere kan søge, gennemse, og finde datakilden og derefter bruge dens placering til at oprette forbindelse til den med programmet eller værktøjet efter eget valg.

## <a name="sources-supported"></a>Understøttede datakilder
Se [Data katalog Dti](data-catalog-dsr.md) til listen over aktuelt understøttede datakilder.
<br/>


## <a name="structural-metadata"></a>Strukturel metadata
Når du registrerer en datakilde, værktøjet til registrering kan finde oplysninger om strukturen af de objekter, du vælger – dette er nævnt som strukturel metadata.

For alle objekter omfatter denne strukturel metadata objektets placering, så brugere, der opdage dataene kan bruge disse oplysninger til at oprette forbindelse til objektet i klientværktøjer efter eget valg. Andre strukturel metadata indeholder navnet på det objekt, og skriv, og skriv attribut/kolonne navne og data.

## <a name="descriptive-metadata"></a>Beskrivende metadata
Ud over de grundlæggende strukturelle metadata hentet fra datakilden, udtrække værktøjet til datakilde registrering også samt beskrivende metadata. Dette er hentet fra egenskaberne beskrivelse, der vises af disse tjenester til SQL Server Analysis Services og SQL Server Reporting Services. Værdier leveres ved hjælp af ms_description udvidet egenskab trækkes ud for SQL Server. Oracle-database udtrække værktøjet til datakilde registrering kolonnen kommentarer fra visningen ALL_TAB_COMMENTS.

Ud over de beskrivende metadata, der er hentet fra datakilden, kan brugerne også angive beskrivende metadata ved hjælp af værktøjet til datakilde registrering. Brugere kan føje mærker og kan identificere eksperter for de objekter, der bliver registreret. Alle disse beskrivende metadata er kopieret til tjenesten **Azure datakatalog** sammen med de strukturelle metadata.

## <a name="including-previews"></a>Herunder eksempler

Som standard kun metadata er hentet fra datakilder og kopieret til **Azure datakatalog** -tjenesten, men om en datakilde er ofte gjort nemmere ved at se et eksempel på dataene, den indeholder.

Værktøjet **Azure datakatalog** datakilde til registrering gør det muligt for brugere at medtage en snapshot eksempel på dataene i hver tabel og visning, der er registreret. Hvis brugeren vælger på for at medtage eksempler under registrering, medtages værktøjet til registrering op til 20 poster fra hver tabel, og få vist. Denne snapshot kopieres derefter til kataloget sammen med de strukturelle og beskrivende metadata.


> [AZURE.NOTE]  Bred tabeller med et stort antal kolonner muligvis færre end 20 poster, der er inkluderet i deres preview.


## <a name="including-data-profiles"></a>Herunder data profiler

Ligesom herunder eksempler kan give værdifulde kontekst for brugere, søge efter datakilder i **Azure datakatalog**, kan herunder en data profil også gøre det nemmere at forstå opdaget datakilder.

Værktøjet **Azure datakatalog** datakilde til registrering gør det muligt for brugere at medtage en data profil for hver tabel og visning, der er registreret. Hvis brugeren vælger at medtage en data profil under registrering, værktøjet til registrering inkluderer statistik om dataene i hver tabel og se, herunder:

* Antallet af rækker og størrelsen af dataene i objektet
* Dato for den seneste opdatering af dataene og skemaet objekt
* Antallet af null poster og entydige værdier for kolonner
* Minimum, maksimum, gennemsnit og standardafvigelsen værdierne for kolonner

Disse statistikker derefter kopieres til kataloget sammen med de strukturelle og beskrivende metadata.

> [AZURE.NOTE]  Tekst og dato kolonner inkluderer ikke gennemsnit eller standardafvigelsen i stikprøverne statistik i deres data profil.

## <a name="updating-registrations"></a>Opdatere registreringer

Registrere en datakilde vil gøre det synlig i **Azure datakatalog** ved hjælp af metadata og valgfrit preview ud under registrering. Hvis datakilden skal opdateres i kataloget (for eksempel hvis skemaet af et objekt er blevet ændret, eller tabeller, der oprindeligt udelades, der skal medtages, eller en bruger ønsker at opdatere de data, der er inkluderet i eksemplerne) køre værktøjet til datakilde registrering kan igen.

Ny registrering en allerede registreret datakilde udfører handlingen Flet "upsert": eksisterende objekter vil blive opdateret, mens der oprettes nye objekter. Alle de metadata, der leveres af brugere via portalen **Azure datakatalog** bevares.

## <a name="summary"></a>Oversigt
Registrering af en datakilde med **Azure datakatalog** gør datakilden lettere at registrere og forstå ved at kopiere strukturelle og beskrivende metadata fra datakilden til tjenesten katalog. Når der er registreret en datakilde, det kan derefter skrive anmærkninger, administrerede og opdaget ved hjælp af portalen **Azure datakatalog** .

## <a name="see-also"></a>Se også
- [Introduktion til Azure datakatalog](data-catalog-get-started.md) selvstudium trinvise få mere at vide om, hvordan du registrere datakilder.
