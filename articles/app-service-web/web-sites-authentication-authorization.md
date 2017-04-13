<properties 
    pageTitle="Godkende med lokale Active Directory i din Azure app | Microsoft Azure" 
    description="Få mere at vide om de forskellige indstillinger for line of business apps i Azure App Service for at godkende med lokale Active Directory" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="web" 
    ms.date="08/31/2016" 
    ms.author="cephalin"/>

# <a name="authenticate-with-on-premises-active-directory-in-your-azure-app"></a>Godkende med lokale Active Directory i din Azure-app #

I denne artikel beskrives, hvordan du vil godkende med lokale Active Directory (AD) i [Azure App Service](../app-service/app-service-value-prop-what-is.md). En Azure-app er placeret i skyen, men der er måder til at godkende lokalt AD brugere sikkert. 

## <a name="authenticate-through-azure-active-directory"></a>Godkende via Azure Active Directory
En Azure Active Directory-lejer kan være directory synkroniseret med et lokalt AD. Denne metode kan AD brugere adgang til din App fra internettet og godkende ved hjælp af deres lokale legitimationsoplysninger. Desuden indeholder Azure App Service en [løsning Aktivér nøgle for denne metode](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). Med nogle få klik på en knap, kan du aktivere godkendelse med en synkroniseret mappe lejer for din Azure-app. Denne metode har følgende fordele:

-   Kræver ikke en hvilken som helst godkendelse kode i din app. Lade App Service godkendelse for dig at gøre og bruge din tid på at levere funktionalitet i din app.
-   [Azure AD Graph API](http://msdn.microsoft.com/library/azure/hh974476.aspx) giver adgang til directory data fra Azure-app.
-   Leverer SSO til [alle programmer, der understøttes af Azure Active Directory](/marketplace/active-directory/), herunder Office 365, Dynamics CRM Online, Microsoft Intune og tusindvis af ikke-Microsoft cloud-programmer. 
-   Azure Active Directory understøtter rollebaseret adgangskontrol. Du kan bruge [Authorize(Roles="X")] mønster med minimale ændringer i din kode.

For at se, hvordan du kan skrive en Azure line of business-app, der godkender med Azure Active Directory skal du se [oprette en Azure line of business-app med Azure Active Directory-godkendelse](web-sites-dotnet-lob-application-azure-ad.md).

## <a name="authenticate-through-an-on-premises-sts"></a>Godkende via en lokal STS
Hvis du har en lokal secure token service (STS) som Active Directory Federation Services (AD FS), kan du bruge, for at samle godkendelse for din Azure-app. Denne metode egner sig bedst, når firmapolitik forhindrer AD data i at blive gemt i Azure. Bemærk imidlertid følgende:

-   STS topologi skal være installeret lokalt, med omkostninger og styring omkostninger.
-   Kun AD FS-administratorer kan konfigurere [afhængige part tillidsforhold og Kræv regler](http://technet.microsoft.com/library/dd807108.aspx), som kan begrænse den udvikler indstillinger. På den anden side er det muligt at administrere og tilpasse [krav](http://technet.microsoft.com/library/ee913571.aspx) på grundlag-program.
-   Adgang til en lokal installation AD data kræver en separat løsning gennem virksomhedens firewall.

For at se, hvordan du kan skrive en Azure line of business-app, der godkender med en lokal STS skal du se [oprette en Azure line of business-app med AD FS-godkendelse](web-sites-dotnet-lob-application-adfs.md).
 
