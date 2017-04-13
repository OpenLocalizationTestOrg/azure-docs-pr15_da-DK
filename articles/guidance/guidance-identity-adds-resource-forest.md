<properties
   pageTitle="Azure arkitektur Reference - IaaS: Oprette en Active Directory ressource-område i Azure | Microsoft Azure"
   description="Sådan oprettes en der er tillid til Active Directory-domæne i Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network,active-directory"
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

# <a name="creating-a-active-directory-directory-services-adds-resource-forest-in-azure"></a>Oprette en Active Directory Directory Services (tilføjer) ressourceskov i Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

I denne artikel beskrives det, hvordan du opretter en Active Directory-domæne i Azure, der er adskilt fra, men der er tillid til, ved at domæner i din lokale område.

> [AZURE.NOTE] Azure har to forskellige installation modeller: [Ressourcestyring] [ resource-manager-overview] og klassisk. Denne referencearkitektur bruger Ressourcestyring, som Microsoft anbefaler til nye installationer.

En organisation, der kører lokale Active Directory (AD) muligvis et område, som omfatter mange forskellige domæner. For eksempel kan du oprette individuelle domæner for forskellige afdelinger eller suborganizations, eller nye domæner, kan være blevet tilføjet som et resultat af acquisition eller sammenlægning af andre organisationer. Du kan bruge domæner til at levere isolationsniveauet mellem funktionelle områder, der skal holdes adskilt, muligvis af sikkerhedsmæssige årsager, men du kan dele oplysninger mellem domæner ved at etablere tillidsforhold.

En organisation, der anvender forskellige domæner kan drage fordel af Azure ved at flytte en eller flere af disse domæner til skyen. Alternativt kan en organisation vil beholde alle skyen ressourcer logisk adskiller sig fra disse venteposition i det lokale miljø, og gemme oplysninger om skyen ressourcer i deres egen mappe i et domæne, som også findes i skyen.

Du kan implementere Active Directory i Azure på flere forskellige måder, som beskrevet i artiklerne [Forlænge Active Directory for Azure] [ extending-ad-to-azure] og [Implementere Azure Active Directory][implementing-aad]. Dette dokument fokuserer på et bestemt scenarie: oprette et domæne i skyen, der er anderledes end alle domæner, der afholdes i det lokale miljø, men, der kan have et tillidsforhold til lokale domæner. 

Typisk anvendelse sager for denne arkitektur omfatter:

- Vedligeholde sikkerhed adskillelsen for objekter og identiteter opbevares i skyen.

- Overføre enkelte domæner fra det lokale til skyen.

## <a name="architecture-diagram"></a>Arkitektur diagram

I det følgende diagram fremhæver de vigtige komponenter i denne arkitektur (*Klik for at zoome ind*). Du kan finde flere oplysninger om de elementer, nedtonet læse [implementere en sikker hybrid netværksarkitektur i Azure] [ implementing-a-secure-hybrid-network-architecture] og [implementere en sikker hybrid netværksarkitektur med internetadgang i Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]:

[! [0]][0]

- **Lokale netværk.** Det lokale netværk indeholder sin egen AD skov og domæner.

- **AD-servere.** Dette er domænecontrollere implementere katalogtjenester (AD DS), der kører som FOS i skyen. Disse servere hoste et område, der indeholder et eller flere domæner, adskilt fra disse placeret i det lokale miljø.

- **Envejs tillidsforhold.** Eksemplet i diagrammet viser en envejs tillid fra domænet i skyen til det lokale domæne. Denne relation giver lokale brugere adgang til ressourcer i domænet i skyen, men ikke anden vej rundt. Dette er en fælles sag. Du kan oprette en tovejs tillid, hvis skyen brugere skal også have adgang til lokale ressourcer.

- **Active Directory-undernet.** Active Directory-Domænetjenester servere er hostet i et separat undernet. NSG regler beskytte AD DS servere og kan give en firewall mod trafik fra uventede kilder.

- **Web niveau undernet**, **Business niveau undernet**og **Data klasse undernet**. Disse undernet hoste servere og komponenter, der kører programmer i skyen. Yderligere oplysninger, du [Kører FOS for en N-delt arkitektur på Azure][running-VMs-for-an-N-tier-architecture-on-Azure]. Ressourcer og FOS i dette undernet er indeholdt i skyen-domæne.

- **Azure Gateway**. Gatewayen Azure indeholder en forbindelse mellem lokale netværk og Azure VNet. Det kan være en [VPN-forbindelse] [ azure-vpn-gateway] eller [Azure ExpressRoute][azure-expressroute]. Du kan finde flere oplysninger, se [implementere en sikker hybrid netværksarkitektur i Azure][implementing-a-secure-hybrid-network-architecture].

## <a name="recommendations"></a>Anbefalinger

Dette afsnit indeholder en liste over anbefalinger baseret på de væsentlige komponenter, der kræves til at implementere den grundlæggende arkitektur. Disse anbefalinger omfatter:

- ADDERER indstillinger, og

- Have tillid til konfiguration af relation.

