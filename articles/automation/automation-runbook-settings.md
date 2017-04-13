<properties 
   pageTitle="Indstillinger for Runbook"
   description="I denne artikel beskrives indstillingerne for søgekonfiguration til en runbook i Azure automatisering, og hvordan du kan ændre dem ved hjælp af både Azure Management portalen og Windows PowerShell."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="bwren" />

# <a name="runbook-settings"></a>Indstillinger for Runbook

Hver runbook i Azure Automation har flere indstillinger, som den vil blive identificeret og ændre funktionsmåden logføring. Hver af disse indstillinger er beskrevet nedenfor efterfulgt af procedurer i at ændre dem.

## <a name="settings"></a>Indstillinger

### <a name="name-and-description"></a>Navn og beskrivelse

Du kan ikke ændre navnet på en runbook, når den er blevet oprettet. Beskrivelsen er valgfri og kan bestå af op til 512 tegn.

### <a name="tags"></a>Mærker

Mærker gør det muligt at tildele særskilte ord og sætninger for at hjælpe med at identificere en runbook. For eksempel, når du sender en runbook til [Runbook galleriet](https://msdn.microsoft.com/library/dn781422.aspx), angiver du bestemt mærker for at identificere, hvilke kategorier runbook skal være angivet i. Du kan angive flere mærker for en runbook ved at adskille dem med kommaer.

### <a name="logging"></a>Logføring

Detaljeret og opgavefremdrift poster er som standard ikke skrevet til jobs. Du kan ændre indstillingerne for en bestemt runbook for at logge af disse poster. Se [Runbook Output og meddelelser](https://msdn.microsoft.com/library/dn879148.aspx)kan finde flere oplysninger om disse poster.

## <a name="changing-runbook-settings"></a>Ændring af indstillinger for runbook

### <a name="changing-runbook-settings-with-the-azure-management-portal"></a>Ændring af indstillinger for runbook ved hjælp af Azure Management-Portal

Du kan ændre indstillingerne for en runbook i portalen Azure Administration fra siden **Konfigurer** for runbook.

1. Vælg **automatisering** i portalen Azure administration, og klik derefter på navnet på en konto med automation.
1. Vælg fanen **Runbooks** .
1. Klik på navnet på en runbook.
1. Vælg fanen **Konfigurer** .

### <a name="changing-runbook-settings-with-windows-powershell"></a>Ændring af indstillinger for runbook med Windows PowerShell

Du kan bruge cmdlet'en [Set-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690275.aspx) til at ændre indstillingerne for en runbook. Hvis du vil angive flere mærker, kan du enten angive en matrix eller et enkelt streng med kommasepareret værdier til parameteren mærker. Du kan få de aktuelle mærker med [Get-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690278.aspx).

Kommandoerne følgende eksempel viser, hvordan du indstille egenskaberne for en runbook. I dette eksempel føjer tre koder til de eksisterende mærker og angiver, at detaljeret poster skal logføres.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="related-articles"></a>Relaterede artikler
- [Runbook Output og meddelelser](../automation-runbook-output-and-messages) 
- [Oprette eller importere en Runbook](https://msdn.microsoft.com/library/dn643637.aspx) 