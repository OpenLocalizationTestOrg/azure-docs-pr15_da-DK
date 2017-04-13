<properties
   pageTitle="Azure automatisering fejlhåndtering | Microsoft Azure"
   description="I denne artikel indeholder grundlæggende fejl håndtering af trin til at fejlfinde og løse almindelige Azure automatisering fejl."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="stevenka"
   editor="tysonn"
   tags="top-support-issue"
   keywords="automatisering fejl, håndtering af fejl"/>
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/06/2016"
   ms.author="sngun; v-reagie"/>

# <a name="error-handling-tips-for-common-azure-automation-errors"></a>Tip til almindelige fejl i Azure automatisering fejlhåndtering

Denne artikel beskrives nogle af de almindelige fejl i Azure automatisering du eventuelt støder og foreslår mulige fejlhåndtering trin.

## <a name="troubleshoot-authentication-errors-when-working-with-azure-automation-runbooks"></a>Fejlfinding i forbindelse med godkendelsesfejl, når du arbejder med Automation Azure runbooks  

### <a name="scenario-sign-in-to-azure-account-failed"></a>Scenarie: Log på Azure konto mislykkedes

**Fejl:** Du får fejl "Unknown_user_type: Ukendt brugertype", når du arbejder med Tilføj AzureAccount eller logon-AzureRmAccount cmdletter.

**Årsagen til fejlen:** Denne fejl opstår, hvis legitimationsoplysninger aktiv navnet ikke er gyldige, eller hvis det brugernavn og adgangskode, du brugte til at konfigurere automatiske legitimationsoplysninger aktiv ikke er gyldige.

**Tip til fejlfinding:** For at finde ud af, hvad er der galt, skal du benytte følgende fremgangsmåde:  

1. Sørg for, at du ikke har nogen specialtegn, herunder den **@** tegn i det automatisering legitimationsoplysninger aktiv navn, du bruger til at oprette forbindelse til Azure.  

2. Kontrollér, at du kan bruge det brugernavn og adgangskode, der er gemt i Azure automatisering legitimationsoplysninger i din lokale PowerShell ISE editor. Du kan gøre dette ved at køre følgende cmdletter i PowerShell ISE:  

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred

3. Hvis din godkendelse mislykkes lokalt, det betyder, at du ikke har konfigureret dine Azure Active Directory-legitimationsoplysninger korrekt. Se [Authenticating til Azure ved hjælp af Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) blogindlæg for at få den Azure Active Directory-konto, der er konfigureret korrekt.  


### <a name="scenario-unable-to-find-the-azure-subscription"></a>Scenarie: Kan ikke finde det Azure abonnement

**Fejl:** Du får fejl "abonnement med navnet ``<subscription name>`` blev ikke fundet" Når du arbejder med Vælg AzureSubscription eller Vælg AzureRmSubscription cmdletter.

**Årsagen til fejlen:** Denne fejl opstår, hvis Abonnementsnavnet ikke er gyldige, eller hvis den Azure Active Directory-bruger, der forsøger at få oplysninger om abonnement ikke er konfigureret som administrator af abonnementet.

**Tip til fejlfinding:** For at finde ud af, hvis du har den korrekte godkendelse til Azure og har adgang til det abonnement, du forsøger at markere, skal du benytte følgende fremgangsmåde:  

1. Sørg for, at du kører **Tilføj AzureAccount** , før du kører **Vælg AzureSubscription** cmdlet.  

2. Hvis du stadig får vist denne fejlmeddelelse, ændrer din kode ved at tilføje Cmdletten **Get-AzureSubscription** efter Cmdletten **Tilføj AzureAccount** , og udfør derefter koden.  Bekræft nu, hvis output fra Get-AzureSubscription indeholder dine abonnementsoplysninger.  
    * Hvis du ikke kan se en hvilken som helst abonnementsoplysninger i outputtet, betyder det, at abonnementet ikke er initialiseret endnu.  
    * Hvis du ser siden abonnementsoplysninger i outputtet, kan du bekræfte, at du bruger det korrekte Abonnementsnavnet eller -ID med Cmdletten **Vælg AzureSubscription** .   


### <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a>Scenarie: Godkendelse til Azure mislykkedes, fordi Multi-Factor godkendelse er aktiveret

**Fejl:** Du får fejl "Tilføj AzureAccount: AADSTS50079: stærk godkendelse tilmelding (bevis-up) er påkrævet" Når godkendelse til Azure med Azure brugernavn og adgangskode.

