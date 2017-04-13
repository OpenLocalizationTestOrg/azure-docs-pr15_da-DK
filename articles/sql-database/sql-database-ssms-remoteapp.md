<properties
    pageTitle="Oprette forbindelse til SQL-Database ved hjælp af SQL Server Management Studio i Azure RemoteApp | Microsoft Azure"
    description="Brug dette selvstudium til at se, hvordan du bruger SQL Server Management Studio i Azure RemoteApp for sikkerhed og ydeevne, når du opretter forbindelse til SQL-Database"
    services="sql-database"
    documentationCenter=""
    authors="adhurwit"
    manager="jhubbard"/>

<tags
    ms.service="sql-database"
    ms.workload="data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="adhurwit"/>

# <a name="use-sql-server-management-studio-in-azure-remoteapp-to-connect-to-sql-database"></a>Bruge SQL Server Management Studio i Azure RemoteApp til at oprette forbindelse til SQL-Database

## <a name="introduction"></a>Introduktion  
Dette selvstudium viser, hvordan du bruger SQL Server Management Studio (SSMS) i Azure RemoteApp til at oprette forbindelse til SQL-Database. Det vejleder dig gennem processen med konfiguration af SQL Server Management Studio i Azure RemoteApp, beskriver fordelene og viser sikkerhedsfunktioner, som du kan bruge i Azure Active Directory.

**Anslået tid at gennemføre:** 45 minutter

## <a name="ssms-in-azure-remoteapp"></a>SSMS i Azure RemoteApp

Azure RemoteApp er en RDS tjeneste i Azure, der leverer programmer. Du kan læse mere om den her: [Hvad er RemoteApp?](../remoteapp/remoteapp-whatis.md)

SSMS kører i Azure RemoteApp får du den samme oplevelse, som kører SSMS lokalt.

![Skærmbillede, der viser SSMS kører i Azure RemoteApp][1]



## <a name="benefits"></a>Fordele

Der er mange fordele ved at bruge SSMS i Azure RemoteApp, herunder:

- Port 1433 på Azure SQL Server behøver ikke at være synlig eksternt (uden for Azure).
- Du behøver ikke at beholde tilføje og fjerne IP-adresser i Azure SQL Server-firewallen.
- Alle forbindelser er Azure RemoteApp forekommer, ved hjælp af HTTPS på port 443 ved hjælp af krypteret Remote Desktop protocol
- Der er flere brugere og kan skalere.
- Der er en af ydeevnen fra at kunne SSMS i samme område som den SQL-Database.
- Du kan overvåge brugen af Azure RemoteApp med Premium-udgaven af Azure Active Directory, som har bruger aktivitetsrapporter.
- Du kan aktivere Multi-Factor authentication (MFA).
- Access SSMS et vilkårligt sted, når du bruger en af de understøttede Azure RemoteApp-klienter, der indeholder iOS, Android, Mac, Windows Phone og Windows-PC.


## <a name="create-the-azure-remoteapp-collection"></a>Oprette samlingen Azure RemoteApp

Her er trinnene for at oprette din Azure RemoteApp af websteder med SSMS:


### <a name="1-create-a-new-windows-vm-from-image"></a>1. Opret en ny Windows VM fra billede
Bruge billedet "Windows Server Remote Desktop Session Host Windows Server 2012 R2" fra galleriet for at gøre dit nye VM.


### <a name="2-install-ssms-from-sql-express"></a>2. Installer SSMS fra SQL Express

