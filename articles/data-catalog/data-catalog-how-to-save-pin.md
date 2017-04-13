<properties
   pageTitle="Sådan gemmes søgninger og Fastgør dataaktiver | Microsoft Azure"
   description="Vejledning til artikel fremhævning-funktioner i Azure datakatalog til lagring af datakilder og dataaktiver til senere brug."
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
   ms.date="10/10/2016"
   ms.author="maroche"/>

# <a name="how-to-save-searches-and-pin-data-assets"></a>Sådan gemmes søgninger og Fastgør over dataaktiver

## <a name="introduction"></a>Introduktion

Microsoft Azure datakatalog indeholder funktioner til dataregistrering kilde. Brugere kan hurtigt søge og filtrere kataloget for at finde datakilder og forstå formålet, hvilket gør det nemmere at finde de rigtige data til jobbet ved hånden.

Men hvad med, når brugere skal kunne vant til at arbejde med de samme data? Hvad med når brugere regelmæssigt bidrage deres kendskab til de samme datakilder i kataloget? I disse situationer skulle gentagne gange problem med de samme søgninger kan være ineffektiv – dette er hvor gemt søgning og fastgjort data Aktiver kan hjælpe.

## <a name="saved-searches"></a>Gemte søgninger

En gemt søgning i Azure datakatalog er en der kan genbruges, hver bruger Søg definition. Når en bruger har defineret en søgning – inklusive søgeord, mærker og andre filtre – kan hun gemme den til senere brug. Gemt søgning definitionen kan derefter køres igen på et senere tidspunkt, til at returnere alle dataaktiver, der stemmer overens med dens søgekriterier.

### <a name="creating-a-saved-search"></a>Oprette en gemt søgning

Hvis du vil oprette en gemt søgning, skal du først angive søgekriterierne, der kan genbruges. Klik derefter på linket "Gem" i "aktuelle" søgefeltet i portalen Azure datakatalog.

 ![Vælg 'Gem' for at gemme de aktuelle søgeindstillinger](./media/data-catalog-how-to-save-pin/01-save-option.png)

Når du bliver bedt om det, kan du angive et navn til den gemte søgning. Vælg et navn, der giver mening og beskriver de dataaktiver, der returneres af søgningen.

 ![Angiv et navn til den gemte søgning](./media/data-catalog-how-to-save-pin/02-name.png)

### <a name="managing-saved-searches"></a>Administrere gemte søgninger

Når en bruger har gemt en eller flere søgninger, vises en indstilling for "Gemt søgninger" i portalen Azure datakatalog under "aktuelle" søgefeltet. Når det er udvidet, vises den komplette liste over gemmer søgninger.

 ![Liste over gemte søgninger](./media/data-catalog-how-to-save-pin/03-list.png)

Vælge en gemt søgning på listen medfører, at feltet Søg til at blive udført.

Vælge på rullelisten giver en række muligheder:

 ![Indstillinger for administration af gemte søgninger](./media/data-catalog-how-to-save-pin/04-managing.png)

Valg af "Omdøbe" spørger brugeren skal angive et nyt navn til den gemte søgning. Søg definitionen ændres ikke.

Valg af "Slet" spørger brugeren om en bekræftelse, og derefter fjerner den gemte søgning fra brugerens liste over.

Hvis du vælger "Gem som standard" bliver markeret i den valgte gemt søgning som standardsøgningen til brugeren. Hvis brugeren udfører en "tom" søgning fra Azure datakatalog hjemmeside, udføres brugerens standard søgning. Desuden søgningen er markeret som standard vises øverst på listen gemte søgning.

### <a name="organizational-saved-searches"></a>Organisatoriske gemte søgninger

Alle brugere kan gemme søger efter deres egen brug. Katalog dataadministratorer kan også gemme søgninger for alle brugere i organisationen. Når du gemmer en søgning, vises administratorer mulighed for at dele den gemte søgning i virksomheden. Hvis denne indstilling er markeret, medtages den gemte søgning i listen over tilgængelige søgninger for alle brugere.

 ![Organisatoriske gemte søgninger](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)


## <a name="pinned-data-assets"></a>Fastgjort over dataaktiver

Gemte søgninger Tillad brugere at gemme og genbruge definitioner af søgning de dataaktiver, der returneres af søgninger måske ændres med tiden som indholdet af kataloget ændringen. Fastgør dataaktiver giver brugerne mulighed at identificere eksplicit bestemte dataaktiver for at gøre dem nemmere at få adgang til uden at skulle bruge en søgning.

Det er nemt at fastgøre et data aktiv – brugere kan blot klikke på ikonet "Fastgør" at føje det til sin liste over fastgjorte data aktivet. Dette ikon vises i hjørnet af feltet aktiv i visningen felt, og i kolonnen længst til venstre i listevisningen i portalen Azure datakatalog.

![Fastgør en data aktiv](./media/data-catalog-how-to-save-pin/05-pinning.png)

Det er lige så nemt at unpinning et aktiv – brugere skal du blot klikke på "Fastgør"-ikonet igen for at slå indstillingen for det valgte aktiv.

![Unpinning et data aktiv](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="my-assets"></a>"Mine Aktiver"
Azure datakatalog portalwebstedets startside indeholder en "Mine Aktiver" sektion, der viser aktiver af interesse for den aktuelle bruger. Dette afsnit indeholder begge fastgjorte aktiver og gemte søgninger.

!['Min Aktiver' på startsiden](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Oversigt
Azure datakatalog indeholder funktioner, som gør det nemmere for brugerne at finde de datakilder, de skal bruge, så de kan bruge mindre tid, der søger efter data og mere tid, der arbejder med den. Gemte søgninger og fastgjort data Aktiver udbygges med disse kernefunktioner, så brugerne kan nemt identificere datakilder, som de fungerer flere gange.
