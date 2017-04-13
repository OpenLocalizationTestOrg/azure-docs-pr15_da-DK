<properties
    pageTitle="Hvad er nyt i Azure stak | Microsoft Azure"
    description="Hvad er nyt i Azure stak"
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="helaw"/>

# <a name="whats-new-in-azure-stack-technical-preview-2"></a>Hvad er nyt i Azure stak Technical Preview 2
Denne version indeholder nye funktioner til både lejere og administratorer.

## <a name="network"></a>Netværk   
   - [IDN'er](azure-stack-understanding-dns-in-tp2.md) indeholder registrering af interne netværk og Domain Name System (DNS) opløsning uden ekstra infrastruktur til DNS.
   - [Virtuel netværk gateways](azure-stack-create-vpn-connection-one-node-tp2.md) giver VPN-forbindelse indstillinger til Azure eller lokale ressourcer.
   - Bruger defineret omdirigerer kan distribuere netværkstrafik via firewalls, sikkerhed, andre udstyr og andre tjenester.
   - Du kan oprette netværksressourcer fra Marketplace.   

## <a name="storage"></a>Lagerplads
 - [Azure](https://msdn.microsoft.com/library/dd179353.aspx) muliggør pålidelige og fast service messaging.
 - [Lagerplads analytics](https://msdn.microsoft.com/library/azure/hh343270.aspx) capture lagerplads ydelsesdata. Du kan bruge disse data til at spore anmodninger, til at analysere tendenser brugen og diagnosticere problemer med din lagerplads-konto.
 - BLOB-lager understøtter [Føj Bloker handling](https://msdn.microsoft.com/library/azure/mt427365.aspx).
 - Understøttelse af Premium Storage API konti.
 - Lejer lagerplads tjenesten understøttelse af almindelige værktøjer og SDK'er som Azure CLI, PowerShell, .NET, Python og Java SDK. 
 - [Lagerplads konto delt Access signatur](https://msdn.microsoft.com/library/azure/mt584140.aspx) aktivere findelt uddelegering af adgang til din lagerplads tjenester uden at dele din fulde kontonøgle.  
 - Lagerplads services nu bruge [Gruppen administrerede tjenestekonti](https://technet.microsoft.com/library/hh831477.aspx) for stærke værdipapir med lav administration.
 - Du kan frigøre ubrugte lejer ressourcer efter behov.
 - Slettede lagerplads konto opbevaring længde kan konfigureres.
 - Du kan gendanne slettede lejer lagerplads konti.

## <a name="compute"></a>Beregne
- Du kan deallokere virtuelle computere.
- Du kan geninstallere virtuelt udvidelser til fejlfinding og konfiguration management formål.
- Du kan ændre størrelsen på virtuelt diske.
- Virtuelle maskiner kan have flere netværkgrænseflader.

### <a name="portal-experience"></a>Portalen oplevelse
 - Azure stablede områder er en logisk måleenhed skala og administration i Azure stablen. I dette eksempel kan du få vist oplysninger på tjenester som Beregn, netværk og lagerplads efter område.
 - Du kan nu få vist grænsefladen (opdaterer) [azure-stak-updates.md].

## <a name="key-vault"></a>Vigtige samling
- [Tasten samling Azure stablede](azure-stack-kv-intro.md) giver sikker administration af dine nøgler og adgangskoder til skyen apps.
- Du kan overvåge og overvåge vigtige brug af apps og FOS.

## <a name="billing-and-usage"></a>Fakturering for og brugen
 - Få vist data på hvordan dine tjenester er consumed, [Fakturering og forbrug API'er](azure-stack-billing-and-chargeback.md) .  
 - Delegeret udbydere aktivere forhandlere til at tilbyde Azure stak tjenester til deres kunder.

## <a name="monitoring-and-health"></a>Overvågning og tilstand
 - Ny overvågning funktioner, der er tilgængelige i portalen og API'er gør det muligt at få vist og administrere beskeder på dit miljø proaktiv.  

## <a name="next-steps"></a>Næste trin
- [Forstå Azure stak Konceptet arkitektur](azure-stack-architecture.md)      
- [Forstå forudsætninger for installation](azure-stack-deploy.md)
- [Installere Azure stak](azure-stack-run-powershell-script.md)

  
