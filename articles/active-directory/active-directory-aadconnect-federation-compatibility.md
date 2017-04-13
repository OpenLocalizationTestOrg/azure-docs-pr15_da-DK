<properties
    pageTitle="Azure AD sammenslutning kompatibilitet listen"
    description="Denne side indeholder ikke-Microsoft identitetsudbydere, der kan bruges til at implementere enkeltlogon."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="billmath"/>

# <a name="azure-ad-federation-compatibility-list"></a>Azure AD sammenslutning kompatibilitet listen
Azure Active Directory indeholder enkelt Log på og forbedret sikkerhed i access til Office 365 og andre Microsoft Online services til hybrid og kun skyen installationer uden en ikke-Microsoft-løsning. Office 365, som de fleste af Microsofts onlinetjenester, er integreret med Azure Active Directory til katalogtjenester, godkendelse og godkendelse. Azure Active Directory indeholder også single sign-on til tusindvis af SaaS programmer og lokale webprogrammer. Se galleriet Azure Active Directory-program til understøttede SaaS programmer.

Dette emne indeholder de organisationer, der har investeret i ikke-Microsoft federation løsninger, vejledning til konfiguration af single sign-on til deres Windows Server Active Directory-brugere med Microsoft Online services ved hjælp af ikke-Microsoft identitetsudbydere på "Azure Active Directory federation kompatibilitet listen" nedenfor. 


