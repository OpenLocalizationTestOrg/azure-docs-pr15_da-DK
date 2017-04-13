<properties
    pageTitle="Azure AD Connect: Porte | Microsoft Azure"
    description="Denne side indeholder en teknisk vejledning til side til porte, der er nødvendige for at være åben for Azure AD-forbindelse"
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
    ms.date="08/25/2016"
    ms.author="billmath"/>

# <a name="hybrid-identity-required-ports-and-protocols"></a>Hybrid identitet påkrævet porte og protokoller
Følgende dokument er en teknisk vejledning til at give oplysninger om de nødvendige porte og protokoller, der kræves til implementering af en hybrid identitet løsning. Brug nedenstående illustration og referere til den tilsvarende tabel.

![Hvad er Azure AD-forbindelse](./media/active-directory-aadconnect-ports/required1.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabel 1 - Azure AD forbinde og lokale AD
Denne tabel beskrives de porte og protokoller, der kræves af kommunikation mellem Azure AD-forbindelse serveren og lokale AD.

Protokol | Porte | Beskrivelse
--------- | --------- |---------
DNS|53 (TCP/UDP)| DNS-opslag på området, der destination.
Kerberos|88 (TCP/UDP)| Kerberos-godkendelse til AD skoven.
MS-RPC |135 (TCP/UDP)| Bruges under den indledende konfiguration af guiden Azure AD-forbindelse, når der oprettes en binding til AD skoven.
LDAP|389 (TCP/UDP)| Bruges til at importere data fra AD. Data er krypteret med Kerberos log & accepteres.
LDAP/SSL|636 (TCP/UDP)| Bruges til at importere data fra AD. Dataoverførslen er signeret og krypteret. Bruges kun, hvis du bruger SSL.
RPC |49152 - 65535 (tilfældig høj RPC Port)(TCP/UDP)| Bruges under den indledende konfiguration af Azure AD-forbindelse, når den binder til AD områder. Se [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017)og [KB224196](https://support.microsoft.com/kb/224196) kan finde flere oplysninger.

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabel 2 - Azure AD Tilslut og Azure AD
I denne tabel beskrives de porte og protokoller, der kræves til kommunikationen mellem den Azure AD-forbindelse server og Azure AD.

Protokol |Porte |Beskrivelse
--------- | --------- |---------
HTTP|80 (TCP/UDP)| Bruges til at hente lister over tilbagekaldte certifikater (lister over tilbagekaldte) til at kontrollere SSL-certifikater.
HTTPS|443(TCP/UDP)| Bruges til at synkronisere med Azure AD.

En liste over URL-adresser og IP-adresser, skal du åbne i din firewall, se [Office 365 URL-adresser og IP-adresseområder](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-federation-serverswap"></a>Tabel 3 - Azure AD-forbindelse og sammenslutning servere/WAP
I denne tabel beskrives de porte og protokoller, der kræves til kommunikationen mellem den Azure AD-forbindelse server og sammenslutning/WAP-servere.  

Protokol |Porte |Beskrivelse
--------- | --------- |---------
HTTP|80 (TCP/UDP)| Bruges til at hente lister over tilbagekaldte certifikater (lister over tilbagekaldte) til at kontrollere SSL-certifikater.
HTTPS|443(TCP/UDP)| Bruges til at synkronisere med Azure AD.
WinRM|5985| WinRM-lytteren

## <a name="table-4---wap-and-federation-servers"></a>Tabel 4 – WAP og Sammenslutningsservere
I denne tabel beskrives de porte og protokoller, der kræves til kommunikationen mellem sammenslutningsservere og WAP-servere.

Protokol |Porte |Beskrivelse
--------- | --------- |---------
HTTPS|443(TCP/UDP)| Bruges til godkendelse.

## <a name="table-5---wap-and-users"></a>Tabel 5 - WAP og brugere
I denne tabel beskrives de porte og protokoller, der kræves af kommunikation mellem brugerne og WAP-servere.

Protokol |Porte |Beskrivelse
--------- | --------- |--------- |
HTTPS|443(TCP/UDP)| Bruges til enhed godkendelse.
TCP|49443 (TCP)| Bruges til certifikatgodkendelse.

## <a name="table-6a--6b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabel 6a & 6b - Azure AD forbinde Health agent for (AD FS/Sync) og Azure AD
I følgende tabel beskrives de slutpunkter, porte og protokoller, der kræves af kommunikation mellem Azure AD forbinde sundhed supportmedarbejdere og Azure AD

### <a name="table-6a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabel 6a - porte og protokoller for Azure AD forbinde Health agent for (AD FS/Sync) og Azure AD
I denne tabel beskrives de følgende udgående porte og protokoller, der kræves til kommunikationen mellem den Azure AD forbinde sundhed supportmedarbejdere og Azure AD.  

Protokol |Porte  |Beskrivelse
--------- | --------- |--------- |
HTTPS|443(TCP/UDP)| Udgående
Azure Service Bus|5671 (TCP/UDP)| Udgående

### <a name="6b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>6B - slutpunkter til Azure AD forbinde Health agent for (AD FS/Sync) og Azure AD
En liste over slutpunkter, i afsnittet [krav til Azure AD forbinde sundhed agent](active-directory-aadconnect-health-agent-install.md#requirements).
