<properties 
    pageTitle="Windows Universal Apps SDK indhold" 
    description="Få mere at vide om indholdet af Windows Universal Apps SDK til Azure Mobile aftale"                    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-store" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-universal-apps-sdk-content"></a>Windows Universal Apps SDK indhold

Dette dokument vises og beskrives det indhold, der er installeret ved SDK i programmet.

##<a name="the-resources-folder"></a>Den `/Resources` mappe

Denne mappe indeholder alle de ressourcer, der skal Mobile aftale. Du kan også tilpasse dem, så de passer til din app.

- `EngagementConfiguration.xml`: Den Mobile aftale konfigurationsfil dette er, hvor du kan tilpasse Mobile aftale indstillinger (Mobile aftale forbindelsesstreng, rapport nedbrud...).

### <a name="html-folder"></a>/HTML mappe

- `EngagementNotification.html`: Det `Notification` web visning HTML-design til i app'en bannere.

- `EngagementAnnouncement.html`: Det `Announcement` web visning HTML-design for i app'en intramuskulært visninger.

### <a name="images-folder"></a>/ images mappe

- `EngagementIconNotification.png`: På brand-ikonet vises til venstre i en meddelelse, erstatter denne ved dit brand-ikon.

- `EngagementIconOk.png`: Det `Ok` ikonet for rækkevidde indholdssiderne til knappen handling eller validering.

- `EngagementIconNOK.png`: Det `NOK` ikon, der bruges, når knappen validering af rækkevidde indholdssiderne er deaktiveret.
 
- `EngagementIconClose.png`: Det `Close` ikonet for rækkevidde meddelelser og indhold til knappen Afvis.

### <a name="overlay-folder"></a>/overlay mappe

- `EngagementPageOverlay.cs`: Overlejring siden ansvarlig for at tilføje aftale når i app'en brugergrænseflade til dens underordnede.
  
