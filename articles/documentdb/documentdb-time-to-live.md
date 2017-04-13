<properties
   pageTitle="Udløbe data i DocumentDB med tid til live | Microsoft Azure"
   description="Med TTL giver Microsoft Azure DocumentDB mulighed for at få dokumenter automatisk fjernet fra systemet efter et bestemt tidsrum."
   services="documentdb"
   documentationCenter=""
   keywords="tid til live"
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2016"
   ms.author="kipandya"/>

# <a name="expire-data-in-documentdb-collections-automatically-with-time-to-live"></a>Udløbe data i DocumentDB samlinger automatisk med tid til live

Programmer kan oprette og gemme store datamængder. Nogle af disse data, som maskine genereres begivenhed data, logfiler og bruger session oplysninger bruges kun i en begrænset periode. Når dataene bliver overskydende til behovet i programmet det er sikkert at fjerne denne data og reducere lagerplads behovene i et program.

"Time to live" eller TTL, giver Microsoft Azure DocumentDB mulighed for at få dokumenter automatisk fjernet fra databasen efter et bestemt tidsrum. Standard tid til live kan angive for websteder og tilsidesættes på grundlag dokument. Når TTL er angivet, som standard af websteder eller i et dokumentniveau, fjernes DocumentDB automatisk dokumenter, der findes efter perioden, i sekunder, da de sidst blev ændret.

Tid til live i DocumentDB bruger en forskydning mod, når dokumentet sidst blev ændret. Hvis du vil gøre dette bruges feltet _ts som findes på alle dokumenter. Feltet _ts er et unix-typografi epoke tidsstempel, der repræsenterer den dato og klokkeslæt. Feltet _ts opdateres, hver gang et dokument er ændret. 

## <a name="ttl-behavior"></a>TTL funktionsmåde

Funktionen TTL styres af TTL egenskaber på to niveauer - niveauet af websteder og niveauet for dokumentet. Værdierne, der er angivet i sekunder og behandles som en delta fra den _ts, dokumentet sidst blev ændret på.

 1.  DefaultTTL til samlingen
  * Hvis mangler (eller angivet til null), dokumenter slettes ikke automatisk.
  
  * Hvis Præsenter og værdien er "-1" = uendelig – dokumenter ikke udløber som standard
  
  * Hvis Præsenter og værdien, der er nogle tal ("n") – dokumenter udløbe sekunder "n" efter seneste ændring

 2.  TTL for dokumenterne: 
  * Egenskaben er gælder kun, hvis DefaultTTL findes til den overordnede websteder.
  
  * Tilsidesætter DefaultTTL værdien for den overordnede websteder.

Når dokumentet er udløbet (ttl + _ts > = server øjeblikket), dokumentet er markeret som "udløbet". Ingen handling skal have på disse dokumenter efter denne gang, og de skal udelukkes fra resultaterne af en hvilken som helst forespørgsler udføres. Dokumenterne, der slettes fysisk i systemet, og slettes i baggrunden efter behov på et senere tidspunkt. Dette forbruger ikke en hvilken som helst [Anmode om enheder (RUs)](documentdb-request-units.md) fra samling budgettet.

Ovenstående logik kan blive vist i følgende matrix:

|       | DefaultTTL indstille mangler/ikke for gruppen af websteder | DefaultTTL = -1 på af websteder | DefaultTTL = "n" i af websteder|
| ------------- |:-------------|:-------------|:-------------|
| TTL mangler i dokument| Intet at tilsidesætte på dokumentniveau, da dokumentet og af websteder har ingen begrebet TTL. | Ingen dokumenter i denne samling udløber. | Dokumenter i denne samling udløber, når interval n går. |
| TTL = -1 i dokument | Intet at tilsidesætte på niveauet for dokument, da samlingen ikke definere egenskaben DefaultTTL, som kan tilsidesætte et dokument. TTL på et dokument er ophævelse fortolket af systemet. | Ingen dokumenter i denne samling udløber. | Dokument med TTL =-1 i denne samling udløber aldrig. Alle andre dokumenter udløber efter "n" interval. |
|  TTL = n i dokument | Intet at tilsidesætte på niveauet for dokumentet. TTL på et dokument i ophævelse fortolket af systemet. | Dokument med TTL = n udløber efter interval n, i sekunder. Andre dokumenter kan nedarve interval-1 og aldrig at udløbe. | Dokument med TTL = n udløber efter interval n, i sekunder. Andre dokumenter arver "n" interval fra samlingen. |


