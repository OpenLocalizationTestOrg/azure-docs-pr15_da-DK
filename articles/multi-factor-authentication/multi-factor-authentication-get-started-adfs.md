<properties
    pageTitle="Azure MFA og AD FS | Microsoft Azure"
    description="Dette er den Azure Multi-Factor authentication side, der beskriver, hvordan du kommer i gang med Azure MFA og AD FS."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na" ms.topic="get-started-article"
    ms.date="10/17/2016"
    ms.author="kgremban"/>

# <a name="getting-started-with-azure-multi-factor-authentication-and-active-directory-federation-services"></a>Introduktion til Azure Multi-Factor Authentication og Active Directory Federation Services



<center>![Skyen](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Hvis din organisation er medlem af organisationsnetværket din lokale Active Directory med Azure Active Directory ved hjælp af AD FS, er der to muligheder for ved hjælp af Azure Multi-Factor Authentication.

- Secure skyen ressourcer ved hjælp af Azure Multi-Factor Authentication eller Active Directory Federation Services
- Secure skyen og lokale ressourcer ved hjælp af Azure Multi-Factor Authentication Server

Den følgende tabel opsummerer bekræftelse oplevelse mellem sikring af ressourcer med Azure Multi-Factor Authentication og AD FS

|Bekræftelse oplevelse - Gennemse-baserede Apps|Bekræftelse oplevelse - ikke-Browser-baseret Apps
:------------- | :------------- | :------------- |
Sikring af Azure AD ressourcer ved hjælp af Azure Multi-Factor Authentication |<li>Det første bekræftelse trin udføres ved hjælp af AD FS lokalt.</li> <li>Det andet trin er en telefonbaseret metode, der foretages ved hjælp af godkendelse i skyen.</li>|Slutbrugere kan bruge appadgangskoder til at logge på.
Sikring af Azure AD ressourcer ved hjælp af Active Directory Federation Services |<li>Det første bekræftelse trin udføres ved hjælp af AD FS lokalt.</li><li>Det andet trin er udført lokalt ved honoring kravet.</li>|Slutbrugere kan bruge appadgangskoder til at logge på.

Advarsler med appadgangskoder for organisationsnetværket brugere:

- Appadgangskoder er godkendt ved hjælp af skyen godkendelse, så de omgå sammenslutning. Sammenslutning bruges kun aktivt, når du konfigurerer en appadgangskode.
- Lokalt klient adgangskontrol indstillinger ikke er accepteret af appadgangskoder.
- Du mister lokale godkendelse logføring egenskab for appadgangskoder.
- Konto Deaktiver/sletningen kan tage op til tre timer for katalogsynkronisering, forsinke Deaktiver/sletning af appadgangskoder i skyen identitet.

## <a name="next-steps"></a>Næste trin

Du kan finde oplysninger om, hvordan du konfigurerer Azure Multi-Factor Authentication eller Azure Multi-Factor Authentication Server med AD FS i følgende artikler:

- [Secure skyen ressourcer ved hjælp af Azure Multi-Factor Authentication og AD FS](multi-factor-authentication-get-started-adfs-cloud.md)
- [Secure skyen og lokale ressourcer ved hjælp af Azure Multi-Factor Authentication Server med Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md)
- [Secure skyen og lokale ressourcer ved hjælp af Azure Multi-Factor Authentication Server med AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)
