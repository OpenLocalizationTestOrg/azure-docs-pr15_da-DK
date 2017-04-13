<properties
   pageTitle="Overvåge en Azure objektbeholder tjenesten klynge med Sysdig | Microsoft Azure"
   description="Overvåge en Azure objektbeholder tjenesten klynge med Sysdig."
   services="container-service"
   documentationCenter=""
   authors="rbitia"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Beholdere, DC/OS Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/08/2016"
   ms.author="t-ribhat"/>

# <a name="monitor-an-azure-container-service-cluster-with-sysdig"></a>Overvåge en Azure objektbeholder tjenesten klynge med Sysdig

I denne artikel vil vi installere Sysdig supportmedarbejdere i alle agent knuderne i din Azure objektbeholder tjenesten klynge. Du kan bruge en konto hos Sysdig til denne konfiguration. 

## <a name="prerequisites"></a>Forudsætninger 

[Implementer](container-service-deployment.md) og [forbinde](container-service-connect.md) en klynge af Azure objektbeholder tjenesten. Udforske den [Marathon Brugergrænsefladen](container-service-mesos-marathon-ui.md). Gå til [http://app.sysdigcloud.com](http://app.sysdigcloud.com) til at konfigurere en Sysdig skyen konto. 

## <a name="sysdig"></a>Sysdig

Sysdig er en overvågning tjeneste, hvor du kan overvåge dine objektbeholdere i din klynge. Sysdig kendes med hjælp til fejlfinding, men der er også din grundlæggende overvågning målepunkter for CPU, netværk, hukommelse og i/o. Sysdig gør det nemt at se, hvilke objektbeholdere arbejder det hardest eller grundlæggende bruger mest hukommelse og CPU. Denne visning er i afsnittet "Oversigt", som er i øjeblikket i beta. 

![Sysdig brugergrænseflade](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>Konfigurere en Sysdig-installation med Marathon

Disse trin viser dig, hvordan du konfigurerer og implementere Sysdig programmer til din klynge med Marathon. 

Få adgang til din DC/OS brugergrænseflade via [http://localhost:80 /](http://localhost:80/) én gang i Brugergrænsefladen DC/OS gå til "Universe", som er nederst til venstre, og Søg efter "Sysdig."

![Sysdig i DC/OS univers](./media/container-service-monitoring-sysdig/sysdig1.png)

Nu for at fuldføre konfigurationen skal du en Sysdig cloud-konto eller en gratis prøveversion konto. Når du er logget på webstedet Sysdig skyen, klik på dit brugernavn, og på siden skal du se afsnittet din "hurtigtast". 

![Sysdig API-nøgle](./media/container-service-monitoring-sysdig/sysdig2.png) 

Næste angive produktnøglen på adgang til Sysdig konfigurationen i DC/OS univers. 

![Konfiguration af Sysdig i DC/OS univers](./media/container-service-monitoring-sysdig/sysdig3.png)

Nu sæt forekomster til 10000000 så når der føjes en ny node til klynge Sysdig bliver automatisk installere en agent til den nye node. Dette er en midlertidig løsning at sikre, at Sysdig vil installere til alle nye supportmedarbejdere inden for klyngen. 

![Konfiguration af Sysdig i DC, OS univers-forekomster](./media/container-service-monitoring-sysdig/sysdig4.png)

Når du har installeret pakken gå tilbage til Sysdig UI, og du vil kunne undersøge forskellige brugen omfanget af objektbeholdere i din klynge. 