## <a name="configuring-ttl"></a>Konfiguration af TTL

Tid til live er som standard deaktiveret som standard i alle DocumentDB af websteder og på alle dokumenter.

## <a name="enabling-ttl"></a>Aktivere TTL

Hvis du vil aktivere TTL på en af websteder eller dokumenter i en samling, skal du angive egenskaben DefaultTTL af en samling til -1 eller et positivt tal ikke er nul. Angive DefaultTTL til-1 betyder, der som standard alle dokumenter i gruppen af websteder vil live uendelig, men DocumentDB skal tjeneste overvåge denne samling for dokumenter, der har tilsidesat denne standard.

## <a name="configuring-default-ttl-on-a-collection"></a>Konfiguration af standard TTL på en af websteder

Du kan konfigurere et standard tidspunkt for direkte på et niveau af websteder. 

Hvis du vil angive TTL på en af websteder, skal du angive et positivt tal til ikke er nul, der angiver perioden, i sekunder at udløbe alle dokumenter i samlingen efter det seneste redigerede tidsstempel i dokumentet (_ts).

Eller du kan angive standarden for til 1, hvilket betyder, at alle dokumenter, der er indsat i til samlingen live på ubestemt tid som standard.

## <a name="setting-ttl-on-a-document"></a>Indstillingen TTL på et dokument

Du kan angive bestemte TTL på et dokumentniveau ud over at angive en standardindstilling TTL på en af websteder. Dette vil tilsidesætte standardindstillingen for gruppen af websteder.

Hvis du vil angive TTL på et dokument, skal du angive et positivt ikke er nul tal, der angiver perioden, i sekunder, til at udløbe dokumentet efter det seneste redigerede tidsstempel i dokumentet (_ts).

For at angive denne udløbet forskydning skal du angive TTL-feltet i dokumentet.

Hvis et dokument indeholder ingen TTL-feltet, gælder standard i af websteder.

Hvis TTL er deaktiveret på niveauet af websteder, ignoreres TTL-feltet i dokumentet, indtil TTL er aktiveret igen på gruppen af websteder.


## <a name="extending-ttl-on-an-existing-document"></a>Hvis du vil udvide TTL på et eksisterende dokument

Du kan nulstille TTL på et dokument ved at gøre en hvilken som helst skrivehandling på dokumentet. På denne måde oprettes _ts til det aktuelle klokkeslæt, og nedtælling til udløbet dokument, som angivet af ttl, begynder igen.

Hvis du vil ændre ttl af et dokument, kan du opdatere feltet, som du kan gøre med et andet angives felt.


## <a name="removing-ttl-from-a-document"></a>Fjerne TTL fra et dokument

Hvis der er angivet en TTL på et dokument, og du ikke længere vil dokumentet til at udløbe, kan du hente dokumentet, fjerne feltet TTL og erstatte dokumentet på serveren.

Når feltet TTL er fjernet fra dokumentet, anvendes standard i af websteder.

Derefter skal du angive TTL-værdien-1 for at stoppe et dokument fra udløber og ikke nedarver fra samlingen.


## <a name="disabling-ttl"></a>Deaktivere TTL

Hvis du vil deaktivere TTL helt på en af websteder og stoppe processen til baggrunden i at søge efter udløbne dokumenter egenskaben DefaultTTL på gruppen af websteder skal slettes.

