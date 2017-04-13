<properties
   pageTitle="Azure arkitektur Reference - IaaS: Implementere en DMZ mellem Azure og internettet | Microsoft Azure"
   description="Sådan implementere en sikker hybrid netværksarkitektur med internetadgang i Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-dmz-between-azure-and-the-internet"></a>Implementere en DMZ mellem Azure og internettet

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

I denne artikel beskriver bedste fremgangsmåder til at implementere et sikkert hybrid-netværk, der udvider dit lokale netværk, der accepterer trafik fra internettet netværk til Azure. Denne referencearkitektur udvider arkitekturen er beskrevet i artiklen [implementere en DMZ mellem Azure og dit lokale datacenter][implementing-a-secure-hybrid-network-architecture]. Det anbefales, du læser dokumentet og forstår, der henviser arkitektur før læse dette dokument.

> [AZURE.NOTE] Azure har to forskellige installation modeller: [Ressourcestyring] [ resource-manager-overview] og klassisk. Denne referencearkitektur bruger Ressourcestyring, som Microsoft anbefaler til nye installationer. 

Typisk anvendelse sager for denne arkitektur omfatter:

- Hybrid programmer hvor arbejdsbelastninger Kør delvist i det lokale miljø og delvist i Azure.

- Azure infrastruktur, som omdirigerer indgående trafik fra lokale og internettet.

## <a name="architecture-diagram"></a>Arkitektur diagram

I det følgende diagram fremhæver de vigtige komponenter i denne arkitektur:

> Et Visio-dokument, der indeholder dette arkitektur diagram kan hentes på [Microsoft download center][visio-download]. I dette diagram er på siden "DMZ – offentlige".

[! [0]][0]

- **Offentlige IP-adresse (PIP).** Dette er IP-adressen på det offentlige slutpunkt. Eksterne brugere, der har forbindelse til internettet kan få adgang til systemet via denne adresse.

- **Netværk virtuelle maskinen (for).**  FOR er et generisk udtryk, der beskriver en VM udføre opgaver som at tillade eller nægte adgang som en firewall, optimere WAN handlinger (herunder Netværkskomprimering), brugerdefinerede routing eller andre netværksfunktioner.

- **Azure justering af belastning.** Alle indgående anmodninger fra internettet gå igennem denne justering af belastning og fordeles på NVAs i den offentlige DMZ indgående undernet.

- **Offentlige DMZ indgående undernet.** Dette undernet accepterer anmodninger fra Azure justering af belastning. Indgående anmodninger sendes til en af NVAs i DMZ.

- **Offentlige DMZ udgående undernet.** Forespørgsler, der er godkendt af for passerer gennem dette undernet til den interne justering af belastning for web niveau.

## <a name="recommendations"></a>Anbefalinger

Azure byder på mange forskellige ressourcer og ressourcetyper, så denne referencearkitektur kan være klargjort mange forskellige måder. Vi har angivet en Azure ressourcestyring skabelon for at installere den referencearkitektur, der følger disse anbefalinger. Hvis du vælger at oprette din egen referencearkitektur skal du følge disse anbefalinger, medmindre du har særlige krav, en anbefaling der ikke passer.

### <a name="nva-recommendations"></a>FOR anbefalinger

Implementere et sæt af NVAs til trafik stammer fra internettet og et andet til trafik kommer lokalt. Det er en sikkerhedsrisiko bruge kun ét sæt NVAs til begge, fordi dette design giver ingen sikkerhed kanten mellem de to sæt netværkstrafik. Der er en fordel at bruge dette design, fordi den reducerer kompleksiteten af sikkerhedsregler for fejlkontrol, og gør det mere klart hvilke regler, der svarer til hver indgående netværksanmodning. For eksempel implementerer ét sæt NVAs regler for internettrafik kun mens en anden række NVAs implementere regler for lokale trafik kun.

Medtage et lag 7 for at afslutte programmet forbindelser på niveauet for og vedligeholde lighed med back end-niveauer. Dette garanterer symmetriske connectivity som svar trafik fra back end-lag returnerer gennem for.  

### <a name="public-load-balancer-recommendations"></a>Offentlige Indlæs belastningsjusteringstjenesten anbefalinger ###

