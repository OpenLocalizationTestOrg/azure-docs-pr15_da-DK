<properties 
   pageTitle="Understøttelse og lukning af politik vejledning til Azure gæst OS | Microsoft Azure" 
   description="Indeholder oplysninger om, hvad Microsoft understøtter med hensyn til til Azure gæst OS bruges af Skytjenester." 
   services="cloud-services" 
   documentationCenter="na" 
   authors="raiye" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="10/24/2016"
   ms.author="raiye"/>

# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Azure gæst OS understøttelse og lukning af politik
Oplysningerne på denne side relaterer til Azure gæst-operativsystem ([Gæst OS](cloud-services-guestos-update-matrix.md)) for Cloud Services arbejder og web roller (PaaS). Det gælder ikke for virtuelle maskiner (IaaS). 

Microsoft har en publiceret [understøtter politik for gæst OS](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq). Den side, du læser nu beskrives, hvordan politikken er implementeret.

Politikken er 

1. Microsoft understøtter **mindst de seneste to familier af gæst OS**. Når en familie er udgået, har kunder 12 måneder fra den officielle lukning af dato til at opdatere til en nyere understøttede gæst OS familie.
2. Microsoft understøtter den **mindst de seneste to versioner af de understøttede gæst OS familier**. 
3. Microsoft understøtter den på **mindst de seneste to versioner af Azure SDK**. Når en version af SDK er udgået, har kunder 12 måneder fra den officielle lukning af dato til at opdatere til en nyere version. 

Nogle gange mere end to familier eller versioner understøttes muligvis. Officiel gæst OS supportoplysninger vises på [Azure gæst OS versioner og SDK kompatibilitet Matrix](cloud-services-guestos-update-matrix.md).


## <a name="when-a-guest-os-family-or-version-is-retired"></a>Når en gæst OS familie eller version er udgået 


En ny gæst OS **familie** er tilføjet et tidspunkt efter version af en ny officiel version af Windows Server-operativsystemet. Når en ny gæst OS familie introduceret, tilbage Microsoft ældste gæst OS familien. 

Gæst OS **versioner** introduceres om hver måned til at inkorporere de seneste opdateringer til MSRC. På grund af de almindelige månedlige opdateringer er en gæst OS version normalt deaktiveret 60 dage efter udgivelsen. Dette bevarer mindst to versioner af gæst Operativsystemet til hver familien, der er tilgængelige til brug. 

### <a name="process-during-a-guest-os-family-retirement"></a>Processen under en gæst OS family pension 


Når lukning af er offentliggjort, har kunder en overgangsperiode på 12-måneders "" før ældre familien officielt fjernet fra service. Denne overgangstiden forlænges efter skøn af Microsoft. Opdateringer skal skrives på [Azure gæst OS versioner og SDK kompatibilitet Matrix](cloud-services-guestos-update-matrix.md).

En gradvist lukning af processen starter 6 måneder i overgangsperioden. I denne periode:

1. Microsoft besked om kunder af lukning af. 
2. Den nyere version af Azure SDK understøtter ikke udgåede gæst OS familien.
3. Nye installationer og geninstallation af Cloud Services er ikke tilladt på udgåede familien

Microsoft fortsætter med at introducere nye gæst OS-version, og som indeholder de seneste opdateringer til MSRC-før den sidste dag i overgangsperioden, kaldet "udløbsdatoen". På det pågældende tidspunkt på en hvilken som helst Cloud Services kører stadig ikke understøttes under Azure SERVICENIVEAUAFTALE. Microsoft har skøn at gennemtvinge opgradering, slette eller stoppe disse tjenester efter denne dato.



### <a name="process-during-a-guest-os-version-retirement"></a>Processen under en lukning af gæst OS-Version 
Hvis kunder angiver deres gæst OS til at opdatere automatisk, behøver de aldrig at bekymre dig om håndtering af gæst OS versioner. De skal altid bruge den nyeste version af gæst OS.

Gæst OS versioner er udgivet hver måned. På grund af rente normalt versioner har hver version en fast levetid.

I 60 dage i levetid er en version "*deaktiveret*". "Deaktiveret" betyder, at versionen fjernes fra Azure klassisk portal. Det kan også ikke længere angives fra CSCFG konfigurationsfil. Eksisterende installationer er udfyldt kører, men nye installationer og kode og konfiguration af opdateringer til eksisterende installationer er ikke tilladt. 

På et senere tidspunkt, gæst OS version "*udløbet*", og alle installationer er stadig kører denne version gældende opgraderet og indstillet til automatisk at opdatere gæst OS i fremtiden. Udløb er udført i batches, så tidsrum fra afbrydelse til udløb kan variere. 

Disse perioder, der kan foretages længere efter Microsofts skøn til lette kunde overgange. Eventuelle ændringer skal kommunikeres på [Azure gæst OS versioner og SDK kompatibilitet Matrix](cloud-services-guestos-update-matrix.md).



### <a name="notifications-during-retirement"></a>Meddelelser under lukning af 

* **Family lukning af** <br>Microsoft bruger blogindlæg og Azure klassisk portalen meddelelse. Kunder, der stadig bruger en udgåede gæst OS familie får besked via direkte kommunikation (mail, portalen meddelelser, telefonopkald) til tildelte tjenesteadministratorer. Alle ændringer, der sendes til denne side og RSS-feed, der er angivet i starten af denne side. 


* **Lukning af version** <br>Alle ændringer, der sendes til denne side og RSS-feed, der er angivet i starten af denne side, herunder den version, deaktiveret og udløbsdatoer. Services administratorer modtager mails, hvis de har installationer, der kører på en deaktiveret gæst OS-version eller familie. Tidsindstilling for disse mails kan variere. Generelt er de mindst en måned før afbrydelse, selvom denne tidsindstilling ikke er en offentlig SERVICENIVEAUAFTALE. 


## <a name="frequently-asked-questions"></a>Ofte stillede spørgsmål

**Hvordan kan jeg reducere indvirkningen ved overflytningen?**

Du skal bruge seneste gæst OS familie til design af Cloud Services. 

1. Planlæg tidligt din migrering til en nyere familie. 
2. Konfigurere midlertidige test installationer til at teste din skybaseret tjeneste, der kører på den nye serie. 
3. Indstil din gæst OS-version til **automatisk** (osVersion = * i filen [.cscfg](cloud-services-model-and-package.md#cscfg) ) så overførsel til gæst OS versioner sker automatisk.

**Hvad nu, hvis min webprogram kræver bedre integration med OS?**

Hvis din web application arkitektur kræver dybere afhængighed af det underliggende operativsystem, understøttes brug platform funktioner som [Start opgaver](cloud-services-startup-tasks.md) eller anden udvidelsesmuligheder af metode der findes i fremtiden. Alternativt kan du også bruge [virtuelle Azure-computere](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – Infrastructure som en tjeneste), hvor du er ansvarlig for at vedligeholde det underliggende operativsystem.
 
## <a name="next-steps"></a>Næste trin
Gennemse de nyeste [Gæst OS versioner](cloud-services-guestos-update-matrix.md).
