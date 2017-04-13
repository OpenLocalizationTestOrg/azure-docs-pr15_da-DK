<properties
   pageTitle="Nedbrud og høj tilgængelighed til Azure programmer | Microsoft Azure"
   description="Tekniske oversigter og uddybende oplysninger om design af programmer til høj tilgængelighed og genoprettelse efter nedbrud af bygget på Microsoft Azure-programmerne."
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

#<a name="disaster-recovery-and-high-availability-for-applications-built-on-microsoft-azure"></a>Nedbrud og høj tilgængelighed for bygget på Microsoft Azure-programmer

##<a name="introduction"></a>Introduktion

I denne artikel fokuserer på høj tilgængelighed til programmer, der kører i Azure. En overordnet strategi for høj tilgængelighed indeholder også aspekter af nedbrud. Planlægning af mislykkede forsøg og nedbrud i skyen, skal du hurtigt kan genkende fejlene i. Du kan derefter implementere en strategi, der svarer til din tolerance programmets nedetid. Desuden kan skal du overveje omfanget af datatab programmet kan acceptere uden medfører negative business konsekvenser, som det er gendannet.

De fleste virksomheder, at de er forberedt på midlertidige og store mislykkede forsøg. Men før du svarer til dig selv spørgsmålet, virksomheden afprøve disse fejl? Tester gendannelse af databaser for at sikre, at du har de korrekte processer på plads? Sandsynligvis ikke. Det skyldes, at vellykket nedbrud starter med masser af planlægning og planlagde arkitektur for at implementere disse processer. Ligesom mange andre ikke-funktionel krav, som sikkerhed, bliver nedbrud sjældent forudgående analyse og tid allokering kræver. Desuden har de fleste virksomheder ikke budgettet for geografisk fordelt områder med overflødige kapacitet. Derfor medtages lige vigtigste programmer ofte ikke i planlægge gendannelse stort efter nedbrud.

Platforme, som Azure i skyen, Giv geografisk spredt områder i hele verden. Disse platforme giver også funktioner, der understøtter tilgængelighed og en række forskellige genoprettelse efter nedbrud scenarier. Nu alle kommunikations kritiske skyen programmerne kan tildeles forfaldne overvejelser til nedbrud korrektur af systemet. Azure har fleksibilitet og nedbrud, der er indbygget i mange af sine tjenester. Du skal undersøge funktionerne platform omhyggeligt og tillæg med programmet strategier.