For at bevare skalerbarhed og tilgængelighed skal du installere den offentlige DMZ indgående NVAs i en [tilgængelighed angive] [ availability-set] og bruge en [internet modstående justering af belastning] [ load-balancer] til at distribuere internetanmodninger på tværs af NVAs i dialogboksen Angiv tilgængelighed.  

Konfigurere justering af belastning for at acceptere anmodninger kun på de porte, der er nødvendige for internettrafik. For eksempel begrænse indgående HTTP-anmodninger om at port 80 og indgående HTTPS-anmodninger port 443.

## <a name="scalability-considerations"></a>Overvejelser i forbindelse med skalerbarhed

Designe infrastrukturen med en via justering af belastning foran det indgående offentlige DMZ undernet fra starten internettet. Selvom din arkitektur initally kræver en enkelt for, kan det bliver nemmere at skalere til flere NVAs i fremtiden, hvis der allerede er implementeret via justering af belastning på internettet.

## <a name="availability-considerations"></a>Overvejelser i forbindelse med tilgængelighed

Internettet modstående justering af belastning kræver hver for i den offentlige DMZ indgående undernet, at implementere en [tilstand efterprøvning af af][lb-probe]. En efterprøvning af af til tilstand, der ikke svarer på dette slutpunkt betragtes som ikke er tilgængelig, og justering af belastning vil direkte anmodninger om at andre NVAs i det samme sæt tilgængelighed. Bemærk, at hvis alle NVAs ikke svare, dit program mislykkes, så det er vigtigt at have overvågning konfigureret til at beskeder om DevOps, når antallet af forekomster af sund for falder under en grænse.

## <a name="manageability-considerations"></a>Overvejelser i forbindelse med administration

Begrænse funktionen overvågning og administration for den indgående offentlige DMZ FORS til at besvare anmodninger fra feltet hyperlink i management undernettet. Som beskrevet i det [implementere en DMZ mellem Azure og dit lokale datacenter] [ implementing-a-secure-hybrid-network-architecture] dokument, skal du angive en enkelt netværk ruten fra det lokale netværk gennem gatewayen til boksen gå i management undernet begrænse adgangen til.

Hvis gateway forbindelse fra dit lokale netværk til Azure ned, kan du stadig når boksen gå ved at installere en PIP, føje det til feltet hyperlink og remoting i fra internettet.

## <a name="security-considerations"></a>Overvejelser om sikkerheden

Denne referencearkitektur implementerer flere sikkerhedsniveauer:

- Internettet modstående justering af belastning omdirigerer anmodninger om til NVAs i den indgående offentlige DMZ kun undernet, og kun på de porte, der er nødvendige for anvendelse.

- NSG regler for den indgående og udgående offentlige DMZ-undernet forhindre, at NVAs bliver skadet ved at blokere anmodninger, der falder uden for NSG regler.

- NAT routing konfigurationen af NVAs omdirigerer indgående anmodninger på port 80 og port 443 til web niveau justering af belastning, men ignorerer anmodninger på alle andre porte.

Bemærk, at du skal logge alle indgående anmodninger på alle porte. Overvåg jævnligt loggene betaling opmærksomheden på anmodninger, der falder uden for forventede parametre, som de kan angive uautoriseret adgang forsøg.

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a>Brug af NSGs til at blokere/gang trafik mellem program

Hver af websteder, business og data lag begrænser trafikken mellem dem ved hjælp af NSGs. Det vil sige, business niveau bruger en NSG for at blokere al trafik, der ikke stammer fra web niveau, og data niveauet bruger en NSG for at blokere al trafik, der ikke stammer fra business niveauet. Hvis du har et krav til at udvide NSG reglerne for at tillade bredere adgang til disse lag, du afveje disse krav ulemper sikkerhed. Hver ny indgående gangsti repræsenterer en salgsmulighed for utilsigtet eller purposeful data uautoriseret videregivelse eller et andet program skader.

## <a name="solution-deployment"></a>Løsningsinstallation

