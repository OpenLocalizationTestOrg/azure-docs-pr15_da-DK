<properties
    pageTitle="Automatisk skalere en skybaseret tjeneste i portalen | Microsoft Azure"
    description="Lær at bruge portalen til at konfigurere automatisk skala regler til en skybaseret tjeneste web rolle eller arbejder rolle i Azure."
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>


# <a name="how-to-auto-scale-a-cloud-service"></a>Sådan automatisk skala en skybaseret tjeneste

> [AZURE.SELECTOR]
- [Azure-portalen](cloud-services-how-to-scale-portal.md)
- [Azure klassisk portal](cloud-services-how-to-scale.md)

Betingelser kan angives til en skybaseret tjeneste arbejder rolle, der udløse en skala ind eller ud handling. Betingelser for rollen kan være baseret på CPU, disk eller netværk indlæsning af rollen. Du kan også angive en conditation, der er baseret på en meddelelseskø eller metrisk på nogle andre Azure ressource, der er knyttet til dit abonnement.

>[AZURE.NOTE] I denne artikel fokuserer på skybaseret tjeneste internettet og arbejder roller. Når du opretter en virtuel maskine (klassisk) direkte, er den placeret i en skybaseret tjeneste. Du kan tilpasse en standard virtuel maskine ved at knytte den til en [tilgængelighed angive](../virtual-machines/virtual-machines-windows-classic-configure-availability.md) og manuelt slå dem til eller fra.

## <a name="considerations"></a>Overvejelser i forbindelse med

Før du konfigurerer skalering for dit program, bør du overveje følgende oplysninger:

- Skalering påvirkes af core brugen. Du kan bruge flere kerner større rolle forekomster. Du kan skalere et program kun inden for kerner for dit abonnement. Eksempelvis hvis dit abonnement har en begrænsning på tyve støbekerner, og du køre et program med to mediet tilpasset cloud services (alt fire kerner), kan du kun skalere op andre skyen service installationer i dit abonnement ved 16 kerner. Du kan få flere oplysninger om størrelser i [Skyen Service størrelser](cloud-services-sizes-specs.md) .

- Du kan skalere baseret på en kø meddelelse tærskelværdi. Du kan finde flere oplysninger om, hvordan du bruger køer, se, [hvordan du bruger tjenesten kø Storage](../storage/storage-dotnet-how-to-use-queues.md).

- Du kan også skalere andre ressourcer, der er knyttet til dit abonnement.

- For at aktivere høj tilgængelighed af dit program, skal du sikre dig, at den er installeret med to eller flere forekomster af rollen. Du kan finde yderligere oplysninger finder [Serviceaftaler niveau](https://azure.microsoft.com/support/legal/sla/).

## <a name="where-scale-is-located"></a>Hvor skala er placeret

Når du vælger skybaseret tjeneste, bør du have skyen service blade synlige.

1. Vælg navnet på tjenesten skyen på bladet skyen service på feltet **roller og forekomster** .   
**Vigtigt**: Sørg for at klikke på skyen service rolle, ikke den rolle forekomst, der er under rollen.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)

2. Vælg feltet **skala** .

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Automatisk skala

Du kan konfigurere indstillinger for skalering til en rolle med to tilstande **Manuel** eller **automatisk**. Manuel er, som du forventer, kan du indstille absolutte antallet af forekomster. Automatisk kan dog skal du angive regler, der bestemmer, hvordan og i hvor meget du skalere.

Angiv indstillingen **skala ved** at **planlægge og ydeevne regler**.

![Indstillinger for cloud services for skalering med profil og regel](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. En eksisterende profil.
2. Tilføj en regel for den overordnede profil.
3. Tilføje en anden profil.

Vælg **Tilføj profil**. Profilen, der bestemmer, hvilke tilstand, du vil bruge til skalaen: **altid**, **Gentagelse**, **fast dato**.

Når du har konfigureret profil- og regler, kan du vælge ikonet **Gem** øverst.

#### <a name="profile"></a>Profil

Profilen angiver mindste og største forekomster til skala, og også, når denne skala område er aktiv.

* **Altid**

    Altid holde dette område af forekomster, der er tilgængelige.  

    ![Skybaseret tjeneste, der altid skalere](./media/cloud-services-how-to-scale-portal/select-always.png)
    
* **Gentagelse**

    Vælg et sæt af dage i ugen skalere.

    ![Skybaseret tjeneste Skaler med en tidsplan for gentagelse](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
    
* **Fast dato**

    Et fast datointerval skalere rollen.

    ![Skybaseret tjeneste Skaler med en bestemt dato](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Når du har konfigureret profilen, Vælg knappen **OK** nederst i bladet profil.

#### <a name="rule"></a>Regel

Regler er føjet til en profil og repræsenterer en betingelse, der skal udløse skalaen. 

Regel udløser er baseret på en metrikværdi til skyen-tjenesten (CPU-brug, aktivitet på disken eller aktivitet på netværket), kan du tilføje en betinget værdi. Desuden kan du få udløser baseret på en meddelelseskø eller metrisk på nogle andre Azure ressource, der er knyttet til dit abonnement.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Når du har konfigureret reglen, klik på knappen **OK** nederst i bladet regel.

## <a name="back-to-manual-scale"></a>Tilbage til Manuel skala

Gå til [Indstillinger for skalering](#where-scale-is-located) , og angiv indstillingen **Skaler ved** til **en forekomst, tæller, som jeg indtaster manuelt**.

![Indstillinger for cloud services for skalering med profil og regel](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Dette fjerner automatiseret skalering fra rollen, og derefter kan du angive antallet forekomst direkte. 

1. (Manuel eller automatisk) indstillingen skalering.
2. En rolle forekomst skyderen for at indstille forekomster at skalere til.
3. Forekomster af rollen at skalere til.

Når du har konfigureret indstillinger for skalering, kan du vælge ikonet **Gem** øverst.

