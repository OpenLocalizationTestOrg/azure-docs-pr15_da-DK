<properties
   pageTitle="Listen over dit program i galleriet Azure Active Directory-program"
   description="Hvordan til at få vist et program, der understøtter enkeltlogon i galleriet Azure Active Directory | Microsoft Azure"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>


# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Listen over dit program i galleriet Azure Active Directory-program

Du kan få vist et program, der understøtter single sign-on – med Azure Active Directory i [Azure AD-galleriet](https://azure.microsoft.com/marketplace/active-directory/all/), skal programmet først at implementere en af følgende tilstande integration:

* **Oprette forbindelse OpenID** - direkte integration med Azure AD med OpenID Opret forbindelse til godkendelse og Azure AD samtykke API til konfiguration. Hvis du fra en integration og dit program ikke understøtter SAML, er dette anbefalet tilstanden.

* **SAML** – dit program allerede har mulighed for at konfigurere fra tredjepart identitetsudbydere ved hjælp af SAML-protokollen.

Listen over krav til hver enkelt tilstand er under.

##<a name="openid-connect-integration"></a>OpenID forbinde Integration

Integrere dit program med Azure AD, følge [udvikler instruktioner](active-directory-authentication-scenarios.md). Derefter fuldføre nedenstående spørgsmål og sende til waadpartners@microsoft.com.

* Angive legitimationsoplysninger for en test lejer eller konto med dit program, der kan bruges til at teste integration af Azure AD-teamet.  

* Angive instruktioner om, hvordan Azure AD-teamet kan logge på og oprette forbindelse mellem en forekomst af Azure AD i dit program ved hjælp af den [Azure AD samtykke framework](active-directory-integrating-applications.md#overview-of-the-consent-framework). 

* Viser eventuelle yderligere instruktioner, der kræves til Azure AD-teamet til at teste single sign-on – med dit program. 

* Angiv de oplysninger nedenfor:

> Firmanavn:
> 
> Virksomhed-websted:
> 
> Programnavn:
> 
> Beskrivelse af programmet (grænsen på 256 tegn):
> 
> Programmet websted (informativ):
> 
> Programmet tekniske Support-webstedet eller kontaktoplysninger:
> 
> Klient-ID'ET for programmet, som vist i programmet oplysninger på https://manage.windowsazure.com:
> 
> Tilmelding til en URL-adressen webprogrammet hvor kunder få for at tilmelde sig og/eller købe programmet:
> 
> Vælg op til tre kategorier for dit program, der vises under (for tilgængelige kategorier se Azure Active Directory Marketplace):
> 
> Vedhæfte programmet lille ikon (PNG-fil, 45px ved 45px, dækkende baggrundsfarve):
> 
> Vedhæfte store programikonet (PNG-fil, 215px ved 215px, dækkende baggrundsfarve):
> 
> Vedhæfte programmet Logo (PNG-fil, 150px ved 122px, gennemsigtig baggrundsfarve):

##<a name="saml-integration"></a>SAML Integration

En app, der understøtter SAML 2.0 kan integreres direkte med en Azure AD-lejer ved hjælp af [disse instruktioner for at tilføje et brugerdefineret program](active-directory-saas-custom-apps.md). Når du har kontrolleret, at dit programintegration fungerer med Azure AD, sende følgende oplysninger for at <waadpartners@microsoft.com>.

* Angive legitimationsoplysninger for en test lejer eller konto med dit program, der kan bruges til at teste integration af Azure AD-teamet.  

* Indeholder de SAML Sign-On URL-adresse, udsteder URL-adresse (enheds-ID) og svar URL-adresse (program consumer service) værdier for dit program, som beskrevet [her](active-directory-saas-custom-apps.md). Hvis du typisk angiver disse værdier som en del af en SAML metadata-fil, derefter skal du sende, samt.

* Indtast en kort beskrivelse af, hvordan du konfigurerer Azure AD som identitetsudbyder i dit program ved hjælp af SAML 2.0. Hvis dit program understøtter konfiguration af Azure AD som identitetsudbyder via en selvbetjening administrative portal, derefter skal du kontrollere de legitimationsoplysninger, der er angivet ovenfor, omfatter muligheden for at konfigurere dette.

* Angiv de oplysninger nedenfor:

> Firmanavn:
> 
> Virksomhed-websted:
> 
> Programnavn:
> 
> Beskrivelse af programmet (grænsen på 256 tegn):
> 
> Programmet websted (informativ):
> 
> Programmet tekniske Support-webstedet eller kontaktoplysninger:
> 
> Tilmelding til en URL-adressen webprogrammet hvor kunder få for at tilmelde sig og/eller købe programmet:
> 
> Vælg op til tre kategorier for dit program, der vises under (for tilgængelige kategorier se [Azure Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/)):)
> 
> Vedhæfte programmet lille ikon (PNG-fil, 45px ved 45px, dækkende baggrundsfarve):
> 
> Vedhæfte store programikonet (PNG-fil, 215px ved 215px, dækkende baggrundsfarve):
> 
> Vedhæfte programmet Logo (PNG-fil, 150px ved 122px, gennemsigtig baggrundsfarve):
