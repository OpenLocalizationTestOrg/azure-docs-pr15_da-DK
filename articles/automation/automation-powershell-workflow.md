<properties 
   pageTitle="Learning PowerShell arbejdsproces"
   description="I denne artikel er beregnet som en hurtig lektion til forfattere fortrolig med PowerShell at forstå bestemte forskellene mellem PowerShell og PowerShell arbejdsproces."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/12/2016"
   ms.author="bwren" />

# <a name="learning-windows-powershell-workflow"></a>Learning Windows PowerShell arbejdsproces

Runbooks i Azure Automation implementeres som Windows PowerShell arbejdsprocesser.  En arbejdsproces til Windows PowerShell svarer til et Windows PowerShell-script, men har nogle vigtige forskelle, som kan være forvirrende til en ny bruger.  I denne artikel henvender sig til brugere, der allerede kender, PowerShell og en kort beskrivelse af, at du kræver, at hvis du konverterer en PowerShell-script til en PowerShell-arbejdsproces til brug i en runbook begreber.  

En arbejdsproces er en sekvens af programmerede, forbundne trin, der udfører længerevarende opgaver eller kræver koordinere flere trin på tværs af flere forskellige enheder eller administrerede noder. Fordelene ved en arbejdsproces på et normal script omfatter muligheden for at udføre en handling med flere enheder og muligheden for automatisk at gendanne fra mislykkede forsøg. En Windows PowerShell-arbejdsproces er et Windows PowerShell-script, der bruger Windows Workflow Foundation. Mens arbejdsprocessen er skrevet med Windows PowerShell syntaks og startet af Windows PowerShell, behandles den af Windows Workflow Foundation.