Du skal muligvis yderligere eller forskellige krav fra dem, der er beskrevet her. Du kan bruge elementerne i dette afsnit som udgangspunkt for overvejer, hvordan du tilpasser arkitekturen for dit eget system.

### <a name="adds-recommendations"></a>ADDERER anbefalinger

For specifikke anbefalinger på implementering af Active Directory i skyen, refererer til dokumentet, [Hvis du vil udvide Active Directory for Azure][extending-ad-to-azure]. I artiklen [retningslinjer for installation af Windows Server Active Directory på Azure virtuelle maskiner] [ ad-azure-guidelines] indeholder flere detaljerede oplysninger.

### <a name="trust-recommendations"></a>Hav tillid til anbefalinger

De lokale domæner befinder sig i en anden skov fra domæner i skyen. Hvis du vil aktivere godkendelse af lokale brugere i skyen, domæner i skyen skal have tillid til logon domænet i det lokale område. På samme måde, hvis skyen leverer en logon-domænet for eksterne brugere, det kan være nødvendigt for den lokale skov til at have tillid til skyen domænet.

Du kan oprette tillidsforhold på niveauet for område ved at [oprette tillidsforhold fra område][creating-forest-trusts], eller på domæneniveauet for ved at [oprette eksterne tillidsforhold][creating-external-trusts]. En skov niveau tillid opretter en relation mellem alle domæner i to områder. Et eksternt domæne niveau tillidsforhold kun opretter en relation mellem to angivne domæner. Du skal kun oprette eksterne domæner niveau tillidsforhold mellem domæner i forskellige områder.

Tillidsforhold kan være envejs (envejs) eller tovejs (tovejs):

- En envejs tillid gør det muligt for brugere i et domæne eller en skov (kaldet *indgående* domæne eller skov) adgang til ressourcer, som findes i en anden ( *udgående* domæne eller skov). 

- En tovejs tillid gør det muligt for brugerne i enten domæne eller en skov at få adgang til ressourcer, som findes i den anden.

Den følgende tabel opsummerer sikkerhed og rettighedsadministration konfigurationer for nogle enkle scenarier:

| Scenarie | Lokalt Hav tillid til | Hav tillid til skyen |
|----------|-------------------|-------------|
| Lokale brugere kræver adgang til ressourcer i skyen, men ikke omvendt | Indgående | Udgående |
| Brugere i skyen kræver adgang til lokale ressourcer, der er placeret, men ikke omvendt | Udgående | Indgående |
| Brugere i skyen og lokale kræver adgang til ressourcer i skyen og lokalt | Tovejs, indgående og udgående | Tovejs, indgående og udgående |

## <a name="security-considerations"></a>Overvejelser om sikkerheden

Niveau tillidsforhold fra område er transitive. Hvis du har oprettet et skov niveau tillidsforhold mellem et lokalt område, og et område i skyen, udvides denne tillid til andre nye domæner, der er oprettet i et område. Hvis du bruger domæner til at levere adskillelse af sikkerhedsmæssige årsager, kan du overveje at oprette tillidsforhold på domæneniveauet. Domæne niveau tillidsforhold er ikke-transitive.

I afsnittet *sikkerhedsovervejelser* i [Forlænge Active Directory til Azure]AD-specifikke sikkerhedsovervejelser[extending-ad-to-azure].

## <a name="availability-considerations"></a>Overvejelser i forbindelse med tilgængelighed

Implementere mindst to domænecontrollere for hvert domæne. Dette giver mulighed for automatisk replikering mellem servere. Oprette en tilgængelighed, der er angivet for de VM'er, der fungerer som AD servere håndtering af hvert domæne. Sørg for, at der er mindst to servere i dialogboksen Angiv. 

Overvej også at angive en eller flere servere i hvert domæne som [standby handlinger mastere] [ standby-operations-masters] i tilfælde af forbindelse til en server, der fungerer som en FSMO-rolle mislykkes.

## <a name="scalability-considerations"></a>Overvejelser i forbindelse med skalerbarhed

AD er automatisk SVG ved domæne enheder, der er en del af det samme domæne. Anmodninger om fordeles på tværs af alle enheder i et domæne. Du kan tilføje et andet domænenavn fra domænecontrolleren, og den synkroniseres automatisk med domænet. Konfigurer ikke en separat belastningsjustering at dirigere trafik til enheder inden for domænet. Sørg for, at alle domænecontrollere har tilstrækkelig hukommelse og lager ressourcer til at håndtere databasen i domænet. Give alle domænenavn fra domænecontrolleren FOS samme størrelse.

## <a name="management-and-monitoring-considerations"></a>Administration og overvågning overvejelser i forbindelse med

Finde oplysninger om administration og overvågning overvejelser i forbindelse med, finder du i tilsvarende afsnittene i [Forlænge Active Directory til Azure][extending-ad-to-azure]. 

Yderligere oplysninger finder du [Overvågning Active Directory][monitoring_ad]. Du kan installere værktøjer som [Microsoft Systems Center] [ microsoft_systems_center] på en overvågning server i management undernettet til at udføre disse opgaver. 

## <a name="solution-components"></a>Løsningskomponenter

