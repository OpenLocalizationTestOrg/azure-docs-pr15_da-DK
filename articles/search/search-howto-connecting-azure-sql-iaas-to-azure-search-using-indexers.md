<properties 
    pageTitle="Konfigurere en forbindelse fra et Azure Søg indekseringsprogram til SQL Server på en Azure virtuelt | Microsoft Azure | Indeks" 
    description="Aktivere krypterede forbindelser og konfigurere firewallen for at tillade forbindelser til SQL Server på en Azure VM (virtual machine) fra et indekseringsprogram på Azure søgning." 
    services="search" 
    documentationCenter="" 
    authors="jack4it" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="09/26/2016" 
    ms.author="jackma"/>

# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Konfigurere en forbindelse fra et Azure Søg indekseringsprogram til SQL Server på en Azure VM

Som nævnt i [Forbindelse Azure SQL-Database til Azure søgning ved hjælp af indeks](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md#frequently-asked-questions), oprettelse af indeks mod **SQL Server på Azure FOS** (eller **SQL Azure FOS** til kort) understøttes via Azure-søgning, men der er et par sikkerhedsrelaterede forudsætninger til tager sig af første. 

**Opgavens varighed:** Om 30 minutter, installeret Hvis du allerede et certifikat på VM.

## <a name="enable-encrypted-connections"></a>Aktivere krypterede forbindelser

Azure Søg kræver en krypteret kanal for alle indekseringsprogram anmodninger over en offentlig internetforbindelse. Dette afsnit indeholder trinnene, hvis du vil gøre dette til at fungere.

1. Se egenskaberne for certifikatet for at bekræfte emnenavnet er det fulde domænenavn (fulde Domænenavn) af Azure VM. Du kan bruge et værktøj som CertUtils eller snap-in certifikater til at se egenskaberne for. Du kan få det fulde Domænenavn fra VM service blade Essentials sektion, i feltet **offentlige IP-adresse/DNS-navnet etiket** i [Azure-portalen](https://portal.azure.com/).

    - For FOS oprettet ved hjælp af skabelonen nyere **Ressourcestyring** , det fulde Domænenavn er formateret som `<your-VM-name>.<region>.cloudapp.azure.com`. 

    - For ældre FOS oprettet som et **Klassisk** VM, det fulde Domænenavn er formateret som `<your-cloud-service-name.cloudapp.net>`. 

2. Konfiguration af SQL Server for at bruge det certifikat, ved hjælp af Registreringseditor (regedit). 

    Selvom SQL Server Configuration Manager bruges ofte til denne opgave, kan du bruge den til dette scenario. Det kan ikke finde det importerede certifikat, fordi det fuldstændige Domænenavn for VM på Azure ikke stemmer overens med det fulde Domænenavn ifølge VM (den identificerer domænet som den lokale computer eller netværksdomænet som den er tilsluttet). Når navne ikke stemmer overens, kan du bruge regedit til at angive certifikatet.

    - Gå til denne registreringsdatabasenøgle i regedit,: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
    Den `[MSSQL13.MSSQLSERVER]` del varierer baseret på version og forekomst navn. 

    - Værdien af tasten **certifikat** til **miniature** for det SSL-certifikat, du har importeret til VM.

    Der er flere måder at få miniature, nogle bedre end andre. Hvis du kopierer det fra snap-in **certifikater** i MMC, vil du sandsynligvis vælge en usynlige foranstillede tegn [som beskrevet i denne supportartikel](https://support.microsoft.com/kb/2023869/), hvilket medfører en fejl, når du forsøger en forbindelse. Der findes flere løsninger for at rette dette problem. Den nemmeste er at tilbage, og skriv derefter det første tegn i miniature til at fjerne det foranstillede tegn i feltet nøgleværdi i regedit. Du kan også bruge et andet værktøj til at kopiere miniature.

3. Give tilladelser til tjenestekontoen. 

    Kontrollér, at SQL Server-tjenestekontoen får relevante tilladelse til den private nøgle for SSL-certifikatet. Hvis du overser dette trin, starter ikke SQL Server. Du kan bruge **certifikater** snap-in eller **CertUtils** til denne opgave.

4. Genstarte SQL Server-tjenesten.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Konfigurere netværksmuligheder for SQL Server i VM

Når du har konfigureret den krypteret forbindelse, der kræves, før Azure søgning, er der flere konfigurationstrinnene indbygget til SQL Server på Azure FOS. Hvis du allerede har gjort det, er næste trin at fuldføre konfigurationen ved hjælp af en af følgende artikler:

- Du kan finde en **Ressourcestyring** VM [Opret forbindelse til en SQL Server virtuel maskine på Azure ved hjælp af Ressourcestyring](../virtual-machines/virtual-machines-windows-sql-connect.md). 

- Se [forbinde til en SQL Server virtuel maskine på Azure klassisk](../virtual-machines/virtual-machines-windows-classic-sql-connect.md)til en **Klassisk** VM.

Gennemse især afsnittet i hver artikel til at "oprette forbindelse via internettet".

## <a name="configure-the-network-security-group-nsg"></a>Konfigurere sikkerhedsgruppen netværk (NSG)

Det er ikke usædvanlige skal du konfigurere NSG og tilsvarende Azure slutpunkt eller listen ACL (Access Control) for at gøre din Azure VM tilgængeligt for andre parter. Du har gjort det før for at give dine egne programlogik til at oprette forbindelse til din SQL Azure VM sandsynligvis. Det er ikke anderledes for en Azure Søg-forbindelse til din SQL Azure VM. 

Linkene nedenfor viser instruktioner på NSG konfiguration for VM installationer. Brug disse instruktioner til ACL Azure Søg ark baseret på dens IP-adresse.

> [AZURE.NOTE] Få baggrundsoplysninger [Hvad er en sikkerhedsgruppe netværk?](../virtual-network/virtual-networks-nsg.md)

- Se, [hvordan du opretter NSGs til ARM installationer](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)for en **Ressourcestyring** VM. 

- Til en **Klassisk** VM, se, [hvordan du opretter NSGs til klassisk installationer](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

IP-adresser, kan dog udgøre nogle udfordringer, der er nemt løse, hvis du kender til problem og mulige løsninger. De resterende afsnit indeholder anbefalinger til håndtering af problemer i forbindelse med IP-adresser i ACL.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Begrænse adgangen til search-tjenesten IP-adresse

Vi anbefaler, at du begrænser adgangen til IP-adressen for din søgetjeneste i ACL i stedet for at gøre dine SQL Azure FOS åbent for alle anmodninger om forbindelse. Du kan nemt finde ud af IP-adressen ved at pinge det fulde Domænenavn (eksempelvis `<your-search-service-name>.search.windows.net`) af search-tjenesten.

#### <a name="managing-ip-address-fluctuations"></a>Administration af IP-adresse udsving

Hvis din søgetjenesten har kun én Søg-enhed (det vil sige, én replika og én partition), ændres IP-adressen under rutinemæssige tjenesten genstartes, gør ugyldig en eksisterende ACL med din søgetjenesten IP-adresse.

En metode til at undgå de efterfølgende forbindelsesfejl er at bruge mere end én replika og én partition i Azure Søg. Hvis du gør det afhænger af farvemodel, men det også løser problemet IP-adresse. I Azure Søg ændres IP-adresser ikke, når du har mere end én Søg enhed.

En anden metode er at tillade forbindelsen til mislykkes, og klik derefter omkonfigurere ACLs i NSG. I gennemsnit, kan du forvente IP-adresser til at ændre efter et par uger. For kunder, der har kontrolleret indeksering på jævnligt, kan denne fremgangsmåde være interessant.

En tredje interessant (men ikke særligt sikker) fremgangsmåde er at angive IP-adresseområder for det Azure område, hvor search-tjenesten er klargjort. På listen over IP-intervaller, offentlige IP-adresser er allokeret til Azure ressourcer er blevet publiceret på [Azure Datacenter IP-intervaller](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Medtage Azure Søg portalen IP-adresser

Hvis du ved hjælp af portalen Azure til at oprette et indeks, Azure Søg portalen logik også skal have adgang til din SQL Azure VM under klokkeslættet for oprettelsen af. Du kan finde Azure Søg portalen IP-adresser ved at pinge `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Næste trin

Med konfiguration af vejen, kan du nu angive en SQL Server på Azure VM som datakilde til et Azure Søg indekseringsprogram. Du kan få flere oplysninger i [Forbindelse Azure SQL-Database til Azure søgning ved hjælp af indeks](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) .
