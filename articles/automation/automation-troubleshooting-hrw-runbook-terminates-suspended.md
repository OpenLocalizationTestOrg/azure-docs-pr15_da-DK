<properties
   pageTitle="Hybrid Runbook arbejder: Et runbook job ophører med statussen afbrudt | Microsoft Azure"
   description="Symptomer årsager og løsninger for hybride Runbook arbejder job opsigelse før fejl."
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
   ms.date="08/17/2016"
   ms.author="magoedte" />

# <a name="hybrid-runbook-worker-a-runbook-job-terminates-with-a-status-of-suspended"></a>Hybrid Runbook arbejder: Et runbook job ophører med statussen afbrudt

## <a name="summary"></a>Oversigt

Din runbook afbrydes midlertidigt, kort efter forsøg på at udføre den tre gange. Der er betingelser, som kan afbryde runbook fra blive udført, og den relaterede fejlmeddelelse omfatter ikke eventuelle yderligere oplysninger, der angiver hvorfor. I denne artikel indeholder trin til fejlfinding for problemer i forbindelse med fejl under udførelse af Hybrid Runbook arbejder af runbook.

Hvis der ikke er adresseret Azure problemet i denne artikel, kan du besøge Azure-fora på [MSDN og stakoverløb](https://azure.microsoft.com/support/forums/). Du kan sende dit problem på disse fora, eller [ @AzureSupport på Twitter](https://twitter.com/AzureSupport). Du kan også sende en Azure supportanmodning ved at vælge **få support** på webstedet [Azure understøtter](https://azure.microsoft.com/support/options/) .

## <a name="symptom"></a>Symptom

Udførelse af Runbook mislykkes, og den returnerede fejl er, "job handlingen 'Aktivér' ikke kan køres, fordi processen stoppede uventet. Handlingen jobbet blev forsøgt 3 gange."


## <a name="cause"></a>Årsag

Der er flere mulige årsager til fejlen: 

  1. Hybrid arbejder er bag en proxy- eller firewall
  2. Hybrid arbejder der kører på computeren er mindre end den mindste hardware- [krav](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-requirements) 
  3. Runbooks kan godkende med lokale ressourcer


## <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>Årsag 1: Hybrid Runbook arbejder er bag proxy- eller firewall

Hybrid Runbook arbejder der kører på computeren er bag en firewall eller proxyserver server og udgående netværksadgang kan ikke tilladt eller konfigureret korrekt.

### <a name="solution"></a>Løsning

Kontrollér, at computeren har udgående adgang til *. cloudapp.net på port 443, 9354 og 30000 30199. 

## <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>Årsag 2: Computer har mindre end minimumkravene til hardware

Computere, der kører Hybrid Runbook arbejder skal opfylder minimumkravene til hardware før angiver det skal være vært for denne funktion. Ellers, afhængigt af ressource anvendelsen af andre baggrundsprocesser og skyldes runbooks under kørsel af konflikt, computeren vil blive over anvendes og medføre runbook job forsinkelser eller timeout. 

### <a name="solution"></a>Løsning 

Kontrol først udpeget til at køre funktionen Hybrid Runbook arbejder computeren opfylder minimumkravene til hardware.  Hvis det er tilfældet, kan du overvåge CPU og hukommelse udnyttelse for at bestemme sammenhængen mellem ydeevnen for hybride Runbook arbejdsprocesser og Windows.  Hvis der er hukommelse eller CPU tryk, skyldes dette er nødvendigheden at opgradere eller tilføje ekstra processorer eller øge hukommelse for at løse ressource flaskehals og løse fejlen. Du kan også vælge en anden Beregn ressource, der kan understøtte minimumskravene og skalering, når arbejdsbelastningen angiver et større er nødvendigt.         

## <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>Årsag 3: Runbooks kan godkende med lokale ressourcer

### <a name="solution"></a>Løsning

Se **Microsoft SMA** hændelseslog for en tilsvarende med beskrivelse *Win32-processen afsluttet med kode [4294967295]*.  Årsag til denne fejl er du ikke har konfigureret godkendelse i din runbooks eller angivet Kør som legitimationsoplysninger for gruppen Hybrid arbejder.  Gennemgå [Runbook tilladelser](automation-hybrid-runbook-worker.md#runbook-permissions) for at bekræfte, at du har konfigureret godkendelse korrekt til din runbooks.  


 

