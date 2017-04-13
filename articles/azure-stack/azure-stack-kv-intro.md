<properties
    pageTitle="Azure stak nøgle samling Introduktion | Microsoft Azure"
    description="Få mere at vide, hvordan Azure stak nøgle samling administrerer nøgler og hemmeligheder"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="introduction-to-key-vault-in-azure-stack"></a>Introduktion til vigtige samling Azure stablede #

## <a name="before-you-start"></a>Før du starter

I denne artikel antages følgende:

- Læseren har adgang til et abonnement, der har Azure-tasten samling aktiveret.
- Azure PowerShell SDK er konfigureret.
- Alle lejer mod handlinger kan udføres kun fra PowerShell til version TP2.

## <a name="key-vault-basics"></a>Grundlæggende om nøgle samling

Tasten samling Azure stablede hjælper med at beskytte cryptographic taster, og brug hemmeligheder, som programmer og tjenester i skyen. Ved hjælp af tasten samling, kan du kryptere nøgler og hemmeligheder (som godkendelse taster, lagerplads konto taster, data kryptering taster, .pfx filer og adgangskoder).

Tasten samling strømliner key management og gør det muligt at bevare kontrollen over taster, få adgang til og kryptere dine data. Udviklere kan oprette taster til udvikling og afprøvning i minutter, og derefter overføre dem til fremstilling taster problemfrit. Sikkerhedsadministratorer kan tildele (og tilbagekalde) tilladelse til at taster, efter behov.

Alle med et stablen Azure-abonnement kan oprette og bruge vigtige vaults. Selvom nøglen samling fordel for udviklere og sikkerhedsadministratorer, kan det være implementeret og administreres af en administrator, der administrerer andre Azure stak tjenester for en organisation. For eksempel denne administrator kan logge på med et stablen Azure-abonnement, oprette en samling af legitimationsoplysninger for organisationen, til at gemme taster, og klik derefter er ansvarlig for disse driftsopgaver:

- Oprette eller importere en nøgle eller hemmeligt
- Ophæve eller slette en nøgle eller hemmeligt
- Godkend brugere eller programmer for at få adgang til de vigtigste samling, så de kan administrere eller bruge dets samt hemmeligheder
- Konfigurere vigtige brugen (for eksempel signere eller kryptere)

Denne administrator kan derefter giver udviklere URI'er til at ringe fra deres programmer, og giv en sikkerhedsadministrator med oplysninger om brugen af nøgler logføring.

Udviklere kan også administrere tasterne direkte ved hjælp af API'er. Du kan finde yderligere oplysninger finder nøgle samling developer's guide.

## <a name="scenarios"></a>Scenarier

Tabellen nedenfor beskrives nogle af de scenarier, hvor tasten samling kan hjælpe med at imødekommer behovet hos udviklere og sikkerhedsadministratorer:


### <a name="developer-for-an-azure-stack-application"></a>Udvikler for et stablen Azure-program

**Problem**: Jeg vil skrive et program til Azure stak, som bruger nøgler til signering og kryptering, men jeg vil disse skal være eksterne fra mit program, så løsningen egner sig til et program, der geografisk er distribueret.

**Sætningen**: nøgler er gemt i en samling af legitimationsoplysninger og aktiveres af URI, når det er nødvendigt.


### <a name="developer-for-software-as-a-service-saas"></a>Udvikler for software, som en tjeneste (SaaS)

**Problem:** Jeg vil ikke have ansvar eller potentielle ansvar for mine kunder lejer nøgler og hemmeligheder.

**Sætningen:** Kunder kan importere deres egen taster til Azure stak og administrere dem. Jeg vil kunder at eje og administrere deres taster, så jeg kan Koncentrer dig om at gøre hvad jeg gøre bedst, som leverer kernen softwarefunktioner.


### <a name="chief-security-officer-cso"></a>Ledende Sikkerhedsvagt (sikringsofficer)

**Problem:** Jeg vil sikre dig, at organisationen er styr på vigtige livscyklus og kan overvåge brugen af nøgler.

**Sætningen** Tasten samling er designet, så Microsoft ikke se eller udtrække dine nøgler.  Når et program skal udføre cryptographic handlinger ved hjælp af kundernes taster, sker nøgle samling på vegne af programmet. Programmet ikke kan se kundernes taster.  Selvom vi bruge flere Azure stak tjenester og ressourcer, vil jeg administrere tasterne fra Azure stablede på ét sted. Samling af legitimationsoplysninger indeholder en enkelt grænseflade, uanset hvor mange vaults du har Azure stablede, hvilke områder de support, og hvilke programmer bruge dem.

## <a name="next-steps"></a>Næste trin

[Introduktion til vigtige samling](azure-stack-kv-getting-started.md)
