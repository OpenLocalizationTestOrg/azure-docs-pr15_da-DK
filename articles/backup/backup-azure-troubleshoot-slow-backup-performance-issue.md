<properties
   pageTitle="Fejlfinding i forbindelse med langsom sikkerhedskopi af filer og mapper i Azure sikkerhedskopiering | Microsoft Azure"
   description="Fejlfinding i forbindelse med vejleder for at hjælpe dig med at diagnosticere årsagen til problemer med ydeevnen Azure sikkerhedskopi"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="jimpark"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="genli"/>

# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Fejlfinding i forbindelse med langsom sikkerhedskopi af filer og mapper i Azure sikkerhedskopiering

Denne artikel indeholder fejlfinding vejledning for at hjælpe dig med at diagnosticere årsagen til langsom sikkerhedskopiering ydeevne efter filer og mapper, når du bruger Azure sikkerhedskopiering. Når du bruger Azure Backup agent til sikkerhedskopiering af filer, kan sikkerhedskopieringen tage længere tid end forventet. Denne forsinkelse kan skyldes en eller flere af følgende:

-   [Der findes flaskehalse for performance på den computer, der sikkerhedskopieres.](#cause1)
-   [En anden proces eller antivirussoftware forstyrrer Azure sikkerhedskopi processen.](#cause2)
-   [Backup agent kører på en Azure virtuelt (VM).](#cause3)  
-   [Du sikkerhedskopiering af et stort antal (millioner) filer.](#cause4)

Før du begynder at foretage fejlfinding af problemer, anbefaler vi, at du downloade og installere den [nyeste version af Azure sikkerhedskopi agent](http://aka.ms/azurebackup_agent). Vi yder hyppige opdateringer til Backup agent til at løse problemer med forskellige, tilføje funktioner og forbedre ydeevnen.

Vi anbefaler også på det kraftigste, at du gennemser [Sikkerhedskopiering af Azure service ofte stillede spørgsmål om](backup-azure-backup-faq.md) at sikre, at du ikke oplever nogen af de almindelige konfigurationsproblemer.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>
## <a name="cause-performance-bottlenecks-on-the-computer"></a>Årsag: Flaskehalse for Performance på computeren

Flaskehalse på den computer, der sikkerhedskopieres kan medføre forsinkelser. For eksempel kan computerens mulighed for at læse eller skrive på disk eller tilgængelige båndbredde til at sende data via netværket, medføre flaskehalse.

Windows indeholder et indbygget værktøj, der hedder [Performance Monitor](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) for at registrere disse flaskehalse.

Her er nogle tællere i ydeevne og områder, der kan være nyttige i diagnosticering flaskehalse til optimal sikkerhedskopier.

| Tæller  | Status  |
|---|---|
|Logisk Disk (fysisk Disk) – % inaktiv   | • 100% idle til 50% inaktiv = orden</br>• 49% idle til 20% inaktiv = advarsel eller skærm</br>• 19% idle til 0% inaktiv = kritisk eller ud af specifikation|
|  Logisk Disk (fysisk Disk) – % gennemsnitlig Disk sek læse- eller skrivetilladelser |  • 0,001 ms til 0.015 ms = orden</br>• 0.015 ms til 0.025 ms = advarsel eller skærm</br>• 0.026 ms eller længere = kritisk eller ud af specifikation|
|  Logisk Disk (fysisk Disk) – Aktuel længde på Disk kø (for alle forekomster) | 80 anmodninger om mere end 6 minutter |
| Hukommelse – ikke Overgangsfejl/sek|• Mindre end 60% af puljen consumed = orden<br>• 61-80% af puljen consumed = advarsel eller skærm</br>• Større end 80% puljen consumed = kritisk eller ud af specifikation|
| Hukommelse – puljen gruppe |• Mindre end 60% af puljen consumed = orden</br>• 61-80% af puljen consumed = advarsel eller skærm</br>• Større end 80% puljen consumed = kritisk eller ud af specifikation|
| Hukommelse – tilgængelige megabyte| • 50% af gratis hukommelse eller mere = orden</br>• 25% af gratis hukommelse = skærm</br>• 10% af gratis hukommelse = advarsel</br>• Mindre end 100 MB eller 5% af gratis hukommelse = kritisk eller ud af specifikation|
|Processor –\%processortid (alle forekomster)|• Mindre end 60% consumed = orden</br>• 61 90% consumed = skærm eller advarsel</br>• 91% til 100% consumed = kritisk|


> [AZURE.NOTE] Hvis du beslutter, at du har infrastrukturen i det pågældende element, anbefaler vi, at du defragmenterer diske jævnligt for at forbedre ydeevnen.

<a id="cause2"></a>
## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Årsag: En anden proces eller antivirussoftware forstyrrer Azure sikkerhedskopi

Vi har set flere forekomster, hvor andre processer i Windows-systemet har opnår ydeevnen af Azure sikkerhedskopi agent processen. Eksempelvis hvis du bruger både Azure Backup agent og et andet program til at sikkerhedskopiere data, eller hvis antivirussoftware kører og har en lås på filer, der skal sikkerhedskopieres, multiplummet låser på kan filer forårsage konflikt. I så fald sikkerhedskopien kan mislykkes, eller jobbet kan tage længere tid end forventet.

Guldkorn i dette scenarie er at deaktivere det andre sikkerhedskopiering program til at se, om den ekstra tid til Azure Backup agent ændres. Normalt, er og Sørg for, at flere sikkerhedskopieringsjob ikke kører på samme tid tilstrækkeligt at forhindre dem i påvirker hinanden.

For antivirus-programmer anbefaler vi, at du udelade følgende filer og placeringer:

- C:\Program Files\Microsoft Azure gendannelse Services Agent\bin\cbengine.exe som en proces
- C:\Program Files\Microsoft Azure gendannelse Services Agent\ mapper
- Ridse placering (Hvis du ikke bruger den standard placering)

<a id="cause3"></a>
## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Årsag: Backup agent kører på en Azure virtuelt

Hvis du kører Backup agent på en VM, bliver ydeevnen langsommere, end når du kører den på en fysisk maskine. Dette forventes på grund af IOP'ER begrænsninger.  Du kan dog optimere ydeevne ved at skifte de drev, der skal sikkerhedskopieres til Azure Premium-lager. Vi arbejder på at løse dette problem, og fix bliver tilgængelig i en senere version.

<a id="cause4"></a>
## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Årsag: Sikkerhedskopiering af et stort antal (millioner) filer

Flytte en stor mængde data tager længere tid end flytte en mindre mængde data. I nogle tilfælde er tidspunktet for sikkerhedskopiering relateret ikke kun størrelsen på dataene, men antallet af filer eller mapper. Dette er tilfældet, når der skal sikkerhedskopieres millioner af små filer (et par byte til et par kilobyte).

Dette sker, fordi mens sikkerhedskopiere dataene, kan du flytte den til Azure Azure er samtidigt udarbejdelse af liste dine filer. I visse sjældne scenarier kan kataloghandlingen tage længere tid end forventet.

Følgende indikatorer kan hjælpe dig med at forstå flaskehals og tilsvarende arbejde på de næste trin:

- **Brugergrænsefladen vises status for dataoverførslen**. Data, der stadig overføres. Netværksbåndbredden eller størrelsen på data kan forårsage forsinkelser.

- **Brugergrænsefladen, ikke vises status for dataoverførslen**. Åbn loggene findes i C:\Microsoft Azure gendannelse Services Agent\Temp, og markér derefter afkrydsningsfeltet for posten FileProvider::EndData i loggene. Denne post betyder, at dataoverførslen er færdig, og kataloghandlingen foregår. Ikke annullere de sikkerhedskopieringsjob. I stedet vente lidt længere kataloghandlingen er udført. Hvis problemet fortsætter, kan du kontakte [Azure understøtter](https://portal.azure.com/#create/Microsoft.Support).
