<properties
    pageTitle="Automatisk skalere en skybaseret tjeneste i portalen | Microsoft Azure"
    description="(klassisk) Lær at bruge klassisk portal til at konfigurere automatisk skala regler til en skybaseret tjeneste web rolle eller arbejder rolle i Azure."
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

Du kan manuelt skalere dit web rolle eller arbejder rolle på siden skala i portalen Azure klassisk, eller du kan aktivere automatisk skalering baseret på CPU-belastning eller en meddelelseskø.

>[AZURE.NOTE] I denne artikel fokuserer på skybaseret tjeneste internettet og arbejder roller. Når du opretter en virtuel maskine (klassisk) direkte, er den placeret i en skybaseret tjeneste. Nogle af disse oplysninger gælder for disse typer virtuelle computere. Skalere et tilgængelighed sæt af virtuelle maskiner lukker blot dem til og fra baseret på de skala regler, du konfigurerer. Du kan finde flere oplysninger om virtuelle maskiner og tilgængelighed sæt, skal du se [administrere tilgængelighed af virtuelle maskiner](../virtual-machines/virtual-machines-windows-classic-configure-availability.md)

Før du konfigurerer skalering for dit program, bør du overveje følgende oplysninger:

- Skalering påvirkes af core brugen. Du kan bruge flere kerner større rolle forekomster. Du kan skalere et program kun inden for kerner for dit abonnement. Eksempelvis hvis dit abonnement har en begrænsning på tyve støbekerner, og du køre et program med to mediet tilpasset cloud services (alt fire kerner), kan du kun skalere op andre skyen service installationer i dit abonnement ved 16 kerner. Du kan få flere oplysninger om størrelser i [Skyen Service størrelser](cloud-services-sizes-specs.md) .

- Du skal oprette en kø og knytte den til en rolle, før du kan skalere et program, der er baseret på en grænseværdi for meddelelsen. Se, [hvordan du bruger tjenesten kø Storage](../storage/storage-dotnet-how-to-use-queues.md)kan finde flere oplysninger.

- Du kan skalere ressourcer, der er knyttet til din skybaseret tjeneste. Finde flere oplysninger om sammenkædning af ressourcer, [Sådan: sammenkæde en ressource til en skybaseret tjeneste](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service).

