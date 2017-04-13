<properties
   pageTitle="Oversigt over adgangskontrol i sø datalager | Microsoft Azure"
   description="Forstå, hvordan adgangskontrol i Azure sø datalager"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/06/2016"
   ms.author="nitinme"/>

# <a name="access-control-in-azure-data-lake-store"></a>Adgangskontrol i Azure sø datalager

Sø datalager implementerer en access control-model, der er afledt fra HDFS og også fra POSIX access kontrolelement modellen. I denne artikel indeholder en oversigt over grundlæggende om access kontrolelement modellen for sø datalager. Du kan finde flere oplysninger om HDFS adgangskontrol i model [HDFS tilladelser vejledning](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="access-control-lists-on-files-and-folders"></a>Adgangskontrollister til filer og mapper

Der findes to typer adgang adgangskontrollister (ACLs) - **Access ACLs** og **Standard ACLs**.

* **Access ACLs** – disse kontrolelement adgang til et objekt. Har adgang til ACL, filer og mapper.

* **Standard ACLs** – en "skabelon" af adgangskontrollister er knyttet til en mappe, der bestemmer Access ACLs for andre underordnede elementer, der er oprettet under den pågældende mappe. Filer har ikke standard ACLs.

![Sø datalager ACLs](./media/data-lake-store-access-control/data-lake-store-acls-1.png)

Både Access ACLs og standard ACLs har samme struktur.

![Sø datalager ACLs](./media/data-lake-store-access-control/data-lake-store-acls-2.png)

>[AZURE.NOTE] Ændre den standard ACL på en overordnet påvirker ikke ACL Access eller standard ACL for underordnede elementer, der allerede findes.

## <a name="users-and-identities"></a>Brugere og identiteter

Alle filer og mapper har entydige tilladelser for disse identiteter:

* Brugeren ejer af filen
* Gruppen ejer
* Navngivet brugere
* Navngivne grupper
* Alle andre brugere

Identiteter for brugere og grupper er Azure Active Directory (AAD) identiteter så medmindre andet er angivet en "brugere", i konteksten af sø datalager, enten betyde, at en AAD bruger eller en AAD sikkerhedsgruppe.

## <a name="permissions"></a>Tilladelser

Tilladelser på et objekt i filsystemet er **læst**, **skrive**, og **Udfør** og de kan bruges til filer og mapper, som vist i nedenstående tabel.

|            |    Fil     |   Mappe |
|------------|-------------|----------|
| **Read (R)** | Kan læse indholdet af en fil | Kræver **Læsning** og **kørsel** til at få vist indholdet af mappen.|
| **Skriv (W)** | Kan skrive eller føje til en fil | Kræver **Skriv og kør** til at oprette underordnede elementer i en mappe. |
| **Udføre (X)** | Betyder ikke noget i forbindelse med sø datalager | Kræves for at gennemgå de underordnede elementer i en mappe. |

### <a name="short-forms-for-permissions"></a>Kort formularer til tilladelser

**RWX**bruges til at angive **læse + skrive + udføre**. Der findes en mere kompakt numeriske formular, hvor **læst = 4**, **skrive = 2**, og **Execute = 1** og deres sum repræsenterer tilladelser. Her er nogle eksempler.

| Numeriske formular | Kort form |      Hvad betyder det?     |
|--------------|------------|------------------------|
| 7            | RWX        | Læse + skrive + udføre |
| 5            | R-X        | Læs + udføre         |
| 4            | R –        | Læs                   |
| 0            | ---        | Ingen tilladelser         |


### <a name="permissions-do-not-inherit"></a>Tilladelser arver ikke

I modellen POSIX-typografi anvendes af sø datalager, gemmes tilladelser for et element på selve elementet. Tilladelser for et element nedarves ikke med andre ord fra de overordnede elementer.

## <a name="common-scenarios-related-to-permissions"></a>Almindelige scenarier, der er relateret til tilladelser

Her er nogle almindelige scenarier at forstå, hvilke tilladelser der kræves for at udføre bestemte handlinger på en sø datalager-konto.

### <a name="permissions-needed-to-read-a-file"></a>Nødvendige tilladelser til at læse en fil

![Sø datalager ACLs](./media/data-lake-store-access-control/data-lake-store-acls-3.png)

* Til den fil, der læses - skal kalderen **læsetilladelser**
* For alle mapper i mappestrukturen, der indeholder filen - skal kalderen **Udfør** -tilladelser

### <a name="permissions-needed-to-append-to-a-file"></a>Nødvendige tilladelser til at føje til en fil

![Sø datalager ACLs](./media/data-lake-store-access-control/data-lake-store-acls-4.png)

* Kalderen skal have **skriverettigheder** for den fil, der skal vedhæftes-
* For alle de mapper, der indeholder filen - skal kalderen **Udfør** -tilladelser

### <a name="permissions-needed-to-delete-a-file"></a>Nødvendige tilladelser til at slette en fil

![Sø datalager ACLs](./media/data-lake-store-access-control/data-lake-store-acls-5.png)

* Til den overordnede mappe - skal kalderen **skrive + udføre** tilladelser
* For alle de andre mapper i sti til filen - skal kalderen **Udfør** -tilladelser

>[AZURE.NOTE] Skriv tilladelserne til filen ikke er påkrævet for at slette fil, så længe de ovenstående to betingelser er opfyldt.

### <a name="permissions-needed-to-enumerate-a-folder"></a>Der kræves tilladelser til optælling af en mappe

![Sø datalager ACLs](./media/data-lake-store-access-control/data-lake-store-acls-6.png)

* Kalderen skal have **læse + Execute** tilladelser for mappen optælles-
* For alle de overordnede mapper - skal kalderen **Udfør** -tilladelser

## <a name="viewing-permissions-in-the-azure-portal"></a>Få vist tilladelser i portalen Azure

Klik på **Access** for at få vist ACLs for en fil eller en mappe fra sø datalager firmaets **Data Explorer** blade. Klik på adgang for at få vist ACLs for mappen **katalog** under kontoen **mydatastore** i skærmbilledet nedenfor.

![Sø datalager ACLs](./media/data-lake-store-access-control/data-lake-store-show-acls-1.png)

Klik på **Simpel visning** for at se visningen enklere fra bladet **Access** herefter.

![Sø datalager ACLs](./media/data-lake-store-access-control/data-lake-store-show-acls-simple-view.png)

Klik på **Avanceret visning** for at se visningen mere avancerede.

![Sø datalager ACLs](./media/data-lake-store-access-control/data-lake-store-show-acls-advance-view.png)

## <a name="the-super-user"></a>Super brugeren

En superbruger har mest rettigheder over alle brugere i lageret med sø Data. En superbruger:

* med RWX tilladelser til **alle** filer og mapper
* kan ændre tilladelser på en hvilken som helst fil eller mappe.
* kan ændre ejer brugeren eller ejer gruppen af enhver fil eller mappe.

I Azure har en sø datalager konto flere Azure roller:

* Ejere
* Bidragydere
* Læsere
* Osv.

Alle i rollen **ejere** for en sø datalager konto er automatisk en særdeles bruger for den pågældende konto. Hvis du vil vide mere om Azure rolle baseret Access kontrolelement (RBAC) under [Rollebaseret adgangskontrol](../active-directory/role-based-access-control-configure.md).

## <a name="the-owning-user"></a>Ejer brugeren

Den bruger, der oprettede elementet er automatisk ejer brugeren af elementet. En ejer bruger kan:

* Ændre tilladelser for en fil, som ejes
* Ændre gruppen ejer af en fil, som ejes, så længe ejer brugeren er også et medlem af målgruppe.

>[AZURE.NOTE] Funktionen ejer bruger **kan ikke** ændre ejer brugeren af en anden ejet fil. Kun særdeles brugere kan ændre ejer brugeren af en fil eller mappe.

## <a name="the-owning-group"></a>Gruppen ejer

Alle brugere er knyttet til en "primære gruppe" i ACLs POSIX. For eksempel kan brugeren "Bent" tilhører økonomigruppen "". Bent kan tilhøre flere grupper, men en gruppe er altid angivet som sin primære gruppe. POSIX, når Bent opretter en fil, er gruppen ejer af den pågældende fil indstillet til at sin primære gruppe, som i dette tilfælde er "økonomi".
 
Når der oprettes et nyt element i filsystemet, tildeler sø datalager en værdi til gruppen ejer. 

* **Eksempel 1** – rodmappen "/". Denne mappe oprettes, når der oprettes en sø datalager firma. I dette tilfælde er gruppen ejer indstillet til den bruger, der oprettede firmaet.
* **Tilfælde 2** (alle andre tilfælde) - når der oprettes et nyt element, gruppen ejer er kopieret fra den overordnede mappe.

Gruppen ejer kan ændres af:
* Alle særdeles brugere
* Den ejer bruger, hvis ejer brugeren er også et medlem af målgruppe.

## <a name="access-check-algorithm"></a>Access Kontrollér algoritme

Følgende illustration repræsenterer access algoritmen for sø datalager konti.

![Sø datalager ACLs algoritme](./media/data-lake-store-access-control/data-lake-store-acls-algorithm.png)


## <a name="the-mask-and-effective-permissions"></a>Maske og "effektive tilladelser"

**Inputmaske** er en RWX værdi, der bruges til at begrænse adgangen til **navngivne brugere**, **at eje gruppe**og **navngivne grupper** , når du udfører algoritmen Access kontrollere. Her er de vigtigste begreber for masken. 

* Masken oprettes "effektive tilladelser", og det vil sige, den ændrer tilladelserne på tidspunktet for Access kontrollere.
* Masken kan redigeres direkte efter filen ejer og alle særdeles brugere.
* Masken har mulighed for at fjerne tilladelser til at oprette effektive tilladelse. Maske **kan ikke** føje tilladelser til den effektive tilladelse. 

Lad os se på nogle eksempler. Nedenfor er masken angivet til **RWX**, hvilket betyder, at masken ikke fjerner alle tilladelser. Bemærk, at de gældende tilladelser for navngivet bruger, ejer gruppen og navngivet gruppe ikke ændres under kontrol af adgang til.

![Sø datalager ACLs](./media/data-lake-store-access-control/data-lake-store-acls-mask-1.png)

I eksemplet herunder er masken angivet til **R-X**. Så den **markeres, deaktiveres skriveadgang** til **med navnet bruger**, **ejer gruppen**og **kaldet gruppe** på tidspunktet for access kontrollere.

![Sø datalager ACLs](./media/data-lake-store-access-control/data-lake-store-acls-mask-2.png)

Til reference er her den hvor inputmaske til en fil eller mappe vises i portalen Azure.

![Sø datalager ACLs](./media/data-lake-store-access-control/data-lake-store-show-acls-mask-view.png)

>[AZURE.NOTE] En ny konto i sø datalager masken til ACL Access og standard ACL for rodmappen ("/") er som standard til RWX.

## <a name="permissions-on-new-files-and-folders"></a>Tilladelser til nye filer og mapper

Når der oprettes en ny fil eller mappe under en eksisterende mappe, bestemmer den standard ACL på den overordnede mappe:

* En underordnet mappe standard ACL og ACL Access
* En underordnet fil Access ACL (filer har ikke en standard ACL)

### <a name="a-child-file-or-folders-access-acl"></a>En underordnet fil eller mappe Access ACL

Når der oprettes en underordnet fil eller mappe, kopieres overordnet standard ACL underordnede fil eller mappe Access ACL. Også, hvis **andre** brugere har RWX tilladelser i det overordnede standard ACL, fjernes det helt fra det underordnede element Access ACL.

![Sø datalager ACLs](./media/data-lake-store-access-control/data-lake-store-acls-child-items-1.png)

I de fleste scenarier er for ovenstående oplysninger skal du bør vide om, hvordan et underordnet element Access ACL bestemmes. Men hvis du allerede kender, POSIX-systemer og ønsker at forstå dybden, hvordan denne transformation opnås, i afsnittet [Umasks rolle i at oprette adgang til ACL for nye filer og mapper](#umasks-role-in-creating-the-access-acl-for-new-files-and-folders) senere i denne artikel.
 

### <a name="a-child-folders-default-acl"></a>En underordnet mappe standard ACL

Når der oprettes en underordnet mappe under en overordnet mappe, kopieres den overordnede mappe standard ACL over, som det er, at mappen underordnede standard ACL.

![Sø datalager ACLs](./media/data-lake-store-access-control/data-lake-store-acls-child-items-2.png)

## <a name="advanced-topics-for-understanding-acls-in-data-lake-store"></a>Avancerede emner for at forstå ACLs i sø datalager

Følgende er nogle af avancerede emner kan hjælpe dig med at forstå, hvordan ACLs bestemmes for Data sø Store filer eller mapper.

### <a name="umasks-role-in-creating-the-access-acl-for-new-files-and-folders"></a>Umasks rolle i at oprette adgang til ACL for nye filer og mapper

I et POSIX-kompatibel system er generelle som repræsenterer umask er en 9-bit værdi for den overordnede mappe, der bruges til at transformere tilladelse til **at eje bruger**, **der ejer gruppen**og **andre** på en ny underordnet fil eller mappe adgang til ACL. Dele af en umask identificere hvilke bit til at slå fra i det underordnede element Access ACL. Det er derfor bruges til at forhindre selektivt overførslen af tilladelser for at eje bruger, der ejer gruppen og andre.
  
I et HDFS system er umask typisk en konfigurationsindstilling for hele webstedet, styres af administratorer. Sø datalager bruger en **konto hele umask** , der ikke kan ændres. Følgende tabel viser Data sø Store umask.

| Brugergruppe  | Indstilling | Indvirkning på nyt underordnet elements Access ACL |
|------------ |---------|---------------------------------------|
| At eje bruger | ---     | Ingen effekt                             |
| At eje gruppe| ---     | Ingen effekt                             |
| Andre       | RWX     | Fjerne læse + skrive + udføre         | 

Følgende illustration viser denne umask i aktion. Resultatet er at fjerne **læse + skrive + udføre** til **anden** bruger. Da umask ikke har angivet bit for **at eje bruger** og **ejer gruppen**, konverteres ikke disse tilladelser.

![Sø datalager ACLs](./media/data-lake-store-access-control/data-lake-store-acls-umask.png) 

### <a name="the-sticky-bit"></a>Den sticky bit

Den sticky bit er en mere avanceret funktion i et POSIX filsystem. I forbindelse med sø datalager er det sandsynligvis ikke, at den sticky bit skal bruges.

Tabellen nedenfor viser, hvordan den sticky bit fungerer i sø datalager.

| Brugergruppe         | Fil    | Mappe |
|--------------------|---------|-------------------------|
| Sticky bit **fra** | Ingen effekt   | Ingen effekt           |
| Sticky bit **på**  | Ingen effekt   | Forhindrer alle undtagen **særdeles brugere** og det **at eje bruger** af et underordnet element fra slette eller omdøbe pågældende underordnet element.               |

Den sticky bit vises ikke i portalen Azure.

## <a name="common-questions-for-acls-in-data-lake-store"></a>Ofte stillede spørgsmål til ACLs i sø datalager

Her er nogle spørgsmål, der ofte opstår i forhold ACLs i sø datalager.

### <a name="do-i-have-to-enable-support-for-acls"></a>Skal jeg aktivere understøttelse af ACLs?

Nej. Adgangskontrol via ACLs er altid slået til for en sø datalager-konto.

### <a name="what-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>Hvilke tilladelser der kræves gælder slette en mappe og dens indhold?

* Den overordnede mappe skal have **skrive + udføre**.
* Mappen, der skal slettes, og alle mapper i postkassen, kræver **læse + skrive + udføre**.
>[AZURE.NOTE] Slette filer i mapper kræver ikke skrive på disse filer. Desuden rodmappen "/" kan **aldrig** slettes.

### <a name="who-is-set-as-the-owner-of-a-file-or-folder"></a>Der er angivet som ejeren af en fil eller mappe?

Oprettelse af en fil eller mappe bliver ejer.

### <a name="who-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>Der er angivet som ejer gruppen af en fil eller mappe til oprettelse af?

Det er kopieret fra gruppen ejer af den overordnede mappe, som den nye fil eller mappe er oprettet under.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Jeg er ejer brugeren af en fil, men jeg har ikke jeg brug for RWX tilladelser. Hvad gør jeg?

Ejer brugeren kan blot ændre tilladelser på filen for at give sig selv en hvilken som helst RWX tilladelser, de skal bruge.

### <a name="does-data-lake-store-support-inheritance-of-acls"></a>Understøtter sø datalager nedarvning af adgangskontrollister?

Nej.

### <a name="what-is-the-difference-between-mask-and-umask"></a>Hvad er forskellen mellem maske og umask?

| inputmaske | umask|
|------|------|
| Egenskaben **inputmaske** er tilgængelig på alle filer og mapper. | **Umask** er en egenskab for kontoen sø datalager. Så, der er kun en enkelt umask i lageret med sø Data.    |
| Egenskaben Inputmaske under en fil eller mappe kan ændres af ejer brugeren eller ejer gruppen af en fil eller en særdeles bruger. | Egenskaben umask kan ikke ændres af enhver bruger, endnu en superbruger. Det er en kan ikke ændres, konstant værdi.|
| Egenskaben Inputmaske bruges til at under algoritmen Access kontrollere på kørselstidspunktet til at afgøre, om en bruger har ret til at udføre på handling på en fil eller mappe. Masken rolle er at oprette "effektive tilladelser" på tidspunktet for kontrol af adgang til. | Umask der ikke bruges i Access kontrollere overhovedet. Umask bruges til at bestemme ACL Access af nye underordnede elementer i en mappe. |
| Masken er en 3-bit RWX værdi, der gælder for navngivet bruger, navngivet gruppe og ejer bruger på tidspunktet for kontrol af adgang til.| Umask er en 9 bit værdi, der gælder for den ejer bruger, ejer gruppen og anden af et nyt underordnet.| 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Hvor kan jeg få mere at vide om POSIX access kontrolelement model?

* [http://www.vanemery.com/Linux/ACL/POSIX_ACL_on_Linux.HTML](http://www.vanemery.com/Linux/ACL/POSIX_ACL_on_Linux.html)

* [HDFS tilladelse vejledning](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html) 

* [POSIX OFTE STILLEDE SPØRGSMÅL](http://www.opengroup.org/austin/papers/posix_faq.html)

* [POSIX 1003.1 2008](http://standards.ieee.org/findstds/standard/1003.1-2008.html)

* [POSIX 1003.1e 1997](http://users.suse.com/~agruen/acl/posix/Posix_1003.1e-990310.pdf)

* [POSIX ACL på Linux](http://users.suse.com/~agruen/acl/linux-acls/online/)

* [ACL ved hjælp af adgangskontrollister på Linux](http://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Se også

* [Oversigt over Azure sø datalager](data-lake-store-overview.md)

* [Introduktion til Azure Data sø Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)





