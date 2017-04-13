<properties
    pageTitle="Klientsiden kryptering med Java til Microsoft Azure-lager | Microsoft Azure"
    description="Biblioteket Azure-lager-klienten til Java understøtter klientsiden kryptering og integration med Azure-tasten samling for maksimal sikkerhed for programmerne Azure-lager."
    services="storage"
    documentationCenter="java"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>


# <a name="client-side-encryption-with-java-for-microsoft-azure-storage"></a>Klientsiden kryptering med Java til Microsoft Azure-lager   

[AZURE.INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Oversigt  

[Azure lagerplads klientbibliotek til Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) understøtter kryptering af data i klientprogrammer før overførsel til Azure-lager og dekryptere data under overførsel af klienten. Biblioteket understøtter også integration med [Azure nøgle samling](https://azure.microsoft.com/services/key-vault/) til Lagerstyring for konto nøgle.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Kryptering og dekryptering via konvolut metode    
Processer af kryptering og dekryptering Følg konvolut metode.  

### <a name="encryption-via-the-envelope-technique"></a>Kryptering via konvolut metode  
Kryptering via konvolut metode fungerer på følgende måde:  

1.  Azure-lager klient biblioteket genererer en indhold krypteringsnøgle (CEK), som er en én gang gang symmetriske nøgle.  

2.  Brugerdata er krypteret ved hjælp af denne CEK.   

3.  CEK er derefter ombrudt (krypteret) ved hjælp af vigtige krypteringsnøglen (KEK). KEK kan er identificeret med en nøgle-id'et og være en asymmetrisk vigtige par eller en symmetriske nøgle og kan administreres lokalt eller gemt i Azure-tasten Vaults.  
Biblioteket lagerplads klient selve har aldrig adgang til KEK. Biblioteket aktiverer algoritmen vigtige ombrydning, som leveres af nøgle samling. Brugere kan vælge at bruge brugerdefinerede udbydere til vigtige ombrydning/udpakning, hvis du ønsker.  

4.  De krypterede data derefter er overført til tjenesten Azure-lager. Tasten ombrudte sammen med nogle yderligere kryptering metadata er gemt som metadata (på et blob) eller interpoleret med den krypterede data (beskeder i kø og tabel enheder).

### <a name="decryption-via-the-envelope-technique"></a>Dekryptering via konvolut metode  
Dekryptering via konvolut metode fungerer på følgende måde:  

1.  Biblioteket klient antages det, at brugeren er administrere vigtige krypteringsnøglen (KEK), enten lokalt eller i Azure-tasten Vaults. Brugeren behøver ikke at kende tasten bestemte, der blev brugt til kryptering. I stedet kan en vigtige resolver som løser forskellige vigtige id'er til nøgler konfigurere og bruges.  

2.  Biblioteket klient henter den krypterede data sammen med en hvilken som helst kryptering materiale, der er gemt på tjenesten.  

3.  Den ombrudte indhold krypteringsnøgle (CEK) er så uindpakkede (dekrypteres) ved hjælp af de vigtigste kryptering centrale (KEK). Her igen, har biblioteket klienten ikke adgang til KEK. Det aktiverer blot brugerdefinerede eller nøgle samling udbyderens fjernelse algoritme.  

4.  Indhold krypteringsnøglen (CEK) bruges derefter dekryptere krypterede brugerdataene.

## <a name="encryption-mechanism"></a>Kryptering ordning  
Biblioteket lagerplads klient bruger [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) for at kryptere brugerdata. Nærmere betegnet kan [Kryptering Bloker sammenkædning (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) tilstand med AES. Alle tjenesterne fungerer lidt anderledes, så vi vil diskutere hver af dem her.

### <a name="blobs"></a>BLOB  
Biblioteket klienten understøtter i øjeblikket kryptering af hele BLOB. Nærmere betegnet kan kryptering understøttes, når brugerne anvender den **overføre** * metoder eller * *openOutputStream** metode. Til overførsler, begge fuldført og område overførsler understøttes.  

Under kryptering, vil biblioteket klient generere et tilfældigt initialisering vektor (IV) i 16 byte, sammen med en tilfældig indhold krypteringsnøgle (CEK) 32 byte, og Udfør konvolut kryptering af blob dataene ved hjælp af disse oplysninger. Den ombrudte CEK og nogle yderligere kryptering metadata derefter gemmes som blob metadata sammen med krypteret blob om tjenesten.

>[AZURE.WARNING] Hvis du redigerer eller overføre dine egne metadataene for blob, skal du sikre dig, at disse metadata bevares. Hvis du overfører nye metadata uden disse metadata, den ombrudte CEK, IV og andre metadata, går tabt, og blob indholdet bliver aldrig størrelse igen.

Hente en krypteret blob omfatter hentning af indholdet af hele blob ved hjælp af * *hente*/openInputStream** nemmere metoder. Den ombrudte CEK uindpakkede og bruges sammen med IV (gemt som blob metadata i dette tilfælde) til at returnere dekrypteres dataene til brugerne.

Hente en vilkårlig område (**downloadRange*** metoder) krypteret BLOB omfatter justering af det område, der leveres af brugere, hvis du vil have en lille mængde flere data, som kan bruges til at regel dekryptere det ønskede område.  

Alle blob typer (blokere blob, siden BLOB og tilføje BLOB) kan krypteret/dekrypteres ved hjælp af denne skemaet.

### <a name="queues"></a>Køer  
Da kø meddelelser kan være en hvilken som helst format, definerer biblioteket klienten et brugerdefineret format, som indeholder initialisering vektor (IV) og den krypterede indhold krypteringsnøgle (CEK) i meddelelsesteksten.  

Under kryptering, biblioteket klient genererer en tilfældigt IV i 16 byte sammen med en tilfældigt CEK 32 byte og udfører konvolut kryptering af meddelelsesteksten kø ved hjælp af disse oplysninger. Derefter tilføjes ombrudte CEK og nogle yderligere kryptering metadata til krypterede kø meddelelsen. Denne ændret meddelelse (vist nedenfor) er gemt på tjenesten.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Under kryptering, er den ombrudte nøgle ud fra kø meddelelsen og uindpakkede. IV er også ud fra kø meddelelsen og bruges sammen med tasten uindpakkede dekryptere kø meddelelse dataene. Bemærk, at kryptering metadata small (under 500 byte), så mens den tæller mod 64KB grænsen for en kø meddelelse, påvirkningen skal administreres.

### <a name="tables"></a>Tabeller  
Biblioteket klienten understøtter kryptering af objektegenskaber for Indsæt og Erstat.

>[AZURE.NOTE] Flet understøttes ikke i øjeblikket. Da et undersæt af egenskaber kan er krypteret tidligere ved hjælp af en anden nøgle, skal du blot fletning af de nye egenskaber og opdatering af metadata, medfører tab af data. Fletning af enten kræver ekstra service opkald til at læse det eksisterende objekt fra tjenesten, eller ved hjælp af en ny nøgle per egenskab, hvor begge er ikke egnet til at forbedre ydeevnen.

Kryptering af tabel data fungerer som følger:  

1.  Brugerne angive egenskaberne, der er krypteret.  

2.  Biblioteket klient genererer en tilfældigt initialisering vektor (IV) i 16 byte sammen med en tilfældig indhold krypteringsnøgle (CEK) 32 byte for hver enhed, og udfører konvolut kryptering på de enkelte egenskaber, der er krypteret af følger en ny IV per egenskaben. Egenskaben krypterede gemmes som binære data.  

3.  Den ombrudte CEK og nogle yderligere kryptering metadata derefter gemmes som to ekstra reserveret egenskaber. Den første reserverede egenskab (_ClientEncryptionMetadata1) er en strengegenskab, der indeholder oplysninger om IV, version og ombrudte nøgle. Egenskaben andet reserveret (_ClientEncryptionMetadata2) er en binær egenskab, der indeholder oplysninger om de egenskaber, der er krypteret. Oplysningerne i denne anden egenskab (_ClientEncryptionMetadata2) er krypteret.  

4.  På grund af disse yderligere reserveret egenskaber, der kræves til kryptering, kan brugere nu have kun 250 brugerdefinerede egenskaber i stedet for 252. Den samlede størrelse af objektet skal være mindre end 1MB.  

    Bemærk, at kun strengegenskaber kan krypteres. Hvis der er andre typer egenskaber skal krypteres, skal de konverteres til strenge. Krypteret strengene er gemt på tjenesten som binære egenskaber, og de konverteres til strenge igen efter dekryptering.

    Brugere skal angive egenskaberne, der skal krypteres for tabeller, ud over politikken kryptering. Dette kan gøres ved at enten angive en [Krypter] attribut (for POCO objekter, der er afledt af TableEntity) eller en kryptering resolver i anmodning indstillinger. En kryptering resolver er stedfortræder, der tager en Partitionsnøgle, række nøgle og egenskabsnavn og returnerer en boolesk værdi, der angiver, om egenskaben skal være krypteret. Under kryptering bruger biblioteket klient disse oplysninger til at beslutte, om en egenskab skal krypteres under skrivning på nettet. Stedfortræderen også giver mulighed for at logik rundt om hvordan egenskaber er krypteret. (For eksempel, hvis X, og derefter kryptere egenskaben A; ellers kryptere egenskaber for A og b) Bemærk, at det ikke er nødvendigt at angive oplysningerne, mens du læser eller forespørgsler enheder.

### <a name="batch-operations"></a>Batchen handlinger  
I batchen handlinger, vil den samme KEK blive anvendt på tværs af alle rækker i den pågældende batchen operation da biblioteket klient tillader kun ét objekt indstillinger (og dermed én politik/KEK) per batchhandling. Dog genererer biblioteket klient internt en ny tilfældige IV og tilfældige CEK hver række i batchen. Brugere kan også vælge at kryptere forskellige egenskaber for hver operation i batchen ved at definere denne funktionsmåde i kryptering resolver.

### <a name="queries"></a>Forespørgsler  
Hvis du vil udføre forespørgsel handlinger, skal du angive en vigtige resolver, der er i stand til at løse alle tasterne i resultatsættet. Hvis et objekt, der er indeholdt i resultatet af forespørgslen ikke kan fortolkes som en udbyder, Udløs biblioteket klienten en fejl. For en forespørgsel, som udfører server side prognoser, føjer biblioteket klient metadataegenskaber speciel kryptering (_ClientEncryptionMetadata1 og _ClientEncryptionMetadata2) som standard til de markerede kolonner.

## <a name="azure-key-vault"></a>Azure vigtige samling  
Azure-tasten samling hjælper med at beskytter cryptographic nøgler og hemmeligheder, der bruges af skyen programmer og tjenester. Ved hjælp af Azure-tasten samling, kan brugere kryptere nøgler og hemmeligheder (såsom godkendelse taster, lagerplads konto taster, data kryptering taster. PFX-filer og adgangskoder) ved hjælp af taster, der er beskyttet af hardware sikkerhed moduler (HSMs). Du kan finde flere oplysninger, se [Hvad er Azure-tasten samling?](../key-vault/key-vault-whatis.md).

Biblioteket lagerplads klient bruger biblioteket nøgle samling core for at skabe en fælles ramme på tværs af Azure til administration af taster. Brugere kan også finde yderligere fordelen ved at bruge tasten samling filtypenavne bibliotek. Biblioteket filtypenavne indeholder praktiske funktioner omkring enkle og problemfri Symmetric/RSA lokale og skyen vigtige udbydere og med cachelagre og sammenlægning.

### <a name="interface-and-dependencies"></a>Brugergrænsefladen og afhængigheder  
Der er tre nøgle samling pakker:  

- Azure-keyvault-core indeholder IKey og IKeyResolver. Det er en lille pakke med ingen afhængigheder. Biblioteket lagerplads klient til Java definerer det som en afhængighed.
- Azure-keyvault indeholder nøgle samling RESTEN klienten.  
- Azure-keyvault-udvidelser indeholder lokalnummer kode, der indeholder cryptographic algoritmer og en RSAKey og en SymmetricKey-installationer. Det afhænger af den grundlæggende og KeyVault navneområder og giver funktionalitet for at definere et aggregat resolver (når brugere vil bruge flere vigtige udbydere) og en cachelagring vigtige resolver. Selvom biblioteket lagerplads klienten direkte ikke afhænger denne pakke, hvis brugere ønsker at bruge Azure-tasten samling til at gemme deres nøgler eller bruge tasten samling udvidelser til at bruge lokalt og cryptographic udbydere i skyen, skal de denne pakke.  

  Tasten samling henvender sig til værdiskabende master taster, og variere den benyttede begrænsninger per nøgle samling er designet med dette huske. Når du udfører klientsiden kryptering med-tasten samling, er den foretrukne model at bruge symmetriske master nøgler gemt som hemmeligheder i nøgle samling og cachelagret lokalt. Brugere skal du gøre følgende:  

1.  Oprette et hemmeligt offline, og Overfør det til tasten samling.  

2.  Brug den hemmeligt base id som en parameter til at løse den aktuelle version af hemmeligt til kryptering og cache oplysningerne lokalt. Bruge CachingKeyResolver til cachelagring; brugerne forventes ikke at implementere deres egen cachelagring logik.  

3.  Brug cachelagring resolver som input, mens du opretter politikken kryptering.
Kan finde flere oplysninger om brugen af tasten samling i kodeeksempler kryptering. <fix URL>  

## <a name="best-practices"></a>Bedste fremgangsmåder  
Understøttelse af kryptering findes kun i biblioteket lagerplads klient til Java.

>[AZURE.IMPORTANT] Vær opmærksom på følgende vigtige punkter, når du bruger klientsiden kryptering:
>  
>- Følg hele blob Overfør kommandoer og område/hele blob download kommandoer, hvis læse fra eller skrive til en krypteret blob. Undgå at skrive til en krypteret blob ved hjælp af protocol handlinger som placere Bloker, placere blokeringsliste, skrive sider, Ryd sider eller tilføje blok Ellers kan du beskadiget krypteret blob og så det ikke kan læses.
>
>- For tabeller, der findes en lignende begrænsning. Pas på ikke opdatere krypterede egenskaber uden at opdatere kryptering metadata.  
>
>- Hvis du indstiller metadata på krypteret blob, kan du overskrive de kryptering beslægtede metadata, der kræves til dekryptering, da angive metadata ikke er additive. Dette gælder også for snapshots; undgå at angive metadata, mens du opretter et øjebliksbillede af en krypteret blob. Hvis metadata skal angives, skal du sørge for at kalde metoden **downloadAttributes** først for at få de aktuelle kryptering metadata, og undgå samtidige skriver, mens metadata indstilles.  
>
>- Aktivere flaget **requireEncryption** i anmodning om standardindstillingerne for brugere, der skal fungerer kun med krypterede data. Se nedenfor for at få flere oplysninger.  

## <a name="client-api--interface"></a>Klient-API / brugergrænseflade  
Mens du opretter et EncryptionPolicy objekt, kan brugerne angive kun en tast (implementere IKey), skal du kun en resolver (implementere IKeyResolver) eller begge dele. IKey er den grundlæggende vigtige type, der er identificeret ved hjælp af en vigtige id og, der indeholder logik til ombrydning/udpakning. IKeyResolver bruges til at løse en nøgle under dekrypteringen igen. Den definerer en ResolveKey metode, der returnerer en IKey fået tildelt en nøgle-id. Dette giver brugere mulighed for at vælge mellem flere taster, der administreres i flere placeringer.

- Tasten bruges altid til kryptering, og ikke findes en nøgle, medfører en fejl.  
- Til dekryptering:  
    - De vigtigste resolver startes, hvis angivet til at hente nøglen. Hvis resolveren er angivet, men ikke har en tilknytning til nøgle-id'et, udløst en fejl.  
    - Hvis resolver ikke er angivet, men er angivet en nøgle, bruges nøglen, hvis dens id svarer til det nødvendige vigtige id. Hvis id'et ikke stemmer overens, er der opstod en fejl.  

      [Eksempler på kryptering](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) <fix URL>demonstrerer en mere detaljeret til slut scenario til BLOB, køer og tabeller, sammen med-tasten samling integration.

### <a name="requireencryption-mode"></a>RequireEncryption tilstand  
Brugere kan vælge at aktivere en tilstand, hvor alle uploads og downloads skal være krypteret. I denne tilstand mislykkes forsøg på at overføre data uden en krypteringspolitik til eller hente data, der ikke er krypteret på tjenesten på klienten. Flaget **requireEncryption** for objektet anmodning indstillinger styrer dette problem. Hvis dit program, krypteres alle objekter, der er gemt i Azure-lager, kan du angive egenskaben **requireEncryption** på anmodning om standardindstillingerne for serviceobjektet-klienten.   

For eksempel bruge **CloudBlobClient.getDefaultRequestOptions().setRequireEncryption(true)** til at kræve kryptering for alle blob handlinger, der udføres via klientobjektet.

### <a name="blob-service-encryption"></a>BLOB service kryptering  
Oprette et **BlobEncryptionPolicy** objekt og angive den anmodning om-indstillinger (per API eller på en klientniveau ved hjælp af **DefaultRequestOptions**). Alt det andet skal, internt behandles af biblioteket klient.

    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

    // Set the encryption policy on the request options.
    BlobRequestOptions options = new BlobRequestOptions();
    options.setEncryptionPolicy(policy);

    // Upload the encrypted contents to the blob.
    blob.upload(stream, size, null, options, null);

    // Download and decrypt the encrypted contents from the blob.
    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    blob.download(outputStream, null, options, null);

### <a name="queue-service-encryption"></a>Kø service kryptering  
Oprette et **QueueEncryptionPolicy** objekt og angive den anmodning om-indstillinger (per API eller på en klient plan ved hjælp af **DefaultRequestOptions**). Alt det andet skal, internt behandles af biblioteket klient.

    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

    // Add message
    QueueRequestOptions options = new QueueRequestOptions();
    options.setEncryptionPolicy(policy);

    queue.addMessage(message, 0, 0, options, null);

    // Retrieve message
    CloudQueueMessage retrMessage = queue.retrieveMessage(30, options, null);

### <a name="table-service-encryption"></a>Tabel-tjenesten kryptering  
Ud over at oprette en krypteringspolitik for og angive den anmodning om indstillinger, skal du enten angive et **EncryptionResolver** i **TableRequestOptions**eller indstille egenskaben [Krypter] for enheden getter og indstillingsfunktion.

### <a name="using-the-resolver"></a>Ved hjælp af resolveren  

    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

    TableRequestOptions options = new TableRequestOptions()
    options.setEncryptionPolicy(policy);
    options.setEncryptionResolver(new EncryptionResolver() {
        public boolean encryptionResolver(String pk, String rk, String key) {
            if (key == "foo")
            {
                return true;
            }
            return false;
        }
    });

    // Insert Entity
    currentTable.execute(TableOperation.insert(ent), options, null);

    // Retrieve Entity
    // No need to specify an encryption resolver for retrieve
    TableRequestOptions retrieveOptions = new TableRequestOptions()
    retrieveOptions.setEncryptionPolicy(policy);

    TableOperation operation = TableOperation.retrieve(ent.PartitionKey, ent.RowKey, DynamicTableEntity.class);
    TableResult result = currentTable.execute(operation, retrieveOptions, null);

### <a name="using-attributes"></a>Brug af attributter  
Som nævnt ovenfor, hvis objektet implementerer TableEntity, og klik derefter Egenskaber getter og opstillertid kan være dekoreret med attributten [Krypter] i stedet for at angive **EncryptionResolver**.

    private string encryptedProperty1;

    @Encrypt
    public String getEncryptedProperty1 () {
        return this.encryptedProperty1;
    }

    @Encrypt
    public void setEncryptedProperty1(final String encryptedProperty1) {
        this.encryptedProperty1 = encryptedProperty1;
    }

## <a name="encryption-and-performance"></a>Kryptering og ydeevne  
Bemærk, at kryptere søgeresultaterne lagerplads data i flere ydeevne omkostninger. Indhold nøgle og IV skal oprettes, selve indholdet skal være krypteret og yderligere metadata skal formateret og overføres. Denne omkostninger varierer afhængigt af mængden af data, som krypteres. Vi anbefaler, at kunder altid teste deres programmer for ydelse under udvikling.

## <a name="next-steps"></a>Næste trin  

- Hente [Azure lagerplads klientbibliotek til Java Maven pakke](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)  
- Hente [Azure lagerplads klientbibliotek til Java kildekode fra GitHub](https://github.com/Azure/azure-storage-java)   
- Hent Azure-tasten samling Maven bibliotek til Java Maven pakker:
    - [Core](http://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core) -pakke
    - [Klient](http://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault) -pakke
- Gå til [Azure nøgle samling dokumentation](../key-vault/key-vault-whatis.md)  