Et eksempel løsning script, [Implementer ReferenceArchitecture.ps1][solution-script], er tilgængelig, som du kan bruge til at implementere arkitekturen, der følger efter de anbefalinger, der er beskrevet i denne artikel. Dette script anvender Azure ressourcestyring skabeloner. Skabelonerne er tilgængelige som et sæt af grundlæggende dokumentkomponenter, hver især udfører en bestemt handling som oprette en VNet eller konfigurere en NSG. Formålet med scriptet er at dirigerer installation af skabelon.

Skabelonerne, har parametre, sammen med parametrene i separate JSON-filer. Du kan ændre parametrene i disse filer for at konfigurere installationen, så det opfylder dine egne krav. Du behøver ikke at ændre skabelonerne sig selv. Du må ikke ændre skemaer objekter i parameter-filer.

Når du redigerer skabelonerne, du opretter objekter, som følger de navngivningskonventioner, der er beskrevet i [Anbefales navngivningskonvention for perioder for Azure ressourcer][naming-conventions].

Eksempel løsningen opretter og konfigurerer miljøet i skyen, der implementerer et domæne med navnet *treyresearch.com*. Dette miljø består af tilføjer undernet og servere, DMZ, web niveau, business niveau og data access niveau komponenter VPN-gateway og administration af lag. Eksempel-løsning indeholder også en valgfri konfiguration til at oprette en simuleret lokalt miljø med sit eget domæne, *contoso.com*. Løsningen omfatter scripts, som etablere et tillidsforhold på tværs af disse domæner, der gør det muligt for lokale brugere adgang til objekter i *treyresearch.com* domænet i skyen.

I følgende afsnit beskrives elementerne i lokale og skyen konfigurationer.

### <a name="on-premises-components"></a>Lokalt komponenter

>[AZURE.NOTE] Disse komponenter er ikke den primære fokus af arkitekturen er beskrevet i dette dokument, og der leveres blot for at give dig mulighed for at teste skybaseret miljø sikkert, i stedet for at bruge et reelt tal produktionsmiljø. Derfor i dette afsnit kun indeholder en oversigt over de vigtigste parameter filer. Du kan ændre indstillinger som IP-adresser eller størrelsen på FOS, men det anbefales at lade mange af de andre parametre være uændret.

Dette miljø består af et AD-område *contoso.com* domænet. Domænet indeholder to tilføjer servere med IP-adresser 192.168.0.4 og 192.168.0.5. Disse to servere kører også DNS-tjenesten. Den lokale administratorkonto på begge FOS hedder `testuser` med adgangskode `AweS0me@PW`. Desuden konfigurerer konfigurationen en VPN-gateway til at oprette forbindelse til VNet i skyen. Du kan ændre konfigurationen ved at redigere de følgende JSON-filer, der er placeret i [**parametre/Premise**] [ on-premises-folder] mappe:

- ** [virtualNetwork.parameters.json][on-premises-vnet-parameters]**. Denne fil definerer adresseområde netværk til det lokale miljø.

- ** [virtualMachines adds.parameters.json][on-premises-virtualmachines-adds-parameters]**. Denne fil indeholder konfigurationen af de lokale FOS tilføjer værtstjenester. Som standard oprettes to *Standard-DS3-v2* FOS.

- ** [virtualNetworkGateway.parameters.json] [ on-premises-virtualnetworkgateway-parameters] ** og ** [connection.parameters.json][on-premises-connection-parameters]**. Disse filer hold indstillingerne for VPN-forbindelse til Azure VPN-gateway i skyen, herunder den delte nøgle skal bruges til at beskytte trafik gå på tværs af gatewayen.

De resterende filer i mappen indeholder konfigurationsoplysninger bruges til at oprette det lokale domæne (*contoso.com*), ved hjælp af denne infrastruktur. Du bruge dem til at installere tilføjer, konfiguration af DNS, og opretter den lokale skov.

Løsningen også bruger følgende script, med navnet [indgående trust.ps1][incoming-trust], som kører på en computer i det lokale domæne:

```Powershell
# Run the following powershell script in ra-adtrust-onpremise-ad-vm1 (ip 192.168.0.4)

$TrustedDomainName = "contoso.com"
#$TrustedDomainDnsIpAddresses = "192.168.0.4,192.168.0.5"

$TrustingDomainName = "treyresearch.com"
$TrustingDomainDnsIpAddresses = "10.0.4.4,10.0.4.5"

$ForwardIpAddress  = $TrustingDomainDnsIpAddresses
$ForwardDomainName = $TrustingDomainName

$IpAddresses = @()
foreach($address in $ForwardIpAddress.Split(',')){
    $IpAddresses += [IPAddress]$address.Trim()
}
Add-DnsServerConditionalForwarderZone -Name "$ForwardDomainName" -ReplicationScope "Domain" -MasterServers $IpAddresses

netdom trust $TrustingDomainName /d:$TrustedDomainName /add
```

Dette script tilføjer AD DS servere IP-adresser i skyen (se næste afsnit) til den lokale DNS-tjeneste, og derefter bruger [netdom] [ netdom] kommandoen til at oprette et indgående envejs tillidsforhold fra domænet i skyen (*treyresearch.com*).

