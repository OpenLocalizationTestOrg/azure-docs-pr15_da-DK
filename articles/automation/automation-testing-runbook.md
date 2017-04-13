<properties 
    pageTitle="Teste en runbook i Azure automatisering | Microsoft Azure"
    description="Før du publicerer en runbook i Azure automatisering, kan du tester den for at sikre, der fungerer som forventet.  I denne artikel beskrives, hvordan du kan teste en runbook og få vist resultatet."
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
    ms.author="magoedte;bwren" />

# <a name="testing-a-runbook-in-azure-automation"></a>Teste en runbook i Azure automatisering
Når du tester en runbook, [kladdeversion](automation-creating-importing-runbook.md#publishing-a-runbook) er udført, og eventuelle handlinger, der udfører den er fuldført. Ingen jobs er oprettet, men streams [Output](automation-runbook-output-and-messages.md#output-stream) og [Advarsel og fejl](automation-runbook-output-and-messages.md#message-streams) vises i testen output rude. Meddelelser til [detaljeret Stream](automation-runbook-output-and-messages.md#message-streams) vises i ruden Output kun, hvis [$VerbosePreference variabel](automation-runbook-output-and-messages.md#preference-variables) er indstillet til Fortsæt.

Selvom der køres kladdeversion, udfører arbejdsprocessen normalt runbook stadig, og udfører alle handlinger i forhold til ressourcer i miljøet. Derfor skal du kun teste runbooks på ikke-produktiv ressourcer.

Fremgangsmåden til at teste hver [type af runbook](automation-runbook-types.md) er den samme, og der er ingen forskel i test mellem tekstbaseret editor og den grafiske editor i portalen Azure.  


## <a name="to-test-a-runbook-in-the-azure-portal"></a>Teste en runbook i portalen Azure

Du kan arbejde med en hvilken som helst [runbook type](automation-runbook-types.md) i portalen Azure.

1. Åbn den kladdeversion af runbook i enten [tekstbaseret editor](automation-editing-a-runbook.md#Portal) eller [grafiske editor](automation-graphical-authoring-intro.md).
2. Klik på knappen **Test** for at åbne bladet Test.
3. Hvis runbook har parametre, vil de være angivet i den venstre rude, hvor du kan angive værdier, der skal bruges til testen.
4. Hvis du vil køre en test på en [Hybrid Runbook arbejder](automation-hybrid-runbook-worker.md), ændre **Køre indstillinger** til **Hybrid arbejder** og vælg derefter navnet på målgruppen.  Ellers skal beholde standard **Azure** til at køre en test i skyen.
5. Klik på knappen **Start** for at starte testen.
6. Hvis runbook er [PowerShell arbejdsproces](automation-runbook-types.md#powershell-workflow-runbooks) eller [grafiske](automation-runbook-types.md#graphical-runbooks), kan du stopper eller annullere den, mens den er testet med knapperne under ruden Output. Når du afbryder runbook, udfylder den aktuelle aktivitet, før der afbrydes midlertidigt. Når runbook er afbrudt, kan du stoppe den eller genstarte den.
7. Undersøg output fra runbook i ruden output.


## <a name="next-steps"></a>Næste trin

- For at lære at oprette eller importere en runbook, skal du se [oprette eller importere en runbook i Azure Automation](automation-creating-importing-runbook.md)
- Hvis du vil vide mere om grafiske redigering skal du se [grafiske redigering i Azure automatisering](automation-graphical-authoring-intro.md)
- For at komme i gang med PowerShell arbejdsproces runbooks skal du se [Min første PowerShell arbejdsproces runbook](automation-first-runbook-textual.md)
- For at få mere for at vide om at konfigurere runboks for at vende tilbage statusmeddelelser og fejl, herunder anbefalede fremgangsmåder, se [Runbook output og meddelelser i Azure automatisering](automation-runbook-output-and-messages.md)