- For at aktivere høj tilgængelighed af dit program, skal du sikre dig, at den er installeret med to eller flere forekomster af rollen. Du kan finde yderligere oplysninger finder [Serviceaftaler niveau](https://azure.microsoft.com/support/legal/sla/).



## <a name="schedule-scaling"></a>Planlægge skalering

Som standard følge alle roller ikke en bestemt tidsplan. Derfor gælder noget ændret for hele tiden og alle dage hele året. Hvis du vil, kan du konfigurere manuel eller automatisk skalering for:

- Ugedage
- Weekender
- Uge overnatninger
- Uge morgen
- Bestemte datoer
- Bestemte datointervaller

Dette er conigured i [Azure klassisk portal](https://manage.windowsazure.com/) på den  
**Cloud Services** > **\[skybaseret tjeneste\]** > **skala** > **\[fremstilling eller midlertidige\] ** side.

Klik på knappen **konfigurere tidsplan tidspunkter** for hver rolle, du vil ændre.

![Tjenesten automatisk skalering baseret på en tidsplan i skyen][scale_schedules]



## <a name="manual-scale"></a>Manuel skala

På siden **skala** , kan du manuelt forøger eller formindsker antallet af aktive forekomster i en skybaseret tjeneste. Dette er konfigureret for hver plan, du har oprettet eller hele tiden, hvis du ikke har oprettet en tidsplan.

1. Klik på **Cloud Services**i [Azure klassisk portal](https://manage.windowsazure.com/), og klik derefter på navnet på skytjeneste til at åbne dashboard.

    > [AZURE.TIP] Hvis du ikke kan se din skybaseret tjeneste, skal du muligvis ændre fra **fremstilling** til **midlertidige** eller omvendt.

2. Klik på **skala**.

3. Vælg den tidsplan, du vil ændre skaleringsindstillinger for. Som standard *ingen planlagte tidspunkter* Hvis du har ingen planer, der er defineret.

4. Find sektionen **skala af metrikværdi** , og vælg ****. Dette er standardindstillingen for alle roller.

5. Hver rolle i skytjenesten har en skyder til at ændre antallet af forekomster, der skal bruge.

    ![Manuelt skalere en skybaseret tjeneste rolle][manual_scale]

    Hvis du har brug for flere forekomster, skal du muligvis ændre [skyen service virtuelt størrelse](cloud-services-sizes-specs.md).

6. Klik på **Gem**.  
Rolle forekomster vil blive tilføjet eller fjernet baseret på dine valg.

>[AZURE.TIP] Når du får vist ![][tip_icon] Flyt musen til den, og du kan få hjælp om, hvad en bestemt indstilling betyder.


## <a name="automatic-scale---cpu"></a>Automatisk skala - CPU

Dette skaleres, hvis den gennemsnitlige procentdel af CPU-brug går over eller under angivne tærskler. rolle forekomster oprettes eller slettes.

1. Klik på **Cloud Services**i [Azure klassisk portal](https://manage.windowsazure.com/), og klik derefter på navnet på skytjeneste til at åbne dashboard.

    > [AZURE.TIP] Hvis du ikke kan se din skybaseret tjeneste, skal du muligvis ændre fra **fremstilling** til **midlertidige** eller omvendt.

2. Klik på **skala**.

3. Vælg den tidsplan, du vil ændre skaleringsindstillinger for. Som standard *ingen planlagte tidspunkter* Hvis du har ingen planer, der er defineret.

4. Find sektionen **skala af metrikværdi** og vælg **CPU**.

5. Nu kan du konfigurere en mindste og største række roller forekomster, målet CPU-brug (for at udløse en skala op), og hvor mange forekomster til at skalere op og ned med.

![Skalere en skybaseret tjeneste rolle med cpu-belastning][cpu_scale]

>[AZURE.TIP] Når du får vist ![][tip_icon] Flyt musen til den, og du kan få hjælp om, hvad en bestemt indstilling betyder.





## <a name="automatic-scale---queue"></a>Automatisk skala - kø

Dette skaleres automatisk, hvis antallet af meddelelser i en kø går over eller under en bestemt grænse rolle forekomster oprettes eller slettes.

1. Klik på **Cloud Services**i [Azure klassisk portal](https://manage.windowsazure.com/), og klik derefter på navnet på skytjeneste til at åbne dashboard.

    > [AZURE.TIP] Hvis du ikke kan se din skybaseret tjeneste, skal du muligvis ændre fra **fremstilling** til **midlertidige** eller omvendt.

2. Klik på **skala**.

3. Find sektionen **skala af metrikværdi** og vælg **CPU**.

4. Nu kan du konfigurere en mindste og største række roller forekomster, køen og mængde beskeder i kø til at behandle for hver forekomst, og hvor mange forekomster til at skalere op og ned med.

![Skalere en skybaseret tjeneste rolle med en meddelelseskø][queue_scale]

>[AZURE.TIP] Når du får vist ![][tip_icon] Flyt musen til den, og du kan få hjælp om, hvad en bestemt indstilling betyder.


## <a name="scale-linked-resources"></a>Skalere sammenkædede ressourcer

Ofte, når du skalere en rolle, er det være en fordel at skalere den database, der bruger programmet også. Hvis du sammenkæder databasen til skybaseret tjeneste, kan du få adgang til skalering indstillingerne for denne ressource ved at klikke på det relevante link.

1. Klik på **Cloud Services**i [Azure klassisk portal](https://manage.windowsazure.com/), og klik derefter på navnet på skytjeneste til at åbne dashboard.

    > [AZURE.TIP] Hvis du ikke kan se din skybaseret tjeneste, skal du muligvis ændre fra **fremstilling** til **midlertidige** eller omvendt.

2. Klik på **skala**.

3. Find afsnittet **sammenkædede ressourcer** og har klikket på **Administrer skalering for denne database**.

    > [AZURE.NOTE] Hvis du ikke kan se en **sammenkædet ressourcer** sektion, har du sandsynligvis ikke nogen sammenkædede ressourcer.

![][linked_resource]


[manual_scale]: ./media/cloud-services-how-to-scale/manual-scale.png
[queue_scale]: ./media/cloud-services-how-to-scale/queue-scale.png
[cpu_scale]: ./media/cloud-services-how-to-scale/cpu-scale.png
[tip_icon]: ./media/cloud-services-how-to-scale/tip.png
[scale_schedules]: ./media/cloud-services-how-to-scale/schedules.png
[scale_popup]: ./media/cloud-services-how-to-scale/schedules-dialog.png
[linked_resource]: ./media/cloud-services-how-to-scale/linked-resources.png
