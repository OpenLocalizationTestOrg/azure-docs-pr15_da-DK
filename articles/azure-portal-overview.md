<properties
    pageTitle="Microsoft Azure portalen oversigt"
    description="Lær at bruge Microsoft Azure-portalen."
    services=""
    documentationCenter=""
    authors="davidwrede"
    manager="dwrede"
    editor="jimbe"/>

<tags
    ms.service="na"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="12/16/2015"
    ms.author="dwrede"/>

# <a name="microsoft-azure-portal-overview"></a>Microsoft Azure portalen oversigt

Microsoft Azure-portalen er et centralt sted, hvor du kan tildele og administrere dine Azure ressourcer.  Dette selvstudium skal lære du med portalen og viser dig, hvordan du kan bruge nogle af disse vigtige funktioner:
- En **omfattende marketplace** , hvor du kan gennemse tusindvis af elementer fra Microsoft og andre leverandører, som kan være købt og/eller klargjort.
- En **samlet og SVG Gennemse oplevelse** , der gør det nemt at finde de ressourcer, du interesserer og udføre forskellige handlinger i administration.
- **Ensartet administrationssider** (eller blade), der gør det muligt at administrere Azures lang af tjenesterne via en ensartet måde for at afsløre indstillinger, handlinger, fakturering oplysninger, sundhed overvågning og brugen af data og meget mere.
- En **personlig oplevelse** , hvor du kan oprette et tilpasset startskærmen, der viser de oplysninger, du vil se hver gang du logger på.  Du kan også tilpasse et af de management blade, der indeholder felter.

 ![Azure portalen Brugergrænsefladen retning][UIOrientation]

## <a name="before-you-get-started"></a>Før du går i gang

Du skal bruge et gyldigt Azure abonnement til at gennemgå dette selvstudium.  Hvis du ikke har en derefter [tilmelde dig en gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/) i dag.  Når du har et abonnement, kan du få adgang til portalen på [https://portal.azure.com].

## <a name="how-to-create-a-resource"></a>Sådan oprettes en ressource

Azure har en marketplace med tusindvis af elementer, du kan oprette fra ét sted.  Antag, at du vil oprette en ny Windows Server 2012 VM.  Den + nye hub er den hurtigste vej til et curated sæt udvalgt kategorier fra marketplace.  Hver kategori har et lille sæt af udvalgte elementer sammen med et link til den fulde marketplace, der viser alle kategorier og søgning. Hvis du vil oprette den nye Windows Server 2012 VM, skal du udføre følgende handlinger:  

1.  Windows Server 2012 klient, så du kan vælge fra kategorien Beregn.  
2.  Udfyld nogle grundlæggende oplysninger i en formular.
3.  Klik på 'Opret', og din VM begynder at klargøre med det samme.

Beskeder hubben giver dig besked, når din ressource er blevet oprettet og en management blade åbnes (du kan altid gå til ressourcer senere).

![Portalen kategorier][PortalCategories]


## <a name="how-to-find-your-resources"></a>Sådan finder du dine ressourcer

Du kan altid fastgøre hyppigt ressourcer til din startboard, men det være nødvendigt at gå til noget, du ofte ikke få adgang til.  Gennemse hubben vist nedenfor er din måde at få adgang til alle dine ressourcer.  Du kan filtrere efter abonnement, vælge eller ændre størrelse på kolonner, og gå til administration af blade ved at klikke på individuelle elementer.

![Gennemse Hub][BrowseHub]

## <a name="how-to-manage-and-delegate-access-to-a-resource"></a>Sådan administreres og stedfortræderadgang til en ressource

Fra denne blade, du kan oprette forbindelse til den virtuelle maskine ved hjælp af Fjernskrivebord, overvåge KPI'er målepunkter, kontrollere adgang til denne VM ved hjælp af rolle baseret access (RBAC), konfigurere VM og udføre andre vigtige administrationsopgaver.  Uddelegere adgang, der er baseret på rolle er meget vigtigt for at administrere skaleres til brug.  Klik [her](./active-directory/role-based-access-control-configure.md) for at lære mere om den. Hvis du vil stedfortræderadgang til en ressource, kan du udføre følgende handlinger:

1.  Gå til din ressource.
2.  Klik på "Alle indstillinger" i sektionen Essentials.
3.  Klik på "Brugere" på listen indstillinger.
4.  Klik på 'Tilføj' i kommandolinjen.
5.  Vælg en bruger og en rolle.

![Administration af en ressource][ManageResource]

## <a name="how-to-customize-a-resource-blade"></a>Hvordan du tilpasser en ressource blade

Azure adgangskodebaseret blade for dine ressourcer, men felterne i disse blade er din egen til kontrolelement.  Du kan nemt gå til Tilpas tilstand for at tilføje, fjerne, ændre størrelsen på eller omarrangere felterne. Hvis du vil tilpasse en blade, skal du udføre følgende handlinger:

1.  Gå til din ressource.
2.  Klik på "..." på øverst del af bladet, du vil tilpasse.
3.  Klik på 'Tilføj dele'.
4.  Begynd at trække og slippe dele.  

![Tilpasning af blade][CustomizeBlades]

## <a name="how-to-get-help"></a>Sådan får du hjælp

Hvis du nogensinde har et problem, er vi klar for dig.  Portalen har en hjælp og support side, der peger du i den rigtige retning.  Afhængigt af din [understøtter plan](https://azure.microsoft.com/support/plans/), kan du også oprette supportbilletter direkte i portalen.  Når du har oprettet en supportbilletter, kan du administrere livscyklus for brugertilladelse fra i portalen. Du kan få adgang til Hjælp og supportside ved at navigere til Gennemse -> Hjælp + support.  

![Hjælp og support][HelpSupport]

## <a name="summary"></a>Oversigt

Lad os gennemgå det, du har lært i dette selvstudium:
- Du har lært Sådan tilmelde dig, får et abonnement, og gå til portalen
- Du har et overblik over portalen Brugergrænsefladen og lært, hvordan du opretter og søge efter ressourcer
- Du har lært at oprette en ressource og gennemse ressourcer
- Du har lært om strukturen eller administration blade, og hvordan du kan administrere forskellige typer ressourcer konsekvent
- Du har lært, hvordan du tilpasser portalen for at få oplysninger der interesserer dig til forside og center
- Du har lært, hvordan du kan styre adgangen til ressourcer ved hjælp af rollen baseret adgang (RBAC)
- Du har lært, hvordan du kan få hjælp og support

Portalen Microsoft Azure forenkler væsentligt opbygge og administrere dine programmer i skyen.  Se nærmere på [management blog](https://azure.microsoft.com/blog/topics/management/) for at holde dig opdateret, som vi er konstant [lytte til feedback](https://feedback.azure.com/forums/223579-azure-preview-portal/) og foretage forbedringer.  [Scottgu's blog](http://weblogs.asp.net/scottgu) er en anden godt sted at søge efter alle Azure opdateringer.

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png
