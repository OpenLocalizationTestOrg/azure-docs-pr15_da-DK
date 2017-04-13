<properties
    pageTitle="Azure MFA skyen eller-server | Microsoft Azure"
    description="Vælg den Multi-Factor authentication secutiry løsning, der er bedst til dig ved at spørge hvilke am i forsøg på at sikre, og hvor er Mine brugere, der er placeret.  Vælg derefter skyen, MFA Server eller AD FS."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>

#<a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>Vælge Azure Multi-Factor Authentication løsningen for dig

Fordi der er flere typer af Azure Multi-Factor Authentication (MFA), skal vi svar på nogle spørgsmål til at finde ud af, hvilken version er stort, at du bruger.  Disse spørgsmål er:

-   [Hvad jeg forsøg på at sikre](#what-am-i-trying-to-secure)
-   [Hvor er brugerne placeret](#where-are-the-users-located)
- [Hvilke funktioner har jeg brug for?](#what-featured-do-i-need)

De følgende afsnit indeholder vejledning ved fastlæggelse af hver af disse svar.

## <a name="what-am-i-trying-to-secure"></a>Hvad jeg forsøg på at sikre?

For at bestemme den korrekte to bekræftelse løsning skal skal først vi besvare spørgsmålet om, hvad vil du forsøg på at sikre med en anden metode til godkendelse.  Er det et program, der er i Azure?  Eller et fjernadgang system?  Ved at bestemme, hvad vi forsøg på at sikre, kan vi besvare spørgsmålet om, hvor Multi-Factor Authentication skal være aktiveret.  


Hvad du forsøg på at sikre| Multi-Factor Authentication i skyen|Multi-Factor Authentication Server
------------- | :-------------: | :-------------: |
Oprindeligt Microsoft apps|● |● |
SaaS apps i galleriet app|● |● |
IIS-programmer, der er publiceret via Azure AD App-Proxy|● |● |
IIS-programmer, der ikke er udgivet via Azure AD App-Proxy | |● |
Fjernadgang som VPN, RDG| |● |



## <a name="where-are-the-users-located"></a>Hvor er brugerne placeret

Dernæst skal kigge på hvor vores brugere er placeret med til at bestemme den korrekte løsning at bruge om i skyen eller ved hjælp af MFA Server lokalt.



Brugerplacering| Multi-Factor Authentication i skyen|Multi-Factor Authentication Server
------------- | :-------------: | :-------------: |
Azure Active Directory|● | |
Azure AD og lokale AD bruger sammenslutning med AD FS|● |● |
Azure AD og lokale AD ved hjælp af DirSync, Azure Active Directory-synkronisering, Azure AD-forbindelse - uden synkronisering af adgangskoder|● |● |
Azure AD og lokale AD ved hjælp af DirSync, Azure Active Directory-synkronisering, Azure AD-forbindelse - med synkronisering af adgangskoder|● | |
Lokale Active Directory| |● |

## <a name="what-features-do-i-need"></a>Hvilke funktioner har jeg brug for?

Følgende tabel indeholder en sammenligning af de funktioner, der er tilgængelige med Multi-Factor Authentication i skyen og med Multi-Factor Authentication-serveren.

 | Multi-Factor Authentication i skyen | Multi-Factor Authentication Server
------------- | :-------------: | :-------------: |
Mobilapp meddelelse som en anden faktor | ● | ● |
Mobilapp verifikationskoden som en anden faktor | ● | ●
Telefonopkald som anden faktor | ● | ●
Envejs SMS som anden faktor | ● | ●
Tovejs-SMS som anden faktor |  | ●
Hardwaretokens som anden faktor |  | ●
Appadgangskoder for klienter, som ikke understøtter MFA | ● |  
Administrator kontrol over godkendelsesmetoder | ● | ●
FASTGØR tilstand |  | ●
Svindel besked | ● | ●
MFA-rapporter | ● | ●
Enkeltstående Spring |  | ●
Brugerdefineret hilsener for telefonopkald | ● | ●
Brugerdefinerbare opkalds-ID for telefonopkald | ● | ●
Der er tillid til IP'er | ● | ●
Husk MFA til der er tillid til enheder  | ● |  
Betinget adgang | ● | ●
Cache |  | ●

Nu hvor vi har besluttet dig om at bruge skyen Multi-Factor authentication eller MFA Server lokalt, vi kan komme i gang at konfigurere og bruge Azure Multi-Factor Authentication. **Vælg ikonet, der repræsenterer scenariet!**

<center>




[![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</center>
