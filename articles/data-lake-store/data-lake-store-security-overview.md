<properties
   pageTitle="Oversigt over sikkerhed i sø datalager | Microsoft Azure"
   description="Forstå, hvordan Azure datalager sø er en mere sikker stor datalager"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/02/2016"
   ms.author="nitinme"/>

# <a name="security-in-azure-data-lake-store"></a>Sikkerhed i Azure sø datalager

Mange virksomheder at drage fordel af stor data analyser af business indsigt til at hjælpe dem med at træffe de rigtige beslutninger. En organisation kan have komplekse og organiseret miljø med et stigende antal forskellige brugere. Det er vigtigt for en virksomhed at kontrollere, at vigtige forretningsdata gemmes mere sikkert, med det korrekte adgangsniveau tildeles individuelle brugere. Azure datalager sø er beregnet til at opfylder sikkerhedskravene. I denne artikel kan du lære sikkerhedsfunktioner for sø datalager, herunder:

* Godkendelse
* Godkendelse
* Netværkets isolation
* Beskytte data
* Overvågning

## <a name="authentication-and-identity-management"></a>Administration af godkendelse og -identitet

Godkendelse er den proces, som en brugers identitet er bekræftet, når brugeren skal arbejde sammen med sø datalager eller med alle de tjenester, der opretter forbindelse til Data sø Store. Af identitet administration og til godkendelse bruger sø datalager [Azure Active Directory](../active-directory/active-directory-whatis.md), en omfattende identitet og access management cloud-løsning, der forenkler for administration af brugere og grupper.

Hvert Azure abonnement kan være knyttet til en forekomst af Azure Active Directory. Kun brugere og -tjeneste-id'erne, der er defineret i din Azure Active Directory-tjenesten kan få adgang til kontoen sø datalager ved hjælp af portalen Azure, kommandolinjen værktøjer eller gennem klientprogrammer opbygger din organisation ved hjælp af Azure Data sø Store SDK. Vigtigste fordele ved at bruge Azure Active Directory som en metode til kontrol af centralt access er:

* Forenklet identitet Livscyklusadministration. På identiteten for en bruger eller en tjeneste (en vigtigste service-identitet) kan hurtigt oprettes og hurtigt tilbagekaldt ved blot at slette eller deaktivering af kontoen i kataloget.

* Multi-Factor authentication. [Multi-Factor authentication](../multi-factor-authentication/multi-factor-authentication.md) indeholder en ekstra sikkerhedslag til bruger logon og transaktioner.

* Godkendelse fra enhver klient gennem et standard Åbn protokol, som OAuth eller OpenID.

* Sammenslutning med enterprise katalogtjenester og skyen id-udbydere.

## <a name="authorization-and-access-control"></a>Godkendelse og adgangskontrol

Når Azure Active Directory godkender en bruger, så brugeren kan få adgang til Azure sø datalager, adgangstilladelser godkendelse kontrolelementer for sø datalager. Sø datalager adskiller godkendelse for konto-relaterede og data-Relaterede aktiviteter på følgende måde:

* [Rollebaseret adgangskontrol](../active-directory/role-based-access-control-what-is.md) (RBAC), som Azure for kontoadministration
* POSIX ACL for at få adgang til data i store


### <a name="rbac-for-account-management"></a>RBAC for kontoadministration

Fire grundlæggende roller er defineret for sø datalager som standard. Rollerne, der tillader forskellige handlinger på en sø datalager konto via Azure-portalen, PowerShell-cmdletter og REST API'er. Rollerne, der ejer og bidragyder kan udføre en række administrationsfunktioner på kontoen. Du kan tildele rollen Læser til brugere, som kun arbejde med data.

![RBAC roller] (./media/data-lake-store-security-overview/rbac-roles.png "RBAC roller")

Bemærk, selvom er tildelt roller for kontoadministration, påvirker nogle roller adgang til data. Du skal bruge ACLs til at styre adgangen til handlinger, som en bruger kan udføre i filsystemet. I følgende tabel vises en oversigt over administration af rettigheder og data adgangsrettigheder til standardrollerne.

| Roller                    | Rights Management               | Data adgangsrettigheder | FORKLARING |
| ------------------------ | ------------------------------- | ------------------ | ----------- |
| Ingen fået tildelt rollen som         | Ingen                            | Underlagt ACL    | Brugeren kan ikke bruge Azure portal eller Azure PowerShell-cmdlet'er til at lede sø datalager. Brugeren kan bruge kommandolinjen værktøjer.
| Ejer  | Alle  | Alle  | Rollen ejer er en superbruger. Denne rolle kan administrere alt og har fuld adgang til data.
| Reader   | Skrivebeskyttet  | Underlagt ACL    | Rollen Læser kan se alt vedrørende kontoadministration, såsom som brugeren er tildelt til hvilken rolle. Rollen Læser kan ikke foretage eventuelle ændringer.   |
| Bidragyder              | Alt undtagen Tilføj og fjern roller | Underlagt ACL    | Rollen Bidragyder kan administrere nogle aspekter af en konto, som installationer og oprettelse og administration af beskeder. Rollen Bidragyder kan ikke tilføje eller fjerne roller.
| Access Brugeradministrator | Tilføje og fjerne roller            | Underlagt ACL    | Rollen Brugeradministrator Access kan administrere brugeradgang til konti. |

