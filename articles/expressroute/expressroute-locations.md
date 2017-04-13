<properties
   pageTitle="ExpressRoute placeringer | Microsoft Azure"
   description="I denne artikel indeholder en detaljeret oversigt over placeringer, hvor tjenester tilbydes og hvordan du opretter forbindelse til Azure områder."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="cherylmc" />

# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute partnere og peering placeringer

Tabellerne i denne artikel indeholder oplysninger på ExpressRoute connectivity udbydere af ExpressRoute geografiske om, skal Microsofts skytjenester understøttes ExpressRoute og ExpressRoute systemintegratorer (SIs).

## <a name="partners"></a>ExpressRoute connectivity udbydere

ExpressRoute understøttes på tværs af alle Azure områder og placeringer. Følgende kortet indeholder en liste over Azure områder og ExpressRoute placeringer. ExpressRoute placeringer referere til dem, hvor Microsoft peers med flere tjenesteudbydere.

![Placering kort][0]

Du vil have adgang til Azure tjenester på tværs af alle områder i en geopolitiske region, hvis du har forbindelse til mindst én ExpressRoute placering i det geopolitiske område. Den følgende tabel indeholder en oversigt over Azure områder til ExpressRoute placeringer i et geopolitiske område.

|**Geopolitiske område**|**Azure områder**|**ExpressRoute placeringer**|
|---|---|---|
|**Nordamerika**|Indtastning af østasiatiske USA, vest USA, af US 2, centrale USA, syd centrale USA, nord centrale USA, Canada Central, Canada øst|Atlanta Chicago Dallas, Las Vegas, Los Angeles, New York, Seattle, Silicon Valley, Washington DC, Montreal +, Quebec by +, og Toronto|
|**Sydamerika**|Brasilien syd|Sao Paulo|
|**Europe**|Nord Europe, vest Europe Storbritannien vest, Storbritannien syd|Amsterdam Dublin London, Newport(Wales) +, og Paris|
|**Asien**|Sydøstasien, Sydøstasien|Hong Kong, Singapore|
|**Japan**|Japan vest, Japan øst|Osaka, Tokyo|
|**Australien**|Australien Sydøst, Australien øst|Melbourne, Sydney|
|**Indien**|Indien vest, Indien Central, Indien syd|Chennai, Mumbai|



Tabellen nedenfor indeholder oplysninger om områder og geopolitiske grænser for nationale skyer.

|**Geopolitiske område**|**Azure områder**|**ExpressRoute placeringer**|
|---|---|---|---|
|**Amerikanske regering skyen**|USA Gov krigsskibe i Iowa, USA Gov Virginia|Chicago, Dallas, New York, Washington DC|
|**Kina**|Kina Nord, Kina øst|Beijing, Shanghai|
|**Tyskland**|Tyskland Central, Tyskland øst|Berlin, Frankfurt|


Forbindelse på tværs af geopolitiske områder understøttes ikke på den standard ExpressRoute SKU. Du skal aktivere tilføjelsesprogrammet premium ExpressRoute til at understøtte globale connectivity. Forbindelse til nationale skyen miljøer understøttes ikke. Hvis der opstår nødvendigt, kan du arbejde med udbyderen connectivity.


## <a name="connectivity-provider-locations"></a>Connectivity udbyder placeringer