**Årsagen til fejlen:** Hvis du har Multi-Factor authentication i din Azure-konto, kan du ikke bruge en Azure Active Directory-bruger til at godkende Azure.  I stedet skal du bruge et certifikat eller en vigtigste tjeneste til at godkende Azure.

**Tip til fejlfinding:** Hvis du vil bruge et certifikat med Azure Service Management cmdletter, skal du henvise til [oprette og tilføje et certifikat for at administrere Azure tjenester.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) For at bruge en tjeneste sikkerhedskonto med Azure ressourcestyring cmdletter skal referere til [oprettelse af tjenesten hovedstolen Azure portalen](./resource-group-create-service-principal-portal.md) og [godkendelse af en tjeneste sikkerhedskonto med Azure ressourcestyring.](./resource-group-authenticate-service-principal.md)


## <a name="troubleshoot-common-errors-when-working-with-runbooks"></a>Foretage fejlfinding af almindelige fejl, når du arbejder med runbooks

### <a name="scenario-runbook-fails-because-of-deserialized-object"></a>Scenarie: Runbook mislykkes på grund af deserialiserede objekt

**Fejl:** Din runbook mislykkes med fejlen "kan ikke bindes parameter ``<ParameterName>``. Du kan ikke konvertere den ``<ParameterType>`` værdi af typen Deserialized ``<ParameterType>`` til at skrive ``<ParameterType>``".

**Årsagen til fejlen:** Hvis din runbook er en PowerShell-arbejdsproces, gemmes komplekse objekter i et deserialiserede format for at kunne fastholdes din runbook stat, hvis arbejdsprocessen afbrydes midlertidigt.  

**Tip til fejlfinding:**  
En af følgende tre løsninger løse dette problem:

1. Hvis du rørplan komplekse objekter fra én cmdlet til en anden, kan du ombryde disse cmdlet'er i en InlineScript.  
2. Sende navn eller værdi, du skal bruge fra komplekse objektet i stedet for at overføre hele objektet.  

3. Brug en PowerShell-runbook i stedet for en arbejdsproces for PowerShell runbook.  


### <a name="scenario-runbook-job-failed-because-the-allocated-quota-exceeded"></a>Scenarie: Runbook jobbet mislykkedes, fordi den tildelte kvote overskredet

**Fejl:** Runbook tingene mislykkes med fejlen "kvoten for månedlige samlede jobbet kørselstid er nået for dette abonnement".