Flere oplysninger under [tildele brugere eller sikkerhedsgrupper til sø datalager konti](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Ved hjælp af ACLs til handlinger på filsystemet

Sø datalager er et hierarkisk filsystem som Hadoop-distribueret fil System (HDFS), og den understøtter [POSIX ACLs](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Det styrer læsning (r), skrivning (w) og udføre (x)-tilladelser til ressourcer for rollen ejer, gruppen Ejere og for andre brugere og grupper. Sø Store offentlige datavisning (den aktuelle udgave), er ACLs aktiveret for rodmappen, undermapper og enkelte filer. De adgangskontrollister, der anvendes på rodmappen gælder også for alle underordnede mapper og filer.

Vi anbefaler, at du definerer ACLs for flere brugere ved hjælp af [sikkerhedsgrupper](../active-directory/active-directory-accessmanagement-manage-groups.md). Føje brugere til en sikkerhedsgruppe, og derefter tildele ACLs for en fil eller mappe til denne sikkerhedsgruppe. Dette er nyttigt, når du vil angive brugerdefinerede access, fordi du er begrænset til at tilføje maksimalt ni posterne til brugerdefineret access. Du kan finde flere oplysninger om, hvordan du bedre sikre data, der er gemt i sø datalager ved hjælp af Azure Active Directory-sikkerhedsgrupper, kan du se [tildele brugere eller sikkerhedsgruppe som ACLs til Azure datalager sø-filsystemet](data-lake-store-secure-data.md#filepermissions).

![Listen standardfarver og brugerdefinerede access] (./media/data-lake-store-security-overview/adl.acl.2.png "Listen standardfarver og brugerdefinerede access")

## <a name="network-isolation"></a>Netværkets isolation

Brug sø datalager til at styre adgangen til dit datalager på niveauet for netværk. Du kan oprette firewalls og definere en IP-adresseområder for de klienter, der er tillid til. Med en IP-adresseområder kun klienter, der har en IP-adresse i det angivne område kan oprette forbindelse til Data sø Store.

![Firewall-indstillinger og IP-adgang] (./media/data-lake-store-security-overview/firewall-ip-access.png "Firewall-indstillinger og IP-adresse")

## <a name="data-protection"></a>Beskytte data

Organisationer vil sikre dig, at deres vigtige data er beskyttet i hele dets livscyklus. Data undervejs skal bruger sø datalager branchestandard sikkerhed TLS (Transport Layer)-protokollen til at sikre data, der flytter mellem en klient og sø datalager.

Beskyttelse af data efter data på resten bliver tilgængelig i fremtidige versioner.

## <a name="auditing-and-diagnostic-logs"></a>Logfilerne overvågning og diagnosticering

Du kan bruge overvågning eller diagnosticering logfiler, afhængigt af om du leder efter logfiler til administration-relaterede aktiviteter eller data-Relaterede aktiviteter.

*  Administration-relaterede aktiviteter bruger Azure ressourcestyring API'er og er fået i portalen Azure via overvågningslogge.
*  Data-Relaterede aktiviteter bruger WebHDFS REST API'er og er fået i portalen Azure via diagnosticeringslogfiler.

### <a name="auditing-logs"></a>Overvågningslogfiler

En organisation kan kræve tilstrækkelig revisionsspor for at overholde regler, hvis den har brug at gå til bestemte hændelser. Sø datalager har indbyggede overvågning og overvågning og logføres alle konto management aktiviteter.

Få vist konto management revisionsspor, og vælg de kolonner, du vil logge. Du kan også eksportere overvågningslogge til Azure-lager.

![Overvågningslogge] (./media/data-lake-store-security-overview/audit-logs.png "Overvågningslogge")

### <a name="diagnostic-logs"></a>Diagnosticeringslogge

Du kan angive dataadgang revisionsspor i Azure-portalen (i indstillinger for diagnosticering) og opretter en Azure Blob storage konto, hvor logfilerne er gemt.

![Diagnosticeringslogge] (./media/data-lake-store-security-overview/diagnostic-logs.png "Diagnosticeringslogge")

Når du konfigurerer diagnosticering indstillinger, kan du se logge på fanen **Diagnosticeringslogfiler** .

Du kan finde flere oplysninger om arbejde med diagnosticeringslogfiler med Azure sø datalager, [Access diagnosticeringslogfiler for sø datalager](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Oversigt

Enterprise-kunder forventer en data analytics skyen platform, der er sikker og nem at bruge. Azure datalager sø er beregnet til at adresse kravene til administration af identitet og godkendelse via Azure Active Directory-integration ACL-baseret godkendelse netværkets isolation, og kryptering af data under overførsel og på Placer (kommer i fremtiden) og revision.

Hvis du vil se nye funktioner i sø datalager, sende os din feedback i [Data sø Store UserVoice-forummet](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Se også

- [Oversigt over Azure sø datalager](data-lake-store-overview.md)
- [Komme i gang med sø datalager](data-lake-store-get-started-portal.md)
- [Sikre data i sø datalager](data-lake-store-secure-data.md)