### <a name="cloud-components"></a>Skyen komponenter

Disse komponenter udgør kernen i denne arkitektur. De installationen infrastruktur for domænet, *treyresearch.com* og oprette tillidsforholdene med lokale *contoso.com* domænet. [**Parametre/azure**] [ azure-folder] mappe indeholder følgende parameter filer til konfiguration af disse komponenter:

- ** [virtualNetwork.parameters.json][vnet-parameters]**. Denne fil definerer strukturen af VNet for FOS og andre komponenter i skyen. Den indeholder indstillinger, som navnet, adresseområde, undernet og adresserne på alle DNS-servere, der er påkrævet. DNS-adresser, der vises i dette eksempel reference IP-adresserne på de lokale DNS-servere, og standard Azure DNS-serveren. Ændre disse adresser, hvis du vil indsætte dine egne DNS-konfiguration, hvis du ikke bruger eksempel lokalt miljø:
    
    ```json
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-adtrust-vnet",
        "resourceGroup": "ra-adtrust-network-rg",
        "addressPrefixes": [
          "10.0.0.0/16"
        ],
        "subnets": [
          {
            "name": "dmz-private-in",
            "addressPrefix": "10.0.0.0/27"
          },
          {
            "name": "dmz-private-out",
            "addressPrefix": "10.0.0.32/27"
          },
          {
            "name": "dmz-public-in",
            "addressPrefix": "10.0.0.64/27"
          },
          {
            "name": "dmz-public-out",
            "addressPrefix": "10.0.0.96/27"
          },
          {
            "name": "mgmt",
            "addressPrefix": "10.0.0.128/25"
          },
          {
            "name": "GatewaySubnet",
            "addressPrefix": "10.0.255.224/27"
          },
          {
            "name": "web",
            "addressPrefix": "10.0.1.0/24"
          },
          {
            "name": "biz",
            "addressPrefix": "10.0.2.0/24"
          },
          {
            "name": "data",
            "addressPrefix": "10.0.3.0/24"
          },
          {
            "name": "adds",
            "addressPrefix": "10.0.4.0/27"
          }
        ],
        "dnsServers": [
          "10.0.4.4",
          "10.0.4.5",
          "168.63.129.16"
        ]
      }
    }
    ```

- ** [virtualMachines adds.parameters.json ] [ virtualmachines-adds-parameters] **. Denne fil konfigurerer de VM'er, der kører ADDERER i skyen. Konfigurationen består af to FOS. Ændre administrator brugernavn og adgangskode i den `virtualMachineSettings` sektion, og du kan eventuelt redigere VM størrelse for at imødekomme behov for domænenavn:

    Finde flere oplysninger i [Forlænge Active Directory til Azure][extending-ad-to-azure].
    
    ```json
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-adtrust-ad",
        "computerNamePrefix": "aad",
        "size": "Standard_DS3_v2",
        "osType": "Windows",
        "adminUsername": "testuser",
        "adminPassword": "AweS0me@PW",
        "osAuthenticationType": "password",
        "nics": [
          {
            "isPublic": "false",
            "subnetName": "adds",
            "privateIPAllocationMethod": "Static",
            "startingIPAddress": "10.0.4.4",
            "enableIPForwarding": false,
            "dnsServers": [
            ],
            "isPrimary": "true"
          }
        ],
        "imageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2012-R2-Datacenter",
          "version": "latest"
        },
        "dataDisks": {
          "count": 1,
          "properties": {
            "diskSizeGB": 127,
            "caching": "None",
            "createOption": "Empty"
          }
        },
        "osDisk": {
          "caching": "ReadWrite"
        },
        "extensions": [
        ],
        "availabilitySet": {
          "useExistingAvailabilitySet": "No",
          "name": "ra-adtrust-as"
        }
      }
    },
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-adtrust-vnet",
        "resourceGroup": "ra-adtrust-network-rg"
      }
    },
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 2,
        "vmCount": 2,
        "vmStartIndex": 1
      }
    }
    ```

- ** [Tilføj-tilføjer-domæne-controller.parameters.json][add-adds-domain-controller-parameters]**. Denne fil indeholder indstillinger til at oprette det *treyresearch.com* domæne, der strækker sig over tilføjer servere. Brugerdefinerede udvidelser, oprette domænet og føje tilføjer servere til den bruges. Medmindre du opretter flere tilføjer servere (i dette tilfælde skal du føje dem til den `vms` matrix), ændre deres navne fra standard eller ønsker at oprette et domæne med et andet navn, du ikke behøver at redigere denne fil.

- ** [virtualNetworkGateway.parameters.json][virtualnetworkgateway-parameters]**. Denne fil indeholder de indstillinger, der bruges til at oprette gatewayen Azure VPN i skyen bruges til at oprette forbindelse til den lokale netværk. Du skal ændre den `sharedKey` værdi i den `connectionsSettings` afsnit for at matche, på den lokale VPN-enhed. Du kan finde flere oplysninger, se [implementere en Hybrid netværksarkitektur med Azure og lokale VPN][hybrid-azure-on-prem-vpn].

