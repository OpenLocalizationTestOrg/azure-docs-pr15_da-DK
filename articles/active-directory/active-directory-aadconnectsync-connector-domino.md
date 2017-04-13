<properties
   pageTitle="Lotus Domino Connector | Microsoft Azure"
   description="I denne artikel beskrives, hvordan du konfigurerer Microsofts Lotus Domino forbindelse."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="lotus-domino-connector-technical-reference"></a>Teknisk vejledning til Lotus Domino-forbindelse
I denne artikel beskrives Lotus Domino forbindelsen. I artiklen gælder for følgende produkter:

- Microsoft identitet Manager 2016 (MIM2016)
- Forefront identitet Manager 2010 R2 (FIM2010R2)
    -   Skal bruge programrettelse 4.1.3671.0 eller nyere [KB3092178](https://support.microsoft.com/kb/3092178).

Forbindelsen er for MIM2016 og FIM2010R2 skal hentes fra [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-lotus-domino-connector"></a>Oversigt over Lotus Domino Connector
Lotus Domino Connector gør det muligt at integrere tjenesten til synkronisering med IBM Lotus Domino-server.

Følgende funktioner understøttes af den aktuelle udgave af forbindelsen fra et overordnet perspektiv:

Funktion | Support
--- | ---
Forbundne datakilde | Server: <li>Lotus Domino 8.5.x</li><li>Lotus Domino 9.x</li>Klient:<li>Lotus Notes-9.x</li>
Scenarier | <li>Objekt Livscyklusadministration</li><li>Gruppeadministration af</li><li>Administration af adgangskoder</li>
Handlinger | <li>Fuld og Deltaimport</li><li>Eksportere</li><li>Angive og ændre adgangskoden på http-adgangskode</li>
Skema | <li>Person (global bruger, kontakt (personer med ingen certifikat))</li><li>Gruppe</li><li>Ressource (ressource, rummet, onlinemøde)</li><li>Mail i databasen</li><li>Dynamisk registrering af attributter for understøttede objekter</li>

Lotus Domino forbindelsen bruger Lotus Notes-klienten til at kommunikere med Lotus Domino-Server. Som følge af denne afhængighed skal være installeret en understøttet Lotus Notes-klienten på synkronisering af serveren. Kommunikationen mellem klienten og serveren er implementeret via brugergrænsefladen i Lotus Notes .NET-kompatibilitet (Interop.domino.dll). Denne grænseflade letter kommunikationen mellem den Microsoft.NET platform og Lotus Notes-klienten og understøtter adgang til Lotus Domino dokumenter og visninger. Til Deltaimport er det også muligt, bruges den oprindelige C++-grænseflade (afhængigt af den valgte delta importmetode).

### <a name="prerequisites"></a>Forudsætninger
Før du bruger forbindelsen, skal du kontrollere følgende på serveren synkronisering være:

- Microsoft .NET 4.5.2 Framework eller nyere
- Lotus Notes-klienten skal være installeret på din server synkronisering
- Lotus Domino Connector kræver standard Lotus Domino LDAP skema database (schema.nsf) skal være til stede på Domino Directory-serveren. Hvis det ikke er installeret, kan du installere det ved at køre eller at genstarte LDAP-tjenesten på Domino-serveren.

### <a name="connected-data-source-permissions"></a>Forbundne datakilde tilladelser
Hvis du vil udføre understøttede opgaver i Lotus Domino forbindelse, skal du være medlem af følgende grupper:

- Fuld adgang til administratorer
- Administratorer
- Databaseadministratorer af

I følgende tabel vises de tilladelser, der kræves for hver handling:

Handling | Adgangsrettigheder
--- | ---
Importér | <li>Læse offentlige dokumenter</li><li> Fuld adgang Administrator (når du er medlem af administratorgruppen fuld adgang, du har automatisk effektive adgang til i ACL.)</li>
Eksportere og angive adgangskode | Effektiv adgang: <li>Oprette dokumenter</li><li>Slette dokumenter</li><li>Læse offentlige dokumenter</li><li>Skrive offentlige dokumenter</li><li>Replikeres eller Kopiér dokumenter</li>For eksporthandlinger skal du også følgende roller: <li>CreateResource</li><li>GroupCreator</li><li>GroupModifier</li><li>UserCreator</li><li>UserModifier</li>

### <a name="direct-operations-and-adminp"></a>Direkte handlinger og AdminP
Handlinger enten gå direkte til mappen Domino eller gennem AdminP processen. Følgende tabeller liste over alle understøttede objekter, handlinger og, hvis det er relevant, den relaterede implementering metode:

**Primære adressekartotek**

Objekt | Oprette | Opdater | Slet
--- | --- | --- | ---
Person | AdminP | Direkte | AdminP
Gruppe | AdminP | Direkte | AdminP
MailInDB | Direkte | Direkte | Direkte
Ressource | AdminP | Direkte | AdminP

**Sekundær adressekartotek**

Objekt | Oprette | Opdater | Slet
--- | --- | --- | ---
Person | I/T. | Direkte | Direkte
Gruppe | Direkte | Direkte | Direkte
MailInDB | Direkte | Direkte | Direkte
Ressource | I/T. | I/T. | I/T.

Når der oprettes en ressource, oprettes et Notes-dokument. På samme måde, når en ressource slettes, slettes Lotus Notes-dokumentet.

### <a name="ports-and-protocols"></a>Porte og protokoller
IBM Lotus Notes-klienten og Domino-servere du kommunikere ved hjælp af noter Remote Procedure Call (NRPC) hvor NRPC skal bruge TCP/IP. Standardportnummeret er 1352, men du kan ændre Domino-administratoren.

### <a name="not-supported"></a>Ikke understøttet
Følgende handlinger understøttes ikke af den aktuelle udgave af Lotus Domino forbindelsen:

- Flyt postkasse mellem servere.

## <a name="create-a-new-connector"></a>Oprette en ny forbindelse

### <a name="client-software-installation-and-configuration"></a>Klient softwareinstallation og konfiguration
Lotus Notes skal være installeret på den server **før** forbindelsen er installeret.

Når du installerer, skal du gøre en **Enkelt bruger installation**. Standard **Flere brugere installere** virker ikke.  
![Notes1](./media/active-directory-aadconnectsync-connector-domino/notes1.png)

Installer kun de nødvendige Lotus Notes-funktioner og **Klienten enkelt Logon**på siden funktioner på. Enkelt Logon er påkrævet for forbindelsen for at kunne logge på Domino-server.  
![Notes2](./media/active-directory-aadconnectsync-connector-domino/notes2.png)

**Note:** Start Lotus Notes én gang med en bruger, der er placeret på den samme server som den konto, du bruger som forbindelsens tjenestekonto. Også sørge for at lukke Lotus Notes-klienten på serveren. Det kan ikke køre samtidig forbindelsen forsøger at oprette forbindelse til Domino-server.

### <a name="create-connector"></a>Oprette forbindelse
Vælg **Administration Agent** og **oprette**for at oprette en Lotus Domino-forbindelse, i **Tjenesten Brugerprofilsynkronisering** . Markér forbindelsen **Lotus Domino (Microsoft)** .  
![CreateConnector](./media/active-directory-aadconnectsync-connector-domino/createconnector.png)

Hvis din version af tjenesten Brugerprofilsynkronisering giver mulighed for at konfigurere **arkitektur**, skal du kontrollere forbindelsen er indstillet til standardværdien til at køre **processen**.

### <a name="connectivity"></a>Forbindelse
På siden Connectivity, skal du angive servernavnet Lotus Domino og angive legitimationsoplysningerne.  
![Forbindelse](./media/active-directory-aadconnectsync-connector-domino/connectivity.png)

Egenskaben Domino-Server understøtter to formater til navnet på server:

- Servernavn
- Servernavn/DirectoryName

**Servernavn/DirectoryName** formatet er det foretrukne format for denne attribut, fordi det giver hurtigere svar, når forbindelsen kontakter Domino-Server.

Den angivne bruger-id-filen er gemt i konfigurationsdatabase af tjenesten til synkronisering.

Du har følgende indstillinger for **Delta Import** :

- **Ingen**. Forbindelsen ikke nogen delta import.
- **Tilføje eller opdatere**. Forbindelsen bruges delta importen kan tilføje og opdatere handlinger. En **Fuldstændig Import** -handling er påkrævet til Slet. Denne handling bruger .net-kompatibilitet.
- **Tilføj/Opdater/Slet**. Forbindelsen bruges delta importen tilføje, opdatere og slette handlinger. Denne handling bruger de oprindelige C++-grænseflader.

I **Skemaet indstillinger** har du følgende indstillinger:

- Som **standard skemaet**. Forbindelsen registrerer skemaet fra Domino-server. Dette er standardindstillingen.
- **DSML-skema**. Bruges kun, hvis Domino-serveren ikke vist i skemaet. Du kan derefter oprette en DSML-fil med skemaet og importere den i stedet. Du kan finde flere oplysninger om DSML, [OASIS](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=dsml).

Når du klikker på Næste, kontrolleres de bruger-id og din adgangskode konfiguration af parametre.

### <a name="global-parameters"></a>Globale parametre
På siden globale parametre skal du konfigurere en tidszone og importen og handlingen indstillingen Eksportér.  
![Globale parametre](./media/active-directory-aadconnectsync-connector-domino/globalparameters.png)

Parameteren **Domino serverens tidszone** definerer placeringen af din Domino-Server.

Indstillingen for konfiguration er påkrævet til at understøtte **delta importere** handlinger, da det kan du synkronisere tjeneste afgøre ændringer mellem de to sidste importen.

#### <a name="import-settings-method"></a>Importindstillingerne, metode
**Udføre fuld Import af** har disse indstillinger:

- Søgning
- Visning (anbefales)

**Søgning** bruger indeksering i Domino, men det er almindelige, indeksene ikke opdateres i realtid og data, der returneres fra serveren er ikke altid korrekt. For et system med mange ændringer denne indstilling normalt fungerer ikke godt og giver falsk sletter i nogle situationer. **Søgning** er hurtigere end **visning**.

**Visning** er den anbefalede indstilling, fordi den indeholder den korrekte tilstand af data. Det er en smule langsommere end søgning.

#### <a name="creation-of-virtual-contact-objects"></a>Oprettelse af virtuelle Kontaktobjekter
Den **aktiveres oprettelse af \_Kontakt objekt** har disse indstillinger:

- Ingen
- Ikke-Reference værdier
- Oversigt og ikke-Reference værdier

I Domino, kan referenceattributter indeholde mange forskellige formater for at referere til andre objekter. Lov til at repræsentere forskellige variationer, Connector implementerer \_kontakte objekter, også kaldet **Virtuelle kontakter** (VC). Disse objekter er oprettet, så de kan deltage til eksisterende MV objekter eller forventet som nye objekter. På denne måde kan attribut referencer bevares.

Ved at aktivere denne indstilling, og hvis indholdet af en referenceattribut ikke er et DN format, en \_Kontakt objekt er oprettet. Attributten medlem af en gruppe kan for eksempel indeholder SMTP-adresser. Det er også muligt at have forkortede navn og andre attributter, der findes i referenceattributter. I dette scenarie skal du markere **Ikke-Reference værdier**. Denne konfiguration er den mest almindelige indstilling for Domino-installationer.

Når Lotus Domino er konfigureret til at få separat adressekartoteker med forskellige entydige navne, der repræsenterer det samme objekt, du kan også oprette \_Kontakt objekter for alle referenceværdier, der findes i et adressekartotek. I dette scenarie skal du vælge indstillingen **Reference og ikke-Reference værdier** .

Hvis du har flere værdier i **fuldt navn** i Domino-attributten, derefter vil du også aktivere oprettelse af virtuelle kontakter, så du kan rette referencer. Denne attribut kan for eksempel har flere værdier efter en ægteskab eller skilsmisse. Markér afkrydsningsfeltet **Aktivér... Fuldt navn har flere værdier** til dette scenario.

Ved at sammenkæde på de korrekte attributter på \_Kontakt objekter skal være medlem af objektet MV.

Disse objekter har VC =\_kontaktperson føjes til deres DN.

#### <a name="import-settings-conflict-object"></a>Importere indstillinger, er i konflikt objekt
**Udelade konfliktobjekt**

I en stor Domino-implementering er det muligt, at flere objekter har det samme DN på grund af problemer med gentagelse. I disse tilfælde vil forbindelsen se to objekter med forskellige UniversalIDs, men samme DN. Denne konflikt medfører en midlertidig objekt, som oprettes i connector-plads. Forbindelsen kan ignorere de objekter, der er valgt i Domino som gentagelse ofre. Det anbefales, at holde dette afkrydsningsfelt, der er markeret.

#### <a name="export-settings"></a>Eksportere indstillinger
Hvis indstillingen **Brug AdminP for at opdatere referencer** er valgt, derefter eksport af referenceattributter, som medlem er et direkte opkald og bruger ikke AdminP processen. Brug kun denne indstilling, når AdminP ikke er konfigureret til at bevare referentiel integritet.

#### <a name="routing-information"></a>Routing-oplysninger
I Domino er det muligt, at en referenceattribut har routing-integreret som et suffiks til DN oplysninger. For eksempel attributten medlem i en gruppe kan indeholde **CN=example/organization@ABC**. Suffikset @ABC er routing-oplysninger. Routing-oplysninger bruges af Domino til at sende mails til korrekte Domino-systemet, hvilket kan være et system i en anden organisation. I feltet Routing Information, kan du angive de routing suffikser, der bruges i organisationen i omfanget af forbindelsen. Hvis en af følgende værdier findes som et suffiks i en referenceattribut, fjernes routing-oplysninger fra referencen. Hvis routing suffikset i en referenceværdi ikke passer til en af disse værdier, der er angivet, en \_Kontakt objekt er oprettet. Disse \_Kontakt objekter er oprettet med ** RO=@ ** indsat i DN. For disse \_Kontakt objekter følgende attributter føjes også kunne tilmelde sig til et reelt tal objekt, hvis det er nødvendigt: \_routingName, \_kontaktperson, \_vist navn, og UniversalID.

#### <a name="additional-address-books"></a>Yderligere adressekartoteker
Hvis du ikke har **directory Hjælp** installeret, som indeholder navnet på sekundær adressekartoteker, kan du manuelt angive disse adressekartoteker.

#### <a name="multivalued-transformation"></a>Med flere værdier Transformation
Mange attributter i Lotus Domino er med flere værdier. De tilsvarende metaverse attributter er typisk enkelt valgmuligheder. Ved at konfigurere Import og eksport handlingen indstillingen kan aktivere du forbindelsen til at hjælpe med påkrævet oversættelsen af de pågældende attributter.

**Eksportere**  
Indstillingen Eksportér handlingen understøtter to tilstande:

- Tilføje element
- Erstatte element

**Erstat element** – når du vælger denne indstilling, forbindelsen altid fjerne de aktuelle værdier for attributten i Domino og erstatte dem med de angivne værdier. Den angivne værdi kan være enten enkeltværdi- eller med flere værdier.

Eksempel: Attributten assistent for en person objekt har følgende værdier:

- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Hvis en ny assistent med navnet **David Alexander** er tildelt til denne person-objekt, er resultatet:

- CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf

**Tilføje element** – når du vælger denne indstilling, forbindelsen bevarer de eksisterende værdier på attributten i Domino og Indsæt nye værdier øverst på listen.

Eksempel: Attributten assistent for en person objekt har følgende værdier:

- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Hvis en ny assistent med navnet **David Alexander** er tildelt til denne person-objekt, er resultatet:

- CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

**Importér**  
Indstillingen Importér handlingen understøtter to tilstande:

- Standard
- Med flere værdier til enkelt værdi

**Standard** – når du vælger indstillingen standard, alle værdier over alle attributterne er importeret.

**Multivalued til enkeltværdi** – når du vælger denne indstilling, attributten med flere værdier konverteres til en enkeltværdi-attribut. Hvis der findes mere end én værdi, bruges værdien øverst (denne værdi er typisk også sidste).

Eksempel: Attributten assistent for en person objekt har følgende værdier:

- CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Den seneste opdatering til denne attribut er **David Alexander**. Da indstillingen Importér handlingen er indstillet til Multivalued til enkeltværdi, importerer forbindelse kun **David Alexander** i connector-plads.

Logik til at konvertere med flere valgmuligheder attributter til enkeltværdi-attributter gælder ikke, at attributten gruppe medlem og attributten person fuldt navn.

Det også muligt at konfigurere Importér og Eksportér transformation regler for med flere værdier attributter per attribut, som en undtagelse til den globale regel. For at konfigurere denne indstilling skal du angive [objekttype]. [attributename] i tekstfelterne **importere udelukkelse attribut liste** og **eksportere udelukkelse attribut liste** . Eksempelvis hvis du angiver Person.Assistant og flaget globale er indstillet til at importere alle værdier, importeres kun den første værdi for assistant.

#### <a name="certifiers"></a>Certifiers
Alle organisation/organisatorisk enheder findes via forbindelsen. En certifier med adgangskoden er påkrævet for at kunne eksportere person objekter til den primære adressekartotek.

Hvis alle certifiers har den samme adgangskode, kan **adgangskode for alle Certifers** bruges. Du kan derefter indtaste adgangskoden her og kun angive filen certifier.

Hvis du kun har importeret, derefter behøver du ikke til at angive en hvilken som helst certifiers.

### <a name="configure-provisioning-hierarchy"></a>Konfigurere klargøring hierarki
Når du konfigurerer Lotus Domino-forbindelsen, kan du springe denne dialogboksside over. Lotus Domino forbindelsen understøtter ikke hierarki klargøring.  
![Klargøring af hierarki](./media/active-directory-aadconnectsync-connector-domino/provisioninghierarchy.png)

### <a name="configure-partitions-and-hierarchies"></a>Konfigurere partitioner og hierarkier
Når du konfigurerer partitioner og hierarkier, skal du vælge kaldet NAB=names.nsf primære adressekartoteket. Ud over den primære adressekartotek, kan du vælge sekundær adressekartoteker, hvis de findes.  
![Partitioner](./media/active-directory-aadconnectsync-connector-domino/partitions.png)

### <a name="select-attributes"></a>Vælg attributter
Når du konfigurerer dine attributter, skal du vælge alle attributter, er præfikset ** \_MMS\_**. Følgende attributter er påkrævet, når du klargør nye objekter til Lotus Domino

![Attributter](./media/active-directory-aadconnectsync-connector-domino/attributes.png)

## <a name="object-lifecycle-management"></a>Objekt Livscyklusadministration
Dette afsnit indeholder en oversigt over de forskellige objekter i Domino.

### <a name="person-objects"></a>Person objekter
Objektet person repræsenterer brugere i organisationen og organisationsenheder. Ud over standardegenskaberne, kan Domino-administrator tilføje brugerdefinerede attributter til en Person objekt. En Person objekt skal som minimum omfatter alle obligatoriske attributter. Du kan finde en komplet liste over obligatoriske attributter [Lotus Notes egenskaber](#lotus-notes-properties). Hvis du vil registrere en person-objekt, skal være opfyldt følgende forudsætninger:

- Adressekartoteket (names.nsf) skal der er defineret, og de skal være primære adressekartoteket.
- Du skal have O/OU certifier Id og adgangskoden til at registrere en bestemt bruger i organisationen / organisationsenhed.
- Du skal angive et bestemt sæt Lotus Notes egenskaberne for en person objekt. Disse egenskaber bruges til klargøring af objektet person. Du kan finde flere oplysninger i afsnittet kaldet [Lotus Notes egenskaber](#lotus-notes-properties) senere i dette dokument.
- Indledende HTTP adgangskoden til en person er en egenskab og angive under klargøringen.
- Objektet person skal være en af følgende tre understøttede typer:
    1. Normal bruger, der indeholder en e-mail-fil og en bruger-id-fil
    2. Global bruger (en Normal bruger, der indeholder alle globale databasefiler)
    3. Kontakter (bruger med nogen id-fil)

Personer (undtagen kontakter) kan yderligere grupperes i OS-brugere og International brugere, som defineret af værdien af den \_MMS\_IDRegType egenskab. Disse personer bruger noter klienten til at få adgang til Lotus Domino-servere har et noter-Id og en Person-dokument. Hvis de bruger noter mail, derefter have de også en e-mail-fil. Brugeren skal være registreret for at blive aktive. Du kan finde flere oplysninger i:

- [Konfiguration af Notes-brugere](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_SETTING_UP_NOTES_USERS.html)
- [Brugerregistrering](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_REGISTERING_USERS.html)
- [Administrere brugere](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_USERS_5151.html)
- [Omdøbe brugere](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_USER_AUTOMATICALLY.html)

Alle disse handlinger er udført i Lotus Domino og derefter importeres til tjenesten til synkronisering.

### <a name="resources-and-rooms"></a>Rum og ressourcer
En ressource er en anden type af en database i Lotus Domino. Ressourcer kan være mødelokalerne med forskellige typer udstyr, f.eks. Der er undertyper af ressourcer, der understøttes af Lotus Domino forbindelse, der er defineret af egenskaben ressourcetype:

Ressourcetype | Ressourceattribut Type
--- | ---
Rum | 1
Ressource (andre) | 2
Onlinemøde | 3

For objektet ressourcetype til at arbejde er følgende påkrævet:

- Ressource-database til Reservation skal allerede findes i den tilsluttede Domino-server
- Webstedet er allerede defineret for ressourcen

Ressourcereservation databasen indeholder tre typer af dokumenter:

- Websted-profil
- Ressource
- Reservation

Du kan finde flere oplysninger om oprettelse af ressourcereservation database [konfigurationen af den ressource reservation database](https://www-01.ibm.com/support/knowledgecenter/SSKTMJ_8.0.1/com.ibm.help.domino.admin.doc/DOC/H_SETTING_UP_THE_RESOURCE_RESERVATIONS_DATABASE.html).

**Oprette, opdatere, og Slet ressourcer**  
Opret, Opdater og Slet handlinger er udført af Lotus Domino forbindelsen i ressourcereservation databasen. Ressourcer oprettes som dokumenter i Names.nsf (det vil sige, den primære adressekartotek). Se [redigere og slette ressource dokumenter](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_EDITING_AND_DELETING_RESOURCE_DOCUMENTS.html)kan finde flere oplysninger om redigerer og sletter ressourcer.

**Import og eksport for ressourcer**  
Ressourcerne, der kan importeres til og eksporteres fra tjenesten til synkronisering, som andre objekttype. Vælg objekttypen som ressource under konfigurationen. Du bør have detaljer for ressourcetype, konference Database og navn på websted vellykket eksporten.

### <a name="mail-in-databases"></a>Mail-In-databaser
En Mail-In-Database er en database, der er udviklet til at modtage mails. Det er en Lotus Domino-postkasse, der ikke er knyttet til en bestemt Lotus Domino-brugerkonto (det vil sige, den ikke har et nyt id-fil og din adgangskode). En post i databasen har et entydigt bruger-id ("kort navn") er knyttet til den og sin egen e-mail-adresse.

Hvis der er behov for en separat postkasse med sin egen e-mail-adresse, der kan deles af forskellige brugere (for eksempel group@contoso.com), en mail i databasen er oprettet. Adgang til denne postkasse styres gennem dens liste ACL (Access Control), som indeholder navnene på de brugere, noter, der har tilladelse til at åbne postkassen.

En liste over de nødvendige attributter, i afsnittet kaldet [Obligatoriske attributter](#mandatory-attributes) senere i denne artikel.

Når en database er udviklet til at modtage en mail, der oprettes et Mail-In Database dokument i Lotus Domino. Dette dokument skal findes i Domino mappe med alle servere, der gemmer en kopi af databasen. Have en detaljeret beskrivelse om at oprette en mail i databasen dokument skal du se [oprette et dokument på Mail i databasen](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_A_MAILIN_DATABASE_DOCUMENT_FOR_A_NEW_DATABASE_OVERVIEW.html).

Før du opretter en Mail i Database, skal allerede findes i databasen (skulle have været oprettet af Lotus administratoren) på Domino-server.

### <a name="group-management"></a>Gruppeadministration af
Du kan få et detaljeret overblik over gruppeadministration Lotus Domino fra følgende ressourcer:

- [Brug af grupper](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)
- [Oprette en gruppe](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS_MIDTOPIC_55038956829238418.html)
- [Oprette og redigere grupper](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS.html)
- [Administrere grupper](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_GROUPS_1804.html)
- [Omdøbe en gruppe](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_GROUP_STEPS.html)

### <a name="password-management"></a>Administration af adgangskoder
En registreret Lotus Domino-bruger er der to typer af adgangskoder:

1. Brugeradgangskode (gemt i User.id-fil)
2. Internet / HTTP-adgangskode

Lotus Domino forbindelsen understøtter kun handlinger med HTTP-adgangskode.

For at udføre administration af adgangskoder, skal du aktivere Administration af adgangskoder for forbindelsen i Management Agent Designer. Vælg **Aktivér administration af adgangskoder** på siden **Konfigurer filtypenavne** dialogboksen for at aktivere Administration af adgangskoder.  
![Konfigurere filtypenavne](./media/active-directory-aadconnectsync-connector-domino/configureextensions.png)

Lotus Domino forbindelse support efter handlinger på internettet adgangskode:

- Angiv adgangskode: Angiv adgangskode Angiver en ny HTTP/Internet adgangskode på brugeren i Domino. Kontoen er som standard også låses op. Flaget unlock vises på WMI-grænsefladen for synkronisering af programmet.
- Skift adgangskode: I dette scenarie skal en bruger kan vil ændre adgangskoden eller bedt om at ændre adgangskode efter et angivet tidsrum. Denne handling kan tage placere, både (gammelt og den nye adgangskode) er obligatorisk. Når ændret, opdateres den nye adgangskode i Lotus Domino.

Du kan finde flere oplysninger i:

- [Ved hjælp af funktionen Internet låsning](http://www.ibm.com/developerworks/lotus/library/domino8-lockout/)
- [Administrere adgangskoder til internettet](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_NOTES_AND_INTERNET_PASSWORD_SYNCHRONIZATION_7570_OVER.html)

## <a name="reference-information"></a>Referenceoplysninger
Dette afsnit beskrives som attributten beskrivelser og attributten krav til Lotus Domino forbindelsen.

### <a name="lotus-notes-properties"></a>Lotus Notes-egenskaber
Når du klargør Person objekter til adresselisten Lotus Domino, skal din objekter har et bestemt sæt af egenskaber med specifikke værdier, der er udfyldt. Disse værdier er kun påkrævet til opretter operationer.

Den følgende tabel viser disse egenskaber og indeholder en beskrivelse af dem.

Egenskaben | Beskrivelse
--- | ---
\_MMS_AltFullName | Det alternative fulde navn på bruger.
\_MMS_AltFullNameLanguage | Sproget, der skal bruges til at angive det alternative fulde navn på bruger.
\_MMS_CertDaysToExpire | Antallet af dage fra dags dato før certifikatet udløber. Hvis du ikke er angivet, er standardformatet for datoen to år fra dags dato.
\_MMS_Certifier | Egenskab, der indeholder navnet på certifier organisatorisk hierarki. Eksempel: OU = OrganizationUnit, O = organisationsdiagram, C = land.
\_MMS_IDPath | Hvis egenskaben er tom, oprettes ingen bruger-id-filer lokalt på synkronisering af serveren. Hvis egenskaben indeholder et filnavn, oprettes en bruger-ID-fil i mappen madata. Egenskaben kan også indeholde den fulde sti.
\_MMS_IDRegType | Personer kan klassificeres som kontakter, OS brugere og internationale brugere. I følgende tabel vises de mulige værdier: <li>0 - Kontakt</li><li>1 – USA bruger</li><li>2 – internationale bruger</li>
\_MMS_IDStoreType | Påkrævet egenskab for USA og internationale brugere. Egenskaben indeholder en heltalsværdi, der angiver, om bruger-id'et er gemt som en vedhæftet fil i adressekartoteket noter eller i den pågældende persons mail-fil. Hvis bruger-ID-filen er en vedhæftet fil i adressekartoteket, det kan eventuelt oprettes som en fil med \_MMS_IDPath. <li>Tøm - ID for Store filer i ID samling, ingen id-fil (bruges til kontakter).</li><li> 1 – vedhæftet fil i adressekartoteket noter. Den \_MMS_Password egenskaben være indstillet til bruger-id-filer, der er vedhæftede filer</li><li>2 – store-ID i persons Mail-fil. Den \_MMS_UseAdminP skal indstilles til falsk at lade filen mail oprettes ved Person registrering. Den \_MMS_Password egenskaben være indstillet til bruger-id-filer.</li>
\_MMS_MailQuotaSizeLimit | Antallet megabyte, der er tilladt for databasen e-mail-fil.
\_MMS_MailQuotaWarningThreshold | Antallet megabyte, der er tilladt for e-mail-fil databasen, før en advarsel.
\_MMS_MailTemplateName | Den e-mail-skabelonfil, der bruges til at oprette brugerens e-mail-fil. Hvis der er angivet en skabelon, oprettes filen mail ved hjælp af den angivne skabelon. Hvis ingen skabelon ikke er angivet, bruges standard-skabelonfilen til at oprette filen.
\_MMS_OU | Valgfri egenskab, der er navnet på OU under certifier. Denne egenskab skal være tomt for kontakter.
\_MMS_Password | Påkrævet egenskab for brugere. Egenskaben indeholder adgangskoden til filen identifikation af objektet.
\_MMS_UseAdminP | Egenskaben skal være indstillet til sand, hvis filen mail skal der oprettes af processen AdminP på Domino-server (asynkron til eksporten). Hvis egenskaben er indstillet til falsk, er mail-fil oprettet med Domino brugeren (synkron i eksporten).

For en bruger med en tilknyttet id-fil på \_MMS_Password egenskab skal indeholde en værdi. E-mail-adgang via Lotus Notes-klienten, skal MailServer og MailFile egenskaberne for en bruger indeholde en værdi.

Få adgang til mail via en webbrowser, skal følgende egenskaber indeholder værdier:

- MailFile - påkrævet egenskab, der indeholder sti på den Lotus Domino-server, hvor mail-fil er gemt.
- MailServer - påkrævet egenskab, der indeholder navnet på Lotus Domino-serveren. Denne værdi er navnet, der skal bruges, når du har oprettet filen Lotus mail på Domino-server.
- HTTPPassword - valgfri egenskab, der indeholder access Web adgangskoden til objektet.

Egenskaben HTTPPassword skal indeholde en værdi for at få adgang til Domino-Server uden mail egenskab. Egenskaben MailFile og egenskaben MailServer kan være tomme.

Med \_MMS_ IDStoreType = 2 (store id i Mail-fil) egenskaben MailSystem for NotesRegistrationclass er indstillet til REG_MAILSYSTEM_INOTES (3).

### <a name="mandatory-attributes"></a>Obligatoriske attributter
Lotus Domino forbindelsen understøtter hovedsageligt disse typer objekter (dokumenttyper):

- Gruppe
- Mail i databasen
- Person
- Kontakt (Person med ingen certifier)
- Ressource

Dette afsnit beskrives de attributter, der er obligatorisk for hvert understøttede objekt til at eksportere til en Domino-server.

Objekttype | Obligatoriske attributter
--- | ---
Gruppe | <li>Listenavn</li>
Hovedvinduet i databasen | <li>Fuldt navn</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li>
Person | <li>Efternavn</li><li>MailFile</li><li>Forkortede navn</li><li>\_MMS_Password</li><li>\_MMS_IDStoreType</li><li>\_MMS_Certifier</li><li>\_MMS_IDRegType</li><li>\_MMS_UseAdminP</li>
Kontakt (Person med ingen certifier) | <li>\_MMS_IDRegType</li>
Ressource | <li>Fuldt navn</li><li>Ressourcetypen</li><li>ConfDB</li><li>Ressourcekapacitet</li><li>Websted</li><li>Vist navn</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li>

## <a name="common-issues-and-questions"></a>Almindelige problemer og spørgsmål

### <a name="schema-detection-does-not-work"></a>Skema registrering virker ikke
Hvis du vil kunne registrere skemaet, er det nødvendigt, at filen schema.nsf findes på Domino-serveren. Denne fil vises kun, hvis LDAP er installeret på serveren. Hvis skemaet ikke kan registreres, skal du kontrollere følgende:

- Filen schema.nsf findes på rodmappen i Domino-Server
- Brugeren har tilladelse til at se filen schema.nsf.
- Tving genstart af LDAP-serveren. Åbn **Lotus Domino Console** og bruge **Fortæl LDAP-ReloadSchema** kommando til at genindlæse i skemaet.

### <a name="not-all-secondary-address-books-are-visible"></a>Ikke alle sekundær adressekartoteker er synlige
Domino forbindelsen er afhængig af funktionen **Directory Hjælp** lov til at finde de sekundære adressekartoteker. Hvis de sekundære adressekartoteker mangler, skal du kontrollere Hvis [Directory Hjælp](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_DIRECTORY_ASSISTANCE.html) er aktiveret og konfigureret på Domino-serveren.

### <a name="custom-attributes-in-domino"></a>Brugerdefinerede attributter i Domino
Der er flere måder i Domino at udvide skemaet, så den vises som en brugerdefineret attribut forbrugsvarer via forbindelsen.

**Metode 1: Udvide Lotus Domino skema**

1. Oprette en kopi af Domino Directory skabelon {PUBNAMES. NTF} ved følgende [følgende](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (du ikke skal tilpasse IBM Lotus Domino standardmappen skabelonen):
2. Åbn kopi af Domino directory skabelonen {CONTOSO. Skabelon til NTF}, der er oprettet i Domino Designer og følge disse trin:
    - Klik på delte elementer og udvide underformularer
    - Dobbeltklik på ${objektnavn} InheritableSchema underformular (hvor {objektnavn} er navnet på standardklassen strukturel object for eksempel: Person).
    - Navngiv attribut, du vil føje til skemaet {MyPersonAtrribute} og tilsvarende til attributten. Oprette et felt ved at vælge menuen **Opret** , og vælg derefter **felt** fra menuen.
    - I feltet tilføjede skal du angive dens egenskaber ved at vælge filtype, typografi, størrelse, skrifttype og andre relaterede parametre på felt egenskabsvinduet.
    - Holde egenskaben standardværdi samme som navnet for attributten (For eksempel hvis attributnavn er MyPersonAttribute, lade standardværdien med samme navn).
    - Gem ${objektnavn} InheritableSchema underformularen med opdaterede værdier.
3. Erstatte skabelonen Domino Directory {PUBNAMES. NTF} med den nye brugerdefinerede skabelon {CONTOSO. NTF} ved følgende [disse trin](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
4. Luk Domino administrator, og Åbn Domino Console at genstarte LDAP-tjenesten og for at genindlæse LDAP-skemaet:
    - Indsæt kommandoen under **Domino kommandoen** tekst arkiveret for at genstarte tjenesten LDAP - [Genstart opgave LDAP]( http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html)Domino-konsollen.
    - For at genindlæse LDAP skema kommandoen Fortæl LDAP - Fortæl LDAP-ReloadSchema
5. Åbn Domino Admin og under fanen Vælg personer og grupper for at se tilføjede attribut afspejles i domino Tilføj Person.
6. Åbne Schema.nsf fra fanen **filer** , og se tilføjede attribut afspejles i dominoPerson LDAP-objektklasse.

**Metode 2: Oprette en auxClass med brugerdefinerede attributter og knytte til Objektklassen**

1. Oprette en kopi af Domino Directory skabelon {PUBNAMES. NTF} ved følgende [disse trin](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (aldrig tilpasse IBM Lotus Domino standardmappen skabelon):
2. Åbn kopi af Domino directory skabelonen {CONTOSO. NTF} skabelon, der er oprettet i Domino Designer.
3. Vælg delt kode og derefter underformularer i venstre rude.
4. Klik på ny underformular
5. Gør følgende for at angive egenskaberne for den nye underformular:
    - Med den nye underformular Åbn, og vælg Design - underformular egenskaber
    - Angiv et navn for Objektklassen ekstra – for eksempel TestSubform ud for egenskaben Name.
    - Holde egenskaben indstillinger "Medtag i Indsæt underformular... dialogboksen" er markeret
    - Fjern markeringen af indstillinger for egenskaben "Gengiv passet gennem HTML i noter."
    - Lade de andre egenskaber på samme måde, og Luk i underformularen egenskaber.
    - Gem og Luk nye underformularen.
6. Gør følgende for at tilføje et felt for at definere klassen ekstra object:
    - Åbn den underformular, du har oprettet.
    - Vælg Opret - felt.
    - Ud for navn under fanen grundlæggende funktioner i dialogboksen felt, angive et navn, for eksempel: {MyPersonTestAttribute}.
    - I feltet tilføjede skal du angive dens egenskaber ved at vælge den Type, typografi, størrelse, skrifttype og relaterede egenskaber.
    - Holde egenskaben standardværdi samme som navnet for attributten (For eksempel hvis attributnavn er MyPersonTestAttribute, lade standardværdien med samme navn).
    - Gemme underformularen med opdaterede værdier og gøre følgende:
        - I venstre rude skal du vælge delt kode og derefter underformularer
        - Vælg ny underformularen, og vælg Design - Designegenskaber.
        - Klik på fanen tredje fra venstre, og vælg **Overfør denne forbyde design ændre**.
7. Åbn ${objektnavn} ExtensibleSchema underformular, (hvor {objektnavn} er navnet på standard strukturel objekt klassen, for eksempel – Person).
8. Indsæt ressource, og vælg underformularen (som du har oprettet, for eksempel – TestSubform), og Gem ${objektnavn} ExtensibleSchema underformularen.
9. Erstatte skabelonen Domino Directory {PUBNAMES. NTF} med den nye brugerdefinerede skabelon {CONTOSO. NTF} ved følgende [disse trin](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
10. Luk Domino administrator, og Åbn Domino Console at genstarte LDAP-tjenesten og for at genindlæse LDAP-skemaet:
    - Indsæt kommandoen under **Domino kommandoen** tekst arkiveret for at genstarte tjenesten LDAP - [Genstart opgave LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html)Domino-konsollen.
    - For at genindlæse LDAP Brug skema Fortæl LDAP-kommandoen **Fortæl LDAP-ReloadSchema**.
11. Åbn Domino administrator, og Vælg personer og grupper fane for at se tilføjede attribut afspejles i domino Tilføj Person (under andre fanen).
12. Åbne Schema.nsf fra fanen **filer** , og se tilføjede attribut afspejles under TestSubform LDAP ekstra objektklasse.

**Metode 3: Føje brugerdefinerede attributter til klassen ExtensibleObject**

1. Åbn {Schema.nsf}-fil, som anbringes på rodmappen
2. Markere LDAP-objekt klasser fra menuen til venstre under **Alle skema dokumenter** og klikke på **Tilføj objekt klasse** :
3. Angive LDAP-navn i form af {zzzExtensibleSchema} (hvor zzz er navnet på standard strukturel objekt klassen, for eksempel Person). For eksempel for at udvide skemaet for Person objektklasse, angive LDAP-navn {PersonExtensibleSchema}.
4. Give overordnet objekt klassenavn, som du vil udvide skemaet. For eksempel indeholder overordnede objekt klassenavnet {dominoPerson} for at udvide skema Person objekt klassen:
5. Angiv et gyldigt objekt-id, der svarer til Objektklassen.
6. Vælg udvidet/brugerdefinerede attributter under obligatorisk eller valgfrit attributtyper felter ud fra kravet:
7. Når du har tilføjet de nødvendige attributter til ExtensibleObjectClass, skal du klikke på **Gem og Luk**.
8. Der oprettes en ExtensibleObjectClass respektive standard objekt klassen med udvidede attributter.

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

-   Du kan finde oplysninger om, hvordan du aktiverer logføring til at foretage fejlfinding af forbindelsen, [hvordan du kan aktivere ETW sporing for forbindelser](http://go.microsoft.com/fwlink/?LinkId=335731).
