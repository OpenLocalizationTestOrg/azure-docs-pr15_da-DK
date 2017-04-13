
<properties 
    pageTitle="Azure AD + Active Directory krav til Azure RemoteApp | Microsoft Azure" 
    description="Lær, hvordan du konfigurerer Active Directory til at arbejde med Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="azure-ad--active-directory-requirements-for-azure-remoteapp"></a>Azure AD + Active Directory krav til Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.


Til Azure RemoteApp hybrid af websteder eller for en skyen af websteder, du vil samle ved hjælp af AD-forbindelse, skal du gøre følgende.

### <a name="connect-azure-ad-and-active-directory"></a>Oprette forbindelse Azure AD og Active Directory

Hvis du vil forbinde din Azure AD-lejer og lokale Active Directory-miljøer, skal du bruge AD-forbindelse. Det tager du kun [4 Klik](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) for at oprette forbindelse til de to mapper.

Note - katalogsynkronisering er påkrævet for hybride af websteder.

### <a name="make-sure-your-domaincom-match"></a>Sørg for, at din "@domain.com" svarer til
Før du går i gang skal du sørge for, at UPN til din lokale skov stemmer overens med suffikset i dit Azure AD-domæne. 

Når du konfigurerer det domæ UPN-suffiks i Azure AD, alle brugere, der logger ind i Azure RemoteApp log på som “user@ <the suffix you set up>". Sørg for, at brugere kan også logge på med den samme user@suffix i det lokale domæne. I visse tilfælde kan du konfigurere et domænenavn i Azure AD og hvis du angiver et andet domænesuffiks for bruger på-prem. I dette tilfælde skal kan dine brugere ikke oprette forbindelse til en hvilken som helst medlem af et domæne computere eller ressourcer via Azure RemoteApp.

Hvis du har konfigureret dit domæne i UPN-suffiks i AAD som contoso.com, men nogle brugere på lokale/AD er konfigureret til at logge på med eksempelvis @contoso.uk, og derefter disse brugere ikke vil kunne logge korrekt til samlingen ARA. Brugere UPN i AAD og AD skal være den samme til logon til at være muligt"

### <a name="create-objects-for-azure-remoteapp"></a>Oprette objekter til Azure RemoteApp
Du skal også oprette de følgende lokalt Active Directory-objekter:

- En tjenestekonto til at give adgang til ressourcer i domænet til RemoteApp-programmer ved at sammenkæde RDSH slutpunkter til det lokale domæne.
- En organisatoriske enhed (OU) til at indeholde RemoteApp maskine objekter. Brug af OU er anbefalede (men ikke påkrævet) til at isolere de konti og politikker, du vil bruge med RemoteApp.

Du skal begge af disse objekter, når du opretter din RemoteApp af websteder, så sørg for at udføre disse trin først.