> [AZURE.SELECTOR]
[Placeringer af udbyder](expressroute-locations.md#connectivity-provider-locations)
[udbydere efter placering](expressroute-locations-providers.md#connectivity-provider-locations)

### <a name="production-azure"></a>Fremstilling Azure

| **Tjenesteudbyder**  |**Microsoft Azure** | **Office 365 og CRM Online** | **Placeringer** |
|-----------------------|--------------------|----------------|---------------|
| **AARNet** | Understøttes | Understøttes | Melbourne, Sydney |
| **[Aryaka netværk]( http://www.aryaka.com/)** | Understøttes | Understøttes | Amsterdam Silicon Valley, Singapore, Tokyo, Washington DC |
| **[AT & T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Understøttes | Understøttes | Amsterdam Chicago Dallas, London, Silicon Valley, Singapore, og Sydney, Washington DC |
| **[British Telecom]( http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | Understøttes | Understøttes | Amsterdam, Hongkong, London, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
|**CenturyLink** | Kommer snart | Kommer snart| Silicon Valley |
|**Kina Telecom Global** | Understøttes | Ikke understøttet | Hongkong |
|**[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** | Understøttes | Kommer snart | Dallas, Montreal +, Århus |
| **[Colt]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)**  |  Understøttes | Understøttes | Amsterdam Dublin, London, Tokyo |
| **Comcast** | Understøttes | Understøttes | Chicago, Silicon Valley, Washington DC |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** | Understøttes | Understøttes | Los Angeles | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Understøttes | Understøttes | Amsterdam Atlanta Chicago, Dallas, Hongkong, London, Los Angeles, Melbourne, New York, Osaka, Paris +, Sao Paulo, Seattle, Silicon Valley, Singapore, Sydney, Tokyo, og Århus, Washington DC |
| **euNetworks** |  Understøttes | Understøttes | Amsterdam |
| **GÉANT** | Understøttes | Understøttes | Amsterdam |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |  Understøttes | Understøttes | Osaka, Tokyo |
| **[InterCloud]( https://www.intercloud.com/)** | Understøttes | Understøttes | Amsterdam, London, Singapore, Washington DC |
| **Oprette forbindelse Internet Solutions - skyen** | Understøttes | Understøttes | Amsterdam, London |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)**  | Understøttes | Understøttes | Amsterdam, London, Paris |
| **Jisc** | Understøttes | Understøttes | London | 
| **[Niveau 3 kommunikation]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Understøttes | Understøttes | Amsterdam Chicago Dallas, Las Vegas +, London, Seattle, og Silicon Valley, Washington DC |
| **Megaport** | Understøttes | Understøttes | Dallas, Hongkong, Las Vegas, Los Angeles Melbourne, New York, Seattle, Singapore, Sydney, Washington DC |
| **MTN** | Understøttes | Understøttes | London |
| **Næste generering af Data** | Kommer snart | Kommer snart | Newport(Wales) + |
| **NEXTDC** | Understøttes | Understøttes | Melbourne, Sydney |
| **NTT kommunikation** | Understøttes | Understøttes | London, Los Angeles Osaka, Tokyo |
| **[Orange]( http://www.orange-business.com/en/products/business-vpn-galerie)** | Understøttes | Understøttes | Amsterdam, Hongkong, London, Silicon Valley, Singapore, Sydney, Washington DC |
| **PCCW Global begrænset** | Understøttes | Understøttes | Hongkong |
| **[SingTel]( http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |  Understøttes | Understøttes | Singapore |
| **Softbank** | Understøttes | Understøttes | Osaka, Tokyo | 
| **[Tata kommunikation](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | Understøttes | Understøttes | Amsterdam, Chennai, Hongkong, London, Mumbai, Silicon Valley, Singapore, Washington DC |
| **[TeleCity gruppe]( http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | Understøttes | Understøttes | Amsterdam Dublin, London |
| **Telefonica** | Understøttes | Understøttes | Sao Paulo |
| **Telenor** | Understøttes | Understøttes | Amsterdam, London |
| **[Telstra Corporation]( http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Understøttes | Understøttes | Melbourne, Sydney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | Understøttes | Understøttes | Amsterdam, Hongkong, London, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **Vodafone** | Understøttes | Ikke understøttet | London | 
| **[Zayo gruppe]( http://www.zayo.com/solutions/industries/connect-to-cloud-data-centers/cloud-connectivity/microsoft-expressroute/)** | Understøttes | Understøttes | Chicago, Los Angeles, New York, Silicon Valley Århus, Washington DC |

 **+**Angiver kommer snart

### <a name="national-cloud-environments"></a>Nationalt skyen miljøer

#### <a name="us-government-cloud"></a>Amerikanske regering skyen

| **Tjenesteudbyder**  |**Microsoft Azure** | **Office 365** | **Placeringer** |
|-----------------------|--------------------|----------------|---------------|
| **[AT & T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Understøttes | Understøttes | Chicago, Washington DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Understøttes | Understøttes | Chicago, Dallas, New York, Washington DC |
| **[Niveau 3 kommunikation]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Understøttes | Understøttes | Chicago, New York +, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | Understøttes | Understøttes | Chicago, Dallas +, New York, Washington DC |

#### <a name="china"></a>Kina

| **Tjenesteudbyder**  |**Microsoft Azure** | **Office 365** | **Placeringer** |
|-----------------------|--------------------|----------------|---------------|
| **Kina Telecom** | Understøttes | Ikke understøttet | Beijing, Shanghai|
Hvis du vil vide mere, skal du se [ExpressRoute i Kina](http://www.windowsazure.cn/home/features/expressroute/).

#### <a name="germany"></a>Tyskland

| **Tjenesteudbyder**  |**Microsoft Azure** | **Office 365** | **Placeringer** |
|-----------------------|--------------------|----------------|---------------|
| **[Colt]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Understøttes | Ikke understøttet | Berlin +, Frankfurt|
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Understøttes | Ikke understøttet | Frankfurt|
| **e-overdækket** | Understøttes | Ikke understøttet | Berlin|
| **Interxion** | Understøttes | Ikke understøttet | Frankfurt|

## <a name="nonpartners"></a>Forbindelsen via tjenesteudbydere ikke vises på listen

Hvis din forbindelse udbyder ikke vises i forrige afsnit, kan du stadig oprette en forbindelse.

- Spørg udbyderen forbindelse for at se, om de er forbundet til en af udveksling i ovenstående tabel. Du kan se linkene nedenfor for at indsamle flere oplysninger om services, som exchange udbydere. Flere connectivity udbydere er allerede tilsluttet Ethernet udveksling.

    - [Equinix sky Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- Har udbyderen connectivity udvide dit netværk til peering placeringen af valg.
    - Sørg for, at udbyderen connectivity udvider forbindelsen på en meget tilgængelige måde, så der er ingen enkelt punkter af fejl.
- Bestil et ExpressRoute kredsløb med exchange som udbyderen forbindelse for at kunne oprette forbindelse til Microsoft.
    - Følg trinnene i [oprette et ExpressRoute kredsløb](expressroute-howto-circuit-classic.md) til at konfigurere forbindelsen.

|**Connectivity udbyder**|**Exchange**|**Placeringer**|
|---|---|---|
|**[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)**|Equinix|Singapore|
|**Alaska kommunikation**|Equinix|Seattle|
|**[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure )**|Equinix|New York, Washington DC|
|**[XO kommunikation](http://www.xo.com/)**|Equinix|Silicon Valley|


## <a name="expressroute-system-integrators"></a>ExpressRoute system ændres

Aktivere privat forbindelse til dine behov kan være en udfordring, baseret på skalaen på dit netværk. Du kan arbejde med en af de systemintegratorer, der er angivet i følgende tabel for at hjælpe dig med at onboarding til ExpressRoute.

|**Workflowsystemet**|**Kontinent**|
|---|---|
|**[Avanade Inc.](http://www.avanade.com/)**| Asien, Europe, USA |
|**[DotNet løsninger](http://www.dotnetsolutions.co.uk/)**| Europe |
|**[Equinix Professional Services](http://www.equinix.com/services/consulting/)**|OS|
|**[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Asien |
|**[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | OS |
|**[Project førerskab](http://www.projectleadership.net/azure)** | OS |

## <a name="next-steps"></a>Næste trin

- Du kan finde flere oplysninger om ExpressRoute, [ExpressRoute ofte stillede spørgsmål](expressroute-faqs.md).
- Sørg for, at alle forudsætninger er opfyldt. Se [ExpressRoute forudsætninger](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Placering kort"
