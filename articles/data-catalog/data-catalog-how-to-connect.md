<properties
   pageTitle="Sådan oprettes forbindelse til datakilder | Microsoft Azure"
   description="Vejledning til artikel fremhævning af hvordan du opretter forbindelse til datakilder, der har opdaget med Azure datakatalog."
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
   ms.date="09/15/2016"
   ms.author="maroche"/>


# <a name="how-to-connect-to-data-sources"></a>Sådan oprettes forbindelse til datakilder

## <a name="introduction"></a>Introduktion
**Microsoft Azure datakatalog** er en fuldt administreret skybaseret tjeneste, der fungerer som et system af registrering og system af registrering til enterprise-datakilder. Med andre ord handler **Azure datakatalog** om hjælpe personer opdage, forstå og bruge datakilder, og hjælp organisationer til at få flere værdi fra deres eksisterende data. Det vigtigste aspekt af dette scenario ved hjælp af dataene – når en bruger opdager en datakilde og forstår sit formål, det næste trin er at oprette forbindelse til datakilden for at placere data for at bruge.

## <a name="data-source-locations"></a>Datakilde placeringer
Under registrering af kilde for data modtager **Azure datakatalog** metadata om datakilden. Disse metadata indeholder oplysninger om datakildens placering. Oplysninger om placeringen, varierer fra datakilde til datakilde, men den vil altid indeholde de oplysninger, der er behov for at oprette forbindelse. For eksempel omfatter en placering til en SQL Server-tabel servernavn, databasenavnet, skemanavn og tabelnavn, mens en placering til en SQL Server Reporting Services-rapport indeholder navnet på serveren og stien til rapporten. Andre datakildetyper har placeringer, der afspejler strukturen og -funktioner til kilde-systemet.

## <a name="integrated-client-tools"></a>Integreret klientværktøjer
Den nemmeste måde at oprette forbindelse til en datakilde er at bruge den "åben i..." menuen i portalen **Azure datakatalog** . Denne menu viser en liste over indstillinger til at oprette forbindelse til den markerede data aktiv.
Når du bruger feltet standardvisningen, findes i denne menu på hvert felt.

 ![Åbne en SQL Server-tabel i Excel fra feltet data aktiv](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Når du bruger listevisningen, findes i menuen i søgepanelet øverst i vinduet portalen.

 ![Åbne en SQL Server Reporting Services-rapport i Rapportstyring fra søgepanelet](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Understøttede klientprogrammer
Når du bruger den "åben i..." menu til datakilder i portalen Azure datakatalog korrekte klientprogrammet skal være installeret på klientcomputeren.

| Åbn i programmet på computeren | Arkivere lokalnummer / protokol | Understøttede programmet versioner |
| --- | --- | --- |
| Excel | .odc | Excel 2010 eller nyere |
| Excel (øverst 1000) | .odc | Excel 2010 eller nyere |
| Power-forespørgsel | .xlsx | Excel 2016 eller Excel 2010 eller Excel 2013 med Power-forespørgsel til Excel-tilføjelsesprogrammet installeret
| Power BI Desktop | .pbix | Power BI Desktop juli 2016 eller nyere |
| SQL Server Data Tools | vsweb: / / | Visual Studio 2013 opdatering 4 eller nyere med SQL Server-værktøj, der er installeret |
| Rapportstyring | http:// | Se [browserkrav til SQL Server Reporting Services](https://technet.microsoft.com/en-us/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Dine data, er din værktøjer
De tilgængelige indstillinger i menuen, afhænger af typen data aktiv i øjeblikket er valgt. Naturligvis ikke alle mulige værktøjer der skal medtages i den "åben i..." menuen, men det er stadig nemt at oprette forbindelse til datakilden ved hjælp af en hvilken som helst client værktøj. Når en aktiv data er markeret i portalen **Azure datakatalog** , vises den fulde placering i egenskabsruden.

 ![Oplysninger om forbindelsen til en SQL Server-tabel](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Oplysninger forbindelsesoplysningerne vil være forskellige fra datakildetype datakildetype, men oplysningerne i portalen kan give dig alt, hvad du har brug for at oprette forbindelse til datakilden i en hvilken som helst client værktøj. Brugere kan kopiere oplysningerne om forbindelse til de datakilder, de har opdaget med **Azure datakatalog**, gør det muligt at arbejde med data i deres værktøjet valg.

## <a name="connecting-and-data-source-permissions"></a>Tilslutning og data kilde tilladelser
Selvom **Azure datakatalog** opretter datakilder, der er synlig, forbliver adgang til selve dataene styres af datakilde ejeren eller administratoren. Finde en datakilde i **Azure datakatalog** giver ikke en bruger en hvilken som helst tilladelser til at få adgang til datakilden selve.

Hvis det nemmere for brugere, der opleve en datakilde, men har ikke tilladelse til at få adgang til dataene, kan brugerne angive oplysninger i egenskaben anmode om adgang, når anmærke en datakilde. Oplysninger, der er angivet her – herunder links til processen eller kontaktsted for at få adgang til datakilder – vises sammen med placering oplysninger om datakilden i portalen.

 ![Forbindelsesoplysningerne med anmodning om adgang vejledningen](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

##<a name="summary"></a>Oversigt
Registrering af en datakilde med **Azure datakatalog** gør dataene så det bliver nemmere ved at kopiere strukturelle og beskrivende metadata fra datakilden til tjenesten katalog. Når en datakilde har registreret, og har opdaget, brugere kan oprette forbindelse til datakilden fra portalen **Azure datakatalog** "åben i..." " menuen eller ved hjælp af deres Dataværktøjer efter valg.

## <a name="see-also"></a>Se også
- [Introduktion til Azure datakatalog](data-catalog-get-started.md) selvstudium trinvise få mere at vide om, hvordan du opretter forbindelse til datakilder.