- ** [dmz-private.parameters.json] [ dmz-private-parameters] ** og ** [dmz-public.parameters.json ] [ dmz-public-parameters] **. Disse filer konfigurere indgående (offentlig) og udgående (privat) sider af FOS, der udgør DMZ, beskytte servere i skyen. Finde flere oplysninger om disse elementer og deres konfiguration, [implementere en DMZ mellem Azure og internettet][implementing-a-secure-hybrid-network-architecture-with-internet-access].

- ** [loadBalancer web.parameters.json][loadBalancer-web-parameters]**, ** [loadBalancer biz.parameters.json][loadBalancer-biz-parameters]**, og ** [loadBalancer data.parameters.json][loadBalancer-data-parameters]**. Filerne parametre indeholder VM specifikationer for data, business og web access lag, og Konfigurer Indlæs balancere for hvert niveau. Dette er de VM'er, der hoster webapps og databaser og udføre business arbejdsmængder for organisationen. FOS i web-lag føjes til domænet i skyen ved hjælp af de indstillinger, der er angivet i den ** [web-vm-domæne-join.parameters.json] [ web-vm-domain-join-parameters] ** fil.

    Hver fil indeholder to sæt af konfiguration af parametre. Den `virtualMachineSettings` sektion definerer de VM'er, der hoster ADFS-tjeneste i skyen. Som standard opretter scriptet to af disse FOS i det samme sæt tilgængelighed. De følgende fragmenter vise de relevante dele af filen *loadBalancer web.parameters.json* :

    ```json
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-adtrust-web",
        "computerNamePrefix": "web",
        "size": "Standard_DS1_v2",
        "osType": "windows",
        "adminUsername": "testuser",
        "adminPassword": "AweS0me@PW",
        "osAuthenticationType": "password",
        "nics": [
          {
            "isPublic": "false",
            "subnetName": "web",
            "privateIPAllocationMethod": "Dynamic",
            "isPrimary": "true",
            "enableIPForwarding": false,
            "dnsServers": [ ]
          }
        ],
        "imageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2012-R2-Datacenter",
          "version": "latest"
        },
        "dataDisks": {
          "count": 1,
          "properties": {
            "diskSizeGB": 128,
            "caching": "None",
            "createOption": "Empty"
          }
        },
        "osDisk": {
          "caching": "ReadWrite"
        },
        "extensions": [ ],
        "availabilitySet": {
          "useExistingAvailabilitySet": "No",
          "name": "ra-adtrust-web-vm-as"
        }
      }
    },
    ...
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 2,
        "vmCount": 2,
        "vmStartIndex": 1
      }
    }
    ````
    Du kan ændre størrelsen og antallet af FOS i hvert niveau efter dine behov.

    Den `loadBalancerSettings` afsnit indeholder en beskrivelse af justering af belastning for disse FOS. Justering af belastning overfører trafik, der vises på port 80 (HTTP) og port 443 (HTTPS) til et eller flere af FOS. 

    >[AZURE.NOTE] Regel for port 80 bruger en TCP-forbindelse i stedet for HTTP. Dette skyldes, at installationen af IIS på web niveau er konfigureret til at understøtte Windows-godkendelse kun. Anonym godkendelse er deaktiveret. Forsøger at *ping* afbrydes port 80 over en HTTP-forbindelse med en 401 (uautoriseret) fejl, mens ved hjælp af en TCP-forbindelse lige registrerer om port er aktiv:

    ```json
    "loadBalancerSettings": {
      "value": {
        "name": "ra-adtrust-web-lb",
        "frontendIPConfigurations": [
          {
            "name": "ra-adtrust-web-lb-fe",
            "loadBalancerType": "internal",
            "internalLoadBalancerSettings": {
              "privateIPAddress": "10.0.1.254",
              "subnetName": "web"
            }
          }
        ],
        "backendPools": [
          {
            "name": "ra-adtrust-web-lb-bep",
            "nicIndex": 0
          }
        ],
        "loadBalancingRules": [
          {
            "name": "http-rule",
            "frontendPort": 80,
            "backendPort": 80,
            "protocol": "Tcp",
            "backendPoolName": "ra-adtrust-web-lb-bep",
            "frontendIPConfigurationName": "ra-adtrust-web-lb-fe",
            "probeName": "http-probe",
            "enableFloatingIP": false
          },
          {
            "name": "https-rule",
            "frontendPort": 443,
            "backendPort": 443,
            "protocol": "Tcp",
            "backendPoolName": "ra-adtrust-web-lb-bep",
            "frontendIPConfigurationName": "ra-adtrust-web-lb-fe",
            "probeName": "https-probe",
            "enableFloatingIP": false
          }
        ],
        "probes": [
          {
            "name": "http-probe",
            "port": 80,
            "protocol": "Tcp",
            "requestPath": null
          },
          {
            "name": "https-probe",
            "port": 443,
            "protocol": "Tcp",
            "requestPath": null
          }
        ],
        "inboundNatRules": [ ]
      }
    }
    ```

- ** [virtualMachines mgmt.parameters.json][virtualMachines-mgmt-parameters]**. Denne fil indeholder konfigurationen til at gå feltet/administrere FOS. Det er kun muligt at få logon og administratoradgang til FOS i web, business og niveauer af data fra feltet hyperlink. Scriptet opretter en enkelt *Standard_DS1_v2* VM som standard, men du kan redigere denne fil for at oprette større eller eventuelle yderligere FOS, hvis management arbejdsbelastningen der sandsynligvis er betydeligt.

Konfigurationen også bruger [udgående trust.ps1] [ outgoing-trust] script nedenfor for at oprette en envejs udgående tillid til domænet, *contoso.com* :

```powershell
# prerequiste: 
# You need to first run incoming-trust.ps1 in ra-adtrust-onpremise-ad-vm1 (ip 192.168.0.4)
# Then,
# Run the following powershell script in ra-adtrust-ad-vm1 (ip 10.0.4.4)