![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
[Oxford Computer gruppe](http://oxfordcomputergroup.com/), en tredjepart på vegne af Microsoft, testet disse enkelt sign-on – oplevelser på ved hjælp af ikke-Microsoft identitetsudbydere mod et sæt use cases almindelige med Azure Active Directory.

Kontakt Oxford Computer Group på for at vide om, hvordan du kan få udbyderen tredjepartsidentitetsudbydere, der er angivet her [idp@oxfordcomputergroup.com](mailto:idp@oxfordcomputergroup.com).

>[AZURE.IMPORTANT] Oxford Computer gruppe testede kun sammenslutning for disse enkeltlogon-scenarier. Oxford Computer gruppe ikke udføre en hvilken som helst tests synkroniseringen, to faktor-godkendelse, osv. komponenterne i disse enkeltlogon-scenarier.

>Brug af logon efter alternative ID til UPN er også ikke testet i dette program.



- [Azure Active Directory](#azure-active-directory)
- [Optimal IDM virtuelle identitet Server Federation Services](#optimal-idm-virtual-identity-server-federation-services) 
- [PingFederate 6.11 tilføjes](#pingfederate-611) 
- [PingFederate 7.2](#pingfederate-72) 
- [PingFederate 8.x](#pingfederate-8x)
- [Centrify](#centrify) 
- [IBM Tivoli samlet identitet Manager 6.2.2](#ibm-tivoli-federated-identity-manager-622) 
- [SecureAuth IdP 7.2.0](#secureauth-idp-720) 
- [CA SiteMinder 12,52](#ca-siteminder-1252-sp1-cumulative-release-4) 
- [RadiantOne CFS 3.0](#radiantone-cfs-30) 
- [Okta](#okta) 
- [OneLogin](#onelogin) 
- [NetIQ Access Manager 4.0.1](#netiq-access-manager-401) 
- [BIG-IP med Access politik Manager BIG-IP ver. 11,3 x – 11.6 x](#big-ip-with-access-policy-manager-big-ip-ver-113x-116x) 
- [VMware arbejdsområde Portal version 2.1](#vmware-workspace-portal-version-21) 
- [Log og gå 5.3](#signampgo-53) 
- [IceWall sammenslutning Version 3.0](#icewall-federation-version-30) 
- [CA sikker skyen](#ca-secure-cloud) 
- [Dell en identitet skyen Access Manager v7.1](#dell-one-identity-cloud-access-manager-v71) 
- [AuthAnvil Single Sign-On 4.5](#authavil-single-sign-on-45) 

>[AZURE.IMPORTANT] Da disse produkter fra tredjepart, indeholder Microsoft ikke understøttelse af installation, konfiguration, fejlfinding, bedste fremgangsmåder, osv. problemer og spørgsmål om disse identitetsudbydere. Tage direkte kontakt understøttede tredjeparter til support- og spørgsmål om disse id-udbydere.

>Disse tredjepartsidentitetsudbydere udbydere er testet for kompatibilitet med Microsofts skytjenester ved hjælp af WS-Federation og WS-Trust protokoller kun. Test omfatter ikke ved hjælp af SAML-protokollen.

## <a name="azure-active-directory"></a>Azure Active Directory 
Azure Active Directory kan godkende brugere ved en sammenslutning med dit lokale Active-Directory eller uden en lokal sammenslutningsserver ved hjælp af synkronisering af adgangskoder. 

Følgende er matrixen scenarie support for at få denne sign-on – oplevelse: 


| Klient |Support  |Undtagelser|
| --------- | --------- |--------- |
| Webbaserede klienter som Exchange Web Access og SharePoint Online | Understøttes |Ingen|
| RTF-klientprogrammer som Lync, Office-abonnement, CRM |  Understøttes |Ingen|
| Mail-RTF-klienter som Outlook og ActiveSync |  Understøttes |Ingen|
|Moderne programmer ved hjælp af ADAL såsom Office 2016| Understøttes|Ingen|

Finde flere oplysninger om brug af Azure Active Directory med AD FS i [Active Directory Federation Services (ADFS)](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)

Du kan finde flere oplysninger om brug af Azure Active Directory med synkronisering af adgangskoder [Azure AD-forbindelse](active-directory-aadconnect.md).


## <a name="optimal-idm-virtual-identity-server-federation-services"></a>Optimal IDM virtuelle identitet Server Federation Services 
Optimal IDM virtuelle identitet Server Federation Services kan godkende brugere, der er placeret i kundernes lokale Active mapper.

Følgende er dette scenario understøtter matrix denne enkelt sign-on – oplevelse:


| Klient |Support  |Undtagelser|
| --------- | --------- |--------- |
| Webbaserede klienter som Exchange Web Access og SharePoint Online | Understøttes |Ingen|
| RTF-klientprogrammer som Lync, Office-abonnement, CRM |  Understøttes |Integreret Windows-godkendelse|
| Mail-RTF-klienter som Outlook og ActiveSync |  Understøttes |For flere oplysninger om klientadgang politikker se [at begrænse adgang til Office 365-tjenester baseret på placeringen af klienten.](https://technet.microsoft.com/library/hh526961.aspx)|



## <a name="pingfederate-611"></a>PingFederate 6.11 tilføjes 

PingFederate 6.11 tilføjes implementerer udbredte WS sammenslutning identitet standard for at give en single sign-on og attributten exchange framework.

Følgende er matrixen scenarie support til denne enkelt sign-on – oplevelse:


| Klient |Support  |Undtagelser|
| --------- | --------- |--------- |
| Webbaserede klienter som Exchange Web Access og SharePoint Online | Understøttes |Ingen|
| RTF-klientprogrammer som Lync, Office-abonnement, CRM |  Understøttes |Ingen (tidligere versioner skal opgradere til 6.11 tilføjes|
| Mail-RTF-klienter som Outlook og ActiveSync |  Understøttes |Ingen|

PingFederate vejledning i at konfigurere denne STS at give den enkelt sign-on – oplevelse dine Active Directory-brugere, hente PDF-filen [her.](http://go.microsoft.com/fwlink/?LinkID=266321)

## <a name="pingfederate-72"></a>PingFederate 7.2 
PingFederate 7.2 implementerer udbredte WS sammenslutning/WS-Trust identitet standard for at give en single sign-on og attributten exchange framework.

Følgende er matrixen scenarie support til denne enkelt sign-on – oplevelse:


| Klient |Support  |Undtagelser|
| --------- | --------- |--------- |
| Webbaserede klienter som Exchange Web Access og SharePoint Online | Understøttes |Ingen|
| RTF-klientprogrammer som Lync, Office-abonnement, CRM |  Understøttes |Ingen|
| Mail-RTF-klienter som Outlook og ActiveSync |  Understøttes |Ingen|

Finde PingFederate vejledning i at konfigurere denne STS at levere enkelt enkeltlogon-oplevelsen til Active Directory-brugere, [her.](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)

## <a name="pingfederate-8x"></a>PingFederate 8.x 
PingFederate 8.x implementerer udbredte WS sammenslutning/WS-Trust identitet standard for at give en single sign-on og attributten exchange framework.

Følgende er matrixen scenarie support til denne enkelt sign-on – oplevelse:


| Klient |Support  |Undtagelser|
| --------- | --------- |--------- |
| Webbaserede klienter som Exchange Web Access og SharePoint Online | Understøttes |Ingen|
| RTF-klientprogrammer som Lync, Office-abonnement, CRM |  Understøttes |Ingen|
| Mail-RTF-klienter som Outlook og ActiveSync |  Understøttes |Ingen|

Finde PingFederate vejledning i at konfigurere denne STS at levere enkelt enkeltlogon-oplevelsen til Active Directory-brugere, [her.](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## <a name="centrify"></a>Centrify 
Centrify hjælper med at opnå en samlet enkelt sign-on – oplevelse til Office 365 uden at der kræves af vært for en lokal sammenslutningsserver.

Følgende er matrixen scenarie support til denne enkelt sign-on – oplevelse:


| Klient |Support  |Undtagelser|
| --------- | --------- |--------- |
| Webbaserede klienter som Exchange Web Access og SharePoint Online | Understøttes |Ingen|
| RTF-klientprogrammer som Lync, Office-abonnement, CRM |  Understøttes |Ingen|
| Mail-RTF-klienter som Outlook og ActiveSync |  Understøttes |Klient adgangskontrol understøttes ikke 

Finde flere oplysninger om Centrify [her.](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp)|

## <a name="ibm-tivoli-federated-identity-manager-622"></a>IBM Tivoli samlet identitet Manager 6.2.2 
IBM Tivoli samlet identitet Manager 6.2.2 med IBM sikkerhed Access Manager til Microsoft-programmer 1.4 implementerer udbredte WS sammenslutning/WS-Trust identitet standard for at give en single sign-on og attributten exchange framework.

Følgende er matrixen scenarie support til denne enkelt sign-on – oplevelse: 

| Klient |Support  |Undtagelser|
| --------- | --------- |--------- |
| Webbaserede klienter som Exchange Web Access og SharePoint Online | Understøttes |Ingen|
| RTF-klientprogrammer som Lync, Office-abonnement, CRM |  Understøttes |Ingen|
| Mail-RTF-klienter som Outlook og ActiveSync |  Understøttes |Ingen|

Finde flere oplysninger om IBM Tivoli samlet identitet Manager [IBM sikkerhed Access Manager for Microsoft Applications.](http://www-01.ibm.com/support/docview.wss?uid=swg24029517)

## <a name="secureauth-idp-720"></a>SecureAuth IdP 7.2.0 
SecureAuth IdP 7.2.0 implementerer udbredte WS sammenslutning/WS-Trust identitet standard for at angive et enkelt sign-on – oplevelse og attributten exchange framework.

Følgende er matrixen scenarie support til denne enkelt sign-on – oplevelse: 

| Klient |Support  |Undtagelser|
| --------- | --------- |--------- |
| Webbaserede klienter som Exchange Web Access og SharePoint Online | Understøttes |Ingen|
| RTF-klientprogrammer som Lync, Office-abonnement, CRM |  Understøttes |Ingen|
| Mail-RTF-klienter som Outlook og ActiveSync |  Understøttes |Ingen|

Du kan finde flere oplysninger om SecureAuth, [SecureAuth IdP](http://go.microsoft.com/?linkid=9845293).

## <a name="ca-siteminder-1252-sp1-cumulative-release-4"></a>CA SiteMinder 12,52 SP1 kumulativ udgivelse 4
CA SiteMinder sammenslutning 12,52 implementerer udbredte WS sammenslutning/WS-Trust identitet standard for at give en single sign-on og attributten exchange framework.

Følgende er matrixen scenarie support til denne enkelt sign-on – oplevelse: 

| Klient |Support  |Undtagelser|
| --------- | --------- |--------- |
| Webbaserede klienter som Exchange Web Access og SharePoint Online | Understøttes |Ingen|
| RTF-klientprogrammer som Lync, Office-abonnement, CRM |  Understøttes |Ingen|
| Mail-RTF-klienter som Outlook og ActiveSync |  Understøttes |Ingen|

Finde flere oplysninger om CA SiteMinder [CA SiteMinder sammenslutning.](http://www.ca.com/us/products/ca-single-sign-on.html) 

## <a name="radiantone-cfs-30"></a>RadiantOne CFS 3.0 
RadiantOne skyen sammenslutning Service (CFS) 3.0 implementerer udbredte WS sammenslutning/WS-Trust identitet standard for at give en single sign-on og attributten exchange framework.

Følgende er matrixen scenarie support til denne enkelt sign-on – oplevelse: 

| Klient |Support  |Undtagelser|
| --------- | --------- |--------- |
| Webbaserede klienter som Exchange Web Access og SharePoint Online | Understøttes |Ingen|
| RTF-klientprogrammer som Lync, Office-abonnement, CRM |  Understøttes |Integreret Windows-godkendelse|
| Mail-RTF-klienter som Outlook og ActiveSync |  Understøttes |Ingen|

Finde flere oplysninger om RadiantOne CFS [RadiantOne CFS.](http://www.radiantlogic.com/products/radiantone-cfs/)


## <a name="okta"></a>Okta 
Okta implementerer udbredte WS sammenslutning/WS-Trust identitet standard for at give en single sign-on og attributten exchange framework.

Følgende er matrixen scenarie support til denne enkelt sign-on – oplevelse: 


| Klient |Support  |Undtagelser|
| --------- | --------- |--------- |
| Webbaserede klienter som Exchange Web Access og SharePoint Online | Understøttes |Integreret Windows-godkendelse kræver installation af yderligere webserver og Okta programmet.|
| RTF-klientprogrammer som Lync, Office-abonnement, CRM |  Understøttes |Integreret Windows-godkendelse|
| Mail-RTF-klienter som Outlook og ActiveSync |  Understøttes |Ingen|

Finde flere oplysninger om Okta [Okta.](https://www.okta.com/)
 
## <a name="onelogin"></a>OneLogin 
OneLogin som testet i maj 2014 implementerer udbredte WS sammenslutning/WS-Trust identitet standard for at give en single sign-on og attributten exchange framework.

Følgende er matrixen scenarie support til denne enkelt sign-on – oplevelse: 

| Klient |Support  |Undtagelser|
| --------- | --------- |--------- |
| Webbaserede klienter som Exchange Web Access og SharePoint Online | Understøttes |Integreret Windows-godkendelse|
| RTF-klientprogrammer som Lync, Office-abonnement, CRM |  Understøttes |Integreret Windows-godkendelse|
| Mail-RTF-klienter som Outlook og ActiveSync |  Understøttes |Ingen|

Finde flere oplysninger om OneLogin [OneLogin.](https://www.onelogin.com/)

## <a name="netiq-access-manager-401"></a>NetIQ Access Manager 4.0.1 
NetIQ Access Manager 4.0.1 implementerer udbredte WS sammenslutning/WS-Trust identitet standard for at give en single sign-on og attributten exchange framework.

Følgende er matrixen scenarie support til denne enkelt sign-on – oplevelse:

| Klient |Support  |Undtagelser|
| --------- | --------- |--------- |
| Webbaserede klienter som Exchange Web Access og SharePoint Online | Understøttes |* Kerberos kontrakter understøttes|
| RTF-klientprogrammer som Lync, Office-abonnement, CRM |  Understøttes |Integreret Windows-godkendelse understøttes ikke|
| Mail-RTF-klienter som Outlook og ActiveSync |  Understøttes |Ingen|

* NetIQ understøtte Kerberos-godkendelse via konfiguration af en Kerberos kontrakt.  For at få hjælp med denne konfiguration, skal du kontakte NetIQ eller få vist konfigurationsvejledningen til. Finde flere oplysninger om NetIQ Access Manager [NetIQ Access Manager.](https://www.netiq.com/documentation/netiqaccessmanager4/identityserverhelp/data/b12iqp0m.html)

## <a name="big-ip-with-access-policy-manager-big-ip-ver-113x--116x"></a>BIG-IP med Access politik Manager BIG-IP ver. 11,3 x – 11.6 x 
BIG-IP med Access politik Manager, (APM) ver. BIG-IP 11.3 x – 11.6 x implementerer udbredte SAML identitet standard for at angive et enkelt sign-on – oplevelse og attributten exchange framework.

Følgende er matrixen scenarie support til denne enkelt sign-on – oplevelse: 

| Klient |Support  |Undtagelser|
| --------- | --------- |--------- |
| Webbaserede klienter som Exchange Web Access og SharePoint Online | Understøttes |Ingen|
| RTF-klientprogrammer som Lync, Office-abonnement, CRM |  Ikke understøttet |Ikke understøttet|
| Mail-RTF-klienter som Outlook og ActiveSync |  Understøttes |Ingen|

Finde flere oplysninger om BIG-IP Access politik Manager [BIG-IP Access politik Manager.](https://f5.com/products/modules/access-policy-manager) 

Styring af BIG-IP Access vejledning i at konfigurere denne STS at give den enkelt sign-on – oplevelse din Active Directory-brugere, hente PDF-filen [her.](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf)

## <a name="vmware--workspace-portal-version-21"></a>VMware arbejdsområde Portal version 2.1 
VMware arbejdsområde Portal version 2.1 implementerer udbredte WS sammenslutning/WS-Trust identitet standard for at give en single sign-on og attributten exchange framework.

Følgende er matrixen scenarie support til denne enkelt sign-on – oplevelse:

| Klient |Support  |Undtagelser|
| --------- | --------- |--------- |
| Webbaserede klienter som Exchange Web Access og SharePoint Online | Understøttes |Integreret Windows-godkendelse understøttes ikke|
| RTF-klientprogrammer som Lync, Office-abonnement, CRM | Understøttes |Integreret Windows-godkendelse understøttes ikke|
| Mail-RTF-klienter som Outlook og ActiveSync |  Understøttes |Ingen|

Du kan finde flere oplysninger om VMware arbejdsområde Portal version 2.1 hente PDF-filen [her.](http://pubs.vmware.com/workspace-portal-21/topic/com.vmware.ICbase/PDF/workspace-portal-21-resource.pdf)

## <a name="signgo-53"></a>Log og gå 5.3 
Logge, og gå 5.3 implementerer den udbredte WS sammenslutning/WS-Trust identitet standard til at levere en single sign-on og attributten exchange framework.

Følgende er matrixen scenarie support til denne enkelt sign-on – oplevelse:

| Klient |Support  |Undtagelser|
| --------- | --------- |--------- |
| Webbaserede klienter som Exchange Web Access og SharePoint Online | Understøttes |Kerberos kontrakter understøttes |
| RTF-klientprogrammer som Lync, Office-abonnement, CRM | Understøttes |Ingen|
| Mail-RTF-klienter som Outlook og ActiveSync |  Understøttes |Ingen|


Signer og gå 5.3 understøtter Kerberos-godkendelse via konfiguration af en Kerberos kontrakt.  For at få hjælp med denne konfiguration, skal du kontakte Ilex eller få vist konfigurationsvejledningen til [her.](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf)


## <a name="icewall-federation-version-30"></a>IceWall sammenslutning Version 3.0 
IceWall sammenslutning Version 3.0 implementerer udbredte WS sammenslutning/WS-Trust identitet standard for at give en single sign-on og attributten exchange framework.

Følgende er matrixen scenarie support til denne enkelt sign-on – oplevelse:

| Klient |Support  |Undtagelser|
| --------- | --------- |--------- |
| Webbaserede klienter som Exchange Web Access og SharePoint Online | Understøttes |Integreret Windows-godkendelse understøttes ikke|
| RTF-klientprogrammer som Lync, Office-abonnement, CRM | Understøttes |Integreret Windows-godkendelse understøttes ikke|
| Mail-RTF-klienter som Outlook og ActiveSync |  Understøttes |Ingen|

Finde flere oplysninger om IceWall sammenslutning [her](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/) og [her.](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html)

## <a name="ca-secure-cloud"></a>CA sikker skyen 

CA Secure skyen implementerer udbredte WS sammenslutning/WS-Trust identitet standard for at give en single sign-on og attributten exchange framework.

Følgende er matrixen scenarie support til denne enkelt sign-on – oplevelse:

| Klient |Support  |Undtagelser|
| --------- | --------- |--------- |
| Webbaserede klienter som Exchange Web Access og SharePoint Online | Understøttes |Integreret Windows-godkendelse understøttes ikke|
| RTF-klientprogrammer som Lync, Office-abonnement, CRM | Understøttes |Integreret Windows-godkendelse understøttes ikke|
| Mail-RTF-klienter som Outlook og ActiveSync |  Understøttes |Ingen|

Finde flere oplysninger om CA Secure skyen [CA Secure sky.](http://www.ca.com/us/products/security-as-a-service.aspx)

## <a name="dell-one-identity-cloud-access-manager-v71"></a>Dell en identitet skyen Access Manager v7.1 
Dell én identitet skyen Access Manager implementerer udbredte WS sammenslutning/WS-Trust identitet standard for at give en single sign-on og attributten exchange framework.

Følgende er matrixen scenarie support til denne enkelt sign-on – oplevelse:

| Klient |Support  |Undtagelser|
| --------- | --------- |--------- |
| Webbaserede klienter som Exchange Web Access og SharePoint Online | Understøttes |Ingen|
| RTF-klientprogrammer som Lync, Office-abonnement, CRM |  Understøttes |Ingen|
| Mail-RTF-klienter som Outlook og ActiveSync |  Understøttes |Ingen|

Finde flere oplysninger om Dell én identitet skyen Access Manager [Dell én identitet skyen Access Manager.](http://software.dell.com/products/cloud-access-manager)

 Flere oplysninger om, hvordan du konfigurerer denne STS til at levere enkelt enkeltlogon-oplevelsen til din Office 365-brugere, under [konfigurere Office 365-brugere.](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365) 

## <a name="authanvil-single-sign-on-45"></a>AuthAnvil Single Sign-On 4.5 
AuthAnvil enkelt Log på 4.5 implementerer udbredte WS sammenslutning/WS-Trust identitet standard for at give en single sign-on og attributten exchange framework.

Følgende er matrixen scenarie support til denne enkelt sign-on – oplevelse:

| Klient |Support  |Undtagelser|
| --------- | --------- |--------- |
| Webbaserede klienter som Exchange Web Access og SharePoint Online | Understøttes |Integreret Windows-godkendelse understøttes ikke|
| RTF-klientprogrammer som Lync, Office-abonnement, CRM | Understøttes |Integreret Windows-godkendelse understøttes ikke|
| Mail-RTF-klienter som Outlook og ActiveSync |  Understøttes |Ingen|


Du kan finde flere oplysninger, se [AuthAnvil Single Sign-On.](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-)
