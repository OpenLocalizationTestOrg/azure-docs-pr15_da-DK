<properties 
    pageTitle="Hvilken type af websteder har du brug for til Azure RemoteApp? | Microsoft Azure" 
    description="Få mere at vide om typerne af websteder, der er tilgængelige med Azure RemoteApp." 
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



# <a name="what-kind-of-collection-do-you-need-for-azure-remoteapp"></a>Hvilken type af websteder har du brug for til Azure RemoteApp?

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Azure RemoteApp kan du dele apps og ressourcer med brugere på en hvilken som helst enhed. Vi gør dette ved oprettelse af websteder for at holde de tilhørende apps og ressourcer, og del af disse websteder med brugere. Der er 2 forskellige indstillinger for dataindsamling, med forskellige Netværks- og indstillinger for godkendelse - der er relevante for dig?

Gennemgå Lad os forskellige overvejelser og valgmuligheder, du har brug for at sikre at få mest muligt ud af din Azure RemoteApp websteder. 


## <a name="quick-differences-between-the-collection-types"></a>Hurtig forskelle mellem de forskellige typer af websteder

|           | Skyen | Hybrid |
|-----------|-------|--------|
|Brug en eksisterende VNET| Ja| Ja|
|Kræver AD-forbundne konti (DirSync)| Nej| Ja|
|Kræver forbindelse til et domæne| Nej| Ja|
|Kræver domænenavn fra domænecontrolleren tilgængeligt for VNET| Nej| Ja|

## <a name="cloud-collections"></a>Skyen af websteder
- Hurtig til at oprette - samlingen er hurtigt klargjort, hvilket betyder, at dine apps få brugere hurtigere.
- Flytte dine egne apps eller dele vores. Du kan bruge et brugerdefineret billede (indbygget fra en Azure VM) eller en af de billeder, der følger med dit abonnement.
- Du behøver ikke at konfigurere en forbindelse mellem din samling og dit lokale domæne.
- Men du kan også vælge at bruge dit eget Azure VNET til at give adgang til dit lokale miljø til deling af data eller til at bruge Windows-godkendelse til ressourcer som SQL Server (ved hjælp af database godkendelse).


OK, hvordan opretter jeg en?

- Kun skyen? Opret med indstillingen **Hurtig oprettelse** i portalen.
- Skyen + VNET? Oprette ved hjælp af indstillingen **Opret med VNET** , men vælger ikke at deltage i et domæne.

## <a name="hybrid-collections"></a>Hybrid af websteder
- Give fuld adgang til lokale netværk + Azure VNET.
- Omfatter domæne joinforbindelse adgang til apps og -data. Remote-programmer kan godkendelse til din lokale Active Directory - de kan derefter få adgang til ressourcer i dit domæne.
- Aktivere avanceret overvågning og administration med eksisterende System Center løsninger og Windows gruppepolitikker (gennem et brugerdefineret billede, der er bygget på Windows Server 2012 R2)
- Understøtte [ExpressRoute](https://azure.microsoft.com/services/expressroute/) for at forbinde dine Azure VNET til din lokale VNET.

Oprette ved hjælp af indstillingen **Opret med VNET** og vælger at deltage i et domæne.

## <a name="authentication-options"></a>Indstillinger for godkendelse
Azure RemoteApp understøtter både Microsoft-konti og Azure Active Directory-konti, men ikke alle websteder understøtter alle metoder. 

| Kontotype                      |                                                             | Skyen | Skyen + VNET | Hybrid |
|-----------------------------------|-------------------------------------------------------------|-------|--------------|--------|
| Microsoft-konto                 |                                                             | Ja   | Ja          | Nej     |
| Azure Active Directory (Azure AD) |                                                             |       |              |        |
|                                   | Azure AD                                               | Ja   | Ja          | Nej     |
|                                   | AD-forbindelse med synkronisering af adgangskoder                               | Ja   | Ja          | Ja    |
|                                   | AD-forbindelse uden synkronisering af adgangskoder                            | Ja   | Ja          | Nej     |
|                                   | AD Connect med AD FS                                       | Ja   | Ja          | Ja    |
|                                   | 3 tredjepart Azure-understøttet id-udbydere (såsom Ping) | Ja   | Ja          | Ja    |
| Multi-Factor Authentication       |                                                             | Ja   | Ja          | Ja    |



### <a name="cloud-and-cloud--vnet"></a>Skyen og skyen + VNET 
Med skyen samlinger, kan du bruge Microsoft-konti, Azure AD-konti eller en blanding af to. Brug de konti, der fungerer bedst til dine brugere.

Der er ingen specifikke krav til brug af Microsoft-konti. 

Hvis du vil bruge Azure AD-konti, skal du kontrollere, at din Azure AD-lejer svarer til den, der er knyttet til dit abonnement. Da du oprettede abonnementet Azure RemoteApp, blev det Azure AD-lejer, du brugte automatisk knyttet til dit abonnement. En hvilken som helst Azure AD-bruger, du giver tilladelse til at skal være den samme lejer. Hvis det er nødvendigt, kan du [ændre Azure AD-lejer](remoteapp-changetenant.md) knyttet til dit abonnement.
 
### <a name="hybrid-or-cloud--azure-ad--ad"></a>Hybrid (eller skyen + Azure AD + AD)

Brug af Azure AD + lokale Active Directory er en betingelse for en hybrid af websteder. Du skal bruge AD-forbindelse til at integrere de to mapper. Men du har nogle valg, når det drejer sig om hvordan du konfigurerer AD-forbindelse. 

Der er 2 AD Connect scenarier - bruger synkronisering af adgangskoder eller bruger AD sammenslutning. Se [oplysninger om AD-forbindelse](../active-directory/active-directory-aadconnect.md) til at finde ud af, hvilken af disse fungerer bedst for dig.

Du kan også bruge Azure AD + AD med en skyen af websteder. Sørg for, at du følger den samme konfigurere trin.

Se [Azure AD + Active Directory krav til Azure RemoteApp](remoteapp-ad.md) for de trin, der kræves for at konfigurere Azure AD og Active Directory.

## <a name="go-create-your-collection"></a>Gå oprette din samling!
OK, mener jeg vi har fundet det ud af nu, så der er kun én ting til venstre for at gøre - Opret din første Azure RemoteApp af websteder.

[Opret en skyen af websteder](remoteapp-create-cloud-deployment.md) eller [oprette en hybrid samling](remoteapp-create-hybrid-deployment.md) - få kun oprettelse.
