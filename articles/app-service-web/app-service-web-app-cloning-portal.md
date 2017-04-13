<properties
    pageTitle="Web App klone portalen til Azure"
    description="Lær, hvordan du klone din Onlines til nye online ved hjælp af Azure Portal."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/08/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-app-cloning-using-azure-portal"></a>Azure App Service App klone ved hjælp af Azure Portal#

Funktionen kloningssoftware i [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) kan du nemt klone eksisterende Onlines til en nyligt oprettet app i et andet område eller i det samme område. Dette vil aktivere kunder at implementere et antal apps på tværs af forskellige regioner hurtigt og nemt.

App klone er i øjeblikket kun understøttet for premium niveau app service-planer. Den nye funktion anvender de samme begrænsninger som sikkerhedskopiering Web Apps, skal du se [sikkerhedskopiere en WebApp i Azure App Service](web-sites-backup.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 


## <a name="cloning-an-existing-app"></a>Klone en eksisterende App ##

Web-appen skal køre i tilstanden **Premium** i rækkefølge for dig at oprette en kopi til WebApp.

1. Åbn din online blade i [Azure-portalen](https://portal.azure.com/).
2. Klik på **værktøjer**. Klik på **Klon App**på bladet **værktøjer** .

    ![][1]

    > [AZURE.NOTE]
    > Hvis WebApp ikke allerede er i tilstanden **Premium** , modtager du en meddelelse om de understøttede tilstande for app klone. På dette tidspunkt har du mulighed for at vælge **opgradere**.
    
3. Angiv et navn på ny WebApp, ressourcegruppe, og App-tjenesten planlægge bladet **Klon App** . Også brugeren skal kunne vælge, om at klone et antal indstillinger kilde online eller ej.

    ![][2]

4. Når du klikker på **Opret** starter platformen arbejder på at oprette en kopi af kilde WebApp.

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Klone en eksisterende App til et App-tjenesten miljø##

I bladet **Klon App** har kunden mulighed for at vælge en programgruppen i et eksisterende App Service-miljø.

## <a name="current-restrictions"></a>Aktuelle begrænsninger ##

Denne funktion er i øjeblikket i Vis udskrift, vi arbejder på at tilføje nye funktionaliteter over tid, på følgende liste er de kendte begrænsninger for den aktuelle understøttelse af app klone Azure-portalen:

- Azure trafik Manager indstillinger sammenkædes ikke
- Indstillinger for automatisk for skalering sammenkædes ikke
- Indstillinger for sikkerhedskopiering tidsplanen sammenkædes ikke
- Indstillinger for VNET sammenkædes ikke
- App indsigt konfigureres ikke automatisk på online destination
- Nemt Auth indstillinger sammenkædes ikke
- Kudu filtypenavn ikke sammenkædes
- TiP regler sammenkædes ikke
- Indhold i databasen ikke sammenkædes


### <a name="references"></a>Referencer ###
- [Web App klone ved hjælp af PowerShell](app-service-web-app-cloning.md)
- [Sikkerhedskopiere en WebApp i Azure App Service](web-sites-backup.md)
- [Sådan oprettes et App Service-miljø](app-service-web-how-to-create-an-app-service-environment.md)
- [Oprette en WebApp i en App Service-miljø](app-service-web-how-to-create-a-web-app-in-an-ase.md)
- [Introduktion til App Service-miljø](app-service-app-service-environment-intro.md)

<!--Image references-->
[1]: ./media/app-service-web-app-cloning-portal/CloningBlade.png
[2]: ./media/app-service-web-app-cloning-portal/CloneSettings.png