$TrustedDomainName = "contoso.com"
$TrustedDomainDnsIpAddresses = "192.168.0.4,192.168.0.5"

#$TrustingDomainName = "treyresearch.com"
#$TrustingDomainDnsIpAddresses = "10.0.4.4,10.0.4.5"

$ForwardIpAddress  = $TrustedDomainDnsIpAddresses
$ForwardDomainName = $TrustedDomainName

$IpAddresses = @()
foreach($address in $ForwardIpAddress.Split(',')){
    $IpAddresses += [IPAddress]$address.Trim()
}
Add-DnsServerConditionalForwarderZone -Name "$ForwardDomainName" -ReplicationScope "Domain" -MasterServers $IpAddresses

#netdom trust $TrustingDomainName /d:$TrustedDomainName /add
```

Dette script ligner *indgående trust.ps1* scriptet beskrevet tidligere. Den tilføjer IP-adressen på lokale Active Directory-Domænetjenester servere til den lokale DNS-tjeneste, og derefter bruger [netdom] [ netdom] kommandoen til at oprette udgående tillidsforhold.

## <a name="solution-deployment"></a>Løsningsinstallation

Løsningen antages det, at følgende forudsætninger:

- Du har et eksisterende Azure-abonnement, kan du oprette ressourcegrupper.

- Du har downloadet og installeret den nyeste version af Azure Powershell. Se [her] [ azure-powershell-download] vejledning.

For at køre scriptet installerer, der løsningen:

1. Flytte til en praktisk mappe på din lokale computer, og Opret følgende undermapper:

    - Scripts

    - Scripts/parametre

    - Scripts/parametre/Premise

    - Scripts/parametre/Azure

2. Hente [Implementer ReferenceArchitecture.ps1] [ solution-script] filen til mappen Scripts

3. Hente indholdet af [parametre/Premise] [ on-premises-folder] mappe til mappen Scripts/parametre/Premise:

4. Hente indholdet af [parametre/azure] [ azure-folder] mappe til mappen Scripts/parametre/Azure.

5. Redigere filen Implementer ReferenceArchitecture.ps1 i mappen Scripts og ændre følgende linjer for at angive de ressource, der skal oprettes eller bruges til at indeholde de ressourcer, der er oprettet af scriptet:

    ```powershell
    # Azure Onpremise Deployments
    $onpremiseNetworkResourceGroupName = "ra-adtrust-onpremise-rg"

    # Azure ADDS Deployments
    $azureNetworkResourceGroupName = "ra-adtrust-network-rg"
    $workloadResourceGroupName = "ra-adtrust-workload-rg"
    $securityResourceGroupName = "ra-adtrust-security-rg"
    $addsResourceGroupName = "ra-adtrust-adds-rg"
    ```

6. Rediger parameter filerne i Scripts/parametre/Premise og Scripts/parametre/Azure mapper. Opdater ressource gruppe referencer i disse filer, der svarer til navnene på de tilknyttede variabler i filen Implementer ReferenceArchitecture.ps1 ressourcegrupper. De følgende tabel viser, hvilke parameter filer henviser til hvilke ressourcegruppe. Grupperne *ra-adfs-arbejdsbelastningen-indbyggede rg*, *ra-adfs-sikkerhed-indbyggede rg*, *ra-adfs-tilføjer-indbyggede rg*, *ra-adfs-adfs-indbyggede rg*og *ra-adfs-proxy-indbyggede rg* ressource bruges kun i PowerShell-script og vises ikke i parameter-filer.

  	|Ressourcegruppe|Parameter filer|
  	|--------------|--------------|
  	|RA-adtrust-Premise-indbyggede rg|parameters\onpremise\connection.Parameters.JSON<br /> parameters\onpremise\virtualMachines-Adds.Parameters.JSON<br />parameters\onpremise\virtualNetwork-Adds-DNS.Parameters.JSON<br />parameters\onpremise\virtualNetwork.Parameters.JSON<br />parameters\onpremise\virtualNetworkGateway.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON
  	|RA-adtrust-netværk-indbyggede rg|parameters\onpremise\connection.Parameters.JSON<br />parameters\azure\dmz-private.Parameters.JSON<br />parameters\azure\dmz-public.Parameters.JSON<br />parameters\azure\loadBalancer-Biz.Parameters.JSON<br />parameters\azure\loadBalancer-data.Parameters.JSON<br />parameters\azure\loadBalancer-Web.Parameters.JSON<br />parameters\azure\virtualMachines-Adds.Parameters.JSON<br />parameters\azure\virtualMachines-MGMT.Parameters.JSON<br />parameters\azure\virtualNetwork-Adds-DNS.Parameters.JSON<br />parameters\azure\virtualNetwork.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON (*to forekomster*)

    Desuden konfiguration for lokale og i skyen komponenter, som beskrevet i [Løsningskomponenter] [ solution-components] sektion.

7. Åbne en Azure PowerShell-vinduet, gå til mappen Scripts og køre følgende kommando:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```

    Erstatte `<subscription id>` med dit Azure abonnement-ID.

    For `<location>`, Angiv en Azure region, såsom `eastus` eller `westus`.

    Den `<mode>` parameter kan have en af følgende værdier:

    - `Onpremise`, til at oprette simuleret lokalt miljø.

    - `Infrastructure`, til at oprette VNet infrastruktur og springe feltet i skyen.

    - `CreateVpn`, at opbygge Azure virtuelt netværksgateway og forbinde den til den lokale netværk.

    - `AzureADDS`, hvis du vil oprette de FOS, som tilføjer servere, installere Active Directory til disse FOS og oprette domænet i skyen.

    - `WebTier`, som opretter internettet klasse FOS og justering af belastning.

    - `Prepare`, som udfører alle de foregående opgaver. **Dette er den anbefalede indstilling, hvis du opretter en helt ny installation og du ikke har et eksisterende lokalt infrastruktur.**

    - `Workload`at oprette business og data niveauet FOS og indlæse balancere. Disse FOS er ikke inkluderet som en del af den `Prepare` indstilling.

    >[AZURE.NOTE] Hvis du bruger den `Prepare` indstillingen scriptet tager flere timer for at gennemføre.