Gå til den nye VM og gå til denne side: [Microsoft® SQL Server® 2014 Express](https://www.microsoft.com/en-us/download/details.aspx?id=42299)

Der findes en indstilling for at hente kun SSMS. Gå til installationsmappen efter overførsel, og kør installationsprogrammet for at installere SSMS.

Du skal installere SQL Server 2014 Service Pack 1. Kan du downloade den her: [Microsoft SQL Server 2014 Service Pack 1 (SP1)](https://www.microsoft.com/en-us/download/details.aspx?id=46694)

SQL Server 2014 Service Pack 1 indeholder væsentlige funktionalitet til at arbejde med Azure SQL-Database.


### <a name="3-run-validate-script-and-sysprep"></a>3. Kør Valider script og Sysprep

På skrivebordet af VM kaldes en PowerShell-script Valider. Kør denne funktion ved at dobbeltklikke på. Det kontrollere, hvor VM er klar til bruges til remote vært over programmer. Når bekræftelsen er gennemført, bliver bedt om at køre sysprep – Vælg at køre den.

Når sysprep er fuldført, skal lukkes den VM.

Hvis du vil vide mere om at oprette et Azure RemoteApp billede, skal du se: [Sådan opretter du et RemoteApp skabelonbillede i Azure](http://blogs.msdn.com/b/rds/archive/2015/03/17/how-to-create-a-remoteapp-template-image-in-azure.aspx)


### <a name="4-capture-image"></a>4. Hent billede

Når VM kører ikke længere, kan du finde den i den aktuelle portal og registrere den.

Hvis du vil vide mere om at hente et billede, se [tage et billede på en Windows Azure virtuelle maskine, der er oprettet med den klassiske implementeringsmodel](../virtual-machines/virtual-machines-windows-classic-capture-image.md)


### <a name="5-add-to-azure-remoteapp-template-images"></a>5. tilføje til Azure RemoteApp skabelon billeder

Gå til fanen billeder af webstedsskabeloner i afsnittet Azure RemoteApp i den aktuelle portal, og klik på Tilføj. Vælg "Importere et billede fra biblioteket virtuelle maskiner" i pop op-feltet, og vælg derefter det billede, du lige har oprettet.



### <a name="6-create-cloud-collection"></a>6. oprette skyen samling

Oprette en ny Azure RemoteApp skyen samling i den aktuelle portal. Vælg den Skabelonbillede, du lige har importeret med SSMS, der er installeret på den.

![Oprette nye skyen samling][2]


### <a name="7-publish-ssms"></a>7. publicere SSMS

Fanen for den nye sky samling, du vælge udgive et program fra menuen Start på publicering og derefter vælge SSMS på listen.

![Publicere App][5]

### <a name="8-add-users"></a>8. tilføje brugere

Du kan vælge de brugere, der har adgang til denne Azure RemoteApp-websteder, som kun omfatter SSMS under fanen brugeradgang.

![Tilføj bruger][6]


### <a name="9-install-the-azure-remoteapp-client-application"></a>9. Installer Azure RemoteApp-klientprogrammet

Du kan hente og installere en Azure RemoteApp klient her: [Hent | Azure RemoteApp](https://www.remoteapp.windowsazure.com/en/clients.aspx)



## <a name="configure-azure-sql-server"></a>Konfigurere Azure SQL Server

Kun konfigurationen behov er at sikre, at Azure Services er aktiveret for firewallen. Hvis du bruger denne løsning, derefter behøver du ikke at tilføje en hvilken som helst IP-adresser for at åbne firewallen. Den netværkstrafik, som er tilladt til SQL Server er fra andre Azure tjenester.


![Azure tillade][4]



## <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

MFA kan aktiveres specifikt for dette program. Gå til fanen programmer i din Azure Active Directory. Du kan finde en post til Microsoft Azure RemoteApp. Hvis du klikker på dette program og derefter konfigurere, får du vist siden herunder hvor du kan aktivere MFA for dette program.

![Aktivere MFA][3]



## <a name="audit-user-activity-with-azure-active-directory-premium"></a>Overvåge brugeraktivitet med Azure Active Directory Premium

Hvis du ikke har Azure AD Premium, derefter skal du aktivere den i afsnittet licenser i mappen. Med Premium er aktiveret, kan du tildele brugere til Premium niveau.

Når du skifter til en bruger i din Azure Active Directory, kan du derefter gå til fanen aktivitet for at se logonoplysninger til Azure RemoteApp.



## <a name="next-steps"></a>Næste trin

Når du har gennemført alle ovenstående trin, bliver du kunne køre Azure RemoteApp klienten og log på med en tildelte bruger. Du der præsenteres med SSMS som et af dine programmer, og du kan køre programmet, som du ville gøre, hvis den er installeret på din computer med adgang til Azure SQL Server.

Finde flere oplysninger om, hvordan du oprette forbindelse til SQL-Database, [Opret forbindelse til SQL-Database med SQL Server Management Studio og udføre en stikprøve T-SQL-forespørgsel](sql-database-connect-query-ssms.md).


Det er alt for nu. Nyd!



<!--Image references-->
[1]: ./media/sql-database-ssms-remoteapp/ssms.png
[2]: ./media/sql-database-ssms-remoteapp/newcloudcollection.png
[3]: ./media/sql-database-ssms-remoteapp/mfa.png
[4]: ./media/sql-database-ssms-remoteapp/allowazure.png
[5]: ./media/sql-database-ssms-remoteapp/publish.png
[6]: ./media/sql-database-ssms-remoteapp/user.png
