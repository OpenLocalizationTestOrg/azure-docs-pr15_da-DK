<properties
   pageTitle="Bedste fremgangsmåder for datasikkerhed og kryptering | Microsoft Azure"
   description="I denne artikel indeholder en række bedste praksis for datasikkerhed og kryptering ved hjælp af indbygget i Azure-funktioner."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="yuridio"/>

#<a name="azure-data-security-and-encryption-best-practices"></a>Bedste fremgangsmåder for Azure datasikkerhed og kryptering

En af de pågældende taster databeskyttelse i skyen accounting for de mulige tilstande hvor dine data kan opstå, og hvilke funktioner er der til tilstanden. Med henblik på Azure data bliver sikkerhed og kryptering bedste fremgangsmåder på anbefalinger omkring datatypen følgende tilstande:

- I resten: Dette omfatter alle oplysninger lagerplads objekter, beholdere og datatyper, der findes statisk på fysiske medie være det magnetisk eller optisk disk.

- I overførsel: Når der overføres data mellem komponenter, placeringer eller programmer, f.eks via netværket, på tværs af en tjeneste bus (fra lokalt til skyen og omvendt, herunder hybrid forbindelser som ExpressRoute), eller under en input/output-proces, det er sammenligne som værende i bevægelse.

I denne artikel beskriver vi en samling af Azure data sikkerhed og kryptering bedste fremgangsmåder. Disse bedste fremgangsmåder er afledt af vores erfaring med Azure datasikkerhed og kryptering og erfaringer fra kunder som dig selv.

For hver fremgangsmåde gennemgår vi:

- Hvad er den bedste fremgangsmåde
- Hvorfor du vil aktivere den bedste fremgangsmåde
- Hvad kan ske, hvis du ikke aktiverer den bedste fremgangsmåde
- Mulige alternativer til den bedste fremgangsmåde
- Hvordan du kan lære at aktivere den bedste fremgangsmåde

I denne artikel Azure datasikkerhed og kryptering bedste fremgangsmåder er baseret på en enighed udtalelse og Azure-platformen funktioner og funktionssæt, som de findes på det tidspunkt, der er skrevet i denne artikel. Meninger og -teknologier ændrer sig med tiden og i denne artikel vil blive opdateret med jævne mellemrum at afspejle disse ændringer.

Azure data sikkerhed og kryptering bedste fremgangsmåder beskrives i denne artikel, omfatter:

- Gennemtvinge Multi-Factor authentication
- Brug rollebaseret adgangskontrol (RBAC)
- Kryptere Azure virtuelle maskiner
- Bruge hardware sikkerhedsmodeller
- Administrere med sikker arbejdsstationer
- Aktivere SQL datakryptering
- Beskytte data undervejs
- Gennemtving kryptering af filer niveau data


## <a name="enforce-multi-factor-authentication"></a>Gennemtvinge Multi-Factor Authentication

Det første trin i dataadgang og kontrolelement i Microsoft Azure, er at godkende brugeren. [Azure Multi-Factor Authentication (MFA)](../multi-factor-authentication/multi-factor-authentication.md) er en metode til at bekræfte brugerens identitet ved hjælp af en anden metode end bare et brugernavn og din adgangskode. Denne godkendelse metode gør det muligt at beskytter adgang til data og programmer under overholdelse af brugernes behov for en enkelt logonprocessen.

Ved at aktivere Azure MFA for dine brugere, tilføjer du en anden sikkerhedslag bruger logon og transaktioner. I dette tilfælde en transaktion kan adgang til et dokument, der er placeret i en filserver eller i SharePoint Online. Azure MFA også, at IT for at reducere sandsynligheden for, at en kompromitteret legitimationsoplysning får adgang til virksomhedens data.

For eksempel: Hvis du gennemtvinge Azure MFA for dine brugere og Konfigurer den til at bruge en telefonopkald eller tekstmeddelelse som godkendelse, hvis brugerens legitimationsoplysninger er afsløret, skal hackeren ikke få adgang til ressourcer, da hun ikke vil have adgang til brugerens telefon. Organisationer, hvor du ikke kan føje denne ekstra lag af beskyttelse af din identitet er mere udsatte legitimationsoplysninger identitetstyveri angreb, hvilket kan medføre med data sammensat.

Alternative organisationer, hvor du vil beholde de godkendelse kontrolelement lokale er at bruge [Azure Multi-Factor Authentication Server](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), der også kaldes MFA lokalt. Ved hjælp af denne metode kan du kan stadig gennemtvinge Multi-Factor godkendelse, mens de MFA server i det lokale miljø.