8.  Hvis du bruger den eksempel lokale konfiguration:

    1. Oprette forbindelse til boksen gå (*ra-adtrust-mgmt-vm1* i gruppen *ra-adtrust-sikkerhed-indbyggede rg* ressource). Log på som *testuser* med adgangskode *AweS0me@PW*.

    2.  Åbn en RDP-session på den første VM på feltet hyperlink i domænet *Contoso.com (det lokale domæne)* . Denne VM har IP-adresse 192.168.0.4. Brugernavnet er *contoso\testuser* med adgangskode *AweS0me@PW*.

    3. Hente [indgående trust.ps1] [ incoming-trust] scripts og kør den for at oprette den indgående tillid fra *treyresearch.com* domæne.

9. Hvis du bruger din egen lokale infrastruktur:

    1. Hente [indgående trust.ps1] [ incoming-trust] script.

    2. Redigere scriptet og erstatte værdien af den `$TrustedDomainName` variable med navnet på dit eget domæne.

    3. Køre scriptet.

10. Gå-feltet, oprette forbindelse til den første VM i *treyresearch.com* domæne (domæne i skyen). Denne VM har IP-adresse 10.0.4.4. Brugernavnet er *treyresearch\testuser* med adgangskode *AweS0me@PW*.

11. Hente [udgående trust.ps1] [ outgoing-trust] scripts og kør den for at oprette den indgående tillid fra *treyresearch.com* domæne. Hvis du bruger din egen lokale computere og derefter redigere scriptet først. Angiv den `$TrustedDomainName` variable til navnet på dit lokale domæne, og Angiv IP-adresserne AD DS webserverne for dette domæne i den `$TrustedDomainDnsIpAddresses` variabel.

12. På en lokal computer, udføre trinnene beskrevet i artiklen [Bekræft et tillidsforhold] [ verify-a-trust] til at afgøre, om tillidsforholdet er konfigureret korrekt mellem *contoso.com* og *treyresearch.com* domæner. Du kan være nødvendigt at vente et par minutter, efter du har udført de forrige trin, før indstillingen Hav tillid til kan valideres.

De resterende valgfrie trin viser, hvordan til at afgøre, om domænet tilliden fungerer som forventet. Denne fremgangsmåde kræver, at du har adgang til en udvikling computer med Visual Studio er installeret.