**Årsagen til fejlen:** Denne fejl opstår, når jobbet udførelsen overstiger 500 minutters gratis kvoten for din konto. Denne kvote gælder for alle typer job udførelse af opgaver såsom test af et job, starte et job fra portalen, afholde et job ved hjælp af webhooks og planlægge et job til at udføre ved hjælp af enten Azure portalen eller i dit datacenter. Du kan finde flere oplysninger om priser for automatisering i [automatisering priser](https://azure.microsoft.com/pricing/details/automation/).

**Tip til fejlfinding:** Hvis du vil bruge mere end 500 minutter behandling månedsbasis skal du ændre dit abonnement fra gratis niveau til det grundlæggende niveau. Du kan opgradere til de grundlæggende trin ved at gøre følgende:  

1. Log på abonnementet Azure  
2. Vælg den automatiske konto, du vil opgradere  
3. Klik på **Indstillinger** > **priser niveau for og brugen** > **priser niveau**  
4. Vælg **grundlæggende** bladet **Vælg din priser niveau**    


### <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a>Scenarie: Cmdlet ikke genkendt under kørsel af en runbook

**Fejl:** Runbook tingene mislykkes med fejlen "``<cmdlet name>``: ordet ``<cmdlet name>`` ikke genkendes som navnet på en-cmdlet, funktionen, script-fil eller et program."

**Årsagen til fejlen:** Denne fejl opstår, når PowerShell-program ikke kan finde cmdlet du bruger i din runbook.  Dette kan skyldes, at det modul, der indeholder cmdlet mangler fra kontoen, der er en navnekonflikt med et runbook navn, eller cmdlet findes også i et andet modul og automatisering ikke identificere navnet.

**Tip til fejlfinding:** En af følgende løsninger løse problemet:  

- Kontrollér, at du har angivet cmdlet navnet korrekt.  

- Sørg for cmdlet findes i din konto med Automation og, der ikke er nogen konflikter. Åbne en runbook i redigeringstilstanden, og Søg efter du vil finde i biblioteket eller Kør cmdlet for at bekræfte, hvis cmdlet er til stede, **Kommandoen Get ``<CommandName>`` **.  Når du har valideret, cmdlet er tilgængelig for kontoen, og at der er ikke nogen navnekonflikt med andre cmdletter eller runbooks, føje den til lærredet, og Sørg for, at du bruger en gyldig parameter, der er angivet i din runbook.  

- Hvis du har en navnekonflikt og cmdlet er tilgængelig i to forskellige moduler, kan du løse problemet ved hjælp af det fulde navn for cmdlet. Du kan for eksempel bruge **ModuleName\CmdletName**.  

- Hvis du udfører runbook lokalt i en hybrid arbejdergruppe, skal du sikre dig, at er modul/cmdlet installeret på den computer, der er vært hybrid arbejder.


### <a name="scenario-a-long-running-runbook-consistently-fails-with-the-exception-the-job-cannot-continue-running-because-it-was-repeatedly-evicted-from-the-same-checkpoint"></a>Scenarie: En længerevarende runbook konsekvent mislykkes med undtagelsen: "jobbet kan ikke fortsætte, kører, da det blev gentagne gange udskilt fra den samme kontrolpunkt".

**Årsagen til fejlen:** Dette er af design funktionalitet på grund af "Sædvanlig del" overvågning af processer i Azure automatisering, som automatisk afbryder en runbook, hvis den udfører længere end 3 timer. Den fejlmeddelelse, giver dog ikke "hvad så nu" indstillinger. En runbook kan være suspenderet for en række mulige årsager. Afbryder happen hovedsageligt på grund af fejl. For eksempel en ikke-opfanget undtagelse i en runbook, et netværksfejl eller nedbrud på den Runbook arbejder kører runbook alle medfører runbook til suspenderet og starter fra den sidste kontrolpunkt når genoptages.

**Tip til fejlfinding:** Dokumenteret løsning for at undgå dette problem er at bruge kontrolpunkter i en arbejdsproces.  Hvis du vil vide referere mere til [Learning PowerShell arbejdsprocesser](automation-powershell-workflow.md#Checkpoints).  En mere detaljeret forklaring af "Sædvanlig del" og kontrolpunkt kan findes i artiklen blog [Ved hjælp af kontrolpunkter i Runbooks](https://azure.microsoft.com/en-us/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).


## <a name="troubleshoot-common-errors-when-importing-modules"></a>Foretage fejlfinding af almindelige fejl, når du importerer moduler

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a>Scenarie: Modul ikke kan importere eller cmdletter kan ikke udføres, når du har importeret

**Fejl:** Et modul ikke kan importere eller importeres korrekt, men ingen cmdletter er hentet.

**Årsagen til fejlen:** Nogle almindelige årsager til, et modul ikke importerer muligvis korrekt til Azure Automation er:  

- Strukturen stemmer ikke overens med den struktur, der automatisering skal være i.  

- Modulet, afhænger af en anden modul, der ikke er blevet installeret på din konto med Automation.  

- Modulet, der mangler dets afhængigheder i mappen.  

- **Ny AzureRmAutomationModule** -cmdlet, der anvendes til at overføre modulet, og du ikke har givet fuld lagerplads stien eller ikke blevet indlæst modulet ved hjælp af en offentligt tilgængelig URL-adresse.  

**Tip til fejlfinding:**  
En af følgende løsninger løse problemet:  

- Sørg for, at modulet, der følger følgende format:  
ModuleName.Zip **->** ModuleName eller versionsnummeret **->** (ModuleName.psm1, ModuleName.psd1)

- Åbn filen .psd1, og se, om modulet har afhængigheder.  Hvis det er tilfældet, kan du overføre disse moduler til den automatiske konto.  

- Sørg for, at en hvilken som helst der refereres til DLL-filer findes i mappen modul.  


## <a name="troubleshoot-common-errors-when-working-with-desired-state-configuration-dsc"></a>Foretage fejlfinding af almindelige fejl, når du arbejder med beskedteksten tilstand konfiguration (DTK)  

### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a>Scenarie: Node er i mislykkedes status med fejlen "Ikke fundet"

**Fejl:** Noden har en rapport med statussen **mislykket** og indeholder fejlen "forsøg på at få handlingen fra server https://``<url>``//accounts/``<account-id>``/Nodes(AgentId=``<agent-id>``)/GetDscAction mislykkedes, fordi en gyldig konfiguration ``<guid>`` blev ikke fundet."

**Årsagen til fejlen:** Denne fejl opstår typisk, når noden er tildelt til en konfigurationsnavn (fx ABC) i stedet for en node konfigurationsnavn (fx ABC. WebServer).  

**Tip til fejlfinding:**  

- Sørg for, at du tildeler noden med "node konfigurationsnavn" og ikke "konfigurationsnavnet".  

- Du kan tildele en node konfiguration til en node Azure portalen eller med en PowerShell-cmdlet.
    - Åbn bladet **DTK noder** for at tildele en node konfiguration til en node Azure portalen, og derefter vælge en node, og klik på **tildele node konfiguration** knappen.  
    - For at tildele en node konfiguration til en node ved hjælp af PowerShell-cmdlet, skal du bruge **Sæt AzureRmAutomationDscNode** cmdlet


### <a name="scenario--no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a>Scenarie: Ingen node konfigurationer (MOF-filer) er produceret, når en konfiguration er kompileret

**Fejl:** DTK kompilering tingene afbryder med fejl: "kompilering blev fuldført, men ingen node konfiguration .mofs er blevet oprettet".

**Årsagen til fejlen:** Når følgende udtryk evalueres nøgleordet **Node** i DTK konfigurationen $null og ingen node konfigurationer vil blive produceret.    

**Tip til fejlfinding:**  
En af følgende løsninger løse problemet:  

- Sørg for, at udtrykket ud for nøgleordet **Node** i definitionen af konfiguration ikke evaluerer til $null.  
- Hvis du overfører ConfigurationData ved sammensætning af konfigurationen, skal du kontrollere, at du overfører de forventede værdier, der kræver, at konfigurationen fra [ConfigurationData](automation-dsc-compile.md#configurationdata).


### <a name="scenario--the-dsc-node-report-becomes-stuck-in-progress-state"></a>Scenarie: DTK node rapporten bliver fastlåst "i gang" tilstand

**Fejl:** DTK Agent skriver "Fundet nogen forekomst med angivne egenskabsværdier."

**Årsagen til fejlen:** Du har opgraderet dit WMF version og have ødelagt WMI.  

**Tip til fejlfinding:** Følg vejledningen i blogindlægget [DTK kendte problemer og begrænsninger](https://msdn.microsoft.com/powershell/wmf/limitation_dsc) for at løse problemet.


### <a name="scenario--unable-to-use-a-credential-in-a-dsc-configuration"></a>Scenarie: Kan ikke bruge en legitimationsoplysninger i en konfiguration med DTK

**Fejl:** DTK kompilering tingene blev afbrudt med fejl: "" system.InvalidOperationException "fejl under behandling egenskaben legitimationsoplysninger for af typen ``<some resource name>``: konvertere og gemme en krypteret adgangskode, som almindelig tekst er tilladt kun, hvis PSDscAllowPlainTextPassword er indstillet til sand".

**Årsagen til fejlen:** Du har brugt en legitimationsoplysninger i en konfiguration, men er ikke stort **ConfigurationData** for at angive **PSDscAllowPlainTextPassword** til Sand for hver node konfiguration.  

**Tip til fejlfinding:**  
- Sørg for at overføre i stort **ConfigurationData** til at angive **PSDscAllowPlainTextPassword** til Sand for hver node konfiguration, der er nævnt i konfigurationen. Yderligere oplysninger finder du referere til [Aktiver i Azure automatisering DTK](automation-dsc-compile.md#assets).


## <a name="next-steps"></a>Næste trin

Hvis du har fulgt trinnene til fejlfinding ovenfor og har brug for yderligere hjælp på en hvilken som helst sted i denne artikel, kan du:

- Få hjælp fra Azure-eksperter. Sende dit problem til den [MSDN Azure eller stakoverløb fora.](https://azure.microsoft.com/support/forums/)

- Arkivere en Azure support-hændelse. Gå til det [Azure Support-webstedet](https://azure.microsoft.com/support/options/) , og klik på **få support** under **tekniske og fakturering support**.

- Sende en Script-anmodning på [Script Center](https://azure.microsoft.com/documentation/scripts/) , hvis du leder efter en Azure automatisering runbook løsning eller et integration modul.

- Sende feedback eller funktion anmodninger for Azure automatisering på [Bruger tale](https://feedback.azure.com/forums/34192--general-feedback).
