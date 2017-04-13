<properties 
    pageTitle="Azure RemoteApp fejlfinding - programfejl start og forbindelse | Microsoft Azure" 
    description="Lær at foretage fejlfinding af problemer med start- og oprette forbindelse til programmer i Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



#<a name="troubleshoot-azure-remoteapp---application-launch-and-connection-failures"></a>Fejlfinding i forbindelse med Azure RemoteApp - programfejl start og forbindelse 

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Vært Azure RemoteApp-programmer ikke til start af forskellige årsager. I denne artikel beskrives forskellige årsager og fejlmeddelelser brugere kan modtage hvornår forsøger at starte programmer. Det er også taler om mislykkede forbindelser. (Men i denne artikel beskrives ikke problemer, når du logger på Azure RemoteApp klienten).  

Læs videre for at finde oplysninger om almindelige fejlmeddelelser på grund af mislykkede forsøg app start og forbindelse.

##<a name="were-getting-you-set-up-try-again-in-10-minutes"></a>Vi får du konfigurerer... Prøv igen i 10 minutter.

Fejlen betyder Azure RemoteApp skalering til at opfylde kapacitet brug af dine brugere. I baggrunden oprettes flere Azure RemoteApp VM forekomster for at håndtere dine brugere kundernes kapacitetsbehov. Typisk dette tager omkring fem minutter, men det kan tage op til 10 minutter. Nogle gange kan dette sker ikke hurtigt nok og ressourcer, der er nødvendige med det samme. For eksempel en 9 AM scenario, hvor mange brugere for at bruge din app i Azure RemoteAppn på samme tid. Hvis dette sker for du kan vi aktivere **kapacitet tilstand** på back end. Hvis du vil åbne en Azure supportbilletter og eller sende en e-mail til [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com). Være sikker på, at medtage dit abonnement-ID i indkaldelsen.  

![Vi er ved at konfigurere](./media/remoteapp-apptrouble/ra-apptrouble1.png)

## <a name="could-not-auto-reconnect-to-your-applications-please-re-launch-your-application"></a>Kunne ikke automatisk-genoprette forbindelse til dine programmer, skal du igen åbne dit program  

Denne fejlmeddelelse er ofte set Hvis du brugte Azure RemoteApp og derefter sætte din computer i dvale længere end 4 timer og derefter vækkede din PC og Azure RemoteApp klienten forsøger at automatisk genoprette forbindelse og timeout blev overskredet.  Få brugerne til at gå tilbage til programmet, og forsøger at åbne den fra Azure RemoteApp klienten på computeren.

![Kunne ikke automatisk-genoprette forbindelse til dine programmer](./media/remoteapp-apptrouble/ra-apptrouble2.png) 

## <a name="problems-with-the-temp-profile"></a>Problemer med den temp profil 

Denne fejl opstår, når din brugerprofil (bruger profil Disk) kunne ikke tilslutte og brugeren har modtaget en midlertidig profil.  Administratorer skal gå til samlingen i portalen Azure og derefter gå til fanen **sessioner** og forsøger at **Log af** brugeren. Dette vil tvinge en fuld log fra brugersessionen - derefter har brugeren forsøger at starte en app igen. Hvis det ikke lykkes kontakte support til Azure og eller sende os på [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

## <a name="azure-remoteapp-has-stopped-working"></a>Azure RemoteApp fungerer ikke længere

Denne fejlmeddelelse betyder Azure RemoteApp klienten har et problem og skal genstartes. Fortæl brugerne at lukke: Vælg **Luk programmet** og derefter starte Azure RemoteApp klienten igen.  Hvis problemet fortsætter, Åbn og Azure supportbilletter og eller sende en e-mail til [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

![Azure RemoteApp fungerer ikke længere](./media/remoteapp-apptrouble/ra-apptrouble3.png)  

## <a name="an-error-occurred-while-remote-desktop-connection-was-accessing-this-resource-retry-the-connection-or-contact-your-system-administrator"></a>Der opstod en fejl, mens Fjernskrivebord havde adgang til denne ressource. Oprette forbindelse igen, eller kontakt systemadministratoren

Dette er en generisk fejlmeddelelse – kontakte support til Azure og eller sende os på [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com) så vi kan undersøge. 

![Generel Azure RemoteApp-meddelelse](./media/remoteapp-apptrouble/ra-apptrouble4.png) 