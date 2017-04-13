<properties
    pageTitle="Avancerede scenarier med Azure Multi-Factor Authentication og 3 fest VPN"
    description="Denne side indeholder oplysninger om konfiguration af trinvise konfiguration til Azure MFA med 3 part prodcuts."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban" 
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-3rd-party-vpn"></a>Avancerede scenarier med Azure Multi-Factor Authentication og 3 fest VPN
Azure Multi-Factor Authentication kan bruges til at forbinde problemfrit sammen med en række 3 part VPN-løsninger.  Dette omfatter Cisco® ASA VPN maskinen, Citrix NetScaler SSL VPN maskinen og Juniper Networks sikker adgang/Pulse Secure forbinde Secure SSL VPN-maskinen.

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Cisco ASA VPN maskinen og Azure Multi-Factor Authentication
Azure Multi-Factor Authentication integrerer problemfrit med Cisco® ASA VPN anvendelsen til at levere ekstra sikkerhed til Cisco AnyConnect® VPN-logon og adgang til portalen.  Dette kan gøres ved hjælp af enten LDAP- eller RADIUS-protokollen.  Vælg en af følgende fremgangsmåder for at hente de detaljerede trinvise konfiguration hjælpelinjer.

Konfigurationsvejledning  | Beskrivelse
------------- | ------------- |
[Cisco ASA med Anyconnect VPN og Azure MFA-konfiguration for LDAP-](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integrere problemfrit Cisco ASA VPN anvendelsen med Azure MFA ved hjælp af LDAP|
[Cisco ASA med Anyconnect VPN og Azure MFA-konfiguration for RADIUS](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integrere problemfrit Cisco ASA VPN anvendelsen med Azure MFA ved hjælp af RADIUS

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Citrix NetScaler SSL VPN og Azure Multi-Factor Authentication
Azure Multi-Factor Authentication integrerer problemfrit med Citrix NetScaler SSL VPN anvendelsen til at levere ekstra sikkerhed til Citrix NetScaler SSL VPN-logon og adgang til portalen.  Dette kan gøres ved hjælp af enten LDAP- eller RADIUS-protokollen.  Vælg en af følgende fremgangsmåder for at hente de detaljerede trinvise konfiguration hjælpelinjer.

Konfigurationsvejledning  | Beskrivelse
------------- | ------------- |
[Konfiguration af Citrix NetScaler SSL VPN og Azure MFA til LDAP-](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrere problemfrit din Citrix NetScaler SSL VPN med Azure MFA anvendelse ved hjælp af LDAP|
[Citrix NetScaler SSL VPN og Azure MFA-konfiguration for RADIUS](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integrere problemfrit Citrix NetScaler SSL VPN anvendelsen med Azure MFA ved hjælp af RADIUS

##<a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Juniper/Pulse Secure SSL VPN maskinen og Azure Multi-Factor Authentication
Azure Multi-Factor Authentication integrerer problemfrit med Juniper/Pulse Secure SSL VPN anvendelsen til at levere ekstra sikkerhed til Juniper/Pulse Secure SSL VPN-logon og adgang til portalen.  Dette kan gøres ved hjælp af enten LDAP- eller RADIUS-protokollen.  Vælg en af følgende fremgangsmåder for at hente de detaljerede trinvise konfiguration hjælpelinjer.

Konfigurationsvejledning  | Beskrivelse
------------- | ------------- |
[Konfiguration af juniper/Pulse sikker SSL VPN- og Azure MFA til LDAP-](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx)| Integrere problemfrit din Juniper/Pulse Secure SSL-VPN med Azure MFA anvendelse ved hjælp af LDAP|
[Juniper/Pulse sikker SSL VPN- og Azure MFA-konfiguration for RADIUS](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integrere problemfrit Juniper/Pulse Secure SSL VPN anvendelsen med Azure MFA ved hjælp af RADIUS