1.  Kør følgende kommando for at oprette web niveauet, hvis du ikke har defineret den tidligere fra vinduet Azure PowerShell (ved hjælp af den `Prepare` indstilling):

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> WebTier
    ```

    Denne kommando opretter web niveau og føjer FOS *treyresearch.com* domænet.

2. Ved hjælp af Visual Studio på udviklingscomputeren,, oprette en ASP.NET-webprogram med navnet *TreyResearchWebApp*. Brug af .NET Framework 4.5.2.

3. Vælg skabelonen, *MVC* og ændre godkendelsen til *Windows-godkendelse*. Ikke oprette en App-tjeneste i skyen.

3. Oprette og køre programmet for at teste godkendelsen. Kontrollér, at din aktuelle Windows-brugernavn vises i menulinjen øverst på siden mod højre.

4. Luk Internet Explorer.

5. Højreklik på TreyResearchWebApp projektet i *Solution Explorer* -vinduet, klik på *Publicer*.

6. Klik på *brugerdefineret*i vinduet *Udgiv websted* . Oprette en brugerdefineret profil med navnet *TreyResearchWebApp*.

7. Angive *Publicer metode* til *Filsystem* på siden *forbindelsen* og angive en mappe med navnet *TreyResearchWebApp*, findes i et praktisk sted på computeren udvikling.

8. På siden *Indstillinger* , du Angiv *konfiguration* til *version*.

9. Klik på *Publicer*på siden *Preview* .

10. Oprette forbindelse til hver VM i web lag igen (via boksen gå) og udføre følgende opgaver. IP-adressen på internettet niveau FOS er 10.0.1.4 og 10.0.1.5. Brugernavn for begge FOS er *treyresearch\testuser* med adgangskode *AweS0me@PW*:

    1. Kopiere mappen *TreyResearchWebApp* og dens indhold fra udviklingscomputeren til mappen *C:\inetpub* .

    2. Ved hjælp af Internet Information Services (IIS) Manager-konsollen, gå til *Sites\Default websted* på computeren.

    3. Klik på *Grundlæggende indstillinger*i ruden *Handlinger* , og ændre den fysiske sti til webstedet til *%SystemDrive%\inetpub\TreyResearchWebApp*.

    4. Dobbeltklik på *godkendelse*i visningsruden *Funktioner* . Kontrollér, at *Windows-godkendelse* er aktiveret og *Anonym godkendelse* er deaktiveret.

11. Åbn Internet Explorer fra en lokal maskine, og gå til webstedet på http://10.0.1.254 (dette er adressen på web niveau justering af belastning).

12. Angive legitimationsoplysninger for en bruger i det lokale domæne i dialogboksen *Windows sikkerhed* . Angive et brugernavn, der ikke findes i *treyresearch* domænet. Hvis du bruger simuleret lokalt miljø først oprette en bruger i *contoso* -domæne, og Angiv brugerens legitimationsoplysninger.

13. Når startsiden vises, skal du kontrollere, at det korrekte domæne og brugernavn vises i menulinjen øverst på siden mod højre.

## <a name="next-steps"></a>Næste trin

- Lær de bedste fremgangsmåder til at [udvide dit lokale tilføjer domæne til Azure][adds-extend-domain]

- Lær de bedste fremgangsmåder til [oprettelse af en ADFS-infrastruktur] [ adfs] i Azure.

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[adfs]: ./guidance-identity-adfs.md
[adds-extend-domain]: ./guidance-identity-adds-extend-domain.md
[extending-ad-to-azure]: ./guidance-identity-adds-extend-domain.md
[implementing-aad]: ./guidance-identity-aad.md
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-hybrid-network-architecture-with-vpn]: ./guidance-hybrid-network-vpn.md
[running-VMs-for-an-N-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[best_practices_ad_password_policy]: https://technet.microsoft.com/magazine/ff741764.aspx
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[creating-forest-trusts]: https://technet.microsoft.com/library/cc816810(v=ws.10).aspx
[creating-external-trusts]: https://technet.microsoft.com/library/cc816837(v=ws.10).aspx
[naming-conventions]: ./guidance-naming-conventions.md
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[hybrid-azure-on-prem-vpn]: ./guidance-hybrid-network-vpn.md
[verify-a-trust]: https://technet.microsoft.com/library/cc753821.aspx
[netdom]: https://technet.microsoft.com/library/cc835085.aspx
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/Deploy-ReferenceArchitecture.ps1
[on-premises-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adds-trust/parameters/onpremise
[on-premises-vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualNetwork.parameters.json
[on-premises-virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualMachines-adds.parameters.json
[on-premises-virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualNetworkGateway.parameters.json
[on-premises-connection-parameters]: https://github.com/mspnp/reference-architectures/blob/master/guidance-identity-adds-trust/parameters/onpremise/connection.parameters.json
[incoming-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/extensions/incoming-trust.ps1
[outgoing-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/extensions/outgoing-trust.ps1
[azure-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adds-trust/parameters/azure
[vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualNetwork.parameters.json
[virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualMachines-adds.parameters.json
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/add-adds-domain-controller.parameters.json
[virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualNetwork.parameters.json
[dmz-private-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/dmz-private.parameters.json
[dmz-public-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/dmz-public.parameters.json
[loadBalancer-web-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-web.parameters.json
[loadBalancer-biz-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-biz.parameters.json
[loadBalancer-data-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-data.parameters.json
[virtualMachines-mgmt-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualMachines-mgmt.parameters.json
[web-vm-domain-join-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/web-vm-domain-join.parameters.json
[solution-components]: [#solution_components]
[0]: ./media/guidance-identity-aad-resource-forest/figure1.png "Secure hybrid netværksarkitektur med forskellige AD domæner"