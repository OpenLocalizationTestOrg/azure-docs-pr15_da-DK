<properties
    pageTitle="Installere provideren MySQL ressource på Azure stak | Microsoft Azure"
    description="Detaljeret vejledning til at udrulle provideren MySQL ressource til Azure stak."
    services="azure-stack"
    documentationCenter=""
    authors="Dumagar"
    manager="byronr"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dumagar"/>


# <a name="deploy-the-mysql-resource-provider-on-azure-stack-to-use-with-webapps"></a>Installere provideren MySQL ressource på Azure stak til brug med WebApp

> [AZURE.NOTE] Følgende oplysninger gælder kun for Azure stak TP1 installationer.

Brug denne artikel for at følge den detaljerede vejledning til konfiguration af provideren MySQL ressource på Azure stak bevis for konceptet, så du kan begynde at [bruge MySQL-databaser](azure-stack-mysql-rp-deploy-short.md) på Azure stak, herunder brug af MySQL som back-end for WordPress websteder, der er oprettet med [Azure Web Apps](azure-stack-webapps-deploy.md).

## <a name="set-up-steps-before-you-deploy"></a>Konfigurere trin, før du installerer

Før du installerer den ressource-udbyder, skal du:

- Har et standardbillede i Windows Server med .NET 3.5
- Deaktivere Internet Explorer (IE) udvidet sikkerhed
- Installere den nyeste version af Azure PowerShell

### <a name="create-an-image-of-windows-server-including-net-35"></a>Oprette et billede af Windows Server, herunder .NET 3.5

Hvis du har hentet Azure stak bit efter 2/23/2016, fordi base Windows Server 2012 R2 standardbilledet indeholder .NET 3.5 framework i denne overførsel og nyere, kan du springe dette trin.

Hvis du hentede, før 2/23/2016, skal du oprette en Windows Server 2012 R2 Datacenter Virtuelle med .NET 3.5 billede, og sæt er som standardbilledet i Platform billede lager.

### <a name="turn-off-ie-enhanced-security-and-enable-cookies"></a>Deaktivere IE forbedret sikkerhed og aktivere cookies

For at installere en ressource-udbyder, skal køre du PowerShell ISE Integrated Scripting miljø () som administrator, så skal du tillade cookies og JavaScript i Internet Explorer profilen du kan bruge til at logge på Azure Active Directory til både administrator- og logon.

**Forbedret sikkerhed for at deaktivere Internet Explorer:**

1. Log på Azure stak bevis af konceptet (konceptet) computeren som en AzureStack/administrator, og Åbn derefter Server Manager.

2. Deaktivere **Internet Explorer Enhanced Security Configuration** for både administratorer og brugere.

3. Log på virtuelt **ClientVM.AzureStack.local** som administrator, og Åbn derefter Server Manager.

4. Deaktivere **Internet Explorer Enhanced Security Configuration** for både administratorer og brugere.

**Aktivere cookies:**

1. På startskærmen i Windows, skal du klikke på **alle apps**, skal du klikke på **Windows tilbehør**, højreklik på **Internet Explorer**, peg på **flere**og vælg derefter **Kør som administrator**.

2. Hvis du bliver bedt om det, Markér **det anbefales at bruge sikkerhed**, og klik derefter på **OK**.

3. I Internet Explorer, skal du klikke på **værktøjer (tandhjulsikonet)** &gt; **Internetindstillinger** &gt; under fanen **beskyttelse af personlige oplysninger** .

4. Klik på **Avanceret**, Kontrollér, at begge **Acceptér** knapper er markeret, skal du klikke på **OK**, og klik derefter på **OK** igen.

5. Luk Internet Explorer, og genstart PowerShell ISE som administrator.

### <a name="install-an-azure-stack-compatible-release-of-azure-powershell"></a>Installere en Azure stak kompatible version af Azure PowerShell

1. Fjerne en eksisterende Azure PowerShell fra din klient VM.

2. Log på Azure stak Konceptet computeren som en AzureStack/administrator.

3. Ved hjælp af Fjernskrivebord, log på virtuelt **ClientVM.AzureStack.local** som administrator.

4. Åbn Kontrolpanel, skal du klikke på **Fjern et program** &gt; skal du klikke på **Azure PowerShell** &gt; skal du klikke på **Fjern**.

