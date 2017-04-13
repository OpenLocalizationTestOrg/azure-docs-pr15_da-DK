<properties 
    pageTitle="Logføring til Machine Learning webtjenester | Microsoft Azure" 
    description="Lær at aktivere logføring til Machine Learning webtjenester. Logføring indeholder flere oplysninger om hjælp til fejlfinding af API'erne." 
    services="machine-learning" 
    documentationCenter="" 
    authors="raymondlaghaeian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data" 
    ms.date="10/05/2016"
    ms.author="raymondl;garye"/>

# <a name="enable-logging-for-machine-learning-web-services"></a>Aktivere logføring til Machine Learning webtjenester  

Dette dokument indeholder oplysninger om muligheden for logføring af klassisk webtjenester. Aktivere logføring i Web-tjenester giver ekstra oplysninger, ud over blot et fejlnummer og en meddelelse, der kan hjælpe dig med at løse dine opkald til Machine Learning API'er.  

Sådan aktiveres logføring i Web-tjenester på Azure klassisk portalen:   

1.  Log på [Azure klassisk portal](https://manage.windowsazure.com/)
2.  Klik på **Computer LEARNING**i kolonnen venstre funktioner.
3.  Klik på dit arbejdsområde, derefter **WEBTJENESTER**.
4.  Klik på navnet på webtjenesten, på listen Web services.
5.  Klik på navnet på listen slutpunkter.
6.  Klik på **KONFIGURER**.
7.  Angive **Diagnosticering SPORINGSNIVEAU** til *fejl* eller *alle*og derefter klikke på **Gem**.

For at aktivere logføring i portalen Azure Machine Learning Web Services.

1. Log på portalen [Azure Machine Learning Web Services](https://services.azureml.net) .
2. Klik på klassisk webtjenester.
3.  Klik på navnet på webtjenesten, på listen Web services.
4.  Klik på navnet på listen slutpunkter.
5.  Klik på **Konfigurer**.
6.  Indstille **logføring** til *fejl* eller *alle*, og klik derefter på **Gem**.

## <a name="the-effects-of-enabling-logging"></a>Virkningerne af aktivering af logføring

Når logføring er aktiveret, sammenkædet alle diagnosticering og fejl fra det valgte slutpunkt er logget på kontoen Azure-lager til brugerens arbejdsområde. Du kan se denne lagerplads konto i Azure klassisk portal dashboardvisning (nederst i sektionen hurtig Glance) af deres arbejdsområde.  

Loggene kan ses ved hjælp af de tilgængelige ' udforske ' en Azure-lager konto flere værktøjer. Den nemmeste muligvis blot Naviger til kontoen lagerplads på portalen Azure klassisk, og klik derefter på **beholdere**. Derefter vises der en objektbeholder med navnet **ml diagnosticering**. Denne beholder indeholder alle diagnosticeringsoplysninger for alle web service slutpunkterne for alle de arbejdsområder, der er knyttet til denne konto lagerplads. 
 
## <a name="log-blob-detail-information"></a>Blob logoplysninger

Hver blob i beholderen indeholder diagnosticering oplysninger til præcis et af følgende:

-   En udførelse af metoden batchen effektueret  
-   En udførelse af metoden anmodning-svar  
-   Initialisering af en anmodning om svar objektbeholder
  
Navnet på hver blob har et præfiks i formularen følgende: 

{Arbejdsområde Id}-{-webtjeneste Id}-{slutpunkt Id} / {Log type}  

Hvor logtype er et af følgende værdier:  

- batchen  
- resultat/anmodninger  
- resultat/initialisering  