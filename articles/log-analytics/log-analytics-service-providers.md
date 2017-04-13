<properties
    pageTitle="Log analysefunktioner til internetudbydere | Microsoft Azure"
    description="Log Analytics kan hjælpe administrerede tjenesteudbydere (MSPs), store virksomheder uafhængige udførelsen leverandører (softwareudviklere) og service udbydere administrere og overvåge servere i kundens lokalt eller skyinfrastruktur."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="richrund"/>

# <a name="log-analytics-features-for-service-providers"></a>Logge analysefunktioner til tjenesteudbydere

Log Analytics kan hjælpe administrerede tjenesteudbydere (MSPs), store virksomheder, uafhængige softwareleverandører (softwareudviklere) og service-udbydere, administrere og overvåge servere i kundens lokalt eller skyinfrastruktur. 

Store virksomheder dele mange ligheder med tjenesteudbydere, særligt, når der er et centralt IT-gruppe, der er ansvarlig for administration af IT for at have mange forskellige afdelinger. Dette dokument bruger ord *tjenesteudbyder* for enkel, men de samme funktioner er også tilgængelig for virksomheder og andre kunder.

## <a name="cloud-solution-provider"></a>Skyen løsningsudbyder

Partnere og tjenesteudbydere, der er en del af programmet [Skyen løsning CSP'EN (Provider)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) , er Log Analytics et af de tilgængelige på et CSP abonnement Azure tjenester. 

For Log analyser aktiveres følgende funktioner i *Skyen løsningsudbyder* abonnementer.

Som en *Skybaseret løsningsudbyder* , kan du:

+ Oprette Log Analytics arbejdsområder i en lejer (kunde) abonnement.
+ Access arbejdsområder, der er oprettet af lejere. 
+ Tilføje og fjerne brugeradgang til arbejdsområdet ved hjælp af Azure brugeradministration. Når i en lejer arbejdsområde i portalen OMS på brugeradministration siden Indstillinger er ikke tilgængelig
  - Log Analytics ikke understøtter rollebaseret adgang og -giver en bruger `reader` tilladelse i portalen Azure gør det muligt at foretage ændringer i konfigurationen i portalen OMS

For at logge på med en lejer abonnement, skal du angive lejer-id. Lejer id'et er ofte den sidste del af den e-mail-adresse bruges til at logge på.

+ I portalen OMS, tilføje `?tenant=contoso.com` i URL-adressen til portalen. For eksempel`mms.microsoft.com/?tenant=contoso.com`
+ Brug i PowerShell, den `-Tenant contoso.com` parameter, når du bruger `Add-AzureRmAccount` cmdlet
+ Lejer id føjes automatisk, når du bruger den `OMS portal` link fra Azure portalen for at åbne, og log på portalen OMS for det valgte arbejdsområde

Som en *kunde* af en sky Solution Provider kan du:

+ Oprette logfil analytics arbejdsområder i et CSP-abonnement
+ Access-arbejdsområder, der er oprettet af Konferencetjenesteudbyderen
  -  Brug den `OMS portal` link fra Azure portalen for at åbne, og log på portalen OMS for det valgte arbejdsområde
+ Få vist og bruge brugeradministrationssiden under indstillinger på portalen OMS

>[AZURE.NOTE] Sikkerhedskopiering og gendannelse af websteder løsninger til Log Analytics kan ikke oprette forbindelse til en samling af legitimationsoplysninger gendannelsestjenester og kan ikke konfigureres i et CSP abonnement.

## <a name="managing-multiple-customers-using-log-analytics"></a>Administrere flere kunder, der bruger Log Analytics 

Det anbefales, at du opretter en logfil Analytics arbejdsområde for hver kunde, du administrerer. Et Log Analytics arbejdsområde indeholder:

+ En geografiske placering til dataene skal gemmes. 
+ Granulariteten for fakturering 
+ Data isolationsniveauet 
+ Unikke konfiguration

Ved at oprette et arbejdsområde for hver kunde, kan du holde hver kundedata separat og også spore brugen af hver kunde.

Flere oplysninger om, hvornår og hvorfor til at oprette flere arbejdsområder er beskrevet i [administrere adgangen for at logge analytics] (log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need).

Oprettelse og konfiguration af kunde arbejdsområder kan automatisk, ved hjælp af [PowerShell](log-analytics-powershell-workspace-configuration.md), [ressourcestyring skabeloner](log-analytics-template-workspace-configuration.md), eller brug af [REST-API](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/).

Brug af Ressourcestyring skabeloner til konfiguration af arbejdsområde kan du har en overordnet konfiguration, som kan bruges til at oprette og konfigurere arbejdsområder. Du kan være sikker på, når der oprettes arbejdsområder for kunder, de er automatisk konfigureret til dine behov. Når du opdaterer dine krav, opdateres skabelonen, og derefter igen de eksisterende arbejdsområder. Denne proces sikrer, at selv eksisterende arbejdsområder overholder din nye.    

Når du administrerer flere Log Analytics arbejdsområder, anbefales det integrering af de enkelte arbejdsområder med dine eksisterende billetsystem / handlinger console ved hjælp af [funktionen påmindelser](log-analytics-alerts.md) . Ved at integrere med dine eksisterende systemer, supportpersonale kan fortsætte med at følge deres velkendte processer. Log Analytics regelmæssigt kontrollerer hvert arbejdsområde mod beskeder om kriterier, du angiver og genererer en besked, når en handling er nødvendig.

For niveau ledelsesrapporter, opsummere data på tværs af arbejdsområder kan du bruge integrationen mellem Log analyser og [PowerBI](log-analytics-powerbi.md). Hvis du vil integrere med et andet reporting system, kan du bruge søgning API (via PowerShell eller [RESTEN](log-analytics-log-search-api.md)) til at køre forespørgsler og eksportere søgeresultaterne.

## <a name="next-steps"></a>Næste trin

+ Automatisere oprettelse og konfiguration af arbejdsområder ved hjælp af [ressourcestyring skabeloner](log-analytics-template-workspace-configuration.md)
+ Automatisere oprettelse af arbejdsområder ved hjælp af [PowerShell](log-analytics-powershell-workspace-configuration.md) 
+ Brug af [beskeder](log-analytics-alerts.md) til integration med eksisterende systemer
+ Generere oversigt rapporter ved hjælp af [PowerBI](log-analytics-powerbi.md)
