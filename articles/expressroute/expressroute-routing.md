<properties
   pageTitle="Routing krav til ExpressRoute | Microsoft Azure"
   description="Denne side indeholder særlige krav til konfiguration og administration af distribution til ExpressRoute kredsløb."
   documentationCenter="na"
   services="expressroute"
   authors="osamazia"
   manager="ganesr"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/19/2016"
   ms.author="osamazia"/>


# <a name="expressroute-routing-requirements"></a>ExpressRoute routing krav  

Hvis du vil oprette forbindelse til Microsofts skytjenester ved hjælp af ExpressRoute, skal du konfigurere og administrere routing. Nogle connectivity udbydere tilbyder konfiguration og administration af routing som en administreret tjeneste. Spørg udbyderen forbindelse for at kunne se, hvis de tilbyder denne tjeneste. Hvis de ikke, skal du overholde følgende krav. 

Se en beskrivelse af de routing sessioner, der skal konfigureres at lette connectivity [kredsløb og routing domæner](expressroute-circuit-peerings.md) artikel.

>[AZURE.NOTE] Microsoft understøtter ikke alle de router redundans protokoller (f.eks., HSRP, VRRP) til konfigurationer med høj tilgængelighed. Vi er afhængige af et overflødige par BGP sessioner per peering for høj tilgængelighed.

## <a name="ip-addresses-used-for-peerings"></a>IP-adresser, der bruges til peerings

Du har brug at reservere et par blokke med IP-adresser til at konfigurere routing mellem dit netværk og Microsofts Enterprise kant (MSEEs) routere. Dette afsnit indeholder en liste over krav og beskrives reglerne for hvordan disse IP-adresser skal hentet og bruges.

### <a name="ip-addresses-used-for-azure-private-peering"></a>IP-adresser, der bruges til Azure privat peering

