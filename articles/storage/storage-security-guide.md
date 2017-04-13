<properties
    pageTitle="Azure lagerplads sikkerhedsvejledning | Microsoft Azure"
    description="Oplysninger om de mange metoder til at sikre Azure-lager, herunder, men ikke begrænset til RBAC, lagerplads Service kryptering, klientsiden kryptering, små og mellemstore virksomheder 3.0 og Azure diskplads kryptering."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="robinsh"/>

#<a name="azure-storage-security-guide"></a>Azure lagerplads sikkerhedsvejledning

##<a name="overview"></a>Oversigt

Azure-lager indeholder et omfattende sæt af sikkerhedsfunktioner hvorved sammen udviklere til at oprette sikker programmer. Kontoen lagerplads selve kan være fastgjort ved hjælp af rollebaseret adgangskontrol og Azure Active Directory. Data kan være fastgjort undervejs mellem et program og Azure ved hjælp af [Kryptering på klientsiden](storage-client-side-encryption.md), HTTPS eller små og mellemstore virksomheder 3.0. Data kan indstilles til krypteres automatisk, når skrevet til Azure-lager ved hjælp af [Lagerplads Service kryptering (SSE)](storage-service-encryption.md). OS og Data diske, der bruges af virtuelle maskiner kan indstilles til krypteres med [Azure Disk kryptering](../security/azure-security-disk-encryption.md). Delegeret adgang til dataobjekter i Azure-lager kan tildeles ved hjælp af [Delt Access signaturer](storage-dotnet-shared-access-signature-part-1.md).

I denne artikel giver et overblik over hver af disse funktioner, der kan bruges med Azure-lager. Leveres links til artikler, der giver oplysninger om hver funktion, så du kan nemt gøre yderligere oplysninger om hvert emne.

Her er emnerne, der skal gennemgås i denne artikel:

-   [Administration af plan sikkerhed](#management-plane-security) – sikring af kontoen lagerplads

    Administration af plan består af de ressourcer, der bruges til at administrere din lagerplads konto. I dette afsnit gennemgår vi om implementeringsmodel Azure ressourcestyring og hvordan du bruger rollebaseret adgangskontrol (RBAC) til at styre adgangen til dine konti på lagerplads. Vi taler også om administration af dine lagerplads konto nøgler og hvordan du kan gendanne dem.

-   [Data Plane sikkerhed](#data-plane-security) – sikring af adgang til dine Data

    I dette afsnit, vil vi se på tillade adgang til de faktiske data-objekter i din lagerplads-konto, som blob, filer, køer og tabeller, ved hjælp af delt Access signaturer og gemt Access politikker. Vi kommer ind på både service niveau SAS og konto niveau SAS. Vi ser også, hvordan du kan begrænse adgangen til et bestemt IP-adresse (eller område af IP-adresser), hvordan du kan begrænse den protokol, der bruges til HTTPS, og hvordan du tilbagekalde en delt Access signatur uden at vente på at udløbe.

-   [Kryptering undervejs](#encryption-in-transit)

    Dette afsnit beskrives, hvordan du sikrer data, når du overfører ind eller ud af Azure-lager. Gennemgår vi om anbefalede brug af HTTPS og den kryptering som små og mellemstore virksomheder 3.0 til Azure filshares. Vi kan også se nærmere på klientsiden kryptering, der gør det muligt at kryptere data, før det overføres til lager i en klientprogrammet og dekryptere dataene, når det er overført tør for lagerplads.

-   [Kryptering på resten](#encryption-at-rest)

    Vi vil tale om lagerplads Service-kryptering (SSE), og hvordan du kan aktivere den til en lagerplads-konto, hvilket resulterer i din Bloker blob, siden BLOB og tilføje BLOB krypteret der automatisk, når skrevet til Azure-lager. Vi ser også på hvordan du kan bruge Azure Disk kryptering og udforske grundlæggende forskellene og tilfælde af Disk kryptering kontra SSE kontra klientsiden kryptering. Vi ser kortvarigt på FIPS overholdelse af angivne standarder for amerikanske computere.

-   Brug af [Lagerplads Analytics](#storage-analytics) til at overvåge adgang Azure-lager

    Dette afsnit beskrives, hvordan du kan finde oplysninger i loggene lagerplads analytics på en anmodning. Vi se nærmere på reelle lagerplads analytics logdata og se, hvordan du skelne om anmodes med tasten lagerplads konto med en delt Access signatur eller anonymt, og om den vellykkede eller mislykkede.

-   [Aktivere browserbaserede kunder ved hjælp af CORS](#Cross-Origin-Resource-Sharing-CORS)

    Dette afsnit taler om, hvordan du tillade tværs origin ressourcedeling (CORS). Gennemgår vi om adgang på tværs af domæner, og hvordan du håndterer det med de CORS-funktioner, der er indbygget i Azure-lager.

##<a name="management-plane-security"></a>Administration af plan sikkerhed

Administration af plan består af handlinger, der påvirker kontoen lagerplads sig selv. For eksempel kan du oprette eller slette en lagerplads-konto, få en liste over lagerplads konti i et abonnement, hente tasterne lagerplads konto eller genoprette tasterne lagerplads konto.

Når du opretter en ny lagerplads-konto, kan du vælge en implementeringsmodel af Classic eller Ressourcestyring. Klassisk model for oprettelse af ressourcer i Azure giver kun adgang til abonnementet, og dernæst, kontoen lagerplads.

Denne vejledning fokuserer på den ressourcestyring model, som er den anbefalede måde til at oprette lagerplads konti. Med den ressourcestyring lagerplads konti i stedet for giver adgang til hele abonnementet, kan du styre adgang til et mere begrænset niveau på management-plan ved hjælp af rollebaseret adgangskontrol (RBAC).

###<a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Hvordan du sikrer kontoen lagerplads med rollebaseret adgangskontrol (RBAC)

Lad os tale om hvad RBAC er, og hvordan du kan bruge den. Hver Azure abonnement har en Azure Active Directory. Brugere, grupper og programmer fra mappen kan få adgang for at administrere ressourcer i Azure-abonnement, der bruger implementeringsmodel ressourcestyring. Dette kaldes rollebaseret adgangskontrol (RBAC). Du kan bruge [Azure-portalen](https://portal.azure.com/), [Azure CLI værktøjer](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md)eller [Azure lagerplads ressource udbyder REST API'er](https://msdn.microsoft.com/library/azure/mt163683.aspx)for at administrere denne adgang.

Med ressourcestyring modellen placerer du kontoen lager i en ressource gruppe og kontrollere adgangen til administration af plan af den bestemte lagerplads konto ved hjælp af Azure Active Directory. For eksempel kan du give bestemte brugere mulighed for at få adgang til tasterne lagerplads konto, mens andre brugere kan se oplysninger om kontoen lagerplads, men kan ikke få adgang til tasterne lagerplads konto.

####<a name="granting-access"></a>Give adgang

Der gives adgang ved at tildele den relevante RBAC rolle til brugere, grupper og programmer i området højre. Hvis du vil give adgang til det hele abonnement, skal tildele du en rolle på niveauet for abonnement. Du kan give adgang til alle ressourcerne i en ressourcegruppe ved at give tilladelser til selve ressourcegruppen. Du kan også tildele bestemte roller til bestemte ressourcer, som lager konti.

Her er de vigtigste punkter, du har brug for at vide om brug af RBAC til at få adgang til at håndteringen af en Azure-lager-konto:

-   Når du tildeler access, tildeler du som en rolle til den konto, du vil have adgang. Du kan kontrollere adgang til de handlinger, der bruges til at administrere kontoen lagerplads, men ikke på dataobjekter i kontoen. For eksempel kan du give tilladelse til at hente egenskaberne for kontoen lagerplads (såsom redundans), men ikke til en objektbeholder eller data i en objektbeholder i Blob-lager.

-   Du kan give dem tilladelse til at læse tasterne lagerplads konto til en person skal have tilladelse til at få adgang til data objekterne i kontoen lagerplads, og brugeren kan derefter bruge disse taster til at få adgang til BLOB, køer, tabeller og filer.

-   Roller kan tildeles til en bestemt brugerkonto, en gruppe af brugere eller til et bestemt program.

-   Hver rolle har en liste over handlinger og ikke handlinger. For eksempel rollen bidragyder virtuelt har en handling af "listKeys", der gør det muligt for tasterne lagerplads konto til at blive læst. Bidragyderen har "Ikke handlinger" som opdatering adgangen for brugere i Active Directory.

-   Roller for lagerplads omfatter (men ikke begrænset til) følgende:

    -   Ejer – de kan administrere alt, herunder adgang.

    -   Bidragyder – brugeren kan gøre noget ejeren kan med undtagelse af Tildel adgang. En person med denne rolle kan få vist og genoprette tasterne lagerplads konto. De kan åbne dataobjekter med tasterne lagerplads konto.

    -   Læser – de kan se oplysninger om kontoen lagerplads, undtagen hemmeligheder. Eksempelvis hvis du tildeler en rolle med Læsertilladelser på kontoen lagerplads til en person, de kan se egenskaberne for kontoen lagerplads, men de kan ikke foretage ændringer i egenskaberne eller få vist tasterne lagerplads konto.

    -   Lagerplads konto bidragyder – de kan administrere kontoen lager – de kan læse abonnementet grupper og ressourcer, og Opret og administrer abonnement ressource gruppe installationer. De kan også få adgang til tasterne lagerplads konto, hvilket også betyder, at de kan få adgang til dataplan.

    -   Access Brugeradministrator – de kan administrere brugeradgang til kontoen lagerplads. De kan for eksempel give læseradgang til en bestemt bruger.

    -   Virtuelt bidragyder – de kan administrere virtuelle maskiner, men ikke lagerplads-konto, som de er forbundet. Denne rolle kan angive tasterne lagerplads konto, hvilket betyder, at den bruger, du tildele denne rolle kan opdatere dataplan.

        Rækkefølge for en bruger til at oprette en virtuel maskine, skal de kunne oprette den tilsvarende Virtuelle-fil med en konto med lagerplads. For at gøre det, skal de kunne hente kontonøgle lager og sende dem til oprettelse af VM API. Derfor, de skal have denne tilladelse, så de kan få vist tasterne lagerplads konto.

- Mulighed for at definere brugerdefinerede roller er en funktion, der gør det muligt at oprette et sæt handlinger på en liste over tilgængelige handlinger, der kan udføres på Azure ressourcer.

- Brugeren skal konfigureres i din Azure Active Directory, før du kan tildele en rolle til dem.

- Du kan oprette en rapport over der gav/tilbagekaldt hvilken type adgang til/fra hvem og i det omfang, ved hjælp af PowerShell eller Azure CLI.

####<a name="resources"></a>Ressourcer

-   [Azure Active Directory rollebaseret adgangskontrol](../active-directory/role-based-access-control-configure.md)

    Denne artikel forklares det Azure Active Directory rollebaseret adgangskontrol, og hvordan det fungerer.

-   [RBAC: Indbygget i roller](../active-directory/role-based-access-built-in-roles.md)

    I denne artikel indeholder oplysninger om alle de indbyggede roller, der er tilgængelige i RBAC.

-   [Forstå ressourcestyring installation og klassisk installation](../resource-manager-deployment-model.md)

    I denne artikel beskrives de ressourcestyring installation og klassisk installation modeller og beskriver fordelene ved at bruge grupperne ressourcestyring og ressource

-   [Azure Beregn-, Netværks- og lagerplads udbydere under Azure ressourcestyring](../virtual-machines/virtual-machines-windows-compare-deployment-models.md)

    I denne artikel forklares, hvordan Azure beregne, netværk og lagerplads udbydere fungerer under ressourcestyring modellen.

-   [Administration af rollebaseret adgangskontrol med REST API'ER](../active-directory/role-based-access-control-manage-access-rest.md)

    I denne artikel viser, hvordan du bruger REST-API til at administrere RBAC.

-   [Azure-lager ressource udbyder RESTEN API Reference](https://msdn.microsoft.com/library/azure/mt163683.aspx)

    Dette er referencen til de API'er, du kan bruge til at administrere din lagerplads konto ved hjælp af programmering.

-   [Developer's guide til auth med Azure ressourcestyring API](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/)

    I denne artikel viser, hvordan du godkende ved hjælp af Ressourcestyring API'er.

-   [Rollebaseret adgangskontrol til Microsoft Azure fra Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Dette er et link til en video på kanal 9 fra 2015 MS Ignite konference. I denne session de taler om adgang til administration og rapporteringsfunktioner i Azure og udforske bedste metoder til sikring af adgang til Azure abonnementer ved hjælp af Azure Active Directory.

###<a name="managing-your-storage-account-keys"></a>Administrere dine lagerplads konto nøgler

Lagerplads konto taster er 512-bit strenge, der er oprettet af Azure, sammen med kontonavn lager kan bruges til at få adgang til de dataobjekter, der er gemt på kontoen lagerplads, f.eks. blob, enheder i en tabel, kø meddelelser og filer på en Azure filer del. Kontrol af adgang til lagerplads konto taster kontrolelementer adgang til dataplan for lagerplads kontoen.

Hver lagerplads konto har to nøgler kaldes "Nøgle 1" og "Primære 2" i [Azure portal](http://portal.azure.com/) og i PowerShell-cmdlet'er. Disse kan gendannes manuelt ved hjælp af en af flere metoder, herunder, men ikke begrænset til bruger [Azure-portalen](https://portal.azure.com/), PowerShell, Azure CLI, eller fra et program bruger .NET lagerplads klientbibliotek eller Azure lagerplads Services REST-API.

Der findes et vilkårligt antal grunde til at genoprette dine lagerplads konto nøgler.

-   Du kan genoprette dem med jævne mellemrum af sikkerhedsmæssige årsager.

-   Du vil genoprette dine lagerplads konto nøgler, hvis nogen administrerede dermed få i et program og hente nøglen, som blev hårdt eller gemt i en konfigurationsfil og give dem fuld adgang til kontoen lagerplads.

-   En anden bogstaver nøgler er Hvis dit team bruger et lager Explorer-program, der bevarer kontonøgle lagerplads, og en af gruppemedlemmerne forlader. Programmet vil fortsætte med at arbejde, giver dem adgang til kontoen lagerplads når de er til stede. Det er faktisk primære grunden til de oprettet konto niveau delt Access signaturer – du kan bruge en konto på brugerniveau SAS i stedet for at gemme hurtigtasterne i en konfigurationsfil.

####<a name="key-regeneration-plan"></a>Nøgler plan

Du vil ikke kun genoprette nøglen du bruger uden del planlægning. Hvis du gør dette, kan du klippe fra alle access pågældende lagerplads konto, der kan medføre overordnede afbrydelser. Dette er grunden til, at der er to nøgler. Du skal genoprette én tast ad gangen.

Inden du genoprette dine nøgler, skal du kontrollere, at du har en liste over alle dine programmer, der er afhængige af kontoen lagerplads samt andre tjenester, som du bruger i Azure. Eksempelvis hvis du bruger Azure Media Services, der er afhængige af din lagerplads konto, skal du igen synkronisere hurtigtasterne med din medier-tjeneste efter at du genoprette nøglen. Hvis du bruger en hvilken som helst programmer som en lagerplads explorer, skal du angive de nye nøgler til disse programmer samt. Bemærk, at hvis du har FOS Hvis Virtuelle filer gemmes i kontoen lagerplads, de ikke påvirkes ved at genoprette tasterne lagerplads konto.

Du kan genoprette dine nøgler i portalen Azure. Når taster genoprettes kan de gå op til 10 minutter til at blive synkroniseret på tværs af lagerplads tjenester.

Når du er klar, er her den generelle proces, der beskriver, hvordan du skal ændre din nøgle. I dette tilfælde antages det, du aktuelt bruger nøgle 1, og du vil ændre alt for at bruge tasten 2 i stedet.

1.  Genoprette nøglen 2 for at sikre, at det er sikkert. Du kan gøre dette i portalen Azure.

2.  Ændre lagerplads nøgle for at bruge tasten 2 ny værdi i alle de programmer, hvor tasten lagerplads er gemt. Teste og publicere programmet.

3.  Når alle programmer og tjenester er op og kører korrekt, genoprette nøglen 1. Dette sikrer, at alle, du ikke har udtrykkeligt er givet den nye nøgle ikke længere vil have adgang til kontoen lagerplads.

Hvis du aktuelt bruger nøgle 2, kan du benytte den samme fremgangsmåde, men du kan tilbageføre vigtige navnene.

Ændring af hvert program for at bruge den nye nøgle og publicere den, kan du overføre over et par dage. Når alle er færdig, skal du gå tilbage og genoprette nøglen gamle, så den ikke længere fungerer.

En anden mulighed er at placere kontonøgle lager i en [Azure tasten samling](https://azure.microsoft.com/services/key-vault/) som et hemmeligt og har programmerne hente tasten derfra. Når du genoprette nøglen og opdatere Azure-tasten samling, skal programmerne derefter ikke installeres igen, fordi de kan vælge den nye nøgle fra Azure-tasten samling automatisk. Bemærk, at du kan have programmet læse nøglen, hver gang, du vil have den, eller du kan gemme den i cachen i hukommelsen og hvis det ikke lykkes, når du bruger det, du hente tasten igen fra Azure-tasten samling.

En anden sikkerhedsniveauet for dine lagerplads nøgler tilføjes ved hjælp af Azure-tasten samling også. Hvis du bruger denne metode, behøver du aldrig den vigtige hårdt lager i en konfigurationsfil, som fjerner pågældende Boulevard af nogen få adgang til de pågældende taster uden særlig tilladelse.

En anden fordel ved at bruge Azure-tasten samling er, du kan også kontrollere adgang til dine nøgler ved hjælp af Azure Active Directory. Det betyder, at du kan give adgang til programmer, der har brug for at hente de pågældende taster fra Azure-tasten samling, og ved, at andre programmer ikke kan få adgang til de pågældende taster uden at give dem tilladelse specifikt håndfuld.

Bemærk: det anbefales at bruge kun én af de pågældende taster i alle dine programmer på samme tid. Hvis du bruger tasten 1 i nogle områder og nøgle 2 i andre, kan du ikke rotere dine nøgler uden et program, at miste adgangen.

####<a name="resources"></a>Ressourcer

-   [Om Azure lagerplads konti](storage-create-storage-account.md#regenerate-storage-access-keys)

    I denne artikel giver et overblik over lagerplads konti og beskriver visning, kopierer og genoprette lagerplads adgangstaster.

-   [Azure-lager ressource udbyder RESTEN API Reference](https://msdn.microsoft.com/library/mt163683.aspx)

    I denne artikel indeholder links til bestemte artikler om hentning af tasterne lagerplads konto og genoprette tasterne lagerplads konto for en Azure-konto ved hjælp af REST-API. Bemærk: Dette er for ressourcestyring lagerplads konti.

-   [Handlinger på lagerplads konti](https://msdn.microsoft.com/library/ee460790.aspx)

    I denne artikel i Storage Service Manager RESTEN API Reference indeholder links til bestemte artikler om hentning af og genoprette tasterne lagerplads konto ved hjælp af REST-API. Bemærk: Dette er for klassisk lagerplads konti.

-   [Slut for at centrale administration – administrere adgang til Azure-lager data ved hjælp af Azure AD](http://www.dushyantgill.com/blog/2015/04/26/say-goodbye-to-key-management-manage-access-to-azure-storage-data-using-azure-ad/)

    I denne artikel viser, hvordan du bruger Active Directory til at styre adgangen til dine Azure-lager nøgler i Azure-tasten samling. Det også viser, hvordan du bruger et Azure automatisering job til at genoprette tasterne på grundlag af hver time.

##<a name="data-plane-security"></a>Plan datasikkerhed

Plan datasikkerhed refererer til metoder til at sikre de dataobjekter, der er gemt i Azure-lager – blob, køer, tabeller og filer. Vi har set metoder til at kryptere data og sikkerhed under overførsel af dataene, men hvordan går om tillade adgang til objekterne?

Der er grundlæggende to metoder til at styre adgang til dataobjekter sig selv. Først er ved at styre adgangen til tasterne lagerplads konto, og andet bruger delt Access signaturer til at give adgang til bestemte Dataobjekter til et bestemt tidsrum.

En undtagelse skal være opmærksom er, at du kan tillade offentlig adgang til din BLOB ved at angive adgangsniveauet for objektbeholderen, der indeholder BLOB i overensstemmelse hermed. Hvis du indstiller adgang til en objektbeholder til Blob eller objektbeholder, kan det offentlige læseadgang for BLOB i objektbeholderen. Det betyder, at alle med en URL-adresse, der peger mod en blob i objektbeholderen kan åbne den i en browser, uden at bruge en delt Access signatur eller har tasterne lagerplads konto.

###<a name="storage-account-keys"></a>Lagerplads konto taster

Lagerplads konto taster er 512-bit strenge, der er oprettet af Azure, sammen med firmanavnet lagerplads, kan bruges til at få adgang til de dataobjekter, der er gemt på kontoen lagerplads.

For eksempel kan du læse blob, skrive til køer, oprette tabeller og ændre filer. Mange af disse handlinger kan udføres via Azure-portalen, eller bruge et af mange Storage Explorer programmer. Du kan også skrive kode for at bruge REST-API eller et af de lagerplads klient-biblioteker til at udføre disse handlinger.

Som beskrevet i afsnittet om [Administration af plan sikkerhed](#management-plane-security), kan få tildelt adgang til lagerplads taster til en klassisk lagerplads konto ved at give fuld adgang til Azure abonnementet. Du kan styre adgang til lagerplads taster til en lagerplads konto ved hjælp af Azure ressourcestyring modellen gennem rollebaseret adgangskontrol (RBAC).

###<a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Sådan stedfortræderadgang til objekter på din konto ved hjælp af delt Access signaturer og gemt Access politikker

En delt Access signatur er en streng, der indeholder et sikkerhedstoken, som kan knyttes til en URI, der gør muligt at stedfortræderadgang til lagerplads objekter og angive begrænsninger som tilladelserne, og dato/klokkeslæt-området i access.

Du kan give adgang til BLOB, objektbeholdere, beskeder i kø, filer og tabeller. Med tabeller, kan du rent faktisk give tilladelse til at få adgang til et område af enheder i tabellen ved at angive de partition og række vigtige områder, du vil brugeren skal have adgang. Eksempelvis hvis du har data, der gemmes med en Partitionsnøgle geografisk område, du kan give en person adgang til kun data for California.

I et andet eksempel, kan du give et webprogram et SAS token, som gør det muligt at skrive poster til en kø, og giv en arbejder rolle programmet et SAS token at hente meddelelser fra køen og behandle dem. Eller du kan give en kunde et SAS token de kan bruge til at overføre billeder til en objektbeholder i Blob-lager og give en web program tilladelse til at læse disse billeder. I begge tilfælde, der er en adskillelse af problemstillinger – hvert program, der kan angives lige i access, de har brug for at udføre deres opgave. Dette er muligt ved hjælp af delt Access signaturer.

####<a name="why-you-want-to-use-shared-access-signatures"></a>Hvorfor du vil bruge delt Access signaturer

Hvorfor skulle du bruge en SAS i stedet for bare give din lagerplads kontonøgle, som er så meget nemmere? Give din lagerplads kontonøgle minder om deling af nøgler din lagerplads Kongeriges. Det giver fuld adgang. En person kan bruge dine nøgler og overføre biblioteket deres hele musik til kontoen lagerplads. De kan også erstatte dine filer med virus på-versioner, eller du kan stjæle dine data. Giver ikke til stede ubegrænset adgang til kontoen lagerplads er noget, der ikke skal udføres let.

Med delt adgang signaturer, kan du give en klient lige hvilke tilladelser der kræves i en begrænset periode. Eksempelvis hvis nogen overføres en blob til din konto, kan du give dem skriveadgang til tid lige nok til at overføre blob (afhængigt af størrelsen på blob naturligvis). Og hvis du skifter mening, kan du tilbagekalde, som access.

Desuden kan du angive, at anmodninger ved hjælp af en SAS er begrænset til en bestemt IP-adresse eller IP-adresseområder ekstern i forhold til Azure. Du kan også kræve, at anmodninger ved hjælp af en bestemt protokol (HTTPS eller HTTP/HTTPS). Det betyder, at hvis du kun vil tillade HTTPS-trafikken, kan du angive den protokol, der kræves til HTTPS kun og HTTP trafikken skal blokeres.

####<a name="definition-of-a-shared-access-signature"></a>Definition af en delt adgang signatur

En delt Access signatur er et sæt af forespørgselsparametre føjet til den URL-adresse peger på ressourcen

der indeholder oplysninger om adgang til det tilladte og hvor lang tid, det er tilladt i access. Her er et eksempel Denne URI indeholder læseadgang til en blob til fem minutter. Bemærk, at SAS forespørgselsparametre skal være URL-kodet, såsom % 3A for kolon (:) eller % 20 for et mellemrum.

    http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
    ?sv=2015-04-05 (storage service version)
    &st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
    &se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
    &sr=b (resource is a blob)
    &sp=r (read access)
    &sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
    &spr=https (only allow HTTPS requests)
    &sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)

####<a name="how-the-shared-access-signature-is-authenticated-by-the-azure-storage-service"></a>Hvordan Access delt signaturen er godkendt af tjenesten Azure-lager

Når tjenesten storage modtager anmodningen, tager input forespørgselsparametrene og opretter en signatur ved hjælp af metoden samme som det program. Derefter sammenlignes to signaturer. Hvis de accepterer den, kan tjenesten storage kontrollere lagerplads service version for at kontrollere, at den er gyldig, Kontroller, at den aktuelle dato og klokkeslæt er i det angivne vindue skal du sørge for anmodning om adgang, der svarer til anmodninger osv.

For eksempel med vores URL-adresse ovenfor, vil Hvis URL-adressen henviser til en fil i stedet for en blob denne anmodning mislykkes, fordi den angiver, at den delte Access signatur er til en blob. Hvis kommandoen RESTEN bliver kaldt var at opdatere en blob, vil det mislykkes, fordi den delte Access signatur angiver, at kun læseadgang er tilladt.

####<a name="types-of-shared-access-signatures"></a>Typer af delt adgang signaturer

-   En tjeneste niveau SAS kan bruges til at få adgang til bestemte ressourcer i en lagerplads konto. Nogle eksempler på dette henter en liste over BLOB i en beholder, hente en blob, opdatering af et objekt i en tabel, føje meddelelser til en kø eller overføre en fil til et filshare.

-   En konto på brugerniveau SAS kan bruges til at få adgang til alt det, en tjeneste niveau SAS kan bruges til. Det kan desuden give indstillinger til ressourcer, der ikke er tilladt sammen med en tjeneste niveau SAS, såsom mulighed for at oprette beholdere, tabeller, forespørgsler og filshares. Du kan også angive adgang til flere tjenester på én gang. For eksempel kan du give en person til både BLOB og filer på din lagerplads konto.

####<a name="creating-an-sas-uri"></a>Oprette en SAS URI

1.  Du kan oprette et ad hoc-URI efter behov, definere alle parametrene til forespørgslen, hver gang.

    Dette er det virkelig fleksible, men hvis du har et logisk sæt af parametre, der har samme hver gang, ved hjælp af en gemt Access-politik er en bedre ide.

2.  Du kan oprette en gemt Access-politik for en hel objektbeholder, filshare, tabel eller kø. Derefter kan du bruge det som grundlag for de SAS URI'er, du opretter. Tilladelser, der er baseret på gemt Access politikker kan tilbagekaldes nemt. Du kan have op til 5 politikker, der er defineret i hver objektbeholder, kø, tabel eller filshare.

    Hvis du skulle har mange personer læse BLOB i en bestemt objektbeholder, kan du oprette en gemt Access-politik, hvor der står "Giv læseadgang" og andre indstillinger, der vil være den samme hver gang. Derefter kan du oprette en SAS URI ved hjælp af indstillingerne for den gemt Access-politik og angive udløb dato og klokkeslæt. Fordelen ved at dette er, at du ikke behøver at angive alle parametrene til forespørgslen, hver gang.

####<a name="revocation"></a>Tilbagekaldte certifikater

Antag, at din SAS er blevet afsløret, eller du vil ændre det på grund af virksomhedens sikkerhed eller lovmæssige krav. Hvordan du fjerne adgangen til en ressource, ved hjælp af SAS? Det afhænger af, hvordan du oprettede SAS URI.

Hvis du bruger ad hoc-URI, har du tre muligheder. Du kan udstede SAS tokens med korte udløb politikker og blot vente SAS til at udløbe. Du kan omdøbe eller slette ressourcen (hvis det er fastsat tokenet til et enkelt objekt). Du kan ændre tasterne lagerplads konto. Denne indstilling for sidste kan have stor betydning, afhængigt af hvor mange services bruger lagerplads kontoen, og sandsynligvis ikke noget, du vil gøre uden del planlægning.

Hvis du bruger en SAS udledt fra en gemt Access-politik, du kan fjerne adgangen ved at trække den gemt Access-politik – du kan kun ændre den, så det allerede er udløbet, eller du kan fjerne den helt. Dette træder i kraft med det samme og gøres ugyldige hver SAS, der er oprettet ved hjælp af denne gemt Access-politik. Opdatere eller fjerne politikken gemt Access kan påvirke andre få adgang til bestemte objektbeholderen filshare, tabel eller kø via SAS, men hvis klienterne, der skrives, så de anmode om en ny SAS, når den gamle liste bliver ugyldige, dette fungerer fint.

Fordi ved hjælp af en SAS udledt fra en gemt Access-politik giver dig mulighed for at ophæve pågældende SAS med det samme, er det anbefalede bedste fremgangsmåde altid skal bruge Access gemt politikker, når det er muligt.

####<a name="resources"></a>Ressourcer

Se følgende artikler for mere detaljerede oplysninger om brug af delte Access signaturer og gemt Access politikker, fuldført med eksempler:

-   Dette er artiklerne reference.

    -   [Tjenesten SAS](https://msdn.microsoft.com/library/dn140256.aspx)

        Denne artikel indeholder eksempler på brug af en tjeneste niveau SAS med blob, beskeder i kø, tabel områder og filer.

    -   [Bygning af en tjeneste SAS](https://msdn.microsoft.com/library/dn140255.aspx)

    -   [Bygning af en konto SAS](https://msdn.microsoft.com/library/mt584140.aspx)

-   Dette er selvstudier til brug af biblioteket .NET-klienten til at oprette delte Access signaturer og gemt Access politikker.

    -   [Brug af delte Access signaturer (SAS)](storage-dotnet-shared-access-signature-part-1.md)

    -   [Delte signaturer Access, del 2: Oprette og bruge en SAS med Blob-tjenesten](storage-dotnet-shared-access-signature-part-2.md)

        Denne artikel indeholder en forklaring af SAS modellen, delt Access signaturer i slutningen af og anbefalinger til den bedste fremgangsmåde brug af Sikkerhedstilknytninger. Også som gennemgået er en tilbagekaldelse af tilladelser.

-   Begrænse adgang med IP-adresse (IP-ACLs)

    -   [Hvad er et slutpunkt Access Control List (ACLs)?](../virtual-network/virtual-networks-acl.md)

    -   [Bygning af en tjeneste SAS](https://msdn.microsoft.com/library/azure/dn140255.aspx)

        Dette er referenceartikel for tjenesten niveau Sikkerhedstilknytninger; den indeholder et eksempel på IP-ACLing.

    -   [Bygning af en konto SAS](https://msdn.microsoft.com/library/azure/mt584140.aspx)

        Dette er referenceartikel for konto-niveau Sikkerhedstilknytninger; den indeholder et eksempel på IP-ACLing.

-   Godkendelse

    -    [Godkendelse for tjenesterne, Azure-lager](https://msdn.microsoft.com/library/azure/dd179428.aspx)

-   Delt Access signaturer Introduktion selvstudium

    -   [SAS Introduktion selvstudium](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

##<a name="encryption-in-transit"></a>Kryptering undervejs

###<a name="transport-level-encryption--using-https"></a>Transport-niveau kryptering – ved hjælp af HTTPS

Et andet trin, du skal udføre for at sikre sikkerheden for dataene Azure-lager er at kryptere data mellem klienten og Azure-lager. Den første anbefales, at Brug altid [HTTPS](https://en.wikipedia.org/wiki/HTTPS) -protokollen, der sikrer sikker kommunikation via offentlige internettet.

Brug altid HTTPS, når du ringer REST API'er eller få adgang til objekter på-lager. Du kan også inkludere **Delt Access signaturer**, som kan bruges til at stedfortræderadgang til Azure-lager objekter, mulighed for at angive, at kun HTTPS-protokollen kan bruges, når du bruger delt Access signaturer, der sikrer, at alle sende links med SAS tokens skal bruge den korrekte protokol.

####<a name="resources"></a>Ressourcer

-   [Aktivere HTTPS for en app i Azure App-tjeneste](../app-service-web/web-sites-configure-ssl-certificate.md)

    I denne artikel beskrives, hvordan du aktiverer HTTPS til en Azure Web App.

###<a name="using-encryption-during-transit-with-azure-file-shares"></a>Ved hjælp af kryptering under overførslen med Azure filshares

Azure fillagring understøtter HTTPS, når du bruger REST-API, men er mere almindeligt brugte som et filshare til små og mellemstore virksomheder knyttet til en VM. Små og mellemstore virksomheder 2.1 understøtter ikke kryptering, så forbindelser kun er tilladt i det samme område i Azure. Dog små og mellemstore virksomheder 3.0 understøtter kryptering og kan bruges sammen med Windows Server 2012 R2, Windows 8, Windows 8.1 og Windows 10, så tværs område få adgang til og endda adgang på skrivebordet.

Bemærk, at mens Azure filshares kan bruges med Unix, Linux SMB klienten endnu ikke understøtter kryptering, så access kun er tilladt i en Azure område. Krypteringsunderstøttelse af Linux er på roadmap med Linux udviklere ansvarlig for små og mellemstore virksomheder funktionalitet. Når de tilføjer kryptering, får du den samme muligheden for at få adgang til et Azure filshare på Linux, som du til Windows.

####<a name="resources"></a>Ressourcer

-   [Sådan bruges Azure fillagring med Linux](storage-how-to-use-files-linux.md)

    I denne artikel viser, hvordan du tilslutte en Azure filshare på et Linux-system og upload/download af filer.

-   [Introduktion til Azure fillagring i Windows](storage-dotnet-how-to-use-files.md)

    I denne artikel giver et overblik over Azure filshares og hvordan du tilslutte og bruge dem med PowerShell og .NET.

-   [Indvendig Azure fillagring](https://azure.microsoft.com/blog/inside-azure-file-storage/)

    I denne artikel læser generelle tilgængeligheden af Azure fillagring og indeholder tekniske detaljer om små og mellemstore virksomheder 3.0-kryptering.

###<a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Brug af klientsiden kryptering til at sikre data, du sender til lager

En anden mulighed, som hjælper dig med at sikre, at dine data er beskyttet, mens overføres mellem en klientprogrammet og lager er klientsiden kryptering. Data, der er krypteret før der overføres til Azure-lager. Når du henter data fra Azure-lager, dekrypteres dataene, når den modtages på klientsiden. Selvom data, der er krypteret gå på tværs af en netværksforbindelse, anbefaler vi, at du også bruge HTTPS, som den har data integritet Kontroller indbygget i hvilken Hjælp der reducere netværksfejl påvirker integriteten af dataene.

Klientsiden kryptering er også en metode til at kryptere dine data på resten, som dataene er gemt i dets krypteret form. Gennemgår vi om dette mere detaljeret i afsnittet om [kryptering på resten](#encryption-at-rest).

##<a name="encryption-at-rest"></a>Kryptering på resten

Der findes tre Azure funktioner, som giver kryptering på resten. Azure disken kryptering bruges til at kryptere diskene OS og data i IaaS virtuelle computere. De andre to – klientsiden kryptering og SSE – er begge bruges til at kryptere data i Azure-lager. Lad os se på hver af disse, gøre en sammenligning og finde når hver af dem kan bruges.

Du kan bruge klientsiden kryptering til at kryptere data undervejs (hvilket også er gemt i krypteret format i lagerplads), kan du foretrækker at blot bruge HTTPS under overførslen, og har nogle måde for dataene, der skal krypteres automatisk, når det er gemt. Der er to måder at gøre det – Azure disken kryptering og SSE. En bruges til at kryptere dataene på OS og data diske bruges af FOS direkte, og den anden bruges til at kryptere data, der er skrevet til Azure Blob-lager.

###<a name="storage-service-encryption-sse"></a>Lagerplads Service kryptering (SSE)

SSE kan du anmode om, at tjenesten storage automatisk krypterer dataene, når du skriver til Azure-lager. Når du læser data fra Azure-lager, dekrypteres ved tjenesten storage før den returneres. Dette giver dig mulighed at sikre dine data uden at ændre kode eller tilføje kode i alle programmer.

Dette er en indstilling, der gælder for hele lagerplads kontoen. Du kan aktivere og deaktivere denne funktion ved at ændre værdien af indstillingen. For at gøre dette, kan du bruge portalen Azure, PowerShell, Azure CLI, lagerplads ressource udbyder REST-API eller biblioteket .NET lagerplads klient. SSE er som standard slået fra.

På nuværende tidspunkt administreres tasterne bruges til kryptering af Microsoft. Vi generere tasterne oprindeligt og administrere sikker opbevaring af de pågældende taster samt regelmæssig rotationen, som defineret af interne Microsofts politik. Du kan i fremtiden, får mulighed for at administrere dine egne kryptering taster, og giv en migreringsstien fra Microsoft-administreret taster til kunde-administreret nøgler.

Denne funktion er tilgængelig for Standard og Premium lager konti, der er oprettet ved hjælp af Ressourcestyring implementeringsmodel. SSE gælder kun for at blokere blob, siden blob, og Føj BLOB. De andre typer data, herunder tabeller, køer og filer, krypteres ikke.

Dataene krypteres kun, når SSE er aktiveret og data, der er skrevet til Blob-lager. Aktivere eller deaktivere SSE påvirker ikke eksisterende data. Når du aktiverer denne kryptering, vil den med andre ord ikke gå tilbage og kryptere data, der allerede findes; og vil dekryptere de data, der allerede findes, når du deaktiverer SSE.

Hvis du vil bruge denne funktion med en klassisk lagerplads konto, kan du oprette en ny ressourcestyring lagerplads konto og bruge AzCopy til at kopiere data til den nye konto. 

###<a name="client-side-encryption"></a>Klientsiden kryptering

Vi nævnt klientsiden kryptering, når du diskuterer kryptering af data undervejs. Denne funktion kan du automatisk kryptere dine data i en klientprogrammet, før du sender den på tværs af en netværksforbindelse kan skrives til Azure-lager og ved hjælp af programmering dekryptere dine data efter hente dem fra Azure-lager.

Dette giver kryptering undervejs, men det indeholder også funktionen af kryptering på resten. Bemærk, at dataene er krypteret under overførsel, stadig anbefales, men ved hjælp af HTTPS for at drage fordel af de indbyggede dataintegritet kontrollerer, hvilken Hjælp der reducere netværksfejl påvirker integriteten af dataene.

Der er et eksempel på, hvor du kan bruge dette, hvis du har et webprogram, der lagrer BLOB og henter blob, og du vil programmet og dataene skal være så sikker som muligt. Det er tilfældet, skal du bruge klientsiden kryptering. Trafik mellem klienten og Azure Blob-tjenesten indeholder krypterede ressourcen, og ingen andre kan forstå dataene undervejs og således rekonstruere den til din private BLOB.

Klientsiden kryptering er indbygget i Java og .NET lagerplads klientbiblioteker, som også bruger Azure-tasten samling API'er, så det er ret nemt for dig at implementere. Processen med at kryptere og dekryptere dataene bruger konvolut metode og gemmer metadata, der bruges af kryptering i hvert lagerplads objekt. For eksempel til BLOB, gemmes det i blob-metadata, mens for køer det tilføjer den i hver kø meddelelse.

Du kan oprette og administrere dine egne kryptering taster til selve kryptering. Du kan også bruge taster, der genereres af biblioteket Azure-lager-klienten, eller du kan have Azure-tasten samling generere de pågældende taster. Du kan gemme dine krypteringsnøgler i din lokale vigtige lagerplads, eller du kan gemme dem i en Azure-tasten samling af legitimationsoplysninger. Azure-tasten samling kan du give adgang til hemmeligheder i Azure-tasten samling til bestemte brugere ved hjælp af Azure Active Directory. Det betyder, at ikke kun alle kan læse den Azure-tasten samling af legitimationsoplysninger og hente de taster, du bruger til klientsiden kryptering.

####<a name="resources"></a>Ressourcer

-   [Kryptere og dekryptere BLOB i Microsoft Azure-lager, med Azure-tasten samling](storage-encrypt-decrypt-blobs-key-vault.md)

    Denne artikel beskrives, hvordan du bruger klientsiden kryptering med Azure-tasten samling, herunder hvordan du opretter KEK og gemme den i den samling af legitimationsoplysninger ved hjælp af PowerShell.

-   [Klientsiden kryptering og Azure vigtige samling til Microsoft Azure-lager](storage-client-side-encryption.md)

    I denne artikel giver en forklaring på klientsiden kryptering og eksempler på brug af biblioteket lagerplads klienten til at kryptere og dekryptere ressourcer fra de fire lagerplads tjenester. Det er også taler om Azure-tasten samling.

###<a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Ved hjælp af Azure Disk kryptering til at kryptere diske, der anvendes af din virtuelle maskiner

Azure Disk kryptering er en ny funktion, der er i Vis udskrift. Denne funktion giver dig mulighed at kryptere den OS og Data diske bruges ved en IaaS Virtual Machine. Til Windows krypteres drevene ved hjælp af branchestandard BitLocker krypteringsteknologi. For Linux krypteres diskene ved hjælp af DM Crypt teknologien. Dette er integreret med Azure-tasten samling, så du kan kontrollere og administrere tasterne disk kryptering.

Azure Disk kryptering løsningen understøtter følgende tre kunde kryptering scenarier:

-   Aktivere kryptering på nye IaaS VM'er, der er oprettet ud fra kunde-krypterede Virtuelle filer og kunden har udleveret krypteringsnøgler, som er gemt i Azure-tasten samling.

-   Aktivere kryptering på nye IaaS FOS oprettet fra Azure Marketplace.

-   Aktivere kryptering på eksisterende IaaS FOS allerede kører i Azure.

>[AZURE.NOTE] For Linux FOS allerede kører i Azure, eller nye Linux VM'er, der er oprettet ud fra billeder i Azure Marketplace, understøttes kryptering af OS disken ikke i øjeblikket. Kryptering af OS lydstyrken for Linux FOS understøttes kun for VM'er, der er krypteret lokalt og overføres til Azure. Denne begrænsning gælder kun for OS disken; kryptering af datamængder til en Linux VM understøttes.

Løsningen understøtter følgende til IaaS FOS for offentlige preview-versionen når aktiveret i Microsoft Azure:

-   Integration med Azure vigtige samling

-   Standard [A, D og G serie IaaS FOS](https://azure.microsoft.com/pricing/details/virtual-machines/)

-   Aktivere kryptering på IaaS FOS oprettet ved hjælp af [Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md) model

-   Alle Azure offentlige [områder](https://azure.microsoft.com/regions/)

Denne funktion sikrer, at alle data på harddisken virtuelt krypteres på resten i Azure-lager.

####<a name="resources"></a>Ressourcer

-   [Azure disken kryptering til Windows og Linux IaaS virtuelle maskiner](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

    I denne artikel omhandler preview-versionen af Azure Disk kryptering og giver et link for at hente hvidbog.

###<a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Sammenligning af Azure disken kryptering, SSE og klientsiden kryptering

####<a name="iaas-vms-and-their-vhd-files"></a>IaaS FOS og deres Virtuelle filer

Om en disk, der bruges af IaaS FOS, anbefales det ved hjælp af Azure Disk kryptering. Du kan aktivere SSE til at kryptere de Virtuelle filer, der bruges til tilbage disketterne i Azure-lager, men det kun krypterer nyligt skrevne data. Det betyder, at hvis du opretter en VM og derefter aktivere SSE på kontoen lagerplads, der indeholder filen Virtuelle, krypteres kun ændringerne, ikke den oprindelige Virtuelle fil.

Hvis du opretter en VM ved hjælp af et billede fra Azure Marketplace, Azure udfører en [flad kopi](https://en.wikipedia.org/wiki/Object_copying) af billedet til kontoen lagerplads i Azure-lager, og det ikke er krypteret, selvom du har aktiveret SSE. Når den opretter VM og starter opdatering af billedet, starter SSE kryptering af data. Derfor er det bedst at bruge Azure Disk kryptering på VM'er, der er oprettet fra billeder i Azure Marketplace, hvis du vil have dem fuldt krypteret.

Hvis du flytter en udfyldt krypterede VM til Azure fra det lokale, vil du kunne overføre tasterne kryptering til Azure-tasten samling, og fortsæt med at bruge krypteringen for pågældende VM, du brugte i det lokale miljø. Azure Disk kryptering er aktiveret til at håndtere dette scenario.

Hvis du har ikke-krypteret Virtuelle fra det lokale, kan du overføre den til galleriet som et brugerdefineret billede og klargøre en VM fra den. Hvis du gør dette, ved hjælp af Ressourcestyring skabeloner, kan du bede den for at aktivere Azure Disk kryptering, når det starter VM.

Når du tilføjer en datadisk og oprette forbindelse til det på VM, kan du aktivere Azure Disk kryptering på den pågældende datadisk. Det, krypteres data disken lokalt først, og derefter service management lag vil gøre en skrevet i baggrund mod lagerplads, så lagerplads indholdet er krypteret.

####<a name="client-side-encryption"></a>Klientsiden kryptering####

Klientsiden kryptering er den mest sikre metode til at kryptere dine data, fordi den krypterer før overførsel og krypterer data på resten. Det kræver dog, at du føjer kode til dine programmer ved hjælp af lagerplads, som du ikke vil gøre. I disse tilfælde kan du bruge HTTPs til dine data i overførsel og SSE til at kryptere dataene på resten.

Du kan kryptere tabel objekter, beskeder i kø og BLOB med klientsiden kryptering. Du kan kun kryptere BLOB med SSE. Hvis du har brug for tabellen og kø data skal krypteres, skal du bruge klientsiden kryptering.

Klientsiden kryptering administreres helt af programmet. Dette er den mest sikre metode, men kræver, at du kan foretage programmeringsmæssig ændringer i dit program og placere key management processer på plads. Du vil bruge dette, når du vil den ekstra sikkerhed under overførslen, og din lagrede data skal krypteres.

Klientsiden kryptering er mere belastning på klienten, og du har konto til denne i dine planer, skalerbarhed, især hvis du kryptering og overførsel af en stor mængde data.

####<a name="storage-service-encryption-sse"></a>Lagerplads Service kryptering (SSE)

SSE administreres af Azure-lager. Brug af SSE giver ikke for sikkerheden for dataene undervejs, men det krypterer dataene, som den er skrevet til Azure-lager. Der er ingen indflydelse på ydeevnen, når du bruger denne funktion.

Du kan kun kryptere Bloker blob, tilføje BLOB og siden BLOB ved hjælp af SSE. Hvis du har brug for til at kryptere tabeldata eller kø data, bør du overveje at bruge klientsiden kryptering.

Hvis du har et arkiv eller bibliotek med Virtuelle filer, der bruges som grundlag for oprettelse af nye virtuelle maskiner, kan du oprette en ny firmapost lagerplads, aktivere SSE og derefter overføre Virtuelle filerne til denne konto. Filerne, som Virtuelle krypteres ved Azure-lager.

Hvis du har Azure diskplads kryptering aktiveret om en disk i et VM og SSE aktiveret på kontoen lagerplads holder Virtuelle filer, fungerer der fint; den medfører nyligt skrevet data som krypteres to gange.

##<a name="storage-analytics"></a>Lagerplads Analytics

###<a name="using-storage-analytics-to-monitor-authorization-type"></a>Brug af lagerplads Analytics til at overvåge godkendelse type

For hver lagerplads, kan du aktivere Azure lagerplads Analytics til at udføre logføring og gemme målepunkter data. Dette er et godt værktøj, der skal bruges, når du vil kontrollere ydeevnen målene for en lagerplads konto, eller har brug at foretage fejlfinding af en lagerplads-konto, fordi du har ydeevneproblemer med.

En anden del af data, du kan se i loggene lagerplads analytics er den anvendte godkendelsesmetode, som bruges af en person, når de åbner lagerplads. For eksempel med Blob-lager, kan du se Hvis de bruges en delt Access signatur eller tasterne lagerplads konto, eller hvis blob adgang til blev offentlige.

Det kan være virkelig nyttigt, hvis du tæt beskytte adgang til lagerplads. For eksempel i Blob-lager kan du angive alle beholderne til private og implementere brugen af en SAS tjeneste i hele dine programmer. Derefter kan du se loggene jævne mellemrum for at se, om din BLOB åbnes ved hjælp af tasterne lagerplads konto, hvilket kan angive et brud på sikkerhed, eller hvis BLOB er offentlige, men de ikke bør være.

####<a name="what-do-the-logs-look-like"></a>Hvordan ser loggene ud?

Når du aktiverer konto lagringsmål og logføring via Azure-portalen, starter analytics-data til at samle hurtigt. Logføring og måleredskaber for hver tjeneste er adskilt; logføring skrives kun, når der er aktivitet på den pågældende konto lagerplads, mens målene vil blive logført hvert minut, hver time eller hver dag, afhængigt af hvordan du konfigurerer.

Logfiler gemmes i Bloker BLOB i en objektbeholder med navnet $logs i kontoen lagerplads. Denne beholder oprettes automatisk, når lagerplads Analytics er aktiveret. Når denne objektbeholder er oprettet, kan du slette den, selvom du kan slette dens indhold.

Der findes en mappe til hver tjeneste under objektbeholderen $logs, og klik derefter der er undermapper i år/måned/dag/time. Under time nummereres blot loggene. Dette er hvad mappestrukturen ser sådan ud:

![Visning af logfiler](./media/storage-security-guide/image1.png)

Alle anmodninger til Azure-lager logføres. Her er et øjebliksbillede af en logfil, der viser de første par felter.

![Øjebliksbillede af en logfil](./media/storage-security-guide/image2.png)

Du kan se, at du kan bruge logfiler til at registrere alle slags opkald til en lagerplads konto.

####<a name="what-are-all-of-those-fields-for"></a>Hvad er alle disse felter til?

Der er en artikel, er angivet i ressourcerne nedenfor, der indeholder en oversigt over de mange felter i loggene og de bruges til. Her er listen over felter i rækkefølge:

![Øjebliksbillede af felter i en logfil](./media/storage-security-guide/image3.png)

Vi er interesseret i posterne til GetBlob, og hvordan de er godkendt, så vi har brug at søge efter poster med handlingstype "Get-Blob", og se anmodning-status (4<sup>de</sup> kolonner) og godkendelse-typen (8<sup>n'te</sup> kolonne).

For eksempel i de første par rækker i listen over anmodning-status er "Udført", og godkendelse-type er "godkendt". Det betyder anmodningen blev valideret ved hjælp af tasten lagerplads konto.

####<a name="how-are-my-blobs-being-authenticated"></a>Hvor er mine BLOB godkendelse?

Vi har tre tilfælde, vi er interesseret i.

1.  Blob er offentlig, og det åbnes ved hjælp af en URL-adresse uden en delt Access-signatur. I dette tilfælde anmodning-status er "AnonymousSuccess" og godkendelse-type er "anonym".

    1.0; 2015-11-17T02:01:29.0488963Z; GetBlob; **AnonymousSuccess**200 124; 37; **anonym**; mystorage...

2.  Blob er privat, og der blev brugt med en delt Access-signatur. I dette tilfælde anmodning-status er "SASSuccess" og godkendelse-type er "sas".

    1.0; 2015-11-16T18:30:05.6556115Z; GetBlob; **SASSuccess**200 416; 64; **sas**; mystorage...

3.  Blob er privat, og tasten lagerplads blev brugt til at få adgang til den. I dette tilfælde anmodning-status er "**udført**", og godkendelse-type er "**godkendt**".

    1.0; 2015-11-16T18:32:24.3174537Z; GetBlob; **Succes**206 59; 22; **godkendte**; mystorage...

Du kan bruge Microsoft meddelelse Analyzer til at få vist og analysere disse logfiler. Det omfatter funktioner til søgning og filtrering. For eksempel kan du søge efter forekomster af GetBlob Hvis Brug er, hvad du forventer, det vil sige at sikre, at nogen ikke har adgang til kontoen lagerplads har.

####<a name="resources"></a>Ressourcer

-   [Lagerplads Analytics](storage-analytics.md)

    I denne artikel er en oversigt over lagerplads analytics, og hvordan du aktiverer dem.

-   [Lagerplads Analytics logformat](https://msdn.microsoft.com/library/azure/hh343259.aspx)

    I denne artikel illustrerer lagerplads Analytics Log Format, og detaljer tilgængelige felter, herunder godkendelsestype, som angiver typen godkendelse, der bruges til anmodningen.

-   [Overvåge en lagerplads konto i portalen Azure](storage-monitor-storage-account.md)

    I denne artikel viser, hvordan du konfigurerer overvågning af målepunkter og registrering for en lagerplads konto.

-   [Til slut fejlfinding ved hjælp af Azure Lagringsmål og logføring, AzCopy og analyse af meddelelse](storage-e2e-troubleshooting.md)

    I denne artikel taler om fejlfinding ved hjælp af lagerplads analyser og viser, hvordan du bruger Microsoft meddelelse Analyzer.

-   [Microsoft Message Analyzer operativsystem vejledning](https://technet.microsoft.com/library/jj649776.aspx)

    I denne artikel er referencen til Microsoft meddelelse Analyzer og indeholder links til en selvstudium, Hurtig start og funktion oversigt.

##<a name="cross-origin-resource-sharing-cors"></a>Tværs Origin ressourcedeling (CORS)

###<a name="cross-domain-access-of-resources"></a>Adgang på tværs af domæner for ressourcer

Når en webbrowser, der kører i ét domæne opretter en HTTP-anmodning om en ressource fra et andet domæne, er dette kaldes en tværs origin HTTP-anmodning. For eksempel gør en HTML-side, der leveres fra contoso.com en anmodning om en jpeg hostes på fabrikam.blob.core.windows.net. Af sikkerhedsmæssige årsager begrænse browsere tværs origin HTTP-anmodninger startet fra i scripts, som JavaScript. Det betyder, at når nogle JavaScript-kode på en webside på contoso.com anmoder om pågældende jpeg på fabrikam.blob.core.windows.net, ikke tillader browseren anmodningen.

Hvad har dette skal du gøre med Azure-lager? Godt, hvis du gemmer statisk medieaktiver som JSON eller XML-datafiler i Blob-lager ved hjælp af en lagerplads konto hedder Fabrikam til domænet for aktiverne bliver fabrikam.blob.core.windows.net og webprogrammet contoso.com vil ikke kunne få adgang til dem ved hjælp af JavaScript, fordi domænerne er forskellige. Dette gælder også hvis du forsøger at ringe til en af de tjenester, Azure-lager – som Table Storage –, der returnerer JSON data skal behandles af JavaScript-klienten.

####<a name="possible-solutions"></a>Mulige løsninger

En metode til at løse dette problem er at tildele et brugerdefineret domæne som "storage.contoso.com" til fabrikam.blob.core.windows.net. Problemet er, kan du kun tildele det brugerdefinerede domæne til én lagerplads konto. Hvad nu, hvis aktiverne, der er gemt i flere lagerplads konti?

En anden måde at løse dette problem er at have webprogrammet fungere som en proxy for lagerplads opkald. Det betyder, at hvis du sender en fil til Blob-lager, webprogrammet skal enten skrive den lokalt og derefter kopiere den til Blob-lager, eller det vil læse hele i hukommelsen og derefter skrive det til Blob-lager. Alternativt kan du skrive et dedikeret webprogram (som en Web API), der overfører filer lokalt og skriver dem til Blob-lager. Uanset hvilken mulighed, du har at tage højde for funktionen, når afgøre skalerbarheden skal.

####<a name="how-can-cors-help"></a>Sådan undgår CORS?

Azure-lager kan du aktivere CORS – Cross Origin Deling af ressourcer. For hver lagerplads, kan du angive domæner, der har adgang til ressourcer i den pågældende lagerplads konto. For eksempel i dette tilfælde, der er beskrevet ovenfor, kan vi aktivere CORS på fabrikam.blob.core.windows.net lagerplads kontoen og konfigurere det til at give adgang til contoso.com. Web application contoso.com kan derefter direkte adgang til ressourcer i fabrikam.blob.core.windows.net.

Én ting at være opmærksom på, er, CORS tillader adgang, men det indeholder ikke-godkendelse, der er påkrævet for alle ikke-offentlige access lagerplads ressourcer. Det betyder, at du kan kun adgang til BLOB, hvis de er offentlige, eller du medtage en delt Access signatur giver dig de relevante tilladelser. Tabeller, køer og filer har ikke adgang til offentlige, og kræver en SAS.

CORS er som standard deaktiveret på alle tjenester. Du kan aktivere CORS ved hjælp af REST-API eller biblioteket lagerplads klienten til at ringe til en af metoder til at indstille politikker tjeneste. Når du gør dette, kan du medtage en CORS regel, som er i XML. Her er et eksempel på en CORS regel, der er oprettet ved hjælp af handlingen Angiv Service egenskaber for tjenesten Blob til en lagerplads konto. Du kan udføre denne handling med lagerplads klientbibliotek eller REST API'er til Azure-lager.

    <Cors>    
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
            <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
            <MaxAgeInSeconds>200</MaxAgeInSeconds>
        </CorsRule>
    <Cors>

Her er, hvad hver række betyder:

-   **AllowedOrigins** Dette kan se, hvilke ikke stemmer overens domæner kan anmode om og hente data fra tjenesten lagerplads. Dette siger, at både contoso.com og fabrikam.com kan anmode om data fra Blob-lager til en bestemt lagerplads konto. Du kan også angive dette til et jokertegn (\*) til at tillade alle domains for at få adgang til anmodninger.

-   **AllowedMethods** Dette er listen over metoder (HTTP-anmodning om verber), der kan bruges, når du foretager anmodningen. I dette eksempel er kun læg og få tilladt. Du kan angive dette til et jokertegn (\*) til at tillade alle metoder til at blive brugt.

-   **AllowedHeaders** Dette er anmodning overskrifterne, som origin domænet kan angive, når du foretager anmodningen. I dette eksempel alle metadata sidehoveder, der starter med x-ms-metadata, x-ms-metakoden-destination, og x-ms-metakoden-abc er tilladt. Jokertegnet (\*) angiver, at et sidehoved, der begynder med det angivne præfiks er tilladt.

-   **ExposedHeaders** Dette kan se, hvilke svar sidehoveder, der skal vises af browseren til anmodning om udsteder. I dette eksempel et sidehoved, der starter med "x-ms - metakoden-" kan vises.

-   **MaxAgeInSeconds** Dette er den maksimale mængde tid, en browser cachelagres Forhåndskontrol indstillinger anmodningen. (Kan finde flere oplysninger om Forhåndskontrol anmodningen, Markér den første artikel nedenfor).

####<a name="resources"></a>Ressourcer

Du kan finde flere oplysninger om CORS og hvordan du aktiverer den, skal du se disse ressourcer.

-   [Tværs Origin ressourcedeling (CORS) understøttelse af tjenesterne på Azure.com Azure-lager](storage-cors-support.md)

    I denne artikel giver et overblik over CORS, og hvordan du opretter regler for de forskellige lagerplads tjenester.

-   [Tværs Origin ressourcedeling (CORS) understøttelse af tjenesterne på MSDN Azure-lager](https://msdn.microsoft.com/library/azure/dn535601.aspx)

    Dette er referencedokumentationen for CORS understøttelse af tjenesterne Azure-lager. Dette er links til artikler, der gælder for hver lagringstjeneste og viser et eksempel og forklarer hvert element i filen CORS.

-   [Microsoft Azure-lager: Introduktion til CORS](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

    Dette er et link til den oprindelige blogartikel præsentation af CORS og viser, hvordan du bruger den.

##<a name="frequently-asked-questions-about-azure-storage-security"></a>Ofte stillede spørgsmål om sikkerhed Azure-lager

1.  **Hvordan kan jeg kontrollere integriteten af BLOB jeg viderestille ind eller ud af Azure-lager, hvis jeg ikke kan bruge HTTPS-protokollen?**

    Hvis for en eller anden grund, du skal bruge HTTP i stedet for HTTPS, og du arbejder med Bloker blob, kan du bruge MD5 fejlkontrol til at kontrollere integriteten af BLOB overføres. Det hjælper med beskyttelse mod netværk/transport lag fejl, men ikke nødvendigvis med mellemliggende angreb.

    Hvis du kan bruge HTTPS, som giver transportsikkerhed, er derefter bruge MD5 kontrollere overflødige og unødvendige.
    
    Du kan finde flere oplysninger, skal du se [Azure Blob MD5 oversigt](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).

2.  **Hvad med FIPS overholdelse for amerikanske regering?**

    I USA national FIPS Information Processing Standard () definerer cryptographic algoritmer, der er godkendt til brug af US national government computersystemer til beskyttelse af følsomme data. Aktivere FIPS fortæller tilstand på en Windows server eller skrivebord OS, at kun FIPS-godkendte cryptographic algoritmer skal bruges. Hvis et program anvender ikke-kompatible algoritmer, brydes programmerne. With.NET Framework versioner 4.5.2 eller højere, skifter programmet automatisk kryptering algoritmer for at bruge FIPS-kompatible algoritmer, når computeren er i FIPS-tilstand.

    Microsoft fokus op til hver kunde beslutte, om du vil aktivere FIPS-tilstand. Vi mener, at der er ingen gode grunde til kunder, der ikke er underlagt government lov til at aktivere FIPS tilstand som standard.

    **Ressourcer**

-   [Hvorfor vi ikke anbefale "FIPS-tilstand" længere](http://blogs.technet.com/b/secguide/archive/2014/04/07/why-we-re-not-recommending-fips-mode-anymore.aspx)

    Denne blogartikel giver et overblik over FIPS og forklarer, hvorfor de ikke kan aktivere FIPS-tilstand som standard.

-   [FIPS 140 validering](https://technet.microsoft.com/library/cc750357.aspx)

    I denne artikel indeholder oplysninger om, hvordan Microsoft-produkter og cryptographic moduler overholder FIPS standarden for den amerikanske regering.

-   ["System kryptering: bruger FIPS kompatible algoritmer til kryptering, krypteres og signering" sikkerhed indstillinger for effekter i Windows XP og nyere versioner af Windows](https://support.microsoft.com/kb/811833)

    I denne artikel taler om brug af FIPS-tilstand i ældre Windows-computere.
