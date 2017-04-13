<properties
    pageTitle="Oprette gendannelse planer | Microsoft Azure" 
    description="Opret gendannelse planer med Azure gendannelse af websteder til mislykkes og gendanne grupper af virtuelle maskiner og fysiske servere." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="create-recovery-plans"></a>Oprette gendannelse planer

Gendannelse af websteder Azure service bidrager til din business løbende og genoprettelse efter genoprettelse (BCDR) strategi ved orchestrating gentagelse, failover og gendannelse af virtuelle maskiner og fysiske servere. Computere kan replikeres til Azure eller til et sekundært lokalt datacenter. En hurtig oversigt over læse [Hvad er Azure gendannelse af websteder?](site-recovery-overview.md).


## <a name="overview"></a>Oversigt

I denne artikel indeholder oplysninger om oprettelse og tilpasning af gendannelse planer. 

Gendannelse planer består af en eller flere bestilte grupper, der indeholder virtuelle maskiner eller fysiske servere, du vil skifte over sammen. Gendannelse planer du gøre følgende:

- Definer grupper af maskiner, mislykkes og derefter starte sammen.
- Model afhængigheder mellem maskiner ved at gruppere dem sammen i en gendannelse plan gruppe. For eksempel, hvis du vil skifte over og få vist et bestemt program, du vil gruppere virtuelle maskiner for det pågældende program i den samme gendannelse plan gruppe.
- Automatisere og udvide failover. Du kan køre en test, planlagt, eller ikke-planlagt failover på en plan for gendannelse. Du kan tilpasse gendannelse planer med scripts, Azure automatiske og manuelle handlinger.

Gendannelse planer vises under **Gendannelse planer** i portalen gendannelse af websteder.


