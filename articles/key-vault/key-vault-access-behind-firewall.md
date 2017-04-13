<properties
    pageTitle="Få adgang til tasten samling bag firewallen | Microsoft Azure"
    description="Lær, hvordan du åbner nøgle samling fra et program bag en firewall"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/13/2016"
    ms.author="ambapat"/>

# <a name="accessing-key-vault-behind-firewall"></a>Få adgang til tasten samling bag firewall
### <a name="q-my-key-vault-client-application-needs-to-be-behind-a-firewall-what-portshostsip-addresses-should-i-open-to-enable-access-to-key-vault"></a>Sp: min vigtige samling klientprogrammet skal være bag en firewall, hvilke porte/hosts/IP-adresser skal jeg åbne for at aktivere adgang til vigtige samling?

For at få adgang til en vigtige samling skal klientprogrammet vigtige samling kunne få adgang til flere endepunkter for forskellige funktioner.

- Godkendelse (via Azure Active Directory)
- Administration af nøgle samling (som indeholder oprette/læst/Opdater/slette, og indstillingen access politikker) via Azure ressourcestyring
- Få adgang til og administration af objekter (nøgler og hemmeligheder) gemt i vigtige samling selve, du gennemgår vigtige samling bestemte slutpunktet (fx [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net)).  

Afhængigt af din konfiguration og -miljø er der nogle variationer.   

## <a name="ports"></a>Porte

Al trafik til vigtige samling for alle 3 funktionerne (godkendelse, administration og data plan adgang) handler om HTTPS: Port 443. For liste over tilbagekaldte certifikater, der kan tage nogle gange HTTP (port 80) trafik. Klienter, der understøtter OCSP ikke bør når liste over tilbagekaldte certifikater, men nogle gange kan nå [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).  

## <a name="authentication"></a>Godkendelse

Nøgle samling klientprogrammet skal have adgang til Azure Active Directory slutpunkter til godkendelse. Slutpunktet bruges afhænger af AAD lejer konfigurationen og typen af hovedstolen – bruger hovedstolen, service hovedstolen og typen konto, f.eks. Microsoft-Account eller Org-ID.  

| Hovedstolen Type | Slutpunkt: port |
|----------------|---------------|
| Bruger, der bruger Microsoft-Account<br> (f.eks.user@hotmail.com) | **Global:**<br> login.microsoftonline.com:443<br><br> **Azure Kina:**<br> login.chinacloudapi.CN:443<br><br>**Azure amerikanske:**<br> logon-us.microsoftonline.com:443<br><br>**Azure Tyskland:**<br> login.microsoftonline.de:443<br><br> og <br>login.live.com:443   |
| Brugertjeneste/hovedstolen med Org-ID med AAD (f.eks.user@contoso.com) | **Global:**<br> login.microsoftonline.com:443<br><br> **Azure Kina:**<br> login.chinacloudapi.CN:443<br><br>**Azure amerikanske:**<br> logon-us.microsoftonline.com:443<br><br>**Azure Tyskland:**<br> login.microsoftonline.de:443 |
| Brugertjeneste/hovedstolen ved hjælp af Org -ID + ADFS eller andet medlem af organisationsnetværket slutpunkt (f.eks.user@contoso.com) | Alle ovenstående slutpunkter for Org-ID samt ADFS eller andre organisationsnetværket slutpunkter |

Der findes andre mulige komplekse scenarier. Se [Azure Active Directory Authentication dataflow](/documentation/articles/active-directory-authentication-scenarios/), [Integrering af programmer med Azure Active Directory](/documentation/articles/active-directory-integrating-applications/) og [Active Directory Authentication protokoller](https://msdn.microsoft.com/library/azure/dn151124.aspx) for yderligere oplysninger.  

## <a name="key-vault-management"></a>Administration af vigtige samling

Key samling Management (CRUD og angive access-politik), have klientprogrammet vigtige samling adgang til Azure ressourcestyring slutpunkt.  

| Typen af handling | Slutpunkt: port |
|----------------|---------------|
| Tasten samling kontrolelement plan handlinger<br> via Azure ressourcestyring | **Global:**<br> Management.Azure.com:443<br><br> **Azure Kina:**<br> Management.chinacloudapi.CN:443<br><br> **Azure amerikanske:**<br> Management.usgovcloudapi.NET:443<br><br> **Azure Tyskland:**<br> Management.microsoftazure.de:443 |
| Azure Active Directory Graph API | **Global:**<br> Graph.Windows.NET:443<br><br> **Azure Kina:**<br> Graph.chinacloudapi.CN:443<br><br> **Azure amerikanske:**<br> Graph.Windows.NET:443<br><br> **Azure Tyskland:**<br> Graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Vigtige samling handlinger

For alle vigtige samling objekt (nøgler og hemmeligheder) administration og cryptographic handlinger skal vigtige samling klienten have adgang til slutpunkt vigtige samling. Afhængigt af placeringen af din nøgle samling er det første eller sidste ark DNS-suffiks anderledes. Slutpunkt nøgle samling, er formatet: < samling-navn >. < område-specifikke-dns-suffiks > som beskrevet i tabellen nedenfor.  

| Typen af handling | Slutpunkt: port |
|----------------|---------------|
| Tasten samling handlinger som cryptographic handlinger på taster oprettet/læst/Opdater/slette nøgler og hemmeligheder, angive/få mærker og andre attributter på vigtige samling objekter (taster/hemmeligheder)     | **Global:**<br> &lt;samling af legitimationsoplysninger navn&gt;. vault.azure.net:443<br><br> **Azure Kina:**<br> &lt;samling af legitimationsoplysninger navn&gt;. vault.azure.cn:443<br><br> **Azure amerikanske:**<br> &lt;samling af legitimationsoplysninger navn&gt;. vault.usgovcloudapi.net:443<br><br> **Azure Tyskland:**<br> &lt;samling af legitimationsoplysninger navn&gt;. vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>IP-adresseområder

Tasten samling tjenesten anvender også andre Azure ressourcer som PaaS infrastruktur, det er derfor ikke muligt at angive et bestemt område af IP-adresser, du vil tildele vigtige samling service slutpunkter til enhver tid. Men hvis din firewall kun understøtter IP-adresse områder derefter skal du henvise til [Microsoft Azure Datacenter IP-intervaller](https://www.microsoft.com/download/details.aspx?id=41653) dokumentet.   For godkendelse og -identitet (Azure Active Directory) kunne dit program oprette forbindelse til de slutpunkter, der er beskrevet i [godkendelse og identitet adresser](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="next-steps"></a>Næste trin

- Hvis du har spørgsmål om nøgle samling, gå til [Azure nøgle samling fora](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)
