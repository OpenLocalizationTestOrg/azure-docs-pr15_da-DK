<properties
    pageTitle="Azure smarte lagerplads på BLOB | Microsoft Azure"
    description="Lagerplads niveauer for Azure Blob-lager tilbyder økonomisk storage til objektdata baseret på access mønstre. Smarte lagerplads niveau er optimeret til data, der åbnes mindre ofte."
    services="storage"
    documentationCenter=""
    authors="michaelhauss"
    manager="vamshik"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="mihauss"/>


# <a name="azure-blob-storage-hot-and-cool-storage-tiers"></a>Azure Blob-lager: Hot og Smart lagerplads niveauer

## <a name="overview"></a>Oversigt

Azure-lager tilbyder nu to lagerplads niveauer for Blob-lager (objekt lagring), så du kan gemme dine data mest rentabel afhængigt af hvordan du bruger den. Det Azure **varmt lagerplads niveau** er optimeret til lagring af data, der åbnes ofte. Det Azure **smarte lagerplads niveau** er optimeret til lagring af data, der er sjældent adgang til og langlivet. Data i det smarte lagerplads niveau kan acceptere en lidt lavere tilgængelighed, men stadig kræver høj holdbarhed og lignende tid til at få adgang til og overførselshastighed egenskaber som varmt data. Smarte data skal er lidt lavere tilgængelighed SERVICENIVEAUAFTALE og højere access omkostninger acceptable kompromiser for meget reducere omkostningerne til lagerplads.

Data gemt i skyen vokser i dag, i en eksponentiel tempo. Hvis du vil administrere omkostninger for voksende behovet for lagring, er det praktisk at organisere dine data, der er baseret på attributter som hyppigheden for access og planlagte opbevaringsperiode. Data gemt i skyen kan være helt forskellige med hensyn til hvordan den er oprettet, behandles og adgang til via dets levetid. Nogle data er aktivt adgang til og ændres i hele dets levetid. Nogle data er hentet meget ofte tidligt i dets levetid via access slippe markant som data alder. Nogle data har været inaktiv i skyen og er sjældent, hvis, adgang til én gang gemt.

Hver af disse scenarier med hybriddataadgang er beskrevet ovenfor fordele fra et opdelte trin lagerplads, der er optimeret til et bestemt access mønster. Med introduktionen af varmt og smarte lagerplads niveauer adskille Azure Blob storage adresser nu dette behov for opdelte lagerplads niveauer med priser modeller.

## <a name="blob-storage-accounts"></a>BLOB storage konti

**BLOB storage konti** er specialiserede lagerplads konti til at gemme dataene ustrukturerede BLOB (objekter) i Azure-lager. Med Blob storage firmaer, kan du nu vælge mellem varmt og smarte lagerplads niveauer til at gemme, dine mindre hyppigt smarte data på en lavere lagerplads omkostninger og gemme mere hyppigt varmt data på en lavere adgang omkostninger. BLOB storage konti ligner kontiene eksisterende generelle lager og dele alle gode holdbarhed, tilgængelighed, skalerbarhed og funktioner, som du bruger i dag, herunder 100% API konsistens for Bloker BLOB og tilføje BLOB.

> [AZURE.NOTE] BLOB storage konti understøtter kun Bloker og tilføje BLOB og ikke siden BLOB.

BLOB storage konti fremvise attributten **Access lag** , som gør det muligt at angive lagerplads niveau som **varmt** eller **smarte** afhængigt af de data, der er gemt på kontoen. Hvis der er en ændring af et mønster for brug af dine data, kan du også skifte mellem disse lagerplads lag når som helst.