Du kan bruge enten private IP-adresser eller offentlige IP-adresser til at konfigurere peerings. Bruges til at konfigurere omdirigerer adresseområde skal ikke overlapper med adresseområder, der bruges til at oprette virtuelle netværk i Azure. 

 - Du skal reservere en /29 undernet eller to /30 undernet for routing grænseflader.
 - De undernet, der bruges til routing kan være enten private IP-adresser eller offentlige IP-adresser.
 - Undernet skal ikke er i konflikt med det område, der er reserveret af kunden til brug i Microsoft cloud.
 - Hvis en /29 undernet bruges, der skal opdeles i to /30 undernet. 
     - De første /30 undernet, der skal bruges til linket primære og andet/30 undernet, der skal bruges til linket sekundær.
     - For hver af /30 undernet, skal du bruge den første IP-adressen på /30 undernet på din router. Microsoft bruger den anden IP-adressen på /30 undernet, at konfigurere en BGP session.
     - Du skal konfigurere begge BGP sessioner for vores [tilgængelighed SERVICENIVEAUAFTALE](https://azure.microsoft.com/support/legal/sla/) skal gælde.  

#### <a name="example-for-private-peering"></a>Eksempel på private peering

Hvis du vælger at bruge a.b.c.d/29 til at konfigurere den peering, vil den opdeles i to /30 undernet. I eksemplet nedenfor vil vi se på hvordan a.b.c.d/29 undernet bruges. 

a.b.c.d/29 er opdelt a.b.c.d/30 og a.b.c.d+4/30 og overføres til Microsoft via klargøring API'er. Du vil bruge a.b.c.d+1 som VRF IP til den primære PE og Microsoft vil forbruge a.b.c.d+2 som VRF IP til den primære MSEE. Du vil bruge a.b.c.d+5 som VRF IP til den sekundære PE og Microsoft bruger a.b.c.d+6 som VRF IP for den sekundære MSEE.

Overvej en sag, hvor du kan vælge 192.168.100.128/29 til konfiguration af privat peering. 192.168.100.128/29 indeholder adresser fra 192.168.100.128 til 192.168.100.135 mellem som:

- 192.168.100.128/30 skal tildeles til link1, med brug af 192.168.100.129 og Microsoft ved hjælp af 192.168.100.130-udbyder.
- 192.168.100.132/30 skal tildeles til link2, med brug af 192.168.100.133 og Microsoft ved hjælp af 192.168.100.134-udbyder.

### <a name="ip-addresses-used-for-azure-public-and-microsoft-peering"></a>IP-adresser, der bruges til Azure offentligt og Microsoft peering

Du skal bruge offentlige IP-adresser, du ejer til konfiguration af BGP sessioner. Microsoft skal kunne bekræfte ejerskabet af IP-adresserne via Routing Internet registre og Internet Routing registre. 

- Du skal bruge et entydigt/29 undernet eller to /30 undernet til konfiguration af BGP peering for hver peering per ExpressRoute kredsløb (Hvis du har mere end ét). 
- Hvis en /29 undernet bruges, der skal opdeles i to /30 undernet. 
    - De første /30 undernet, der skal bruges til linket primære og andet/30 undernet, der skal bruges til linket sekundær.
    - For hver af /30 undernet, skal du bruge den første IP-adressen på /30 undernet på din router. Microsoft bruger den anden IP-adressen på /30 undernet, at konfigurere en BGP session.
    - Du skal konfigurere begge BGP sessioner for vores [tilgængelighed SERVICENIVEAUAFTALE](https://azure.microsoft.com/support/legal/sla/) skal gælde.

## <a name="public-ip-address-requirement"></a>Offentlige IP-adresse krav 

### <a name="private-peering"></a>Privat Peering 

Du kan vælge at bruge offentlig eller privat IPv4-adresser til private peering. Vi giver dig til slut isolationsniveauet af din trafik, så overlappende af adresser med andre kunder ikke er muligt i tilfælde af private peering. Disse adresser meddeles ikke til internettet. 

### <a name="public-peering"></a>Offentlige Peering

Azure offentlige peering stien gør det muligt at oprette forbindelse til alle tjenester, der findes i Azure over deres offentlige IP-adresser. Disse omfatter tjenester, der er angivet i [ExpessRoute ofte stillede spørgsmål](expressroute-faqs.md) og de tjenester, der er hostet af softwareudviklere på Microsoft Azure. Forbindelse til Microsoft Azure-tjenester på offentlige peering startes altid fra dit netværk til Microsoft network. Du skal bruge offentlige IP-adresser for den trafik, der er beregnet til Microsoft-netværk.

### <a name="microsoft-peering"></a>Microsoft Peering

Microsoft peering stien kan du oprette forbindelse til Microsofts skytjenester, der ikke understøttes via Azure offentlige peering stien. Listen over tjenester omfatter Office 365-tjenester, som Exchange Online, SharePoint Online, Skype for Business og CRM Online. Microsoft understøtter tovejs-forbindelse på Microsoft peering. Trafik bestemt til Microsofts skytjenester skal bruge gyldige offentlige IPv4-adresser, inden de angiver Microsoft network.

Sørg for, at din IP-adresse, og som tal er registreret i et registre nedenfor.

- [ARIN](https://www.arin.net/)
- [APNIC](https://www.apnic.net/)
- [AFRINIC](https://www.afrinic.net/)
- [LACNIC](http://www.lacnic.net/)
- [RIPENCC](https://www.ripe.net/)
- [RADB](http://www.radb.net/)
- [ALTDB](http://altdb.net/)

>[AZURE.IMPORTANT] Offentlige IP-adresser reklame for Microsoft over ExpressRoute skal ikke reklame for internettet. Dette kan muligvis ødelægge forbindelse til andre Microsoft-tjenester. Offentlige IP-adresser, der bruges af servere i dit netværk, der kommunikerer med O365 slutpunkter i Microsoft kan dog vises over ExpressRoute. 

## <a name="dynamic-route-exchange"></a>Dynamisk omdirigere exchange

Routing exchange bliver over eBGP protocol. EBGP sessioner er etableret mellem MSEEs og din routere. Godkendelse af BGP sessioner er ikke et krav. Hvis det er nødvendigt, kan være konfigureret en MD5-hash. Se [konfigurere routing](expressroute-howto-routing-classic.md) og [kredsløb klargøring kredsløb tilstandene for arbejdsprocesser og](expressroute-workflows.md) oplysninger om konfiguration af BGP sessioner.

## <a name="autonomous-system-numbers"></a>Selvstændige System tal

Microsoft bruger AS 12076 til Azure offentligt tilgængelig, Azure privat og Microsoft peering. Vi har reserveret ASN'er fra 65515 til 65520 til intern brug. Både 16 og 32 bit som tal understøttes.

Der er ingen krav omkring data filoverførsel symmetri. Frem og tilbage stier kan gennemgå forskellige router par. Identiske omdirigerer skal vises på begge sider på tværs af flere kredsløb par, der hører du. Distribuere målepunkter behøver ikke at være identiske.

## <a name="route-aggregation-and-prefix-limits"></a>Distribuere sammenlægning og begrænsninger for præfiks

Vi understøtter op til 4.000 præfikser reklame for os via den Azure private peering. Dette kan øges op til 10.000 præfikser, hvis tilføjelsesprogrammet ExpressRoute premium er aktiveret. Vi accepterer op til 200 præfikser for hver BGP session til Azure offentlige og Microsoft peering. 

BGP sessionen fjernes, hvis antallet af præfikser overstiger grænsen. Vi accepterer standard omdirigerer på private peering linket kun. Udbyder skal filtrere standard distribuere og private IP-adresser (RFC 1918) fra den offentlige Azure og Microsoft peering stier. 

## <a name="transit-routing-and-cross-region-routing"></a>Overførsel routing- og routing i tværs område

ExpressRoute kan ikke konfigureres som overførsel routere. Du er nødt til at regne udbyderen connectivity for overførsel routing-tjenester.

## <a name="advertising-default-routes"></a>Reklamer standard omdirigerer

Standard omdirigerer der må kun på Azure privat peering sessioner. I så fald skal vi distribuere al trafik fra de tilknyttede virtuelle netværk til netværket. Reklamer standard omdirigerer til private peering medfører internet stien fra Azure blive blokeret. Du skal stole på din virksomhedens kant for at omdirigere trafikken fra og til internettet for tjenester, der findes i Azure. 

 Hvis du vil aktivere forbindelse til andre Azure tjenester og infrastrukturtjenester, skal du sikre dig en af følgende elementer er på plads:

 - Azure offentlige peering er aktiveret til at dirigere trafik til den offentlige slutpunkter
 - Du kan bruge brugerdefinerede routing til at tillade forbindelse til internettet for hver undernet, der kræver forbindelse til internettet.
 
>[AZURE.NOTE] Reklamer standard omdirigerer brydes, Windows og andre VM licens aktivering. Følg vejledningen [her](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) kan løse dette problem.

## <a name="support-for-bgp-communities-preview"></a>Understøttelse af BGP community'er (Preview)


Dette afsnit indeholder en oversigt over, hvordan BGP communities skal bruges sammen med ExpressRoute. Microsoft vil annoncere omdirigerer i den offentlige og Microsoft peering stier med omdirigerer mærket med relevante community værdier. Begrundelse for at gøre dette og oplysningerne om community værdier er beskrevet nedenfor. Microsoft, men kan ikke vises korrekt mærket til omdirigerer reklame for Microsoft community værdier.

Hvis du opretter forbindelse til Microsoft via ExpressRoute på en hvilken som helst én peering placering i et geopolitiske område, har du adgang til alle Microsofts skytjenester på tværs af alle områder inden for geopolitiske grænsen. 

Eksempelvis hvis du har forbindelse til Microsoft i Amsterdam gennem ExpressRoute, har du adgang til alle Microsofts skytjenester North Europa og Vest Europe som vært. 

Referere til siden [ExpressRoute partnere og peering placeringer](expressroute-locations.md) for en detaljeret liste over geopolitiske områder, tilknyttede Azure områder og tilsvarende ExpressRoute peering placeringer.

Du kan købe mere end én ExpressRoute kredsløb per geopolitiske område. Har flere forbindelser giver dig betydelige fordele på høj tilgængelighed på grund af geografisk redundans. I tilfælde, hvor du har flere ExpressRoute kredsløb, får du det samme sæt præfikser annonceret fra Microsoft på den offentlige peering og Microsoft peering stier. Dette betyder, at du har forskellige stier fra dit netværk til Microsoft. Dette kan forårsage optimal routing beslutninger skal foretages i dit netværk. Som et resultat, kan du opleve optimal connectivity oplevelser til forskellige tjenester. 

Microsoft kan mærke præfikser annonceret gennem offentlige peering og præfikser Microsoft peering med relevante BGP community værdier, der angiver området, der er placeret i. Du kan stole på community værdier til træffe relevante routing beslutninger til at tilbyde [optimal routing til kunder](expressroute-optimize-routing.md).

| **Geopolitiske område** | **Microsoft Azure område** | **BGP community værdi** |
|---|---|---|
| **Nordamerika** |    |  |
|    | Indtastning af østasiatiske USA | 12076:51004 |
|    | Indtastning af østasiatiske USA 2 | 12076:51005 |
|    | Vest USA | 12076:51006 |
|    | Vest USA 2 | 12076:51026 |
|    | Vest centrale USA | 12076:51027 |
|    | Nord centrale USA | 12076:51007 |
|    | Syd centrale USA | 12076:51008 |
|    | Centrale USA | 12076:51009 |
|    | Canada Central | 12076:51020 |
|    | Canada øst | 12076:51021 |
| **Sydamerika** |  |  |
|    | Brasilien syd | 12076:51014 |
| **Europe** |    |  |
|    | Nord Europe | 12076:51003 |
|    | Vest Europe | 12076:51002 |
| **Asien/Stillehavsområdet** |    |   |
|    | Østasien | 12076:51010 |
|    | Sydøstasien | 12076:51011 |
| **Japan** |     |   |
|    | Japan øst | 12076:51012 |
|    | Japan vest | 12076:51013 |
| **Australien** |    |   | 
|    | Australien øst | 12076:51015 |
|    | Australien Sydøst | 12076:51016 |
| **Indien** |    |   |
|    | Indien syd | 12076:51019 |
|    | Indien vest | 12076:51018 |
|    | Indien Central | 12076:51017 |

Alle omdirigerer annonceret fra Microsoft vil være mærket med den relevante community-værdi. 

>[AZURE.IMPORTANT] Globale præfikser vil blive mærket med en passende community-værdi, og der vises kun, når ExpressRoute premium tilføjelsesprogrammet er aktiveret.


Ud over ovenstående, Microsoft vil også mærke præfikser baseret på den tjeneste, de tilhører. Dette gælder kun for Microsoft peering. Tabellen nedenfor indeholder en tilknytning af tjenesten til BGP community værdi.

| **Tjenesten** | **BGP community værdi** |
|---|---|
| **Exchange** | 12076:5010 |
| **SharePoint** | 12076:5020 |
| **Skype For Business** | 12076:5030 |
| **CRM Online** | 12076:5040 |
| **Andre Office 365-tjenester** | 12076:5100 |

>[AZURE.NOTE] Microsoft imødekomme ikke BGP community værdier, du angav på omdirigerer reklame for Microsoft.

## <a name="next-steps"></a>Næste trin

- Konfigurere ExpressRoute forbindelsen.

    - [Opret et ExpressRoute kredsløb for den klassiske implementeringsmodel](expressroute-howto-circuit-classic.md) eller [oprette og redigere en ExpressRoute kredsløb, ved hjælp af Azure ressourcestyring](expressroute-howto-circuit-arm.md)
    - [Konfigurer distribution til den klassiske implementeringsmodel](expressroute-howto-routing-classic.md) eller [konfigurere distribution til implementeringsmodel ressourcestyring](expressroute-howto-routing-arm.md)
    - [Link en klassisk VNet til en ExpressRoute kredsløb](expressroute-howto-linkvnet-classic.md) eller [En ressourcestyring VNet til en ExpressRoute kredsløb](expressroute-howto-linkvnet-arm.md)