En installation for en referencearkitektur, der implementerer disse anbefalinger er tilgængelig på Github. Denne referencearkitektur omfatter et virtuelt netværk (VNet), netværk sikkerhedsgruppe (NSG), justering af belastning og to virtuelle maskiner (VM'er).

Arkitekturen reference kan installeres enten med Windows eller Linux FOS ved at følge vejledningen herunder: 

1. Højreklik på knappen nedenfor, og vælg enten "Åbn link på en ny fane" eller "Åbn hyperlink i nyt vindue":  
[![Installere på Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2FvirtualNetwork.azuredeploy.json)

2. Når linket er åbnet i portalen Azure, skal du angive værdier for nogle af indstillingerne: 
    - **Ressourcegruppe** navnet allerede er defineret i parameterfilen, så vælge **Opret ny** og angive `ra-public-dmz-network-rg` i tekstfeltet.
    - Vælg området på rullelisten **placering** .
    - Ikke redigere **Skabelon rod Uri** eller tekstfelterne **Parameter rod Uri** .
    - Vælg den **Os Type** på rullelisten boks, **windows** eller **linux**.
    - Gennemse vilkår og betingelser og derefter klikke på afkrydsningsfeltet **jeg accepterer du vilkår og betingelser, der er anført ovenfor** .
    - Klik på knappen **Køb** .

3. Vente på at fuldføre installationen.

4. Højreklik på knappen nedenfor, og vælg enten "Åbn link på en ny fane" eller "Åbn hyperlink i nyt vindue":  
[![Installere på Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2Fworkload.azuredeploy.json)

5. Når linket er åbnet i portalen Azure, skal du angive værdier for nogle af indstillingerne: 
    - **Ressourcegruppe** navnet allerede er defineret i parameterfilen, så vælge **Opret ny** og angive `ra-public-dmz-wl-rg` i tekstfeltet.
    - Vælg området på rullelisten **placering** .
    - Ikke redigere **Skabelon rod Uri** eller tekstfelterne **Parameter rod Uri** .
    - Gennemse vilkår og betingelser og derefter klikke på afkrydsningsfeltet **jeg accepterer du vilkår og betingelser, der er anført ovenfor** .
    - Klik på knappen **Køb** .

6. Vente på at fuldføre installationen.

7. Højreklik på knappen nedenfor, og vælg enten "Åbn link på en ny fane" eller "Åbn hyperlink i nyt vindue":  
[![Installere på Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2Fsecurity.azuredeploy.json)

8. Når linket er åbnet i portalen Azure, skal du angive værdier for nogle af indstillingerne: 
    - **Ressourcegruppe** navnet allerede er defineret i parameterfilen, så vælge **Brug eksisterende** og angive `ra-public-dmz-network-rg` i tekstfeltet.
    - Vælg området på rullelisten **placering** .
    - Ikke redigere **Skabelon rod Uri** eller tekstfelterne **Parameter rod Uri** .
    - Gennemse vilkår og betingelser og derefter klikke på afkrydsningsfeltet **jeg accepterer du vilkår og betingelser, der er anført ovenfor** .
    - Klik på knappen **Køb** .

9. Vente på at fuldføre installationen.

10. Parameter filerne indeholder faste administratorens brugernavn og adgangskode til alle FOS, og det anbefales, at du straks ændrer begge. Markere det i portalen Azure for hver VM i installationen, og klik derefter på på **Nulstil adgangskode** i bladet **Support + fejlfinding** . Vælg **Nulstil adgangskode** i feltet **tilstand** rullemenuen og derefter vælge et nyt **brugernavn** og en **adgangskode**. Klik på knappen **Opdater** for at bevare.


<!-- links -->

[availability-set]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-3-tier-vm.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[iptables]: https://help.ubuntu.com/community/IptablesHowTo
[lb-probe]: ../load-balancer/load-balancer-custom-probe-overview.md
[load-balancer]: ../load-balancer/load-balancer-internet-overview.md
[network-security-group]: ../virtual-network/virtual-networks-nsg.md
[ra-vpn]: ./guidance-hybrid-network-vpn.md
[ra-expressroute]: ./guidance-hybrid-network-expressroute.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[0]: ./media/blueprints/hybrid-network-secure-vnet-dmz.png "Secure hybrid netværksarkitektur"
