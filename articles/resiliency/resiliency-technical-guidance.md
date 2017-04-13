<properties
   pageTitle="Fleksibilitet teknisk vejledning indeks | Microsoft Azure"
   description="Indekset for tekniske artikler på forstå og design tolerant, høj tilgængelighed, fejlsikret programmer samt planlægning af nedbrud gendannelse og business løbende"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance"></a>Azure fleksibilitet teknisk vejledning

##<a name="introduction"></a>Introduktion

Høj tilgængelighed og krav til genoprettelse efter genoprettelse kræver to typer af viden:

- Detaljerede tekniske forståelse af en sky platform funktioner
- Viden om, hvordan at korrekt udvikle en fordelt tjeneste

Denne serie af artikler dækker tidligere: funktioner og begrænsninger for Azure-platformen i forhold fleksibilitet (også kaldet Forretningskontinuitet). Hvis du er interesseret i disse, skal du se artiklen serien fokuseret på [nedbrud og høj tilgængelighed til Azure-programmer](https://aka.ms/drtechguide). Selvom denne artikel serie rører på arkitektur og design mønstre, er, som ikke fokus i serien. Design en vejledning, kan du kontakte materiale i afsnittet [Yderligere ressourcer](#additional-resources) .

Oplysninger, der er inddelt i følgende artikler:

- [Gendannelse fra lokale fejl](resiliency-technical-guidance-recovery-local-failures.md).
Fysisk hardware (for eksempel drev, servere og netværksenheder) kan mislykkes. Ressourcer kan være for småt når Indlæs højest. I denne artikel beskrives de funktioner, der Azure leverer for at bevare høj tilgængelighed under disse omstændigheder.

- [Gendannelse fra en Azure område hele tjenesteforstyrrelse](resiliency-technical-guidance-recovery-loss-azure-region.md).
Udbredte mislykkede forsøg er sjældne, men de er teoretisk kan. Hele områder kan blive isolerede på grund af mislykkede forsøg på netværk, eller de kan være fysisk beskadiget efter neutralt nedbrud. I denne artikel forklares, hvordan du bruger Azure til at oprette programmer, der strækker sig over geografisk forskellige områder.

- [Gendannelse fra lokalt til Azure](resiliency-technical-guidance-recovery-on-premises-azure.md).
Skyen ændrer betydeligt økonomi af nedbrud, så organisationer kan bruge Azure til at oprette et andet websted for gendannelse. Du kan gøre dette for en brøkdel af omkostninger for bygge og vedligeholde en sekundær datacenter. Denne artikel beskrives de funktioner, hvor du Azure kan udvide en lokal datacenter til skyen.

- [Gendannelse fra databeskadigelse eller utilsigtet sletning](resiliency-technical-guidance-recovery-data-corruption.md).
Programmer kan have fejl, beskadiget data. Operatorer kan forkert slette vigtige data. I denne artikel beskrives det, hvad Azure leverer til sikkerhedskopiering af data og gendanne til et tidligere punkt den tid.

##<a name="additional-resources"></a>Yderligere ressourcer

- [Nedbrud og høj tilgængelighed for bygget på Microsoft Azure-programmer](resiliency-disaster-recovery-high-availability-azure-applications.md).
I denne artikel er et detaljeret overblik over tilgængelighed og nedbrud. Det dækker udfordringen af manuel gentagelse til reference og transaktions data. Det sidste afsnit indeholder oversigter over de forskellige typer af nedbrud gendannelse topologier, der spænder over Azure områder for det højeste niveau af tilgængelighed.

- [Tjekliste for høj tilgængelighed](resiliency-high-availability-checklist.md).
I denne artikel er en liste over funktioner, tjenester og design, der kan hjælpe dig med at øge fleksibilitet og tilgængeligheden af dit program.

- [Microsoft Azure service fleksibilitet vejledning](resiliency-service-guidance-index.md).
I denne artikel er et indeks over Azure tjenester og indeholder links til både nedbrud gendannelse vejledning og designvejledning.

- [Oversigt: business løbende og database nedbrud med SQL-Database i skyen](../sql-database/sql-database-business-continuity.md).
I denne artikel indeholder Azure SQL-Database teknikker til tilgængelighed. Den Centrerer primært på sikkerhedskopiering og gendannelse strategier. Hvis du bruger Azure SQL-Database i skybaseret tjeneste, skal du gennemse dette dokument og dens relaterede ressourcer.

- [Høj tilgængelighed og nedbrud til SQL Server på virtuelle Azure-computere](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).
I denne artikel beskrives tilgængelighed indstillinger, du kan undersøge, når du bruger infrastruktur som en tjeneste (IaaS) til at hoste din database services. Den behandler AlwaysOn tilgængelighed grupper, databasespejling, log forsendelses og sikkerhedskopiering/gendannelse. Flere selvstudier viser, hvordan du bruger disse metoder.

- [Bedste fremgangsmåder til design af store services på Azure Cloud Services](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/).
I denne artikel fokuserer på at udvikle meget SVG skyen arkitekturer. Mange af de teknikker, som du anvender, til at forbedre skalerbarhed også forbedre tilgængelighed. Hvis dit program ikke kan skalere øget belastning, bliver skalerbarhed også et tilgængelighedsproblem, der.

- [Sikkerhedskopiere og gendanne til SQL Server på virtuelle Azure-computere](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).
I denne artikel indeholder teknisk vejledning i at sikkerhedskopiere og gendanne Microsoft SQL Server, der kører på virtuelle Azure-computere.

- [Fejlsikret: vejledning til tolerant skyen arkitekturer](https://channel9.msdn.com/Series/FailSafe).
I denne artikel indeholder en vejledning til opbygning af tolerant skyen arkitekturer vejledning til implementering af disse arkitekturer på Microsoft-teknologier og opskrifter til implementering af disse arkitekturer for bestemte scenarier.

- [Tekniske Casestudie: ved hjælp af skyen teknologier til at forbedre nedbrud](https://www.microsoft.com/itshowcase/Article/Content/737/Using-cloud-technologies-to-improve-disaster-recovery).
Denne Casestudie viser, hvordan Microsoft IT anvendes Azure til at forbedre nedbrud.

##<a name="next-steps"></a>Næste trin

I denne artikel er en del af en serie, fokuseret på tekniske vejledning til Azure fleksibilitet. Hvis du er interesseret i at læse andre artikler i denne serie, kan du starte med [gendannelse fra lokale fejl](resiliency-technical-guidance-recovery-local-failures.md).
