<properties
    pageTitle="Bruge MySQL databaser som PaaS på Azure stak | Microsoft Azure"
    description="Forstå de hurtige trin til at implementere MySQL ressource udbyder og angiver MySQL som en tjeneste på Azure stablen."
    services="azure-stack"
    documentationCenter=""
    authors="Dumagar"
    manager="bradleyb"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dumagar"/>

# <a name="use-mysql-databases-as-paas-on-azure-stack"></a>Bruge MySQL databaser som PaaS på Azure stak

> [AZURE.NOTE] Følgende oplysninger gælder kun for Azure stak TP1 installationer.

Du kan installere en MySQL ressource udbyder på Azure stablen. Når du installerer den ressource-udbyder, kan du oprette MySQL-servere og databaser via Azure ressourcestyring installation skabeloner og give MySQL databaser som en tjeneste. MySQL-databaser, der er almindelige på websteder, understøtter mange websted platforme. Når du installerer den ressource-udbyder, kan du oprette WordPress websteder fra Azure Web Apps-platformen som et tilføjelsesprogram til service (PaaS) til Azure stablen.

## <a name="quick-steps-to-deploy-the-resource-provider"></a>Hurtige trin til at udrulle provideren ressource
Følg disse trin, hvis du allerede kender, Azure stak. Hvis du vil have flere detaljer, klikke på hyperlinkene i hver sektion eller gå direkte til [Implementer MySQL Database ressource udbyder kortet på Azure stak Konceptet](azure-stack-mysql-rp-deploy-long.md).

1.  Sørg for, at den du [fuldføre alle konfigurere trin](azure-stack-mysql-rp-deploy-long.md#set-up-steps-before-you-deploy) før du installerer provideren ressource:

    - .NET 3.5 framework er allerede konfigureret i den grundlæggende Windows Server-afbildning. (Hvis du har hentet Azure stak bit efter februar, 23, 2016, kan du springe dette trin).
    - [En version af Azure PowerShell, der er kompatibelt med Azure stak er installeret](http://aka.ms/azStackPsh).
    - I Internet Explorer sikkerhedsindstillingerne på ClientVM, [Internet Explorer øget sikkerhed er slået fra og cookies er aktiveret](azure-stack-mysql-rp-deploy-long.md#Turn-off-IE-enhanced-security-and-enable-cookies).

2. [Hente filen MySQL ressource udbyder binære filer](http://aka.ms/masmysqlrp) og udtrække på ClientVM i din dokumentation for konceptet Azure stak.

3. [Køre bootstrap.cmd og scriptene](azure-stack-mysql-rp-deploy-long.md#Bootstrap-the-resource-provider-deployment-PowerShell-and-Prepare-for-deployment).

    Et sæt af scripts, der er grupperet efter to overordnede faner åbnes i PowerShell ISE Integrated Scripting miljø (). Køre alle de indlæste scripts i rækkefølge fra venstre mod højre i hver fane.

    1. Køre scripts i fanen **Forbered** fra venstre til højre for at:

        - Oprette et certifikat med jokertegn for at sikre kommunikationen mellem den ressource udbyder og Azure ressourcestyring.
        - Acceptér vilkårene MySQL slutbrugerlicensaftale (EULA) og hente de MySQL binære filer.
        - Overføre certifikater og alle andre elementer til en Azure stak lagerplads konto.
        - Publicere galleriet-pakker, så du kan implementere MySQL ressourcer via galleriet.

        > [AZURE.IMPORTANT] Hvis et af scriptene afbryder uden nogen indlysende årsag, når du har indsendt din Azure Active Directory-lejer, blokerer sikkerhedsindstillingerne muligvis en DLL-fil, der kræves at køre installationen. Se efter Microsoft.AzureStack.Deployment.Telemetry.Dll i mappen ressource udbyder for at løse dette problem ved Højreklik på det, skal du klikke på **Egenskaber**, og markér derefter afkrydsningsfeltet **Fjern blokering** under fanen **Generelt** .

    2. Køre scripts i fanen **Implementer** fra venstre til højre for at:

        - [Implementer en virtuel maskine (VM)](azure-stack-mysql-rp-deploy-long.md#Deploy-the-MySQLResource-Provider-VM) , der er vært for både din ressource udbyder, MySQL-servere og databaser, som du vil oprette en forekomst. Dette script refererer til en JSON parameterfil, som du vil opdatere med nogle værdier, før du kører scriptet.
        - [Registrere en lokal DNS-post](azure-stack-mysql-rp-deploy-long.md#Update-the-local-DNS) , der skal knyttes til udbyderen ressource VM.
        - [Registrere udbyderen ressource](azure-stack-mysql-rp-deploy-long.md#Register-the-MySQL-RP-Resource-Provider) med lokale Azure Resource Manager.

        > [AZURE.IMPORTANT] Alle scripts forudsætter, at billedet af grundlæggende operativsystemet opfylder forudsætningerne (.NET 3.5 installeret, JavaScript og cookies aktiveret på ClientVM, og den seneste version af Azure PowerShell installeret). Hvis du får fejl, når du kører scripts, Kontrollér, at du opfyldt forudsætningerne.

5. Installere en MySQL-database fra portalen Azure stak til at [teste din nye MySQL ressource udbyder](/azure-stack-MySql-rp-deploy-long.md#create-your-first-mysql-database-to=test-your-deployment). Klik på **Opret** &gt; **brugerdefineret** &gt; **MySQL Server og Database**.

Dette bør få udbyderen MySQL ressource op og køre i ca. 25 minutter.
