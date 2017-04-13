<properties
    pageTitle="Klientsiden kryptering med Python til Microsoft Azure-lager | Microsoft Azure"
    description="Azure-lager klientbibliotek til Python understøtter klientsiden kryptering for maksimal sikkerhed for programmerne Azure-lager."
    services="storage"
    documentationCenter="python"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>


# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>Klientsiden kryptering med Python til Microsoft Azure-lager

[AZURE.INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Oversigt

[Azure lagerplads klientbibliotek til Python](https://pypi.python.org/pypi/azure-storage) understøtter kryptering af data i klientprogrammer før overførsel til Azure-lager og dekryptere data under hentning af klienten.

>[AZURE.NOTE] Azure-lager Python biblioteket er i Vis udskrift.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Kryptering og dekryptering via konvolut metode
Processer af kryptering og dekryptering Følg konvolut metode.

### <a name="encryption-via-the-envelope-technique"></a>Kryptering via konvolut metode
Kryptering via konvolut metode fungerer på følgende måde:

1.  Biblioteket Azure lagerplads klient genererer en indhold krypteringsnøgle (CEK), som er en én gang gang symmetriske nøgle.

2.  Brugerdata er krypteret ved hjælp af denne CEK.

3.  CEK er derefter ombrudt (krypteret) ved hjælp af vigtige krypteringsnøglen (KEK). KEK er identificeret med en nøgle-id'et og kan være en asymmetrisk vigtige par eller en symmetriske nøgle, som administreres lokalt.
Biblioteket lagerplads klient selve har aldrig adgang til KEK. Biblioteket aktiverer algoritmen vigtige ombrydning, som leveres af KEK. Brugere kan vælge at bruge brugerdefinerede udbydere til vigtige ombrydning/udpakning, hvis du ønsker.

4.  De krypterede data derefter er overført til tjenesten Azure-lager. Tasten ombrudte sammen med nogle yderligere kryptering metadata er gemt som metadata (på et blob) eller interpoleret med den krypterede data (beskeder i kø og tabel enheder).

### <a name="decryption-via-the-envelope-technique"></a>Dekryptering via konvolut metode
Dekryptering via konvolut metode fungerer på følgende måde:

1.  Biblioteket klient antages det, at brugeren er administrere vigtige krypteringsnøglen (KEK) lokalt. Brugeren behøver ikke at kende tasten bestemte, der blev brugt til kryptering. I stedet kan en vigtige resolver, som løser forskellige vigtige id'er til nøgler, konfigurere og bruges.

2.  Biblioteket klient henter den krypterede data sammen med en hvilken som helst kryptering materiale, der er gemt på tjenesten.

3.  Den ombrudte indhold krypteringsnøgle (CEK) er så uindpakkede (dekrypteres) ved hjælp af de vigtigste kryptering centrale (KEK). Her igen, har biblioteket klienten ikke adgang til KEK. Det aktiverer blot provideren brugerdefinerede fjernelse algoritme.

4.  Indhold krypteringsnøglen (CEK) bruges derefter dekryptere krypterede brugerdataene.

## <a name="encryption-mechanism"></a>Kryptering ordning  
Biblioteket lagerplads klient bruger [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) for at kryptere brugerdata. Nærmere betegnet kan [Kryptering Bloker sammenkædning (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) tilstand med AES. Alle tjenesterne fungerer lidt anderledes, så vi vil diskutere hver af dem her.

### <a name="blobs"></a>BLOB
Biblioteket klienten understøtter i øjeblikket kryptering af hele BLOB. Nærmere betegnet kan kryptering understøttes, når brugerne anvender den **oprette*** metoder. Til overførsler, begge fuldført og område overførsler understøttes, og parallelization af begge upload og download er tilgængelig.

Under kryptering, vil biblioteket klient generere et tilfældigt initialisering vektor (IV) i 16 byte, sammen med en tilfældig indhold krypteringsnøgle (CEK) 32 byte, og Udfør konvolut kryptering blob data ved hjælp af disse oplysninger. Den ombrudte CEK og nogle yderligere kryptering metadata derefter gemmes som blob metadata sammen med den krypterede blob om tjenesten.

>[AZURE.WARNING] Hvis du redigerer eller overføre din egen metadataene for blob, skal du sikre dig, at disse metadata bevares. Hvis du overfører nye metadata uden disse metadata, den ombrudte CEK, IV og andre metadata, går tabt, og blob indholdet bliver aldrig størrelse igen.

Hente en krypteret blob omfatter hentning af indholdet af hele blob ved hjælp af **få*** nemmere metoder. Den ombrudte CEK uindpakkede og bruges sammen med IV (gemt som blob metadata i dette tilfælde) til at returnere dekrypteres dataene til brugerne.

Hente en vilkårlig område (**få*** metoder med område parametre overføres i) krypteret BLOB omfatter justering af det område, der leveres af brugere, hvis du vil have en lille mængde flere data, som kan bruges til at regel dekryptere det ønskede område.

Bloker BLOB og siden BLOB kan kun være krypteret/dekrypteres ved hjælp af denne skemaet. Der er i øjeblikket ingen understøttelse for kryptere Føj BLOB.

### <a name="queues"></a>Køer
Da kø meddelelser kan være en hvilken som helst format, definerer biblioteket klienten et brugerdefineret format, som indeholder initialisering vektor (IV) og den krypterede indhold krypteringsnøgle (CEK) i meddelelsesteksten.

Under kryptering, biblioteket klient genererer en tilfældigt IV i 16 byte sammen med en tilfældigt CEK 32 byte og udfører konvolut kryptering af meddelelsesteksten kø ved hjælp af disse oplysninger. Den ombrudte CEK og nogle yderligere kryptering metadata føjes derefter til den krypterede kø meddelelse. Denne ændret meddelelse (vist nedenfor) er gemt på tjenesten.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Under kryptering, er den ombrudte nøgle ud fra kø meddelelsen og uindpakkede. IV er også ud fra kø meddelelsen og bruges sammen med tasten uindpakkede dekryptere kø meddelelse dataene. Bemærk, at kryptering metadata small (under 500 byte), så mens den tæller mod 64KB grænsen for en kø meddelelse, påvirkningen skal administreres.

### <a name="tables"></a>Tabeller
Biblioteket klienten understøtter kryptering af objektegenskaber for Indsæt og Erstat.

>[AZURE.NOTE] Flet understøttes ikke i øjeblikket. Da et undersæt af egenskaber kan er krypteret tidligere ved hjælp af en anden nøgle, skal du blot fletning af de nye egenskaber og opdatering af metadata, medfører tab af data. Fletning af enten kræver ekstra service opkald til at læse det eksisterende objekt fra tjenesten, eller ved hjælp af en ny nøgle per egenskab, hvor begge er ikke egnet til at forbedre ydeevnen.

Kryptering af tabel data fungerer som følger:

1.  Brugerne angive egenskaberne, der er krypteret.

2.  Biblioteket klient genererer en tilfældigt initialisering vektor (IV) i 16 byte sammen med en tilfældig indhold krypteringsnøgle (CEK) 32 byte for hver enhed, og udfører konvolut kryptering på de individuelle egenskaber, der er krypteret af følger en ny IV per egenskab. Egenskaben krypterede gemmes som binære data.

3.  Den ombrudte CEK og nogle yderligere kryptering metadata derefter gemmes som to ekstra reserveret egenskaber. Først reserveret egenskab (\_ClientEncryptionMetadata1) er en strengegenskab, der indeholder oplysninger om IV, version og ombrudte nøgle. Andet reserveret egenskab (\_ClientEncryptionMetadata2) er en binær egenskab, der indeholder oplysninger om de egenskaber, der er krypteret. Oplysningerne i denne anden egenskab (\_ClientEncryptionMetadata2) er krypteret.

4.  På grund af disse yderligere reserveret egenskaber, der kræves til kryptering, kan brugere nu have kun 250 brugerdefinerede egenskaber i stedet for 252. Den samlede størrelse af objektet skal være mindre end 1MB.

    Bemærk, at kun strengegenskaber kan krypteres. Hvis der er andre typer egenskaber skal krypteres, skal de konverteres til strenge. Krypteret strengene er gemt på tjenesten, som binære egenskaber, og de konverteres til strenge (rå strenge, ikke EntityProperties med type EdmType.STRING) igen efter dekryptering.

    Brugere skal angive egenskaberne, der skal krypteres for tabeller, ud over politikken kryptering. Dette kan gøres ved at gemme disse egenskaber i TableEntity objekter med type er indstillet til EdmType.STRING og kryptere indstillet til SAND eller ved at angive encryption_resolver_function på tableservice objektet. En kryptering resolver er en funktion, der tager en Partitionsnøgle, række nøgle og egenskabsnavn og returnerer en boolesk værdi, der angiver, om egenskaben skal krypteres. Under kryptering bruger biblioteket klient disse oplysninger til at beslutte, om en egenskab skal krypteres under skrivning på nettet. Stedfortræderen også giver mulighed for at logik rundt om hvordan egenskaber er krypteret. (For eksempel, hvis X, og derefter kryptere egenskaben A; ellers kryptere egenskaber for A og b) Bemærk, at det ikke er nødvendigt at angive oplysningerne, mens du læser eller forespørgsler enheder.

### <a name="batch-operations"></a>Batchen handlinger
Én krypteringspolitik gælder for alle rækker i batchen. Biblioteket klient genererer internt en ny tilfældige IV og tilfældige CEK hver række i batchen. Brugere kan også vælge at kryptere forskellige egenskaber for hver operation i batchen ved at definere denne funktionsmåde i kryptering resolver.
Hvis der oprettes en kladde som en kontekst manager via metoden tableservice batch(), anvendes automatisk på tableservice krypteringspolitik på batchen. Hvis der oprettes eksplicit en kladde ved at ringe til parametre, politikken kryptering blev overført som en parameter og venstre uændrede i batchen levetid.
Bemærk, at objekter er krypteret, som de er indsat i batchen ved hjælp af den batchen krypteringspolitik (enheder ikke krypteres på tidspunktet for gemmer batchen ved hjælp af den tableservice krypteringspolitik).

### <a name="queries"></a>Forespørgsler
Hvis du vil udføre forespørgsel handlinger, skal du angive en vigtige resolver, der er i stand til at løse alle tasterne i resultatsættet. Hvis et objekt, der er indeholdt i resultatet af forespørgslen ikke kan fortolkes som en udbyder, Udløs biblioteket klienten en fejl. For en forespørgsel, som udfører server side prognoser, biblioteket klient føjer metadataegenskaber speciel kryptering (\_ClientEncryptionMetadata1 og \_ClientEncryptionMetadata2) som standard på de markerede kolonner.

>[AZURE.IMPORTANT] Vær opmærksom på følgende vigtige punkter, når du bruger klientsiden kryptering:
>
>- Når læse fra eller skrive en krypteret blob, bruge hele blob Overfør kommandoer og område/helt blob download kommandoer. Undgå at skrive til en krypteret blob ved hjælp af protocol handlinger som placere Bloker, placere blokeringsliste, skrive sider eller Ryd sider. Ellers kan du beskadiget krypteret blob og så det ikke kan læses.
>
>- For tabeller, der findes en lignende begrænsning. Pas på ikke opdatere krypterede egenskaber uden at opdatere kryptering metadata.
>
>- Hvis du indstiller metadata på krypteret blob, kan du overskrive de kryptering beslægtede metadata, der kræves til dekryptering, da angive metadata ikke er additive. Dette gælder også for snapshots; undgå at angive metadata, mens du opretter et øjebliksbillede af en krypteret blob. Hvis metadata skal angives, skal du sørge for at kalde metoden **get_blob_metadata** først for at få de aktuelle kryptering metadata, og undgå samtidige skriver, mens metadata indstilles.
>
>- Aktivere flaget **require_encryption** på serviceobjektet for brugere, som skal fungerer kun med krypterede data. Se nedenfor for at få flere oplysninger.

Biblioteket lagerplads klient forventer, at de medfølgende KEK og vigtige resolver at implementere følgende grænseflade. [Azure nøgle samling](https://azure.microsoft.com/services/key-vault/) understøttelse af Python KEK management afventer og vil blive integreret i dette bibliotek, når fuldført.


## <a name="client-api--interface"></a>Klient-API / brugergrænseflade
Når et lager serviceobjekt (det vil sige blockblobservice) er blevet oprettet, brugeren kan tildele værdier til de felter, der udgør en krypteringspolitik til: key_encryption_key, key_resolver_function, og require_encryption. Brugere kan give kun KEK kun en resolver eller begge dele. key_encryption_key er den grundlæggende vigtige type, der er identificeret ved hjælp af en vigtige id og, der indeholder logik til ombrydning/udpakning. key_resolver_function bruges til at løse en nøgle under dekrypteringen igen. Returnerer den en gyldig KEK fået tildelt en nøgle-id. Dette giver brugere mulighed for at vælge mellem flere taster, der administreres i flere placeringer.

KEK skal implementere følgende metoder for at kryptere korrekt data:
- wrap_key(cek): ombrydes den angivne CEK (byte) ved hjælp af en algoritme af brugerens valg. Returnerer den ombrudte nøgle.
- get_key_wrap_algorithm(): returnerer den algoritme, der bruges til at ombryde taster.
- get_kid(): returnerer den streng nøgle-id for denne KEK.
KEK skal implementere følgende metoder til regel dekryptere data:
- unwrap_key (cek, algoritme): returnerer uindpakkede form af den angivne CEK ved hjælp af den angivne streng algoritme.
- get_kid(): returnerer en streng nøgle-id for denne KEK.

De vigtigste resolver skal mindst implementere en metode, givet en nøgle-id, returnerer den tilsvarende KEK implementerer grænsefladen ovenfor. Kun denne metode er at være tildelt egenskaben key_resolver_function på serviceobjektet.

- Tasten bruges altid til kryptering, og fravær af en nøgle, medfører en fejl.
- Til dekryptering:
    - De vigtigste resolver startes, hvis angivet til at hente nøglen. Hvis resolveren er angivet, men ikke har en tilknytning til nøgle-id'et, udløst en fejl.
    - Hvis resolver ikke er angivet, men er angivet en nøgle, bruges nøglen, hvis dens id svarer til det nødvendige vigtige id. Hvis id'et ikke stemmer overens, er der opstod en fejl.

      Kryptering eksemplerne i azure.storage.samples <fix URL>demonstrerer en mere detaljeret til slut scenario til BLOB, køer og tabeller.
        Eksempel-installationer KEK og vigtige resolver er angivet i eksempelfilerne KeyWrapper og KeyResolver henholdsvis.

### <a name="requireencryption-mode"></a>RequireEncryption tilstand
Brugere kan vælge at aktivere en tilstand, hvor alle uploads og downloads skal krypteres. I denne tilstand mislykkes forsøg på at overføre data uden en krypteringspolitik til eller hente data, der ikke er krypteret på tjenesten på klienten. Flaget **require_encryption** på serviceobjektet styrer dette problem.

### <a name="blob-service-encryption"></a>BLOB service kryptering
Angiv krypteringen felter til politik for objektet blockblobservice. Alt det andet skal, internt behandles af biblioteket klient.

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Set the KEK and key resolver on the service object.
    my_block_blob_service.key_encryption_key = kek
    my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

    # Upload the encrypted contents to the blob.
    my_block_blob_service.create_blob_from_stream(container_name, blob_name, stream)

    # Download and decrypt the encrypted contents from the blob.
    blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)

### <a name="queue-service-encryption"></a>Kø service kryptering
Angiv krypteringen felter til politik for objektet queueservice. Alt det andet skal, internt behandles af biblioteket klient.

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Set the KEK and key resolver on the service object.
    my_queue_service.key_encryption_key = kek
    my_queue_service.key_resolver_funcion = key_resolver.resolve_key

    # Add message
    my_queue_service.put_message(queue_name, content)

    # Retrieve message
    retrieved_message_list = my_queue_service.get_messages(queue_name)

### <a name="table-service-encryption"></a>Tabel-tjenesten kryptering
Ud over at oprette en krypteringspolitik for og angive den anmodning om indstillinger, skal du enten angive et **encryption_resolver_function** på **tableservice**eller angive attributten Krypter på EntityProperty.

### <a name="using-the-resolver"></a>Ved hjælp af resolveren

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Define the encryption resolver_function.
    def my_encryption_resolver(pk, rk, property_name):
            if property_name == 'foo':
                    return True
            return False

    # Set the KEK and key resolver on the service object.
    my_table_service.key_encryption_key = kek
    my_table_service.key_resolver_funcion = key_resolver.resolve_key
    my_table_service.encryption_resolver_function = my_encryption_resolver

    # Insert Entity
    my_table_service.insert_entity(table_name, entity)

    # Retrieve Entity
    # Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
    my_table_service.get_entity(table_name, entity['PartitionKey'], entity['RowKey'])

### <a name="using-attributes"></a>Brug af attributter
Som nævnt ovenfor, kan en egenskab ved at gemme den i et EntityProperty objekt og indstille feltet Krypter markeres til kryptering.

    encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)

## <a name="encryption-and-performance"></a>Kryptering og ydeevne
Bemærk, at kryptere søgeresultaterne lagerplads data i flere ydeevne omkostninger. Indhold nøgle og IV skal oprettes, selve indholdet skal være krypteret og ekstra metadata skal formateret og overføres. Denne omkostninger varierer afhængigt af mængden af data, som krypteres. Vi anbefaler, at kunder altid teste deres programmer for ydelse under udvikling.

## <a name="next-steps"></a>Næste trin

- Hente [Azure lagerplads klientbibliotek til Java PyPi pakke](https://pypi.python.org/pypi/azure-storage)
- Hente den [Azure lagerplads klientbibliotek til Python kildekode fra GitHub](https://github.com/Azure/azure-storage-python)
