<properties 
    pageTitle="Hvad er Azure RemoteApp? | Microsoft Azure" 
    description="Lær at dele apps og ressourcer til en hvilken som helst enhed via Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" 
    editor=""/>

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="what-is-azure-remoteapp"></a>Hvad er Azure RemoteApp?

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Azure RemoteApp viser funktionaliteten i programmet lokale Microsoft RemoteApp, understøttes af Remote Desktop Services til Azure. Azure RemoteApp hjælper dig med at levere sikker og ekstern adgang til programmer fra mange forskellige enheder. Azure RemoteApp som vært ikke-fast Terminal Server-sessioner i skyen, og du kan bruge dem og dele dem med dine brugere.

Du kan dele apps og ressourcer med brugere på næsten enhver enhed med Azure RemoteApp. Vi hoste dine apps i skyen, hvilket betyder, at vi tager sig af hardwaren og skalering for at imødekomme brugernes behov. Alt, du skal gøre, er overføre de apps, du vil dele, og derefter få dine brugere at bruge disse apps. [Brugerne adgang til at synkronisere deres egne enheder](remoteapp-clients.md), mens du administrerer alt via Azure-portalen. Du har også mulighed for at bruge dine virksomhedens legitimationsoplysninger, så du kan sikre sikkerheden for apps og data.

Læs videre for at finde flere oplysninger om Azure RemoteApp, eller hvis vi har allerede overbevist om dig, [kan du prøve ud af det nu](https://azure.microsoft.com/services/remoteapp/).

Har du spørgsmål om Azure RemoteApp? Se vores [ofte stillede spørgsmål](remoteapp-faq.md).

Azure RemoteApp er en del af [Microsoft Virtual Desktop Infrastructure](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Nyt!** Vil du vide mere om Azure RemoteApp? Eller klar til at validere Azure RemoteApp skaleres? Deltage i vores ugentligt [stil webinar](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website).

## <a name="azure-remoteapp-collections"></a>Azure RemoteApp af websteder
Der er to typer af [Azure RemoteApp af websteder](remoteapp-collections.md):


- En **skyen af websteder** er hostet i og lagrer data til programmer i skyen. Brugere kan få adgang til apps ved at logge på med deres Microsoft-konto eller virksomhedens legitimationsoplysninger synkroniseret eller knyttet til Azure Active Directory.

    Vælg en skyen websteder, når det program, du vil dele ikke kræver en forbindelse til en hvilken som helst ressource virksomhedens privat netværk (for eksempel via en VPN-enhed). Hvis programmet bruger ressourcer på internettet, OneDrive eller Azure, fungerer en skyen samling for dig. Det er også den hurtigste til at oprette.

- En **hybrid af websteder** er hostet i og lagrer data i Azure skyen, men kan brugere access-data og ressourcer, der er gemt på dit lokale netværk. Brugere kan få adgang til apps ved at logge på med legitimationsoplysningerne virksomhedens synkroniseret eller knyttet til Azure Active Directory.

    Vælg en hybrid websteder, hvis du kræver en forbindelse til ressourcer på virksomhedens privat netværk. Eksempelvis hvis programmet skal have adgang til et af følgende:

    - Filservere, der er placeret på intranettet
    - Quicken
    - Databaser bag en firewall

    Dette er normalt mere nyttig til store virksomheder med masser af ressourcer på deres privat netværk, der ikke kan flyttes til skyen.

De forskellige samlinger har forskellige indstillinger, herunder netværk, så finde ud af, [hvilken samling](remoteapp-collections.md) fungerer bedst for dig. 


### <a name="updating-your-collection"></a>Opdatere din samling
En af de tre vigtigste forskelle mellem hybrid og skyen af websteder er hvordan softwareopdateringer håndteres. Med en skyen af websteder, der bruger det forudinstallerede Office 365 ProPlus eller Office 2013-billede, har du ikke at bekymre dig om eventuelle opdateringer. Tjenesten fører selve og ruller opdateringer løbende, både apps og operativsystemet.

For hybride samlinger, samt skyen samlinger, der bruger et brugerdefineret skabelonbillede, er du ansvarlig for vedligeholdelse af billedet og apps. Du kan styre opdateringer til billeder der er medlem af et domæne, ved hjælp af funktioner som Windows Update, Gruppepolitik eller System Center.

Når du opdaterer din brugerdefinerede skabelonbillede, du overfører det nye billede til Azure skyen og derefter opdatere samlingen for at bruge det nye billede. (Du kan gøre dette fra siden Azure RemoteApp **Hurtig Start** eller dashboardet.)

Se [opdatere din af websteder](remoteapp-update.md) for flere oplysninger.

## <a name="supported-remoteapp-clients"></a>Understøttede RemoteApp-klienter
Azure RemoteApp understøttes på RemoteApp klient apps til Windows og Windows RT, samt Microsoft Remote Desktop-apps til Mac, iOS og Android. Brugerne kan bruge disse apps på deres mobile eller beregne enheder for at få adgang til de nye Azure RemoteApp-programmer.

Se [få adgang til dine apps i Azure RemoteApp](remoteapp-clients.md) finde flere oplysninger om klienterne.

## <a name="next-steps"></a>Næste trin
Gå! Prøv det! Disse artikler at komme i gang med Azure RemoteApp:

- [Hvilken type af websteder har du brug for til Azure RemoteApp?](remoteapp-collections.md)
- [Oprette et Azure RemoteApp-billede](remoteapp-imageoptions.md)
- [Sådan oprettes en skyen samling af Azure RemoteApp](remoteapp-create-cloud-deployment.md)
- [Sådan oprettes en hybrid samling af Azure RemoteApp](remoteapp-create-hybrid-deployment.md)
- [Hvordan fungerer licensering arbejde i Azure RemoteApp?](remoteapp-licensing.md)
- [Bedste fremgangsmåder til brug af Azure RemoteApp](remoteapp-bestpractices.md)
- [Azure RemoteApp ofte stillede spørgsmål](remoteapp-faq.md)
 

### <a name="help-us-help-you"></a>Hjælp os med at hjælpe dig med at 
Vidste du, at ud over vurdering i denne artikel og foretage kommentarer ned under, du kan foretage ændringer i artiklen selve? Noget gå glip af? Noget galt? Jeg skrive noget, som er lige forvirrende? Rulle op og klikke på **Rediger på GitHub** eller **Rediger** for at foretage ændringer – dem, der kommer til os til gennemsyn, og derefter, når vi logge af dem, får du vist dine ændringer og forbedringer her.