5. [Hent den nyeste Azure PowerShell, der understøtter Azure stak](http://aka.ms/azstackpsh) og installere den.

    Når du installerer PowerShell, kan du køre denne kontrol PowerShell-script for at sikre dig, at du kan oprette forbindelse til din Azure stak forekomst (en login webside skal vises).

## <a name="bootstrap-the-resource-provider-deployment-powershell"></a>Bootstrap ressource udbyder installation PowerShell

1. Tilslut Azure stak Konceptet Fjernskrivebord til clientVm.AzureStack.Local og logge på som azurestack\\azurestackuser.

2. [Hent de MySQL RP binære filer](http://aka.ms/masmysqlrp) fil og Udpak til D:\\MySQLRP.

3. Køre D:\\MySQLRP\\Bootstrap.cmd filen som en administrator (azurestack\administrator).

    Dette åbner filen Bootstrap.ps1 i PowerShell ISE.

4. Når windows PowerShell ISE fuldfører indlæsning, skal du klikke på knappen "Afspil", eller tryk på F5.

    To overordnede faner indlæses, hver indeholder alle scripts og filer, du vil installere udbyderen MySQL ressource.

## <a name="prepare-prerequisites"></a>Forberede forudsætninger

Klik på fanen **Forberede forudsætninger for** at:

- Oprette kræves certifikater
- Hente MySQL binære filer til din Azure stak
- Overføre elementer til en konto til lagerplads på Azure stak
- Publicere galleriet elementer

### <a name="create-the-required-certificates"></a>Oprette de krævede certifikater
Denne **Ny SslCert.ps1** script tilføjer den \_. AzureStack.local.pfx SSL-certifikat til D:\\MySQLRP\\forudsætninger\\BlobStorage\\objektbeholder mappe. Certifikatet, der sikrer kommunikationen mellem provideren ressourcen og den lokale forekomst af Azure ressourcestyring.

1. Klik på fanen **Ny SslCert.ps1** under fanen **Forberede forudsætninger** overordnede og køre den.

2. Skriv en PFX adgangskode, der beskytter privat nøgle og **notere denne adgangskode**i prompten, der vises. Du skal bruge den senere.

### <a name="download-mysql-binaries-to-your-azure-stack"></a>Hente MySQL binære filer til din Azure stak

1. Vælg fanen **Hent MySqlServer.ps1** , og kør den.
2. Når du bliver bedt om det, skal du klikke på **Ja** i dialogboksen Bekræft for at acceptere den slutbrugerlicensaftale (EULA).

    Denne kommando tilføjer to zip-filer til mappen D:\MySql\Prerequisites\BlobStorage\Container.

### <a name="upload-all-artifacts-to-a-storage-account-on-azure-stack"></a>Overføre alle elementer til en konto til lagerplads på Azure stak

1. Klik på fanen **Overfør-Microsoft.MySql-RP.ps1** og køre den.

2. Skriv Azure stak service administratorlegitimationsoplysninger i dialogboksen Windows PowerShell legitimationsoplysninger anmodning.

3. Når du bliver bedt om Azure Active Directory-lejer ID, Skriv dit Azure Active Directory-lejer fuldstændige domænenavn: eksempelvis microsoftazurestack.onmicrosoft.com.

    Et pop op-vindue, der beder om legitimationsoplysninger.

    > [AZURE.TIP] Hvis pop op-vinduet ikke vises, skal du enten ikke har deaktiveret IE forbedret sikkerhed for at aktivere JavaScript på denne computer, og brugeren, eller du ikke har accepteret cookies i Internet Explorer. Se [konfigurere trin, før du installerer](#set-up-steps-before-you-deploy).

4. Skriv dine Azure stak Service Administratoroplysninger, og klik derefter på **Log på**.

### <a name="publish-gallery-items-for-later-resource-creation"></a>Publicere galleriet elementer til oprettelse af nyere ressource

Vælg fanen **Publicer GalleryPackages.ps1** og køre den. Dette script tilføjer to marketplace elementer til portalen Azure stak Konceptet marketplace, som du kan bruge til at udrulle databaseressourcer som marketplace elementer.

## <a name="deploy-the-mysql-resource-provider-vm"></a>Installere provideren MySQL ressource VM

Nu hvor du har forberedt Azure stak konceptet med de nødvendige certifikater og marketplace elementer, kan du installere en SQL Server ressource-udbyder. Klik på fanen **installere MySQL-provider** til:

   - Angive værdier i en JSON-fil, der refererer til installationsprocessen
   - Installere provideren ressource
   - Opdatere lokale DNS-Posterne
   - Registrere SQL Server ressource udbyder Adapter

### <a name="provide-values-in-the-json-file"></a>Angive værdier i JSON-fil

Klik på **Microsoft.MySqlprovider.Parameters.JSON**. Denne fil har parametre, der skal installeres korrekt til Azure stak skabelonen Azure ressourcestyring.

1. Udfyld de **tomme** parametre i JSON-filen:

    - Kontrollér, at du giver dig **adminusername** og **adminpassword** til MySQL ressource udbyder VM.

    - Kontrollér, at du angive adgangskoden til parameteren **SetupPfxPassword** , du har foretaget en note på trinnet [Forbered prequisites](#prepare-prerequisites) .

    - Kontrollér, at du angiver **basicAuthUserName** og **basicAuthPassword** parametre. **Skal du notere af disse værdier.** Du skal bruge dem senere til at registrere provideren ressource.

2. Klik på **Gem**.

### <a name="deploy-the-resource-provider"></a>Installere provideren ressource

1. Klik på fanen **Implementer-Microsoft.Mysql-provider.PS1** og køre scriptet.
2. Skriv navnet på din lejer i Azure Active Directory, når du bliver bedt om.
3. Sende dine Azure stak service Administratoroplysninger i pop op-vinduet.

Den fulde installation kan tage mellem 15 og 45 minutter på nogle meget udnyttet Azure stak POCs.

### <a name="update-the-local-dns"></a>Opdatere lokale DNS-Posterne

1. Klik på fanen **Register-Microsoft.MySQL-fqdn.ps1** og køre scriptet.
2. Når du bliver bedt om Azure Active Directory-lejer ID, Indtast dit Azure Active Directory-lejer fuldstændige domænenavn: eksempelvis **microsoftazurestack.onmicrosoft.com**.

### <a name="register-the-sql-rp-resource-provider"></a>Registrere provideren SQL RP ressource

1. Klik på fanen **Register-Microsoft.My-provider.ps1** og køre scriptet.

2. Når du bliver bedt om legitimationsoplysninger, kan du bruge hvad du har angivet som parametrene **basicAuthUserName** og **basicAuthPassword** .

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Bekræfte den ved hjælp af portalen Azure stak installation

1. Log af ClientVM, og log på igen som **AzureStack\User**.

2. Klik på **Azure stak Konceptet Portal** på computeren, og log på portalen som tjeneste.

3. Kontrollér, at installationen er fuldført. Klik på **Gennemse** &gt; **Ressourcegrupper**, skal du klikke på den ressourcegruppe, du har brugt (standard er **MySQLRP**), og Sørg for, at essentials del af bladet (øverste halvdel) læser **installation lykkedes**.


4. Kontrollér, at registreringen lykkedes. Klik på **Gennemse** &gt; **ressource udbydere**, og se efter **MySQL lokale**.

## <a name="create-your-first-mysql-database-to-test-your-deployment"></a>Oprette din første MySQL-database for at teste din installation

1. Log på portalen Azure stak Konceptet som tjeneste.

2. Klik på den **+** knappen &gt; **brugerdefineret** &gt; **MySQL Server og databaser**.

3. Udfyld formularen med database detaljer.

    **Skal du notere "servernavnet" du angiver.** Strengen forbindelser til din database omfatter "servernavnet" som en del af brugernavnet: eksempelvis ** "user@ <ServerName>"**. Du skal angive et brugernavn i dette format, når du opretter forbindelse til databasen: f.eks, når du installerer et MySQL-websted ved hjælp af Azure websted ressource-provider


## <a name="next-steps"></a>Næste trin

Prøv andre [PaaS tjenester](azure-stack-tools-paas-services.md) som [SQL Server ressource udbyder](azure-stack-sql-rp-deploy-short.md) og [Web Apps ressource udbyder](azure-stack-webapps-deploy.md).