> [AZURE.NOTE] Ændre lagerplads niveau, kan det medføre yderligere gebyrer. Se afsnittet [priser og fakturering](storage-blob-storage-tiers.md#pricing-and-billing) for at få mere at vide.

Eksempel på brugsscenarier for varme lagerplads niveau omfatter:

- Data, der bruges aktivt eller forventede at åbnes (læst fra og skrevet til) ofte.
- Data, der er midlertidigt gemt til kataloger og eventuel overførsel til det smarte lagerplads niveau.

Eksempel på brugsscenarier for det smarte lagerplads niveau omfatter:

- Sikkerhedskopien, arkivering og genoprettelse efter genoprettelse datasæt.
- Ældre medieindhold ikke ses ofte længere, men der forventes skal være tilgængeligt, så snart der er åbnet.
- Store datasæt, der skal være lagrede omkostninger effektivt, mens flere data indsamles til fremtidige behandling. (*f.eks.*, længerevarende opbevaring af videnskabelige data, rå telemetridata fra et produktionsanlæg facilitet)
- Oprindelige () rækkedata, der skal bevares, selv efter at den er blevet behandlet i sidste brugbare formular. (*f.eks.*, rå mediefiler efter kodning i andre formater)
- Overholdelse af regler og arkivering data, der skal være gemt i lang tid og bruges ikke ret meget. (*f.eks.*, sikkerhed kamera optagelser, gamle X-Rays/MRIs til sundhedssektoren organisationer, lyd-optagelser og udskrifter af kundeopkald for finansielle tjenesteydelser)

Se [om Azure lagerplads konti](storage-create-storage-account.md) for flere oplysninger om lagerplads konti.

For programmer, der kræver kun blokere eller tilføje blob-lager, anbefales det at bruge Blob storage konti, for at kunne udnytte opdelte priser modellen lagdelte lagerplads. Men vi forstår, at det ikke er muligt under visse omstændigheder såfremt ved hjælp af generelle lagerplads konti ville være metode til at gå, f.eks.:

- Du skal bruge tabeller, køer eller filer, og vil din blob, der er gemt i den samme konto lagerplads. Bemærk, at der ikke er nogen tekniske fordel at gemme dem i den samme konto end har den samme delt taster.
- Du skal stadig bruge implementeringsmodel klassisk. BLOB storage konti er kun tilgængelige via implementeringsmodel Azure ressourcestyring.
- Du skal bruge siden BLOB. BLOB storage konti understøtter ikke siden BLOB. Vi anbefaler generelt ved hjælp af Bloker blob, medmindre du har særlige behov for siden BLOB.
- Du bruger en version af [Lagerplads Services REST-API](https://msdn.microsoft.com/library/azure/dd894041.aspx) , der er ældre end 2014-02-14 eller et klientbibliotek, med en version, som er lavere end 4.x, og du kan ikke opgradere dit program.

> [AZURE.NOTE] BLOB storage konti understøttes i øjeblikket i en størstedelen af Azure områder med mere følge. Du kan finde den opdaterede liste over tilgængelige områder på siden [Azure Services efter område](https://azure.microsoft.com/regions/#services) .

## <a name="comparison-between-the-storage-tiers"></a>Sammenligning mellem lagerplads niveauer

Den følgende tabel fremhæver sammenligningen mellem to lagerplads niveauer:

<table border="1" cellspacing="0" cellpadding="0" style="border: 1px solid #000000;">
<col width="250">
<col width="250">
<col width="250">
<tbody>
<tr>
    <td><strong><center></center></strong></td>
    <td><strong><center>Hurtigtast lagerplads niveau</center></strong></td>
    <td><strong><center>Smarte lagerplads niveau</center></strong></td
</tr>
<tr>
    <td><strong><center>Tilgængelighed</center></strong></td>
    <td><center>99,9%</center></td>
    <td><center>99%</center></td>
</tr>
<tr>
    <td><strong><center>Tilgængelighed<br>(RA-GRS læser)</center></strong></td>
    <td><center>99,99%</center></td>
    <td><center>99,9%</center></td>
</tr>
<tr>
    <td><strong><center>Brugsgebyrer</center></strong></td>
    <td><center>Større lagerplads omkostninger<br>Reducere omkostningerne til access og transaction</center></td>
    <td><center>Reducere omkostningerne til lager<br>Større adgang og transaktion omkostninger</center></td>
</tr>
<tr>
    <td><strong><center>Mindste Objektstørrelse<center></strong></td>
    <td colspan="2"><center>I/T.</center></td>
</tr>
<tr>
    <td><strong><center>Mindste lagerplads varighed<center></strong></td>
    <td colspan="2"><center>I/T.</center></td>
</tr>
<tr>
    <td><strong><center>Ventetid<br>(Time to første byte)<center></strong></td>
    <td colspan="2"><center>millisekunder</center></td>
</tr>
<tr>
    <td><strong><center>Skalerbarhed og ydeevne destinationer<center></strong></td>
    <td colspan="2"><center>Samme som generelle lagerplads konti</center></td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] BLOB storage konti understøtter den samme ydeevne og skalerbarhed destinationer som generelle lagerplads konti. Se [Azure lagerplads skalerbarhed og ydeevne destinationer](storage-scalability-targets.md) for flere oplysninger.

## <a name="pricing-and-billing"></a>Priser og fakturering

BLOB storage konti Brug en ny priser model for blob-lager, der er baseret på lagerplads niveau. Når du bruger en Blob storage-konto, gælder følgende fakturering overvejelser:

- **Lagerplads omkostninger**: ud over mængden data, der er gemt, omkostninger for lagring af data varierer afhængigt af lagerplads niveau. Per GB omkostninger er lavere for smarte lagerplads niveau end for varmt lagerplads niveau.
- **Dataadgangsside omkostninger**: data i det smarte lagerplads niveau, skal du betale en per GB data access gebyr for læsning og skrivning.
- **Transaktionsomkostninger**: der er et hver transaktion gebyr for begge dele. Men hver transaktion omkostninger til den smarte lagerplads niveau er højere end for varmt lagerplads niveau.
- **Geografisk gentagelse dataoverførsel omkostninger**: Dette gælder kun for konti med geografisk replikering er konfigureret, herunder GRS og RA-GRS. Geografisk gentagelse dataoverførsel medfører et gebyr for hver GB.
- **Udgående data overføre omkostninger**: udgående dataoverførsel (data, der overføres af et Azure område) påløber fakturering for brugen af båndbredde på grundlag per GB, overensstemmelse med generelle lagerplads konti.
- **Ændre lagerplads lag**: ændre lagerplads niveau fra fantastiske til hot vil betale et gebyr, der er lig med læser alle de data, der er eksisterende lagerplads kontoen for hver overgang. På den anden side bliver ændrer lagerplads niveau fra genvejstast til fantastiske gratis af omkostninger.

> [AZURE.NOTE] For at tillade brugere at prøve de nye lagerplads niveauer og validere funktionalitet efter lancering, gebyr til at ændre opbevaring frafaldes niveau fra fantastiske til hot fra indtil juni 30 2016. Starter juli 1st 2016, anvendes gratis på alle overgange fra fantastiske til hot. Yderligere oplysninger om priser modellen for Blob-lager se konti [Azure lagerplads priser](https://azure.microsoft.com/pricing/details/storage/) side. Yderligere oplysninger om udgående dataene se filoverførsel gebyrer [Data overførsler priser](https://azure.microsoft.com/pricing/details/data-transfers/) oplysningssiden.

## <a name="quick-start"></a>Hurtig Start

Nedenfor gennemgås følgende scenarier ved hjælp af portalen Azure i dette afsnit:

- Sådan oprettes en Blob storage-konto.
- Sådan administreres en Blob storage-konto.

### <a name="using-the-azure-portal"></a>Ved hjælp af portalen Azure

#### <a name="create-a-blob-storage-account-using-the-azure-portal"></a>Oprette en ved hjælp af portalen Azure Blob-lagerplads-konto

1. Log på [Azure-portalen](https://portal.azure.com).

2. Vælg **Ny**i menuen Hub > **Data + lagerplads** > **lagerplads konto**.

3. Angiv et navn til kontoen lagerplads.

    Dette navn skal være globalt entydige; det bruges som en del af URL-adressen bruges til at få adgang til objekterne i kontoen lagerplads.  

4. Vælg **Ressourcestyring** som implementeringsmodel.

    Lagdelte lagerplads kan kun bruges med ressourcestyring lagerplads konti; Dette er det anbefalede implementeringsmodel for nye ressourcer. Se [Oversigt over Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md)kan finde flere oplysninger.  

5. Vælg **Blob-lager**på rullelisten type konto.

    Dette kan du vælge typen lagerplads konto. Lagdelte lagerplads er ikke tilgængelig i generelle lagerplads; det er kun tilgængelige for Blob storage type konto.    

    Bemærk, at når du vælger denne indstilling, angives det ydeevne niveau til Standard. Lagdelte lagerplads er ikke tilgængelig med Premium ydeevne niveau.

6. Vælg indstillingen gentagelse for kontoen lagerplads: **LRS**, **GRS**eller **RA-GRS**. Standard er **RA-GRS**.

    LRS = lokalt overflødige lagerplads; GRS = geografisk overflødige lagerplads (2 områder) RA-GRS er læseadgang geografisk overflødige lagerplads (2 områder med læseadgang til andet).

    Få mere at vide om indstillinger for gentagelse Azure-lager, se [Azure-lager gentagelse](storage-redundancy.md).

7. Vælg det rette storage niveau til dine behov: angive **Access niveau** til **smarte** eller **varmt**. Standard er **varmt**.

8. Vælg det abonnement, hvor du vil oprette den nye konto lagerplads.

9. Angiv en ny ressourcegruppe eller Vælg en eksisterende ressourcegruppe. Du kan finde flere oplysninger om ressourcegrupper, [Azure ressourcestyring oversigt](../azure-resource-manager/resource-group-overview.md).

10. Vælg område for kontoen lagerplads.

11. Klik på **Opret** for at oprette kontoen lagerplads.

#### <a name="change-the-storage-tier-of-a-blob-storage-account-using-the-azure-portal"></a>Ændre lagerplads niveau i en Blob storage-konto ved hjælp af portalen Azure

1. Log på [Azure-portalen](https://portal.azure.com).

2. Markér alle de ressourcer for at gå til kontoen lagerplads, og derefter vælge kontoen lagerplads.

3. Klik på **konfiguration** for at få vist og/eller ændre kontokonfigurationen i bladet indstillinger.

4. Vælg det rette storage niveau til dine behov: angive **Access niveau** til **smarte** eller **varmt**.

5. Klik på Gem øverst på bladet.

> [AZURE.NOTE] Ændre lagerplads niveau, kan det medføre yderligere gebyrer. Se afsnittet [priser og fakturering](storage-blob-storage-tiers.md#pricing-and-billing) for at få mere at vide.

## <a name="evaluating-and-migrating-to-blob-storage-accounts"></a>Evaluering af og overføre til Blob storage konti

Formålet med dette afsnit er kan hjælpe brugerne med at sikre en problemfri overgang til at bruge Blob storage konti. Der er to brugerscenarier:

- Du har en eksisterende konto generelle lager og ønsker at evaluere en ændring af en Blob storage konto med det rigtige lagerplads niveau.
- Du har besluttet at bruge en Blob storage konto eller allerede har en og ønsker at evaluere, om du skal bruge det varme eller smarte lagerplads niveau.

I begge tilfælde er første rækkefølge for business til at anslå omkostninger for lagring og få adgang til dine data, der er gemt i en Blob storage konto og Sammenlign det med de aktuelle omkostninger.

### <a name="evaluating-blob-storage-account-tiers"></a>Evaluering af Blob storage konto niveauer

For at anslå omkostninger for lagring af og adgang til data, der er gemt i en Blob storage-konto, skal du evaluere din eksisterende brugsmønster eller omtrentlig din forventede brugsmønster. Generelt, vil du gerne vil vide:

- Din lagerplads forbrug - hvor meget data gemmes, og hvordan ændrer det på månedsbasis?
- Din lagerplads access mønster -, hvor meget data der læses fra og skrives til kontoen (herunder nye data)? Hvor mange transaktioner bruges til adgang til data, og hvilke typer af transaktioner er de?

#### <a name="monitoring-existing-storage-accounts"></a>Overvågning af eksisterende lagerplads konti

Hvis du vil overvåge dine eksisterende lagerplads konti og indsamle disse data, kan du gøre brug af Azure lagerplads Analytics som udfører logføring og giver målepunkter data til en lagerplads konto.
Lagerplads Analytics kan gemme mål, som omfatter aggregeret transaktion statistik og kapacitet data om anmodninger til tjenesten Blob storage til både generelle lagerplads konti samt Blob storage konti.
Disse data er gemt i velkendte tabeller i den samme lagerplads-konto.

Få mere at vide, skal du se [Om Analytics Lagringsmål](https://msdn.microsoft.com/library/azure/hh343258.aspx) og [Lagerplads Analytics målepunkter tabelskema](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [AZURE.NOTE] BLOB storage konti fremvise tabellen tjenesteslutpunkt kun til lagring af og adgang til målepunkter dataene for den pågældende konto.

Hvis du vil overvåge lagerplads forbrug for tjenesten Blob storage, skal du aktivere kapacitet målepunkter.
Med dette aktiveret, registreres databehandling daglige til en lagerplads konto Blob-tjenesten og registrerede som en tabel-post, der er skrevet til tabellen *$MetricsCapacityBlob* i den samme lagerplads-konto.

Hvis du vil overvåge data access mønsteret for tjenesten Blob storage, skal du aktivere hver time transaktion målene på en API niveau.
Med dette aktiveret, per API er transaktioner aggregeret hver time, og er registreret som en tabel-post, der er skrevet til tabellen *$MetricsHourPrimaryTransactionsBlob* i den samme konto lagerplads. Tabellen *$MetricsHourSecondaryTransactionsBlob* poster transaktioner til sekundær slutpunktet i tilfælde af RA-GRS lagerplads konti.

> [AZURE.NOTE] I tilfælde af, at du har en generel lagerplads konto, hvor du har gemt, siden BLOB og virtuelt diske sammen med Bloker og tilføje blob-data, er denne proces skøn er ikke tilgængelig. Dette skyldes, at du vil har ingen mulighed for kendingsnummer kapacitet og transaktion målepunkter baseret på typen af blob til kun blokere og Føj BLOB som kan overføres til en Blob storage-konto.

For at få en god tilnærmelse af du data forbrug og access mønster, anbefaler vi du vælger en opbevaringsperiode for målene, der er repræsentant for din almindelige brugen og ekstrapolere.
En af mulighederne er at bevare dataene målepunkter for 7 dage og samle dataene, hver uge til analyse i slutningen af måneden.
En anden mulighed er at bevare målepunkter dataene for de seneste 30 dage og indsamle og analysere dataene i slutningen af perioden på 30 dage.

Få mere at vide om aktivering af se indsamling og visning af målepunkter data, skal du [aktivere Azure lagringsmål og få vist målepunkter data](storage-enable-and-view-metrics.md).

> [AZURE.NOTE] Gemme, få adgang til og hente analytics-data også betale ligesom almindelige brugerdata.

#### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Ved at benytte brugen målepunkter til at anslå omkostninger

##### <a name="storage-costs"></a>Lagerplads omkostninger

Den seneste post i kapacitet målepunkter tabel *$MetricsCapacityBlob* med række vigtige *'data'* viser lagerkapacitet consumed ved brugerdata.
Den seneste post i kapacitet målepunkter tabel *$MetricsCapacityBlob* med række vigtige *'analytics'* viser lagerkapacitet consumed ved loggene analyser.

Denne samlede kapacitet consumed ved begge brugerdata og logge på analytics (hvis det er aktiveret) kan derefter bruges til at anslå omkostninger for lagring af data i kontoen lagerplads.
Den samme metode kan også bruges til vurdering af lagerplads omkostninger for blok og tilføje BLOB i generelle lagerplads konti.

##### <a name="transaction-costs"></a>Transaktionsomkostninger

Summen af *'TotalBillableRequests'*, på tværs af alle poster på en API i tabellen målepunkter angiver det samlede antal transaktioner for den pågældende API. *f.eks.*, det samlede antal *'GetBlob'* transaktioner i en given periode kan beregnes med summen af samlede fakturerbar anmodninger om alle poster med tasten række *' bruger. GetBlob'*.

For at anslå omkostninger for Blob storage konti, skal du opdele transaktioner i tre grupper, da de er pris anderledes.

- Skriv transaktioner som *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'*og *'CopyBlob'*.
- Slet transaktioner som *'DeleteBlob'* og *'DeleteContainer'*.
- Alle andre transaktioner.

For at anslå omkostninger for generelle lagerplads konti, skal du sammenlægge alle transaktioner uanset handlingen/API.

##### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Data access og geografisk gentagelse data overføre omkostninger

Mens lagerplads analytics ikke indeholder mængden data, der læses fra og skrives til en lagerplads konto, kan det stort set beregnede ved at kigge på tabellen målepunkter.
Summen af *'TotalIngress'* på tværs af alle poster på en API i tabellen målepunkter angiver den samlede mængde af vandindtrængen data i byte for den pågældende API.
Summen af *'TotalEgress'* angiver på samme måde, den samlede mængde af data udgangspunkt i byte.

For at beregne data access omkostningerne for Blob storage konti, skal du opdele transaktioner i to grupper.

- Mængden data, der er hentet fra kontoen lagerplads kan vurderes ved at kigge på summen af *'TotalEgress'* for primært handlingerne *'GetBlob'* og *'CopyBlob'* .
- Mængden data, der er skrevet på kontoen lagerplads kan vurderes ved at kigge på summen af *'TotalIngress'* for primært *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* og *'AppendBlock'* handlinger.

Omkostningerne for geografisk gentagelse dataoverførsel for Blob storage konti kan også beregnes ved hjælp af estimatet for de data, der er skrevet i tilfælde af en GRS eller RA-GRS lagerplads konto.

> [AZURE.NOTE] For en mere detaljeret eksempel om beregning af omkostninger til brug af varme eller smarte lagerplads niveau, skal du se nærmere på ofte stillede spørgsmål med titlen *'Hvad er varmt og fantastiske access niveauer, og hvordan skal jeg ud af, hvilken profil der skal use?'* i den [Azure lagerplads priser side](https://azure.microsoft.com/pricing/details/storage/).

### <a name="migrating-existing-data"></a>Overføre eksisterende data

En Blob storage konto er specialiserede til lagring af kun Bloker og tilføje BLOB. Eksisterende generelle lagerplads konti, som gør det muligt at gemme tabeller, køer, filer og diske samt blob, kan ikke konverteres til Blob storage konti. Hvis du vil bruge lagerplads lag, skal du oprette nye Blob storage konti og overføre din eksisterende data til de nyligt oprettet konti.
Du kan bruge følgende metoder til at overføre eksisterende data til Blob storage konti fra lokal lagerenheder, fra tredjeparter cloud storage udbydere eller fra din eksisterende generelle lagerplads konti i Azure:

#### <a name="azcopy"></a>AzCopy

AzCopy er et Windows kommandolinjen værktøj til høj ydeevne kopiering af data til og fra Azure-lager. Du kan bruge AzCopy til at kopiere data til kontoen Blob-lagerplads fra dit eksisterende generelle lagerplads konti eller for at overføre data fra dit lokale lagerenheder til kontoen Blob-lager.

Yderligere oplysninger finder du [overføre data med AzCopy kommandolinjeparametre Utility](storage-use-azcopy.md).

#### <a name="data-movement-library"></a>Data bevægelse bibliotek

Azure bibliotek på bevægelse lagerplads til .NET er baseret på den core data bevægelse struktur, der styrer AzCopy. Biblioteket er udviklet til høj ydeevne, pålideligt og nemt data videregivelse ligner AzCopy. Dette kan du drage alle fordelene ved de funktioner, der leveres af AzCopy i dit program oprindeligt uden at håndtere kører og overvågning eksterne forekomster af AzCopy.

Yderligere oplysninger finder du [Azure lagerplads Data bevægelse bibliotek til .net](https://github.com/Azure/azure-storage-net-data-movement)

#### <a name="rest-api-or-client-library"></a>REST-API eller klientbibliotek

Du kan oprette et brugerdefineret program til at overføre dine data til en Blob storage-konto ved hjælp af en af Azure klient bibliotekerne eller Azure lagerplads tjenesterne REST-API. Azure-lager indeholder omfattende klientbiblioteker til flere sprog og platforme som .NET, Java, C++, Node.JS, PHP, fonetisk og Python. Klientbiblioteker tilbyder avancerede funktioner som forsøg, logføring og parallelle overførsler. Du kan også udvikle direkte mod REST-API, som kan kaldes af en hvilken som helst sprog, der gør HTTP/HTTPS-anmodninger.

Få mere at vide ved at se [Introduktion til Azure Blob-lager](storage-dotnet-how-to-use-blobs.md).

> [AZURE.NOTE] BLOB, der er krypteret med klientsiden kryptering store kryptering beslægtede metadata, der er gemt med blob. Det er absolut meget vigtigt, at en hvilken som helst kopi ordning skal sikre, at blob metadata, og især kryptering beslægtede metadataene, bevares. Hvis du kopierer BLOB uden disse metadata, kan blob indhold ikke hentes igen. Du kan finde flere oplysninger om kryptering beslægtede metadata [Azure-lager kryptering på klientsiden](storage-client-side-encryption.md).

## <a name="faqs"></a>Ofte stillede spørgsmål

1. **Eksisterende lagerplads konti, der er stadig tilgængelige?**

    Ja, eksisterende lagerplads konti er stadig tilgængelige og er uændret i priser eller funktionalitet.  De har ikke mulighed for at vælge en lagerplads niveau og vil ikke have overgang switchmiljø funktioner i fremtiden.

2. **Hvorfor og hvornår skal jeg begynde at bruge Blob storage konti?**

    BLOB storage konti er specialiserede til lagring af BLOB og gør det muligt at introducere nye blob-centreret funktioner. Fremover, Blob storage konti er den anbefalede måde til at gemme blob, fremtidige funktioner som hierarkiske lager og overgang switchmiljø introduceres baseret på denne kontotype. Det er dog op til dig besked, når du vil overføre baseret på virksomhedens behov.

3. **Kan jeg konvertere mit eksisterende lagerplads-konto til en Blob storage konto?**

    Nej. BLOB storage konto er en anden type konto, lagring, og du skal oprette den nye og overføre dine data, som beskrevet ovenfor.

4. **Kan jeg gemme objekter i begge lagerplads niveauer i den samme konto?**

    Attributten *' Access niveau '* , som angiver lagerplads niveau er angivet på en kontoniveau og gælder for alle objekter i den pågældende konto. Du kan ikke angive attributten access niveau på et objektniveau.

5. **Kan jeg ændre lagerplads niveau i min Blob storage konto?**

    Ja. Du vil kunne ændre lagerplads niveau ved at angive egenskaben *Access-niveauet* på kontoen lagerplads. Ændre lagerplads niveau gælder for alle objekter, der er gemt på kontoen. Ændre lagerplads niveau fra genvejstast til fantastiske ikke vil betale eventuelle gebyrer, mens du ændrer fra fantastiske til hot vil betale en per GB omkostninger for at læse alle data i kontoen.

6. **Hvor ofte kan jeg ændre lagerplads niveau i min Blob storage konto?**

    Mens vi ikke gennemtvinge en begrænsning på hvor ofte kan lagerplads niveauet kan ændres, skal du være opmærksom på, ved at ændre lagerplads niveau fra fantastiske til hot vil betale betydeligt gebyrer. Vi anbefaler ikke ændre lagerplads niveau ofte.

7. **Vil BLOB i smarte lagerplads lag fungerer anderledes end dem på varmt lagerplads niveau?**

    BLOB i varmt lagerplads lag har den samme ventetid som BLOB i generelle lagerplads konti. BLOB i smarte lagerplads lag har en lignende ventetid (i millisekunder) som BLOB i generelle lagerplads konti.

    BLOB i smarte lagerplads lag har en lidt lavere tilgængelighed serviceniveau (SLA) end de blob, der er gemt i varmt lagerplads lag. Du kan finde flere detaljer, [SERVICENIVEAUAFTALE for lagerplads](https://azure.microsoft.com/support/legal/sla/storage).

8. **Kan jeg gemme siden BLOB og virtuelt diske i Blob storage konti?**

    BLOB storage konti understøtter kun Bloker og tilføje BLOB og ikke siden BLOB. Azure virtuelt diske understøttes af siden BLOB og derfor Blob storage konti kan ikke bruges til at gemme virtuelt diske. Men det er muligt at gemme sikkerhedskopier af virtuelt diskene som Bloker BLOB med en konto med Blob-lager.

9. **Skal jeg ændre min eksisterende programmer til at bruge Blob storage konti?**

    BLOB storage konti er 100% API overensstemmelse med generelle lagerplads konti for blok og tilføje BLOB. Så længe programmet er ved hjælp af blokere BLOB eller tilføje blob, og du bruger 2014-02-14-versionen af [Lagerplads Services REST-API](https://msdn.microsoft.com/library/azure/dd894041.aspx) eller større, så dit program skal kun arbejde. Hvis du bruger en ældre version af protokollen, skal du opdatere dit program tilladelse til at bruge den nye version med henblik på at arbejde problemfrit sammen med begge typer lagerplads konti. Generelt, bruger vi altid anbefalet bruger den seneste version uanset hvilken konto der lagerplads Skriv du.

10. **En ændring i brugeroplevelsen vil der være?**

    BLOB storage konti ligner meget en generel lagerplads konti til lagring af Bloker og tilføje BLOB og understøtter alle de vigtigste funktioner i Azure-lager, herunder stor holdbarhed og tilgængelighed, skalerbarhed, ydeevne og sikkerhed. Forbliver uændret end funktionerne og begrænsninger, der er specifikke for Blob storage konti og dens lagerplads lag, der har ringet over alt det andet.

## <a name="next-steps"></a>Næste trin

### <a name="evaluate-blob-storage-accounts"></a>Evaluere Blob storage konti

[Kontrollere tilgængelighed af Blob storage konti efter område](https://azure.microsoft.com/regions/#services)

[Evaluere brugen af dine aktuelle lagerplads konti ved at aktivere Azure lagringsmål](storage-enable-and-view-metrics.md)

[Kontrollere Blob storage priser efter område](https://azure.microsoft.com/pricing/details/storage/)

[Kontrollér dataoverførsel priser](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-blob-storage-accounts"></a>Begynde at bruge Blob storage konti

[Introduktion til Azure Blob-lager](storage-dotnet-how-to-use-blobs.md)

[Flytning af data til og fra Azure-lager](storage-moving-data.md)

[Overføre data med AzCopy kommandolinjeparametre Utility](storage-use-azcopy.md)

[Gennemse og udforske kontiene lagerplads](http://storageexplorer.com/)
