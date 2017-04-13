<properties
    pageTitle="Hvordan du konfigurerer Business ordliste for omfattet mærkning | Microsoft Azure"
    description="Vejledning til artikel fremhævning business ordliste i Azure datakatalog for at definere og bruge en fælles ordliste business mærke registreret dataaktiver."
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
    ms.date="09/21/2016"
    ms.author="maroche"/>

# <a name="how-to-set-up-the-business-glossary-for-governed-tagging"></a>Hvordan du konfigurerer Business Ordliste til omfattet mærkning

## <a name="introduction"></a>Introduktion

Azure datakatalog giver dig mulighed for kilde dataregistrering, så brugerne kan finde og forstå de datakilder, de skal udføre analyser og træffe beslutninger. Disse funktioner registrering gør den største virkning, når brugere kan finde og forstå den størst mulige antal tilgængelige datakilder.

Et datakatalog funktion, som gør reklamerer for større forståelse af aktiver data mærkning. Mærkning gør det muligt for brugerne at knytte nøgleord til et aktiv eller en kolonne, hvilket også gør det nemmere at finde aktiv via søgning, og gør det muligt for brugere at nemmere at forstå konteksten og indholdet af aktivet.

Dog medføre mærkning nogle gange kan problemer sin egen. Nogle eksempler på problemer, der kan være introduceret ved mærkning er:

1.  Brug af forkortelser på nogle aktiver og udvidet tekst på andre mens mærkning-brugere. Denne uoverensstemmelse hindrer registrering af aktiver, selvom formålet var at mærke aktiverne med det samme mærke.
2.  Mærker, som giver forskellige ting i forskellige kontekster. For eksempel en såkaldt "Omsætning" på datasættet kunde betyde omsætning efter kunde, men det samme mærke på en kvartalsvis salgs datasættet kunne betyder Kvartalsvis omsætning for virksomheden.  

For at kunne løse disse og andre lignende udfordringer, indeholder datakatalog en Business-ordliste.

Data katalog Business ordliste kan organisationer til dokument vigtige business terms samt deres definitioner til at oprette en fælles ordliste business. Denne styring aktiverer konsistens i brug af data på tværs af organisationen. Når vilkår er defineret i business ordliste, de kan tildeles til dataaktiver i kataloget, ved hjælp af den samme fremgangsmåde som mærkning, og aktivere _omfattet mærkning_.

> [AZURE.NOTE] De funktioner, der er beskrevet i denne artikel er kun tilgængelig i Standard Edition af Azure datakatalog. Den gratis version giver ikke mulighed for regulerede mærkning eller en business-ordliste.

## <a name="glossary-availability-and-privileges"></a>Ordliste tilgængelighed og tilladelser

*Business-ordliste er tilgængelig i Standard Edition af Azure-Datakataloget. Gratis udgave af datakatalog inkluderer ikke en ordliste.*

Kan få adgang til business-ordliste via indstillingen "Ordliste" i portalen datakatalog navigationsmenu.  

![Få adgang til business-ordliste](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)


Katalog dataadministratorer og medlemmer af rollen ordliste administratorer kan oprette, redigere og slette ordliste i business ordliste. Alle datakatalog brugere kan se ordet definitioner, og kan mærke Aktiver med ordliste.

![Tilføje et nyt ordliste udtryk](./media/data-catalog-how-to-business-glossary/02-new-term.png)


## <a name="creating-glossary-terms"></a>Oprette ordliste

Katalog dataadministratorer og ordliste administratorer kan oprette nye ordliste udtryk ved at klikke på det nye udtryk ' knappen for at oprette ordliste med følgende felter:

* En business definition for ordet
* En beskrivelse, som henter den tilsigtede brug eller forretningsregler til aktiv/kolonne
* En liste over interessenter, der kender mest om ordet
* Det overordnede ord, der definerer det hierarki, hvori ordet er organiseret


## <a name="glossary-term-hierarchies"></a>Ordliste ord hierarkier

Datakatalog business ordliste giver mulighed for at beskrive dit business ordliste som et hierarki af betingelserne. Dette giver mulighed for organisationer til at oprette en klassifikation af udtryk som bedre repræsenterer deres business taksonomi.

Navnet på et udtryk, der skal være entydige på et bestemt niveau i hierarkiet - dublerede navne er ikke tilladt. Der er ingen grænse for antallet af niveauer i et hierarki, men et hierarki er ofte nemmere at forstå når der er tre niveauer eller færre.

Brug af hierarkier i business ordliste er valgfrit. At forlade overordnet ord feltet være tomt for ordliste opretter en flad (ikke-hierarkisk) liste over ord i ordlisten.  

## <a name="tagging-assets-with-glossary-terms"></a>Mærkning af aktiver med ordliste

Når ordliste, der er defineret i kataloget, er opleve mærkning Aktiver optimeret til at søge på ordliste, som brugeren skriver deres mærke. Portalen datakatalog viser en liste over tilsvarende ordliste for brugeren at vælge mellem. Hvis brugeren vælger et ordliste ord på listen, føjes til aktivet som et mærke (kaldes også Ordliste mærke). Brugeren kan også vælge at oprette et nyt mærke ved at skrive et udtryk, ikke der er i ordlisten (kaldes også brugermærke).

![Data aktiv mærket med én brugermærke- og to ordliste mærker](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [AZURE.NOTE] Bruger mærker er den eneste type tag, der understøttes i gratis udgave af datakatalog.

### <a name="hover-behavior-on-tags"></a>Hold markøren funktionsmåde efter mærker
På portalen datakatalog er de to typer af mærker visuelt distinct med forskellige pegefølsomme funktionsmåder. Når brugeren holdes over et tag, som brugeren kan de se mærketeksten og den bruger eller brugere, der har tilføjet mærket. Når brugeren holdes over mærket ordliste, se de også definitionen af ordliste ordet og et link til at åbne business ordliste for at få vist det fulde definition af udtrykket.

### <a name="search-filters-for-tags"></a>Søgefiltre efter mærker
Både ordliste mærker og de mærker, bruger kan søges i, og kan anvendes som filtre i en søgning.

## <a name="summary"></a>Oversigt
Business ordliste i Azure datakatalog og den regulerede mærkning gør det muligt, Tillad data anlægsaktiver, der skal identificeres, administreres og har opdaget på en ensartet måde. Business-ordliste kan forvandle læring af business ordliste blandt brugere af en organisation og understøtter sigende metadata til at blive hentet, foretage aktiv registrering og forstå lynhurtigt.

## <a name="see-also"></a>Se også

- [REST-API dokumentation for business-ordliste handlinger](https://msdn.microsoft.com/library/mt708855.aspx)