Hvis du sletter denne egenskab er forskellig fra indstille den til -1. Indstilling til-1 betyder nye dokumenter, der er føjet til samlingen vil live uendelig, men du kan tilsidesætte dette på bestemte dokumenter i gruppen af websteder.

Fjerne denne egenskab helt fra samlingen betyder, at, udløber ingen dokumenter, selvom der er dokumenter, der eksplicit har tilsidesat forrige standard.


## <a name="faq"></a>OFTE STILLEDE SPØRGSMÅL

**Hvad koster TTL mig?**

Der er ingen yderligere omkostninger for at angive en TTL på et dokument.

**Hvor lang tid tager det for at slette mit dokument, når TTL er op?**

Dokumenterne, der er markeret som ikke-tilgængelige, når dokumentet er udløbet (ttl + _ts > = klokkeslæt server). Ingen handling skal have på disse dokumenter efter denne gang, og de skal udelukkes fra resultaterne af en hvilken som helst forespørgsler udføres. Dokumenterne, der slettes fysisk af systemet i baggrunden. Dette kan ikke bruge en hvilken som helst RUs fra den samling budget.

**Hvis det tager et stykke tid til at slette dokumenterne, de tæller med i min kvote (og faktura) indtil de får slettet?**

Nej, når dokumentet er udløbet, du vil ikke blive faktureret til opbevaring af disse dokumenter og størrelsen på dokumenterne, der tæller ikke med i kvoten for lagerpladsen for en af websteder.

**TTL på et dokument, der skal være en hvilken som helst virkning på RU gebyrer?**

Nej, vises der ingen indflydelse på RU gebyrer for handlinger, der udføres på et dokument i DocumentDB.

**Vil sletningen af dokumenter påvirkning af overførselshastigheden jeg har klargjort på mine websteder?**

Nej, fungerer anmodninger på din samling, modtager prioritet forhold til at køre processen til baggrunden for at slette dine dokumenter. Tilføje TTL til ethvert dokument påvirker ikke dette.

**Når et dokument udløber, hvor lang tid det forbliver i Mine websteder indtil det bliver slettet?**

Når dokumentet udløber, vil det ikke længere være tilgængelige. Det nøjagtige klokkeslæt et dokument, forbliver i din samling, før faktisk slettes er ikke-deterministisk og baseret på, når processen baggrund er muligt at slette dokumentet.

**Slettes udløbne dokumenter på tværs af alle noder, eller er det "til sidst consistent?"**

Dokumentet bliver ikke tilgængelig på samme tid, på tværs af alle noder og i alle områder.

**Er der en RU omkostning for TTL-overvågning opgaver i baggrunden?**

Nej, der er ingen RU omkostninger for dette.

**Hvor ofte kontrolleres TTL udløber?**

Kontrollere TTL udløb sker ikke som en i baggrunden. Når du besvarer en anmodning om tjenesten back end-gør Kontroller indbygget og udelukke dokumenter, der er udløbet. Sletning af det fysiske dokument er den eneste proces, der er asynkront kører i baggrunden. Hyppigheden for denne proces bestemmes af de tilgængelige RUs på samlingen.

**Funktionen TTL kun gælder for hele dokumenter, eller jeg udløbe individuelle dokument egenskabsværdier?**

TTL gælder for hele dokumentet. Hvis du gerne vil udløbe blot en del af et dokument, derefter anbefales det, at du udtrække del fra hoveddokumentet i et separat "sammenkædede" dokument og derefter bruge TTL på det pågældende udpakkede dokument.

**Har funktionen TTL eventuelle specifikke indeksering krav?**

Ja. Gruppen af websteder skal have [indeksering politik indstillet](documentdb-indexing-policies.md) til Lazy eller ensartede. Forsøg på at angive DefaultTTL på en af websteder med indeksering indstillet til ingen medfører en fejl, når der forsøger at deaktivere indeksering på en af websteder, der indeholder en DefaultTTL allerede konfigureret.


## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om Azure DocumentDB skal referere til siden [*dokumentation*](https://azure.microsoft.com/documentation/services/documentdb/) .