Sende en hvilken som helst kommentarer eller spørgsmål nederst i denne artikel eller på [Azure gendannelse Services-forummet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Før du starter

Bemærk følgende:

- En gendannelse plan ikke bør blande FOS med et eller flere netværkskort. Dette skyldes, at blande disse FOS ikke er tilladt i en Azure skybaseret tjeneste.
- Du kan udvide gendannelse planer med scripts og manuel handlinger. Bemærk følgende:
    - Skrive ved hjælp af Windows PowerShell-scripts.
    - Sørg for, scripts, som bruger til at prøve produkter blokke, så undtagelserne, der skal håndteres problemfrit. Hvis der er en undtagelse i scriptet den stopper og opgaven viser som mislykkedes.  Hvis fejlen opstår, køre eventuelle resterende del af scriptet ikke. Hvis dette sker, når du kører en ikke-planlagt failover, fortsat gendannelse planen. Hvis dette sker, når du kører en planlagt failover, stoppes den gendannelse plan. Hvis dette sker, løse scriptet, Sørg for, at der køres som forventet, og kør derefter gendannelse planen igen.
    - Kommandoen Skriv Host fungerer ikke i et gendannelse plan script, og scriptet mislykkes. Hvis du vil oprette output, oprette en proxyscript, der også kører din primære script og sikre, at alle outputtet pipes ud ved hjælp af den >> kommandoen.
    - Scriptet får timeout, hvis den ikke returnerer i 600 sekunder.
    - Hvis noget er skrevet til STDERR, klassificeres scriptet som mislykket. Disse oplysninger vises i script udførelse af oplysninger.
    - Hvis du bruger VMM i din installation Bemærk, at:

        - Scripts i en plan for gendannelse køres i konteksten for VMM tjenestekontoen. Sørg for, at denne konto har læsetilladelser remote delt som scriptet er placeret, og teste scriptet skal køres på niveauet for VMM service konto rettigheder.
        - VMM cmdletter leveres i en Windows PowerShell-modulet. VMM Windows PowerShell-modulet er installeret, når du installerer VMM konsollen. VMM modul kan indlæses i din script ved hjælp af følgende kommando i scriptet: Importér modul-navn virtualmachinemanager. [Få flere oplysninger](hhttps://technet.microsoft.com/library/hh875013.aspx).
        - Sikre, at du har mindst én serveren til dokumentbiblioteket i VMM installationen. Som standard findes bibliotek del stien til en VMM server lokalt på serveren VMM med navnet på mappen MSCVMMLibrary.
        - Hvis dit bibliotek del sti kører på en fjerncomputer (eller lokale men ikke deles med MSCVMMLibrary, skal du konfigurere del på følgende måde (ved hjælp af \\libserver2.contoso.com\share\ som eksempel):
            - Åbn Registreringseditor, og gå til HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure websted Recovery\Registration.
            -  Redigere værdien ScriptLibraryPath, og Placer værdien som \\libserver2.contoso.com\share\. angive det fulde fuldstændige Domænenavn. Give tilladelser til placeringen, del.
            -  Sørg for, at du tester scriptet til en brugerkonto, der indeholder de samme tilladelser som tjenestekonto VMM at sikre, at enkeltstående testet scripts kører på samme måde, at de vil i gendannelse planer. På serveren VMM du indstille politikken udførelse af kan springe på følgende måde:
                -  Åbn 64-bit Windows PowerShell-konsollen ved hjælp af administratorrettigheder.
                -  Type: **Angiv Udførselspolitikken igennem**. [Få flere oplysninger](https://technet.microsoft.com/library/ee176961.aspx).

## <a name="create-a-recovery-plan"></a>Oprette en plan for gendannelse

Den måde, som du opretter en gendannelse plan afhænger af installationen af gendannelse af websteder.

- **Replikering VMware FOS og fysiske servere**– du opretter en plan og tilføje gentagelse grupper, der indeholder virtuelle maskiner og fysiske servere til en gendannelse plan.
- **Replikering Hyper-V FOS (i VMM skyer)**– du opretter en plan og tilføje beskyttet Hyper-V virtuelle maskiner fra en VMM sky til en plan for gendannelse.
- **Replikering Hyper-V FOS (ikke i VMM skyer)**– Opret en plan og føje beskyttet Hyper-V virtuelle maskiner fra en beskyttelse gruppe til en plan for gendannelse.
- **SAN gentagelse**– Opret en plan, og Tilføj en gentagelse gruppe, der indeholder virtuelle maskiner til planen, gendannelse. Du vælger en gentagelse gruppe i stedet for bestemte virtuelle maskiner, fordi alle virtuelle maskiner i en gentagelse gruppe skal mislykkes sammen (serverskift på lagerplads laget først).


Oprette en plan for gendannelse på følgende måde:

1. Klik på **Gendannelse planer** fane > **Opret gendannelse planlægge**.
Angiv et navn for gendannelse planen, og en kilde- og destinationswebsteder. Kildeserveren skal have virtuelle maskiner, der er aktiveret for failover og gendannelse.

    - Hvis du replikering fra VMM til VMM Vælg **Kildetype** > **VMM**og kilde- og destinationswebsteder VMM servere. Klik på **Hyper-V** for at få vist sky, der er konfigureret til at bruge Hyper-V replika. 
    - Hvis du replikering fra VMM til VMM ved hjælp af SAN Vælg **Kildetype** > **VMM**og kilde- og destinationswebsteder VMM servere. Klik på **SAN** for at få vist sky, der er konfigureret til SAN gentagelse.
    - Hvis du replikering fra VMM til Azure Vælg **Kildetype** > **VMM**.  Vælg VMM kildeserveren og **Azure** som destination.
    - Hvis du replikering fra et websted med Hyper-V Vælg **Kildetype** > **Hyper-V-websted**. Vælg webstedet som kilde- og **Azure **som destination.
    - Hvis du replikering fra VMware eller en fysisk lokalt server Azure, Vælg en server configuration som kilde- og **Azure** som destination

2. Vælg den virtuelle maskiner (eller Replikeringsgruppe), du vil føje til standardgruppen (gruppe 1) i den gendannelse plan i **Vælg virtuelle computere** .

## <a name="customize-recovery-plans"></a>Tilpasse gendannelse planer

Når du har tilføjet beskyttet virtuelle maskiner eller gentagelse grupper til standardgruppen for gendannelse og oprettet den plan, du kan tilpasse det:

- **Tilføj nye grupper**– du kan tilføje yderligere betroet planlægningsgrupper. Grupper, du har tilføjet nummereres i den rækkefølge, hvori du tilføje dem. Du kan føje op til tre grupper. Du kan tilføje flere computere eller replikeringsgrupper til disse nye grupper. Bemærk, at et virtuelt eller en gentagelse gruppe kan kun medtages i én gendannelse plan gruppe.
- **Tilføj et script **– kan du tilføje scripts, før eller efter en genoprettelse planlægger gruppe. Når du tilføjer et script tilføjer et nyt sæt handlinger til gruppen. For eksempel oprettes et sæt foreløbig trin til gruppe 1 med navnet: gruppe 1: foreløbig trin. Vises alle foreløbige trin i dette sæt. Bemærk, at du kan kun tilføje et script på webstedet primære Hvis du har en VMM server, der er installeret.
- **Tilføje en manuel handling**– du kan tilføje manuel handlinger til at køre før eller efter en gendannelse plan gruppe. Når den gendannelse plan kører, det afbrydes på det sted, hvor du har indsat handlingen manuel og en dialogboks beder dig om at angive, at handlingen manuel blev afsluttet.

## <a name="extend-recovery-plans-with-scripts"></a>Udvide gendannelse planer med scripts

Du kan føje et script til din gendannelse plan:

- Hvis du har en VMM kildewebstedet du kan oprette et script på serveren VMM og medtage dem i din gendannelse plan.
- Hvis du replikeres til Azure kan du integrere Azure automatiske runbooks i tidsplanen gendannelse

### <a name="create-a-vmm-script"></a>Oprette et VMM script


Oprette scriptet på følgende måde:

1. Oprette en ny mappe i dele bibliotek for eksempel \<VMMServerName > \MSSCVMMLibrary\RPScripts. Placere den i kilden og målrette VMM servere.
2. Oprette script (for eksempel RPScript), og Markér den fungerer som forventet.
3. Indsætte scriptet på sted \<VMMServerName > \MSSCVMMLibrary på kilde- og destinationswebsteder VMM servere.

### <a name="create-an-azure-automation-runbook"></a>Oprette en Azure automatisering runbook

Du kan udvide din gendannelse plan ved at køre en Azure automatisering runbook som en del af planen. [Du finder flere oplysninger](site-recovery-runbook-automation.md).


### <a name="add-custom-settings-to-a-recovery-plan"></a>Føje brugerdefinerede indstillinger til en plan for gendannelse

1. Åbn den gendannelse plan, du vil tilpasse.
2. Klik for at tilføje en virtuelle maskiner eller ny gruppe.
3. Tilføje et script eller manuel handling skal du klikke på et element på listen **trin** og klik derefter på **Script** eller **Manuel handling**. Angiv, om du vil føje det script eller den handling, før eller efter det valgte element. Brug knapperne **Flyt op** og **Flyt ned** til at flytte placeringen af scriptet, op eller ned.
4. Hvis du føjer et VMM script, Vælg **Failover til VMM script**, og skriv den relative sti til del i **Script Path** i. I så fald i vores eksempel, hvor del er placeret på \\ <VMMServerName>\MSSCVMMLibrary\RPScripts, angive stien: \RPScripts\RPScript.PS1.
5. Hvis du føjer en Azure automatiske Kør adressekartotek, angiver **Azure automatiske konto** som runbook er placeret, og vælg den relevante **Azure Runbook Script**.
5. Gøre en failover af gendannelse planen for at sikre, at scriptet fungerer som forventet.


## <a name="next-steps"></a>Næste trin

Du kan køre forskellige typer failover på gendannelse planen, herunder en test failover for at kontrollere dit miljø og planlagt eller uventet failover. [Få mere at vide](site-recovery-failover.md).


 
