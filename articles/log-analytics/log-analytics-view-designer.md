<properties
    pageTitle="Log Analytics visning Designer | Microsoft Azure"
    description="Vis Designer i Log Analytics gør det muligt at oprette brugerdefinerede visninger i konsollen OMS, der indeholder forskellige visualiseringer af data i OMS-lager. I denne artikel indeholder en oversigt over Designer visninger og procedurerne for at oprette og redigere brugerdefinerede visninger."
    services="log-analytics"
    documentationCenter=""
    authors="bwren"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="bwren"/>

# <a name="log-analytics-view-designer"></a>Log Analytics visning Designer
Vis Designer i Log Analytics gør det muligt at oprette brugerdefinerede visninger i konsollen OMS, der indeholder forskellige visualiseringer af data i OMS-lager. I denne artikel indeholder en oversigt over Designer visninger og procedurerne for at oprette og redigere brugerdefinerede visninger.

Andre artikler tilgængelige til visning Designer er:

- [Feltet reference](log-analytics-view-designer-tiles.md) - referencen til indstillingerne for hver af de felter, der er tilgængelige til brug i dine brugerdefinerede visninger. 
- [Visualisering del referencen](log-analytics-view-designer-parts.md) - referencen til indstillingerne for hver af de felter, der er tilgængelige til brug i dine brugerdefinerede visninger. 


## <a name="concepts"></a>Begreber
Visninger, der er oprettet med Vis Designer indeholder elementerne i den følgende tabel.

| Del | Beskrivelse |
|:--|:--|
| Side om side | Vises på dashboardet primære Log Analytics oversigt.  Indeholder en visuel opsummering af oplysningerne i den brugerdefinerede visning.  Feltet forskellige giver forskellige visualiseringer af poster i OMS-lager.  Klik på feltet for at åbne den brugerdefinerede visning. |
| Brugerdefineret visning | Vises, når brugeren klikker på feltet.  Indeholder en eller flere visualisering dele. |
| Visualisering af webdele | Visualisering af data i lageret OMS baseret på en eller flere [log søgninger](log-analytics-log-searches.md).  De fleste dele indeholder et sidehoved, der indeholder en højt niveau visualisering og en liste over de bedste resultater.  Forskellige typer giver forskellige visualiseringer af poster i OMS-lager.  Klik på elementer i webdelen til at udføre en log søgning, give detaljerede poster. |

![Få vist Designer oversigt](media/log-analytics-view-designer/overview.png)

## <a name="add-view-designer-to-your-workspace"></a>Tilføje visningen Designer til dit arbejdsområde
Mens Designer visninger er i Vis udskrift, skal du føje den til dit arbejdsområde ved at vælge **Vis funktioner** i sektionen **Indstillinger** af portalen OMS.

![Aktivere preview](media/log-analytics-view-designer/preview.png)

## <a name="creating-and-editing-views"></a>Oprette og redigere visninger

### <a name="create-a-new-view"></a>Oprette en ny visning
Åbn en ny visning i **Designer visninger** ved at klikke på feltet Vis Designer i den primære OMS dashboard.

![Få vist Designer-felt](media/log-analytics-view-designer/view-designer-tile.png)

### <a name="edit-an-existing-view"></a>Redigere en eksisterende visning
For at redigere en eksisterende visning i Designer visning skal du åbne visningen ved at klikke på dens felt i den primære OMS dashboard.  Klik derefter på knappen **Rediger** for at åbne visningen i visningen Designer.

![Redigere en visning](media/log-analytics-view-designer/menu-edit.png)

### <a name="clone-an-existing-view"></a>Klone en eksisterende visning
Når du klone en visning, opretter en ny visning og åbner den i visningen Designer.  Den nye visning har samme navn som oprindeligt med "kopiere" tilføjede til slutningen af den.  Hvis du vil klone en visning, kan du åbne visningen eksisterende ved at klikke på dens felt i den primære OMS dashboard.  Klik derefter på knappen **Klon** for at åbne visningen i visningen Designer.

![Klone en visning](media/log-analytics-view-designer/edit-menu-clone.png)

### <a name="delete-an-existing-view"></a>Slette en eksisterende visning
Hvis du vil slette en eksisterende visning, skal du åbne visningen ved at klikke på dens felt i den primære OMS dashboard.  Derefter skal du klikke på knappen **Rediger** for at åbne visningen i visningen Designer, og klik på **Slet visning**.

![Slette en visning](media/log-analytics-view-designer/edit-menu-delete.png)

### <a name="export-an-existing-view"></a>Eksportere en eksisterende visning
Du kan eksportere en visning til en JSON-fil, som du kan importere til et andet arbejdsområde eller bruge i en [Azure ressourcestyring skabelon](../resource-group-authoring-templates.md).  For at eksportere en eksisterende visning, skal du åbne visningen ved at klikke på dens felt i den primære OMS dashboard.  Klik derefter på knappen **Eksporter** for at oprette en fil i mappen Overførsler på i browseren.  Navnet på filen bliver navnet på visningen med filtypenavnet *omsview*.

![Eksportere en visning](media/log-analytics-view-designer/edit-menu-export.png)

### <a name="import-an-existing-view"></a>Importere en eksisterende visning
Du kan importere en *omsview* -fil, du har eksporteret fra en anden gruppe i administration.  Hvis du vil importere en eksisterende visning, skal du først oprette en ny visning.  Klik på knappen **Importér** , og Vælg fil, *omsview* .  Konfigurationen i filen skal kopieres til den eksisterende visning.

![Eksportere en visning](media/log-analytics-view-designer/edit-menu-import.png)

## <a name="working-with-view-designer"></a>Arbejde med Designer visninger
Vis Designer indeholder tre ruder.  Ruden **Design** repræsenterer den brugerdefinerede visning.  Når du føjer felter og dele fra ruden **kontrolelement** til ruden **Design** tilføjes de i visningen.  Ruden **Egenskaber** vises egenskaberne for hvert felt eller den markerede del.

![Vis Designer](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>Konfigurere view-felt
En brugerdefineret visning kan have et enkelt felt.  Vælg fanen **side om side** i ruden **kontrolelement** for at få vist den aktuelle flise eller Vælg en alternativ.  Ruden **Egenskaber** vises egenskaberne for den aktuelle flise.  Konfigurere egenskaber for feltet ifølge de detaljerede oplysninger i [Flisen Reference](log-analytics-view-designer-tiles.md) , og klik på **Anvend** for at gemme ændringerne.

### <a name="configure-visualization-parts"></a>Konfigurere visualisering dele
En visning kan omfatte en hvilken som helst antal visualisering dele.  Vælg fanen **Vis** , og vælg derefter en visualisering del til at føje til visningen.  Ruden **Egenskaber** vises egenskaberne for den markerede del.  Konfigurere egenskaber for visning efter de detaljerede oplysninger i [Visualiseringen del reference](log-analytics-view-designer-parts.md) , og klik på **Anvend** for at gemme ændringerne.

### <a name="delete-a-visualization-part"></a>Slette et visualisering
Du kan fjerne en visualisering del fra visningen ved at klikke på knappen **X** i øverste højre hjørne af del.

### <a name="rearrange-visualization-parts"></a>Omarrangere visualisering dele
Visninger kun have én række af visualisering dele.  Omarrangere eksisterende webdele i en visning ved at klikke på og trække dem til en ny placering.


## <a name="next-steps"></a>Næste trin

- Føje [felter](log-analytics-view-designer-tiles.md) til den brugerdefinerede visning.
- Tilføj [Visualisering webdele](log-analytics-view-designer-parts.md) på den brugerdefinerede visning.
