<properties
    pageTitle="Hvad er Azure-tasten samling? | Microsoft Azure"
    description="Azure-tasten samling hjælper med at beskytter cryptographic nøgler og hemmeligheder, der bruges af skyen programmer og tjenester. Ved hjælp af Azure-tasten samling, kan kunder kryptere nøgler og hemmeligheder (såsom godkendelse taster, lagerplads konto taster, data kryptering taster. PFX-filer og adgangskoder) ved hjælp af taster, der er beskyttet af hardware sikkerhed moduler (HSMs)."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/10/2016"
    ms.author="cabailey"/>



# <a name="what-is-azure-key-vault"></a>Hvad er Azure-tasten samling?

Azure-tasten samling findes i de fleste områder. Få mere at vide ved at se [tasten samling priser side](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduktion

Azure-tasten samling hjælper med at beskytter cryptographic nøgler og hemmeligheder, der bruges af skyen programmer og tjenester. Ved hjælp af tasten samling, kan du kryptere nøgler og hemmeligheder (såsom godkendelse taster, lagerplads konto taster, data kryptering taster. PFX-filer og adgangskoder) ved hjælp af taster, der er beskyttet af hardware sikkerhed moduler (HSMs). Du kan importere eller oprette nøgler i HSMs for større sikkerhed. Hvis du vælger at gøre dette, Microsoft processer dine nøgler i FIPS 140-2 niveau 2 valideret HSMs (hardware og firmware).  

Tasten samling strømliner key management og gør det muligt at bevare kontrollen over taster, få adgang til og kryptere dine data. Udviklere kan oprette taster til udvikling og afprøvning i minutter, og derefter overføre dem til fremstilling taster problemfrit. Sikkerhedsadministratorer kan tildele (og tilbagekalde) tilladelse til at taster, efter behov.

Brug den følgende tabel for bedre at forstå, hvordan nøgle samling kan hjælpe med at imødekommer behovet hos udviklere og sikkerhedsadministratorer.





| Rolle        | Beskrivelse af problemet           | Løser Azure vigtige samling  |
| ------------- |-------------|-----|
| Udvikler til et Azure-program      | "Jeg vil skrive et program til Azure, som bruger taster til signering og kryptering, men jeg vil disse taster skal være eksterne fra mit program, så løsningen egner sig til et program, der geografisk er distribueret. <br/><br/>Jeg vil også disse taster og tip til beskyttes, uden at skrive koden selv. Jeg vil også disse taster og tip til at være let for mig bruge mine programmer med optimal ydeevne." | Taster på √ er gemt i en samling af legitimationsoplysninger og aktiveres af URI, når det er nødvendigt.<br/><br/> √ Taster kan opfyldes ved Azure, ved hjælp af branchestandard algoritmer, nøgler gør og hardware sikkerhed moduler (HSMs).<br/><br/> √ Taster behandles i HSMs, der er placeret i den samme Azure datacentre som programmerne. Dette giver større pålidelighed og reduceret ventetid end hvis tasterne er placeret i en separat placering, som i det lokale miljø.|
| Udvikler for Software, som en tjeneste (SaaS)      |"Jeg vil ikke have ansvar eller potentielle ansvar for mine kunder lejer nøgler og hemmeligheder. <br/><br/>Jeg vil kunder at eje og administrere deres taster, så jeg kan Koncentrer dig om at gøre hvad jeg gøre bedst, som leverer grundlæggende programfunktioner." | √ Kunder kan importere deres egen taster til Azure og administrere dem. Når et SaaS program skal udføre cryptographic handlinger ved hjælp af deres kunders taster, understøtter nøgle samling disse handlinger på vegne af programmet. Programmet ikke kan se kundernes taster.|
| Ledende sikkerhedsvagt (sikringsofficer) | "Jeg gerne vil vide, at vores programmer i overensstemmelse med FIPS 140-2 niveau 2 HSMs til sikker key management. <br/><br/>Jeg vil sikre dig, at organisationen er styr på vigtige livscyklus og kan overvåge brugen af nøgler. <br/><br/>Og selvom vi bruge flere Azure tjenester og ressourcer, jeg vil administrere tasterne fra ét sted i Azure."     |√ HSMs er FIPS 140-2 niveau 2 valideres.<br/><br/>√ Nøgle samling er designet, så Microsoft ikke se eller udtrække dine nøgler.<br/><br/>√ i realtid logføring af vigtige brugen.<br/><br/>√ samling af legitimationsoplysninger indeholder en enkelt grænseflade, uanset hvor mange vaults du har i Azure, hvilke områder de support, og hvilke programmer bruge dem. |


Alle med et Azure-abonnement kan oprette og bruge tasten vaults. Selvom nøglen samling fordel for udviklere og sikkerhedsadministratorer, kan det være implementeret og administreres af en organisations administrator, der administrerer andre Azure tjenester for en organisation. For eksempel denne administrator skal logge på med et Azure-abonnement, oprette en samling af legitimationsoplysninger for organisationen, til at gemme taster, og klik derefter er ansvarlig for driftsopgaver, f.eks.:

+ Oprette eller importere en nøgle eller hemmeligt
+ Ophæve eller slette en nøgle eller hemmeligt
+ Godkend brugere eller programmer for at få adgang til de vigtigste samling, så de kan administrere eller bruge dets samt hemmeligheder
+ Konfigurere vigtige brugen (for eksempel signere eller kryptere)
+ Overvåge vigtige brugen

Denne administrator ville derefter giver udviklere URI'er til at ringe fra deres programmer, og giv deres sikkerhedsadministrator med oplysninger om brugen af nøgler logføring. 

   ![Oversigt over Azure vigtige samling][1]

Udviklere kan også administrere tasterne direkte ved hjælp af API'er. Du kan finde yderligere oplysninger finder [nøgle samling developer's guide](key-vault-developers-guide.md).

## <a name="next-steps"></a>Næste trin

Se [Introduktion til Azure nøgle samling](key-vault-get-started.md)et få Introduktion selvstudium for en administrator.

Se [Azure nøgle samling logføring](key-vault-logging.md)kan finde flere oplysninger om brugen af logføring for nøgle samling.

Se [om taster, hemmeligheder, og certifikater](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx), du kan finde flere oplysninger om brug af nøgler og hemmeligheder med Azure-tasten samling.


<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