I denne artikel dispositioner de nødvendige arkitektur trin, du skal bruge til nedbrud bevis, en Azure-installation. Du kan derefter implementere større løbende forretningsproces. En forretningsplan løbende er en vejledning til fortsætter handlinger på negative betingelser. Det kan skyldes en fejl med teknologi, som en downed tjeneste eller neutralt nedbrud, som en storm eller power afbrydelse. Programmet fleksibilitet for nedbrud er kun et undersæt af større nedbrud gendannelsesprocessen, som beskrevet i dette dokument NIST: [Begivenhed Planlægningsvejledning for oplysninger teknologi Systems](https://www.fismacenter.com/sp800-34.pdf).

De følgende afsnit definere forskellige niveauer af fejl, teknikker til at løse dem, og arkitekturer, der understøtter disse metoder. Disse oplysninger indeholder input til din nedbrud gendannelsesprocesser og procedurer til at kontrollere, at din strategi for genoprettelse efter genoprettelse virker korrekt og effektivt.

##<a name="characteristics-of-resilient-cloud-applications"></a>Egenskaber for tolerant skyen programmer

Et godt ændrede arkitektur program kan klare egenskab mislykkede forsøg på et taktisk niveau, og det kan også acceptere strategiske systemindstillinger mislykkede forsøg på områdeniveauet for. De følgende afsnit definere den terminologi, der refereres til i hele dokumentet til at beskrive forskellige aspekter af tolerant skytjenester.

###<a name="high-availability"></a>Høj tilgængelighed

En meget tilgængelige skyen programmet implementerer strategier for at opfanger afbrydelse af afhængigheder, som de administrerede tjenester, skyen platform. Denne metode tillader på trods af mulige fejl i skyen-platform-funktioner, programmet kan fortsætte med at udvise de forventede funktionelle og ikke-funktionel systematiske egenskaber. Dette er dækket dybden i kanal 9 video-serien [fejlsikret: vejledning til tolerant skyen arkitekturer](https://channel9.msdn.com/Series/FailSafe).

Når du implementerer programmet, skal du overveje sandsynligheden for en egenskab afbrydelse. Desuden kan du overveje påvirkningen en afbrydelse skal være på programmet fra virksomhedens perspektiv, før dykker dybe i implementering strategier. Uden forfaldsdatoer overvejelser til virksomheden og sandsynligheden for, at nå betingelsen risikoen implementeringen kan være dyrt og potentielt unødvendige.

Overvej en bil anvendelse for høj tilgængelighed. Selv kvalitet dele og overordnede teknisk forhindrer ikke lejlighedsvise fejl. For eksempel når din bil henter en flad dæk, bil kører stadig, men det fungerer med nedgraderede funktionalitet. Hvis du havde planlagt til denne potentielle forekomst, kan du bruge en af disse ekstra dæk tynde-med kant, indtil du når en reparation. Selvom den ekstra dæk ikke tillader høje hastigheder, kan du stadig fungerer på biltype, indtil du erstatte dæk. På samme måde, en skybaseret tjeneste, der planlægger for muligt tab af funktioner kan forhindre en relativt mindre problem hvilket ned hele programmet. Dette er tilfældet, selvom skytjenesten skal køre med nedgraderede funktionalitet.

Der er et par vigtigste karakteristika meget tilgængelige skytjenester: tilgængelighed, skalerbarhed og fejltolerance. Selvom disse egenskaber er indbyrdes forbundne, er det vigtigt at forstå hver, og hvordan de bidrager til overordnede tilgængeligheden af løsningen.

###<a name="availability"></a>Tilgængelighed

Et tilgængeligt program finder tilgængeligheden af dens underliggende infrastruktur og afhængige tjenester. Tilgængelige programmer fjerne enkelt punkter af manglende via redundans og robust design. Når du udvide omfanget skal du tænke på tilgængelighed i Azure, er det vigtigt at forstå begrebet effektive tilgængeligheden af platformen. Effektive tilgængelighed finder Service niveau aftaler (SLA) af hver afhængige tjeneste, og deres akkumuleret påvirkning af samlede system er ledige.

Systemets tilgængelighed er mål for procentdelen af en tidsramme, systemet vil kunne fungere. For eksempel er ledige SLA af mindst to forekomster af en web eller arbejder rolle i Azure 99.95 procent (ikke til 100 procent). Det ikke mål for ydeevnen eller funktionaliteten i de tjenester, der kører på disse roller. Effektive tilgængeligheden af skybaseret tjeneste er dog også påvirkes af de forskellige SLA af de andre afhængige tjenester. De mere dynamiske dele i systemet, på flere omtanke, du skal udføre for at sikre, at programmet kan resiliently opfylder kravene om tilgængelighed i dets slutbrugere.

Overvej følgende SLA for en Azure-tjeneste, der bruger Azure tjenester: Beregn, Azure SQL-Database og Azure-lager.

|Azure service|SLA   |Potentielle minutter nedetid/måned (30 dage)|
|:------------|:-----|:----------------------------------------:|
|Beregne      |99.95%|21,6 minutter                              |
|SQL-Database |99,99%|4,3 minutter                              |
|Lagerplads      |99.90%|43.2 minutter                              |

Du skal planlægge for alle tjenester til potentielt gå på forskellige tidspunkter. I dette forenklet eksempel er det samlede antal minutter om måneden, programmet kan være nede 108 minutter. En 30-dages måned har en total på 43,200 minutter. 108 minutter er.25 procent af det samlede antal minutter i en 30-dages måned (43,200 minutter). Det giver dig en effektiv tilgængeligheden af 99.75 procent for skytjenesten.

Brug af tilgængelighed teknikker er beskrevet i dette dokument kan dog forbedre dette. Eksempelvis hvis du designer dit program tilladelse til at fortsætte med at køre, når den SQL-Database er tilgængelig, kan du fjerne, på ligningen. Det kan betyde, at programmet kører med reducerede funktioner, så der er også skal du tænke på virksomhedens behov. Du kan finde en komplet liste over Azure SLA [Serviceaftaler niveau](https://azure.microsoft.com/support/legal/sla/).

###<a name="scalability"></a>Skalerbarhed

Skalerbarhed påvirker direkte tilgængelighed. Et program, der opstår fejl under øgede belastning er ikke længere tilgængelig. SVG-programmer kan opfylder øget efterspørgsel med ensartet resultaterne i acceptable tid windows. Når et system er SVG, det skaleres vandret eller lodret til at administrere øges i belastning og samtidig bevare ensartet ydeevne. Grundlæggende for tilføjer vandret skalering flere computere i samme størrelse (processor, hukommelse og båndbredde), mens lodret skalering øges størrelsen på de eksisterende computere. Azure har du lodret skaleringsindstillinger for at vælge forskellige maskine størrelser for Beregn. Men ændring af størrelsen på computer kræver en ny installation. Derfor er de mest fleksible løsninger udviklet til vandret skalering. Dette er især sande Beregn, fordi du nemt kan øge antallet af aktive forekomster af enhver web eller arbejder rolle. Disse yderligere forekomster håndtere øget trafik via Azure Web-portalen, PowerShell-scripts, eller kode. Basere denne beslutning på øges i bestemte overvåget målepunkter. I dette scenarie skal ydeevne eller målepunkter ikke lide en chatanmodning slip belastning. Typisk store rollerne web og arbejder enhver stat eksternt. Dette giver mulighed for fleksible belastning og korrekt håndtering af ændringerne i forekomst tæller. Vandret skalering fungerer også godt med tjenester som Azure-lager, som ikke understøtter lagdelte indstillinger for lodret skalering.

Skyen installationer skal betragtes som en samling af skala-enheder. Dette betyder, at være elastiske betjene slutbrugere overførselshastighed kundernes behov. Skala enhederne er nemmere at visualisere på serverniveau web og programmet. Dette skyldes, at Azure allerede indeholder uden status Beregn noder via internettet og arbejder roller. Tilføje mere beregne skala enheder til installationen, bliver programmet tilstand management side effekter, fordi Beregn skala enheder er uden tilstand. En lagerplads-enhed for tidsskala er ansvarlig for at administrere en partition data (strukturerede eller ustrukturerede). Eksempler på lagerplads skala enheder omfatter Azure-tabel partition, Azure Blob objektbeholder og Azure SQL-Database. Selv brugen af flere Azure-lager-konti har en direkte indvirkning på programmet skalerbarhed. Du skal oprette en meget SVG skybaseret tjeneste til at inkorporere flere lagerplads skala enheder. Eksempelvis hvis et program anvender relationelle data, dele data på tværs af flere SQL-databaser. Gør dette, kan den lagerplads til at holde med elastiske Beregn enhed for tidsskala modellen. Azure-lager kan på samme måde, data partitionering farveskemaer, der kræver bevidst design til at imødekomme overførselshastighed af Beregn lag. Se [Bedste fremgangsmåder til Design af stor skala tjenesterne på Azure Cloud Services](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/)for en liste over bedste fremgangsmåder til design af SVG-skytjenester.

###<a name="fault-tolerance"></a>Fejltolerance

Programmer skal forudsætter, at alle afhængige skyen egenskab kan og går på et tidspunkt i gang. En fejl tolerant programmet registrerer, og træk omkring mislykkedes elementer, du vil fortsætte og returnere de korrekte resultater inden for en bestemt tidsramme. Midlertidige fejlbetingelser anvender et fejl tolerant system en politik for prøv igen. Mere alvorlige fejl, kan programmet registrere problemer og skifte over til alternative hardware eller beredskabsplaner, indtil fejlen er rettet. En pålidelig programmet kan korrekt administrere fejl i en eller flere dele, og fortsætte med at fungere korrekt. Fejl tolerant programmer kan bruge en eller flere design strategier, som redundans, gentagelse eller nedgraderede funktionalitet.

##<a name="disaster-recovery"></a>Nedbrud

En skyen installation kan ikke længere fungerer på grund af en systematiske afbrydelse af de afhængige tjenester eller den underliggende struktur. Under disse omstændigheder udløser en løbende forretningsplan nedbrud gendannelsen. Denne proces involverer typisk både handlinger personale og automatiseret procedurer for at genaktivere programmet i et område, der er tilgængelige. Dette kræver overførsel af brugere af programmet, data og tjenester til det nye område. Det indebærer også brugen af medier med sikkerhedskopier eller igangværende gentagelse.

Overvej at den forrige anvendelse, sammenlignes høj tilgængelighed til muligheden for at gendanne fra en flad dæk ved hjælp af en ekstra. I modsætning omfatter nedbrud de trin, der er foretaget efter en bil går ned, hvor bil er ikke længere er funktionsdygtige. Det er tilfældet, er den bedste løsning at finde en effektiv måde at ændre biler, ved at ringe til en rejse tjeneste eller en ven. I dette scenarie, er der sandsynligvis skal være en længere forsinkelsen i åbningen tilbage på farten. Der findes også flere kompleksitet i reparation og vende tilbage til den oprindelige biltype. På samme måde er nedbrud til en anden region en kompleks opgave, der involverer typisk nogle nedetid og potentielle tab af data. Hvis du vil bedre forstå og evaluerer nedbrud gendannelse strategier, er det vigtigt at definere to betingelser: gendannelse tid mål (RTO) og gendannelsesfiler Peg mål (frigivne Produktionsordre).

###<a name="recovery-time-objective"></a>Gendannelse tid mål

RTO er den maksimale mængde tid, der er allokeret for at gendanne programmets funktionalitet. Dette er baseret på virksomhedens behov, og den er relateret til vigtigheden af programmet. Virksomhedens vigtige programmer kræver en lav RTO.

###<a name="recovery-point-objective"></a>Gendannelse punkt mål

Den frigivne Produktionsordre er en acceptabel tidsramme tabt data på grund af gendannelsen. For eksempel, hvis den frigivne Produktionsordre er en time, skal du helt sikkerhedskopiere eller gentage dataene mindst hver time. Når du flytter programmet på et andet område, kan de sikkerhedskopierede data mangle op til en time af data. Sådan RTO målrette kritiske programmer en meget mindre frigivne Produktionsordre.

##<a name="checklist"></a>Tjekliste

Lad os opsummere de vigtigste punkter, som er blevet behandlet i denne artikel (og dens relaterede artikler for [høj tilgængelighed](./resiliency-high-availability-azure-applications.md) og [nedbrud](./resiliency-disaster-recovery-azure-applications.md) til Azure-programmer). Denne oversigt skal fungere som en tjekliste over elementer, du bør overveje for dine egne tilgængelighed og planlægge gendannelse efter nedbrud. Dette er bedste fremgangsmåder, der er nyttige til kunder, der ønsker at få alvorlige om implementering af en vellykket løsning.

1. Udføre en risikovurdering for hvert program, da hver kan have forskellige krav. Nogle programmer er flere kritiske end andre og gerne vil justere de ekstra omkostninger for at udvikle dem til nedbrud.
1. Brug disse oplysninger til at definere RTO og frigivne Produktionsordre for hvert program.
1. Design giver fejl, startende med programmet arkitekturen.
1. Implementere bedste praksis for høj tilgængelighed, mens du justering af omkostninger, kompleksitet og risiko.
1. Implementere nedbrud gendannelse planer og processer.
  * Overvej fejl, der strækker sig over niveauet modul helt til en komplet skyen afbrydelse.
  * Oprette ekstra strategier for alle reference og transaktions data.
  * Vælg en med flere websted nedbrud gendannelse arkitektur.
1. Definere en bestemt ejer for nedbrud gendannelsesprocesser, automatisering og test. Ejeren skal administrere og ejer hele processen.
1. Dokumentere processer, så de nemt kan gentages. Selvom der er én ejer, skal flere personer kunne forstå og følge processerne i nødstilfælde.
1. Uddannelse af personale at implementere processen.
1. Du kan bruge almindelige nedbrud simulering for både undervisning og validering af processen.

##<a name="summary"></a>Oversigt

Når hardware eller programmer ikke i Azure, er teknikker og strategier til administration af dem anderledes end når der opstår fejl på lokale systemer. Den vigtigste årsag til dette er, at cloud-løsninger, der typisk flere afhængigheder på infrastruktur, der er fordelt over en Azure område og administrerede som separate tjenester. Du skal håndtere delvis fejl ved hjælp af høj tilgængelighed teknikker. Brug nedbrud gendannelse strategier for at administrere mere alvorlige fejl, sandsynligvis på grund af en nedbrud begivenhed.

Azure registreres og håndteres mislykkede forsøg på mange, men der er mange typer af fejl, der kræver program-specifikke strategier. Du skal aktivt forberede og administrere fejl for programmer, tjenester og data.

Overvej business konsekvenserne af programmets fejl, når du opretter dit program tilgængelighed og genoprettelse efter genoprettelse plan. Definere processer, politikker og procedurer til at gendanne vigtige systemer, når en katastrofal hændelse tager tid, planlægning og anvendelsen. Og når du opretter planerne, du kan ikke standse der. Du skal regelmæssigt analysere, teste og hele tiden at forbedre de planer, der er baseret på projektoversigten programmet, forretningsmæssige behov og de teknologier, der er tilgængelige for dig. Azure indeholder nye funktioner og opretter nye udfordringer til at oprette robuste programmer, der klare mislykkede forsøg.

##<a name="additional-resources"></a>Yderligere ressourcer

[Høj tilgængelighed for bygget på Microsoft Azure-programmer](resiliency-high-availability-azure-applications.md)

[Nedbrud for bygget på Microsoft Azure-programmer](resiliency-disaster-recovery-azure-applications.md)

[Azure fleksibilitet teknisk vejledning](resiliency-technical-guidance.md)

[Oversigt: Skyen business løbende og database nedbrud med SQL-Database](../sql-database/sql-database-business-continuity.md)

[Høj tilgængelighed og genoprettelse efter nedbrud til SQL Server på virtuelle Azure-computere](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)

[Fejlsikret: Vejledning til tolerant skyen arkitekturer](https://channel9.msdn.com/Series/FailSafe)

[Bedste fremgangsmåder til design af store tjenester på Azure Cloud Services](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/)

##<a name="next-steps"></a>Næste trin

I denne artikel er en del af en række artikler fokuseret på nedbrud og høj tilgængelighed til Azure-programmer. Næste artikel i denne serie er [høj tilgængelighed for bygget på Microsoft Azure-programmer](resiliency-high-availability-azure-applications.md).