Læs i artiklen [Introduktion til Azure Multi-Factor Authentication i skyen](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)kan finde flere oplysninger om Azure MFA.

## <a name="use-role-based-access-control-rbac"></a>Brug rollebaseret adgangskontrol (RBAC)
Begrænse adgangen baseret på de [har brug at vide](https://en.wikipedia.org/wiki/Need_to_know) og [minimale privilegier](https://en.wikipedia.org/wiki/Principle_of_least_privilege) sikkerhedsprincipper. Dette er vigtigt for organisationer, hvor du vil gennemtvinge sikkerhedspolitikker for dataadgang. Azure rollebaseret Access kontrolelement (RBAC) kan bruges til at tildele tilladelser til brugere, grupper og programmer på et bestemt område. Omfanget af en rolletildeling kan være et abonnement, en ressourcegruppe eller en enkelt ressource.

Du kan udnytte [indbyggede RBAC roller](../active-directory/role-based-access-built-in-roles.md) i Azure tildele tilladelser til brugere. Overvej at bruge *Lagerplads konto bidragyder* til skyen operatorer, der skal administrere lagerplads konti og *Klassisk lagerplads konto bidragyder* rolle til at administrere klassisk lagerplads konti. Skyen operatorer, der skal administrere FOS og lagerplads konto, kan du overveje at føje dem til rollen *Bidragyder virtuelt* .

Organisationer, hvor du ikke gennemtvinger dataadgangskontrol ved at udnytte funktioner som RBAC kan give flere rettigheder end det er nødvendigt for deres brugere. Dette kan føre til data sammensat ved at få nogle brugere, der har adgang til data, som de ikke skulle have i første omgang.

Du kan få mere at vide om Azure RBAC ved at læse artiklen [Azure Role-Based adgangskontrol](../active-directory/role-based-access-control-configure.md).

## <a name="encrypt-azure-virtual-machines"></a>Kryptere Azure virtuelle maskiner
[Kryptering af data på resten](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) er for mange organisationer, et obligatorisk trin mod data beskyttelse af personlige oplysninger, overholdelse af regler og datasuverænitet. Azure Disk kryptering gør det muligt for IT-administratorer til at kryptere Windows og Linux IaaS Virtual Machine (VM). Azure Disk kryptering bruger funktionen branche standard BitLocker i Windows og funktionen DM Crypt af Linux til at levere lydstyrken kryptering til Operativsystemet og datadisce.

Du kan udnytte Azure Disk kryptering for at beskytte og beskytte dine data, så det opfylder dine organisatoriske sikkerhed og overholdelse af krav. Organisationer skal også overveje ved hjælp af kryptering for at reducere risici, som er relateret til uautoriseret adgang til. Det anbefales også, at du krypterer drev før du skriver følsomme data for dem.

Sørg for at kryptere din VM datamængder og start lydstyrken for at beskytte data på resten i kontoen Azure-lager. Beskytte krypteringsnøgler og hemmeligheder ved at udnytte [Azure nøgle samling](../key-vault/key-vault-whatis.md).

Dine lokale Windows-servere, kan du overveje følgende kryptering bedste fremgangsmåder:

- Bruge [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) til kryptering af data
- Gemme oplysninger om genoprettelse i Active Directory-Domænetjenester.
- Hvis der er et problem, BitLocker-nøgler er blevet afsløret, anbefaler vi, at du enten formatere drevet til at fjerne alle forekomster af BitLocker metadata fra drevet, eller, at du dekryptere og kryptere hele drevet igen.

Organisationer, der ikke gennemtvinger kryptering af data er mere sandsynligvis problemer med dataintegritet, såsom skadelig eller uautoriserede brugere stjæle data skal være tilgængelig og er blevet kompromitteret konti får uautoriseret adgang til data i Ryd format. Ud over disse risici skal virksomheder, der skal overholde brancheregler bevise, at de er forsigtig og bruger den korrekte sikkerhedskontrol til at forbedre datasikkerhed.

Du kan få mere at vide om Azure Disk kryptering ved at læse artiklen [Azure Disk kryptering til Windows og Linux IaaS FOS](azure-security-disk-encryption.md).

## <a name="use-hardware-security-modules"></a>Bruge Hardware sikkerhed moduler

Branche krypteringsløsninger Brug hemmeligt taster til at kryptere data. Det er derfor vigtigt, at disse taster er gemt på en sikker måde. Key management bliver en integreret del af databeskyttelse, da det kan bruges til at gemme hemmeligt nøgler, der bruges til at kryptere data.

Azure disk kryptering bruger [Azure nøgle samling](https://azure.microsoft.com/services/key-vault/) til at hjælpe dig med at kontrollere og administrere disk krypteringsnøgler og hemmeligheder i abonnementet vigtige samling og sikre, at alle data i virtuelt diskene er krypteret på resten i dit Azure-lager. Du skal bruge Azure-tasten samling overvåge taster for og brugen af politik.

Der findes mange naturlige risici i forbindelse med ikke at have relevante sikkerhedskontrol for at beskytte tasterne hemmeligt, der blev brugt til at kryptere dine data. Hvis hackere har adgang til de hemmelige nøgler, vil de kunne dekryptere dataene og potentielt har adgang til fortrolige oplysninger.

Du kan få mere at vide om generelle anbefalinger til Certifikatstyring i Azure ved at læse artiklen [Certifikatstyring i Azure: råd](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/).

Læs [Introduktion til Azure nøgle samling](../key-vault/key-vault-get-started.md)kan finde flere oplysninger om Azure-tasten samling.

## <a name="manage-with-secure-workstations"></a>Administrere med sikker arbejdsstationer

Da størstedelen af angreb tilpasser slutbrugerens, bliver slutpunktet en af de primære punkter af angreb. Hvis en hacker bringer slutpunktet, kan hun udnytte brugerens legitimationsoplysninger for at få adgang til virksomhedens data. De fleste slutpunkt angreb kan udnytte fakultet, slutbrugere er administratorer på deres lokale arbejdsplads.

Du kan reducere disse risici ved hjælp af en sikker administrationsstation. Vi anbefaler, at du bruger en [Privilegerede Access arbejdsstationer (PAW)](https://technet.microsoft.com/library/mt634654.aspx) til at reducere angrebsoverfladen i arbejdsstationer. Disse sikker administrationsarbejdsstationerne kan hjælpe dig med at reducere nogle af disse angreb sikrer, at dine data er bedre sikret. Sørg for at bruge PAW at øger ydeevnen og låse computeren. Dette er et vigtigt trin til at levere høj sikkerhed sikkerhed for følsomme konti, opgaver og beskyttelse af data.

Manglende slutpunkt beskyttelse kan lægge dataene på risiko, Sørg for at håndhæve sikkerhedspolitikker på tværs af alle enheder, der bruges til at bruge data, uanset hvor data (skyen eller lokalt).

Du kan læse mere om rettigheder få adgang til arbejdsstation ved at læse artiklen [Sikring af privilegerede adgang](https://technet.microsoft.com/library/mt631194.aspx).

## <a name="enable-sql-data-encryption"></a>Aktivere SQL datakryptering

[Kryptering af gennemsigtig data Azure SQL-Database](https://msdn.microsoft.com/library/dn948096.aspx) (TDE) hjælper med at beskytte mod truslen om skadelig aktivitet ved at udføre realtid kryptering og dekryptering af den database, tilknyttede sikkerhedskopier og transaktionslogfiler på resten uden ændringer til programmet.  TDE krypterer opbevaring af en hel database ved hjælp af en symmetriske nøgle, kaldes krypteringsnøglen database.

Selvom for hele lagringen er krypteret, er det vigtigt at også kryptere databasen sig selv. Dette er en implementering af beskyttelse i metode til databeskyttelse. Hvis du bruger [Azure SQL-Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) og ønsker at beskytte følsomme oplysninger som kreditkort eller CPR-numre, kan du kryptere databaser med FIPS 140-2 valideret 256 bit AES-kryptering som opfylder kravene i mange branchestandarder (f.eks., HIPAA, PCI).

Det er vigtigt at forstå, filer, der relaterer til [bufferen puljen lokalnummer](https://msdn.microsoft.com/library/dn133176.aspx) (BPE) ikke er krypteret, når en database er krypteret med TDE. Du skal bruge fil niveau kryptering Systemværktøjer som BitLocker eller [Kryptering af filsystem](https://technet.microsoft.com/library/cc700811.aspx) (EFS) til BPE relaterede filer.

Siden en autoriseret bruger som en sikkerhedsadministrator eller en databaseadministrator kan få adgang til dataene selvom databasen er krypteret med TDE, skal du også følge nedenstående anbefalinger:

- SQL-godkendelse på niveauet for database
- Azure AD-godkendelse ved hjælp af RBAC roller
- Brugere og programmer skal bruge separate konti til at godkende. Denne måde, du kan begrænse tilladelser til brugere og programmer og reducere risikoen for skadelig aktivitet
- Implementere database-sikkerhed på brugerniveau ved hjælp af faste databaseroller (såsom db_datareader eller db_datawriter), eller du kan oprette brugerdefinerede roller for dit program til at give eksplicitte tilladelser til markerede databaseobjekter

Organisationer, der ikke bruger kryptering på databasen kan være mere udsatte for angreb, der kan få adgang til data, der er placeret i SQL-databaser.

Du kan få mere at vide om SQL TDE kryptering ved at læse artiklen [Gennemsigtig kryptering af Data med Azure SQL-Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="protect-data-in-transit"></a>Beskytte data undervejs

Beskytte data undervejs skal være vigtig del af din strategi for beskyttelse af data. Da data Flytter frem og tilbage fra mange placeringer, er den generelle anbefaling, at du altid bruger SSL/TLS protokoller til at udveksle data på tværs af forskellige steder. I nogle tilfælde vil du måske til at isolere salgskanalen hele kommunikationen mellem din i det lokale miljø og skyen infrastruktur ved hjælp af et virtuelt privat netværk (VPN).

Til at flytte mellem dit lokale infrastruktur og Azure-data, bør du overveje relevante sikkerhedsforanstaltninger som HTTPS eller VPN.

Organisationer, hvor du har brug for at sikre adgangen fra flere arbejdsstationer Brug placeret i det lokale miljø til Azure, [Azure - til-websted VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md).

For organisationer, hvor du har brug for at sikre adgangen fra én arbejdsstation, der er placeret i det lokale miljø til Azure, bruge [punkt-til-websted VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md).

Større data sæt kan flyttes via et dedikeret hurtige WAN link som [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Hvis du vælger at bruge ExpressRoute, du kan også kryptere data på program-niveau ved hjælp af [SSL/TLS](https://support.microsoft.com/kb/257591) eller andre protokoller for tilføjet beskyttelse.

Hvis du interagerer med Azure-lager via Azure-portalen, sker alle transaktioner via HTTPS. [Lagerplads REST-API](https://msdn.microsoft.com/library/azure/dd179355.aspx) over HTTPS kan også bruges til at interagere med [Azure-lager](https://azure.microsoft.com/services/storage/) og [Azure SQL-Database](https://azure.microsoft.com/services/sql-database/).

Organisationer, der ikke til at beskytte data undervejs er mere udsatte for [mand i midten angreb](https://technet.microsoft.com/library/gg195821.aspx), [aflytning](https://technet.microsoft.com/library/gg195641.aspx) og session kapring. Disse angreb kan være det første trin i at få adgang til fortrolige data.

Du kan få mere at vide om Azure VPN-indstilling ved at læse denne artikel for [planlægning og design til VPN-Gateway](../vpn-gateway/vpn-gateway-plan-design.md).

## <a name="enforce-file-level-data-encryption"></a>Gennemtving kryptering af filer niveau data

Endnu et lag med beskyttelse, der kan øge sikkerhedsniveauet til dine data kryptere filen selve, uanset filens placering.

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) bruger kryptering, identitet og godkendelse politikker til at beskytte dine filer og mail. Azure RMS fungerer på tværs af flere enheder – telefoner, tablets og pc'er ved at beskytte både i din organisation og uden for organisationen. Denne funktion er muligt, fordi Azure RMS tilføjer et niveau for beskyttelse, forbliver med dataene, selvom den forlader organisationen grænser.

Når du bruger Azure RMS til at beskytte dine filer, bruger du branchestandard kryptering med fuld understøttelse af [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Når du udnytte Azure RMS til databeskyttelse, du har den sikkerhed, beskyttelse forbliver med fil, selvom det er kopieret til lagerplads, ikke der styres af IT, som en skylagringstjeneste. Den samme indtræffer for filer, der deles via mail, filen er beskyttet som en vedhæftet fil til en e-mail-meddelelse med instruktioner om at åbne den vedhæftede fil beskyttet.

Ved planlægning af Azure RMS indføring anbefaler vi følgende:

- Installere [RMS deling app](https://technet.microsoft.com/library/dn339006.aspx). Denne app integrerer med Office programmer ved at installere en Office-tilføjelsesprogram så brugere kan nemt beskytte filer direkte.
- Konfigurere programmer og tjenester for at understøtte Azure RMS
- Oprette [brugerdefinerede skabeloner](https://technet.microsoft.com/library/dn642472.aspx) , der afspejler virksomhedens behov. For eksempel: en skabelon til øverste hemmeligt data, der skal anvendes i alle øverste hemmeligt relateret mails.

Organisationer, der er svage om beskyttelse af [data klassificering](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) og filen kan være mere udsatte for uautoriseret videregivelse data. Uden korrekte Filbeskyttelse organisationer ikke kunne hente business viden, overvåge for misbrug og forhindre skadelig adgang til filer.

Du kan få mere at vide om Azure RMS ved at læse artiklen [Introduktion til Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).