Oplysninger om emner i denne artikel, skal du se [Komme i gang med Windows PowerShell arbejdsproces](http://technet.microsoft.com/library/jj134242.aspx).

## <a name="types-of-runbook"></a>Typer af runbook

Der findes tre typer af runbook i Azure automatisering, *PowerShell arbejdsproces*, *PowerShell* og *grafiske*.  Du definerer typen runbook, når du opretter runbook, og du ikke konvertere en runbook til anden typen, når den er blevet oprettet.

PowerShell arbejdsproces runbooks og PowerShell runbooks bruger for brugere, der foretrækker at arbejde direkte med PowerShell koden enten redigeringsprogrammet indeholder tekst i Azure Automation eller redaktør offline som PowerShell ISE. Hvis du opretter en PowerShell arbejdsproces runbook, skal du vide oplysninger i denne artikel. 

Grafiske runbooks gør det muligt at oprette en runbook bruger de samme aktiviteter og -cmdletter, men bruger en grafisk grænseflade, der skjuler lede underliggende PowerShell arbejdsprocessen.  Begreberne i denne artikel som kontrolpunkterne og parallel udførelse stadig gælder for grafiske runbooks, men du behøver ikke at bekymre dig om den detaljerede syntaks. 

## <a name="basic-structure-of-a-workflow"></a>Grundlæggende opbygning af en arbejdsproces

Det første trin til konvertering af en PowerShell-script til en PowerShell-arbejdsproces er omslutte den med nøgleordet **arbejdsproces** .  En arbejdsproces startes med nøgleordet **arbejdsproces** efterfulgt af brødteksten i scriptet i klammeparenteser. Navnet på arbejdsprocessen følger nøgleordet **arbejdsproces** , som vist i følgende syntaks. 

    Workflow Test-Workflow
    {
       <Commands>
    }

Navnet på arbejdsprocessen skal svare til navnet af runbook automatisering. Hvis runbook er importeret, derefter filnavnet skal svare til et navn til arbejdsprocessen og skal slutte i .ps1.

Brug nøgleordet **parameteren** for at føje parametre til arbejdsprocessen, ligesom du ville gøre til et script. 

## <a name="code-changes"></a>Kodeændringer

PowerShell arbejdsproces kode ser næsten identisk med PowerShell scriptkode med undtagelse af nogle væsentlige ændringer.  I følgende afsnit beskrives ændringer, skal du foretage en PowerShell-script til det til at køre i en arbejdsproces.

### <a name="activities"></a>Aktiviteter

En aktivitet er en bestemt opgave i en arbejdsproces. Ligesom et script består af en eller flere kommandoer, der en arbejdsproces består af en eller flere aktiviteter, der udføres i en sekvens. Windows PowerShell arbejdsproces konverterer automatisk mange af Windows PowerShell-cmdlet'er til aktiviteter når der køres en arbejdsproces. Når du angiver en af disse cmdlet'er i din runbook, er den tilsvarende aktivitet faktisk køres af Windows Workflow Foundation. Windows PowerShell arbejdsprocessen køres automatisk cmdlet i en [InlineScript](#inlinescript) aktivitet for disse cmdlet'er uden en tilsvarende aktivitet. Der er et sæt af cmdletter, der er udeladt og kan ikke bruges i en arbejdsproces, medmindre du eksplicit inkludere dem i en InlineScript blok. Du kan finde yderligere oplysninger om disse begreber, [Ved hjælp af aktiviteter i Script-arbejdsprocesser](http://technet.microsoft.com/library/jj574194.aspx).

Arbejdsprocesaktiviteter dele et sæt af almindelige parametre for at konfigurere deres handling. Flere oplysninger om arbejdsprocessen fælles parametre i [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Position parametre

Du kan ikke bruge position parametre med aktiviteter og -cmdletter i en arbejdsproces.  Betyder er, at du skal bruge parameternavne.

For eksempel bør du overveje følgende kode, der henter alle tjenester, der kører.

     Get-Service | Where-Object {$_.Status -eq "Running"}

Hvis du forsøger at køre denne samme kode i en arbejdsproces, får du vist en meddelelse som "parametersæt ikke kan løses ved hjælp af den angivne navngivne parametre."  Du kan rette dette ved ganske enkelt levere parameternavn som i følgende.

    Workflow Get-RunningServices
    {
        Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
    }

### <a name="deserialized-objects"></a>Deserialiserede objekter

Objekter i arbejdsprocesser er deserialiseres.  Det betyder, at deres egenskaber er stadig tilgængelige, men ikke deres metoder.  For eksempel bør du overveje følgende PowerShell-kode, der holder en tjeneste, ved hjælp af metoden Stop for objektet Service.

    $Service = Get-Service -Name MyService
    $Service.Stop()

Hvis du forsøger at køre dette i en arbejdsproces, får du en fejl, siger "metode aktivering ikke understøttes i en arbejdsproces til Windows PowerShell".  

En af mulighederne er at ombryde disse to kodelinjer i en [InlineScript](#InlineScript) blok i så fald $Service ville være et serviceobjekt i blokken. 

    Workflow Stop-Service
    {
        InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
        }
    } 

En anden mulighed er at bruge en anden-cmdlet, der udfører de samme funktioner som metoden, hvis der findes.  Cmdletten Stop-tjenesten giver de samme funktioner som Stop-metoden i forbindelse med vores eksempel, og du kan bruge følgende for en arbejdsproces.

    Workflow Stop-MyService
    {
        $Service = Get-Service -Name MyService
        Stop-Service -Name $Service.Name
    }


## <a name="inlinescript"></a>InlineScript

**InlineScript** aktiviteten er nyttig, når du har brug at køre en eller flere kommandoer som traditionelle PowerShell-script i stedet for PowerShell arbejdsproces.  Mens kommandoer i en arbejdsproces, som sendes til Windows Workflow Foundation til behandling, behandles kommandoer i en InlineScript blok af Windows PowerShell. 

InlineScript bruger syntaks, som vist nedenfor.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

Du kan returnere output fra en InlineScript ved at tildele output til en variabel. I følgende eksempel stopper en tjeneste og derefter skriver navnet på tjenesten.

    Workflow Stop-MyService
    {
        $Output = InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Du kan overføre værdier i en InlineScript-blok, men du skal bruge **$Using** omfang ændring.  I følgende eksempel er identisk med det forrige eksempel, bortset fra at navnet på tjenesten er angivet af en variabel. 

    Workflow Stop-MyService
    {
        $ServiceName = "MyService"
    
        $Output = InlineScript {
            $Service = Get-Service -Name $Using:ServiceName
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Mens InlineScript aktiviteter kan være kritiske i bestemte arbejdsprocesser, understøtter ikke arbejdsproces konstruktioner og bør kun bruges, når det er nødvendigt af følgende årsager:

- Du kan ikke bruge [kontrolpunkter](#Checkpoints) inde i en InlineScript-blok. Hvis der opstår fejl i blokering, skal den genoptages fra begyndelsen af blokering.
- Du kan ikke bruge [parallel udførelse](#parallel-execution) inde i en InlineScriptBlock.
- InlineScript påvirker skalerbarhed af arbejdsprocessen, fordi den indeholder Windows PowerShell-session for hele længden af InlineScript blokering.

Se yderligere oplysninger om brug af InlineScript, [Kører Windows PowerShell-kommandoer i en arbejdsproces](http://technet.microsoft.com/library/jj574197.aspx) og [about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx).


## <a name="parallel-processing"></a>Parallel behandling

En af fordelene ved Windows PowerShell arbejdsprocesser er muligheden for at udføre en række kommandoer parallelt i stedet for sekventielt som med et typisk script. 

Du kan bruge nøgleordet **parallelle** til at oprette en scriptblok med flere kommandoer, der skal køre samtidig. Dette bruger syntaks vist nedenfor. I dette tilfælde begynder Activity1 og Activity2 på samme tid. Activity3 starter, når både Activity1 og Activity2 er fuldført.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


For eksempel bør du overveje følgende PowerShell-kommandoer, kopierer flere filer til et netværk destination.  Disse kommandoer køres sekventielt så end en fil skal afsluttes, kopierer, før næste startes.     

    $Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
    $Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
    $Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

Følgende arbejdsprocessen køres kommandoerne samme parallelt, så de alle begynde at kopiere på samme tid.  Kun, når de er alle vises helt kopieret fuldførelse meddelelsen.

    Workflow Copy-Files
    {
        Parallel 
        {
            $Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
        }

        Write-Output "Files copied."
    }


Du kan bruge den **ForEach-parallelle** konstruktion proces kommandoer for hvert element på en samling samtidigt. Elementer i samlingen behandles parallelt, mens kommandoerne i scriptblokken kører sekventielt. Dette bruger syntaks vist nedenfor. I dette tilfælde begynder Activity1 på samme tid for alle elementer i gruppen af websteder. For hvert element starter Activity2, når Activity1 er fuldført. Activity3 starter, når både Activity1 og Activity2 er fuldført for alle elementer.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

I følgende eksempel er svarer til det forrige eksempel kopiering af filer i parallelt.  I dette tilfælde vises en meddelelse for hver fil, når den er kopieret.  Kun, når de er alle vises helt kopieret endelig afslutning meddelelsen.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach -Parallel ($File in $Files) 
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
        }
        
        Write-Output "All files copied."
    }

> [AZURE.NOTE]  Vi anbefaler ikke kører underordnede runbooks parallelt, da dette er blevet vist upålidelig resultater.  Output fra underordnede runbook nogle gange bliver ikke vist, og indstillinger i én underordnet runbook kan påvirke de andre parallelle underordnede runbooks 


## <a name="checkpoints"></a>Kontrolpunkter

*Kontrolpunkt* er et øjebliksbillede af den aktuelle status for arbejdsprocessen, der indeholder den aktuelle værdi af variabler og en hvilken som helst output, der oprettes til dette punkt. Hvis en arbejdsproces afsluttes med fejl eller afbrydes midlertidigt, derefter starter køres den næste gang fra den sidste kontrolpunkt i stedet for starten af worfklow.  Du kan angive et kontrolpunkt i en arbejdsproces med aktiviteten **Kontrolpunkt-arbejdsproces** .

I følgende eksempelkode, en undtagelse er placeret efter Activity2 medfører, at arbejdsprocessen skal slutte. Når arbejdsprocessen køres igen, starter det ved at køre Activity2, da det var lige efter det sidste kontrolpunkt angive.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

Du skal angive kontrolpunkter i en arbejdsproces, når aktiviteter, der kan være giver risiko for undtagelse, og der ikke skal gentages, hvis arbejdsprocessen er blevet. Arbejdsprocessen kan for eksempel oprette en virtuel maskine. Du kan angive et kontrolpunkt både før og efter kommandoer til at oprette den virtuelle maskine. Hvis det ikke lykkes at oprettelsen, skal derefter kommandoerne gentages, hvis arbejdsprocessen startes igen. Hvis den worfklow mislykkes efter at oprettelsen lykkes, og derefter den virtuelle maskine ikke oprettes der igen, når arbejdsprocessen genoptages.

I følgende eksempel kopierer flere filer til en netværksplacering og angiver en kontrolpunkt efter hver fil.  Hvis netværksplaceringen går tabt, afsluttes arbejdsprocessen ved en fejl.  Når det startes igen, genoptage den på den sidste kontrolpunkt, hvilket betyder, at kun de filer, der allerede er blevet kopieret ignoreres.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach ($File in $Files) 
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
            Checkpoint-Workflow
        }
        
        Write-Output "All files copied."
    }

Da brugernavn legitimationsoplysninger ikke bevares, når du har kaldt [Pause-arbejdsproces](https://technet.microsoft.com/library/jj733586.aspx) aktivitet eller efter det sidste kontrolpunkt, skal du angive legitimationsoplysninger til null og derefter hente dem igen fra aktiv store efter **Pause-arbejdsproces** eller kontrolpunkt hedder.  Ellers skal du muligvis følgende fejlmeddelelse: *kørslen arbejdsproces kan ikke genoptages, enten fordi brugerdata data ikke kan gemmes helt, eller gemt brugerdata data er blevet beskadiget. Du skal genstarte arbejdsprocessen.*

Den følgende samme kode demonstrerer Sådan håndteres dette i din PowerShell arbejdsproces runbooks.

       
    workflow CreateTestVms
    {
       $Cred = Get-AzureAutomationCredential -Name "MyCredential"
       $null = Add-AzureRmAccount -Credential $Cred

       $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

       foreach ($VmName in $VmsToCreate)
         {
          # Do work first to create the VM (code not shown)
        
          # Now add the VM
          New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

          # Checkpoint so that VM creation is not repeated if workflow suspends
          $Cred = $null
          Checkpoint-Workflow
          $Cred = Get-AzureAutomationCredential -Name "MyCredential"
          $null = Add-AzureRmAccount -Credential $Cred
         }
     } 


Dette er ikke påkrævet, hvis du godkendelse ved hjælp af en konto, konfigureret med en tjeneste vigtigste Kør som.  

Se [Tilføje kontrolpunkter til et Script arbejdsproces](http://technet.microsoft.com/library/jj574114.aspx)kan finde flere oplysninger om kontrolpunkter.


## <a name="next-steps"></a>Næste trin

- For at komme i gang med PowerShell arbejdsproces runbooks skal du se [Min første PowerShell arbejdsproces runbook](automation-first-runbook-textual.md) 
