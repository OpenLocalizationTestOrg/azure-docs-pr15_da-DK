<properties
    pageTitle="Azure stak App Service Technical Preview 1, før du går i gang | Microsoft Azure"
    description="Trin til at fuldføre før du anvender Web Apps på Azure stak"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>
    
# <a name="before-you-get-started-with-azure-stack-web-apps"></a>Før du går i gang med Azure stak Web Apps

> [AZURE.NOTE] Følgende oplysninger gælder kun for Azure stak TP1 installationer.

Du skal nogle elementer, der skal installere Azure stak Web Apps:

- En færdige installation af [Azure stak Technical Preview 1](azure-stack-run-powershell-script.md)
- Nok ledig plads i systemet Azure stak til en lille installation af Azure stak Web Apps.  Den nødvendige plads er omkring 20GB ledig diskplads.
- [En server, der kører SQL Server](#SQL-Server).

>[AZURE.NOTE] Følgende trin alle foregå på klienten VM.

## <a name="before-you-deploy-azure-stack-web-apps"></a>Inden du implementerer Azure stak Web Apps

Du skal installere en ressource-udbyder, skal du køre PowerShell integreret Scripting Environment(ISE) som administrator. Derfor skal du tillade cookies og JavaScript i Internet Explorer-profil, du bruger til at logge på Azure Active Directory.

## <a name="turn-off-internet-explorer-enhanced-security"></a>Deaktivere sikkerhed i Internet Explorer udvidet

1.  Log på Azure stak bevis af konceptet (Konceptet) computeren som **AzureStack/administrator**, og Åbn derefter **Server Manager**.
2.  Deaktivere **Internet Explorer Enhanced Security Configuration** for både administratorer og brugere.
3.  Log på virtuelt ClientVM.AzureStack.local som administrator, og Åbn derefter **Server Manager**.
4.  Deaktivere **Internet Explorer Enhanced Security Configuration** for både administratorer og brugere.

## <a name="enable-cookies"></a>Aktivere cookies

1.  Vælg **Start**> **alle apps**> **Windows tilbehør**. Højreklik på **Internet Explorer** > **flere** > **Kør som administrator**.
2.  Hvis du bliver bedt om det, Vælg **det anbefales at bruge sikkerhed**, og klik derefter på **OK**.
3.  I Internet Explorer skal du vælge **værktøjer** (tandhjulsikonet) > **Internetindstillinger** > **beskyttelse af personlige oplysninger** > **Avanceret**.
4.  Vælg **Avanceret**. Sørg for, at begge **Acceptér** afkrydsningsfelterne er markeret, og vælg derefter **OK** og **OK** igen.
5.  Luk Internet Explorer, og genstart PowerShell ISE som administrator.

## <a name="install-the-latest-version-of-azure-powershell"></a>Installere den nyeste version af Azure PowerShell

1.  Log på computeren Azure stak Konceptet som **AzureStack/administrator**.
2.  Brug forbindelse til Fjernskrivebord til at logge på virtuelt ClientVM.AzureStack.local som administrator.
3.  Åbn **Kontrolpanel** og vælg **Fjern et program**. 
4.  Højreklik på **Microsoft Azure PowerShell - November 2015** , og vælg **Fjern**.
5.  Når installationen er fjernet er afsluttet, genstart virtuelt ClientVM.AzureStack.local
6.  Hente og installere den nyeste [Azure PowerShell](http://aka.ms/azstackpsh).


## <a name="sql-server"></a>SQL Server

Azure stak Web Apps-installer er som standard angivet til brug SQL Server, der er installeret sammen med Azure stak SQL ressource Provider. Når du installerer SQL Server ressource Provider (SQL RP) sikre, at du noterer database administrator brugernavn og din adgangskode. Du skal have både, når du installerer Azure stak Web Apps.
Bemærk: Du får også mulighed for at installere og bruge en anden server til at køre SQL Server. Du kan vælge SQL Server-forekomsten skal bruges, når du har fuldført indstillingerne i installationsprogrammet til Azure stak Web Apps.
