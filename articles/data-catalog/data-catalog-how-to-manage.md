<properties
   pageTitle="Sådan administreres Dataaktiver | Microsoft Azure"
   description="Vejledning til artikel fremhævning af hvordan du styrer synlighed og ejerskabet af dataaktiver registreret i Azure datakatalog."
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


# <a name="how-to-manage-data-assets"></a>Sådan administreres over dataaktiver

## <a name="introduction"></a>Introduktion

**Azure datakatalog** giver dig mulighed for kilde dataregistrering, så brugerne kan finde og forstå de datakilder, de skal udføre analyser og træffe beslutninger. Disse funktioner registrering gør den største virkning, når alle brugere kan finde og forstå den størst mulige antal tilgængelige datakilder. Med dette huske standardfunktionsmåden for datakatalog gælder for alle registrerede datakilder skal være synlige for – og så det bliver nemmere ved – alle katalogfletning brugere.

Datakatalog giver ikke brugere adgang til selve dataene. Dataadgang styres af ejeren af datakilden. Datakatalog gør det muligt for brugerne at finde datakilder og til at få vist de metadata, der er relateret til de kilder, der er registreret i kataloget.

Der kan være situationer, men hvor datakilder skal kun være synlige til bestemte brugere eller til medlemmer af bestemte grupper. Datakatalog gør det muligt for brugerne at overtage ejerskabet af registrerede dataaktiver i kataloget for disse scenarier, og at styre, derefter synligheden af aktiverne, de ejer.

> [AZURE.NOTE] De funktioner, der er beskrevet i denne artikel er kun tilgængelig i Standard Edition af Azure datakatalog. Gratis Edition giver ikke mulighed for ejerskab og begrænse data aktiv synlighed.

## <a name="managing-ownership-of-data-assets"></a>Administrere ejerskabet af over dataaktiver
Som standard er dataaktiver, der er registreret i datakatalog ukendt; alle brugere med tilladelse til at få adgang til kataloget kan opdage og anmærke disse aktiver. Brugere kan overtage ejerskabet af ukendt dataaktiver og derefter kan begrænse synligheden af de aktiver, de ejer.

Når en data aktiv i datakatalog ejes, kun brugere, der er godkendt af ejerne kan opdage aktivet og få vist dets metadata, og kun ejere kan slette aktivet fra kataloget.

> [AZURE.NOTE] Ejerskab i datakatalog påvirker kun de metadata, der er gemt i kataloget. Det giver ikke eventuelle tilladelser til den underliggende datakilde.

### <a name="taking-ownership"></a>Overtage ejerskabet
Brugere kan overtage ejerskabet af dataaktiver ved at vælge indstillingen "Tage ejerskab" i portalen datakatalog. Ingen særlige tilladelser der kræves for at overtage ejerskabet af et ukendt data aktiv. alle brugere kan overtage ejerskabet af et ukendt data aktiv.

### <a name="adding-owners-and-co-owners"></a>Tilføje ejere og samtidig ejere
Hvis et data aktiv ejes allerede, brugere kan ikke blot overtage ejerskabet – de skal føjes som samtidig ejere af en eksisterende ejer. En hvilken som helst ejer kan tilføje flere brugere eller sikkerhedsgrupper som samtidig ejere.

> [AZURE.NOTE] Det er en bedste fremgangsmåde at have mindst to personer som ejere for en hvilken som helst ejet data aktiv.

### <a name="removing-owners"></a>Fjerne ejere
Ligesom en hvilken som helst aktiv ejer kan tilføje samtidig ejere, kan en hvilken som helst aktiv ejer fjerne eventuelle medejer.

Hvis en aktiv ejer fjerner sig selv som en ejer, kan han ikke længere administrere aktivet. Hvis en aktiv ejer fjerner sig selv som en ejer, og der er ingen andre samtidig ejere, gendannes aktivet til en ukendt tilstand.

## <a name="visibility"></a>Synlighed
Data aktiv ejere styrer synligheden af de dataaktiver de ejer. Hvis du vil begrænse synlighed fra standard – kan hvor alle datakatalog brugere kan opdage og få vist data aktiv – ejeren af aktiv skifte synlighedsindstillingen fra "Alle" til "Ejere og disse brugere" i egenskaberne for aktivet. Ejere kan derefter tilføje bestemte brugere og -sikkerhedsgrupper.

> [AZURE.NOTE] Når det er muligt, skal være tildelt aktiv ejerskab og synlighed tilladelser til sikkerhedsgrupper og ikke til individuelle brugere.

## <a name="catalog-administrators"></a>Administratorer for en katalogside
Katalog dataadministratorer er implicit samtidig ejere af alle aktiver i kataloget. Aktiv ejere kan ikke fjerne synlighed fra katalog administratorer og administratorer kan administrere ejerskab og synlighed for alle dataaktiver i kataloget.

## <a name="summary"></a>Oversigt
-Datakataloget crowdsourcing model til søgning efter metadata og data gør det muligt for alle brugere i kataloget til at bidrage til at finde. Standard Edition af datakatalog giver dig mulighed for ejerskab og administration til at begrænse synlighed og anvendelse af bestemte dataaktiver.
