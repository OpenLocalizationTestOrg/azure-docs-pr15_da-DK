<properties
   pageTitle="Overvåge en Azure objektbeholder tjenesten klynge med Datadog | Microsoft Azure"
   description="Overvåge en Azure objektbeholder tjenesten klynge med Datadog. Brug webdelen DC/OS Brugergrænsefladen skal installeres Datadog supportmedarbejdere til din klynge."
   services="container-service"
   documentationCenter=""
   authors="rbitia"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Beholdere, DC/OS, Docker sværme Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure"   
   ms.date="07/28/2016"
   ms.author="t-ribhat"/>

# <a name="monitor-an-azure-container-service-cluster-with-datadog"></a>Overvåge en Azure objektbeholder tjenesten klynge med Datadog

I denne artikel vil vi installere Datadog supportmedarbejdere i alle agent knuderne i din Azure objektbeholder tjenesten klynge. Du skal en konto hos Datadog for denne konfiguration. 

## <a name="prerequisites"></a>Forudsætninger 

[Implementer](container-service-deployment.md) og [forbinde](container-service-connect.md) en klynge af Azure objektbeholder tjenesten. Udforske den [Marathon Brugergrænsefladen](container-service-mesos-marathon-ui.md). Gå til [http://datadoghq.com](http://datadoghq.com) til at konfigurere en Datadog-konto. 

## <a name="datadog"></a>Datadog 

Datadog er en overvågning tjeneste, der samler overvågningsdata fra dine objektbeholdere i din Azure objektbeholder tjenesten klynge. Datadog har et Docker Integration Dashboard, hvor du kan se bestemte målepunkter i dine objektbeholdere. Målepunkter indsamlede fra dine objektbeholdere er organiseret efter CPU, hukommelse, Netværks- og i/o. Datadog opdeler målepunkter i beholdere og billeder. Et eksempel på hvordan Brugergrænsefladen ser ud for CPU-brug er nedenfor.

![Datadog brugergrænseflade](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Konfigurere en Datadog-installation med Marathon

Disse trin viser dig, hvordan du konfigurerer og implementere Datadog programmer til din klynge med Marathon. 

Få adgang til din DC/OS brugergrænseflade via [http://localhost:80 /](http://localhost:80/). Én gang i Brugergrænsefladen DC/OS gå til den "univers", som er nederst til venstre, og Søg efter "Datadog", og klik på "Installer".

![Datadog pakke i DC/OS univers](./media/container-service-monitoring/datadog1.png)

Nu for at fuldføre konfigurationen skal du en Datadog-konto eller en gratis prøveversion konto. Når du er logget på Datadog websted udseende til venstre, og gå til integrationer -> derefter API'EN. 

![Datadog API-nøgle](./media/container-service-monitoring/datadog2.png)

Derefter skal du angive din API-nøgle til Datadog konfigurationen i DC/OS univers. 

![Konfiguration af Datadog i DC/OS univers](./media/container-service-monitoring/datadog3.png) 

I ovenstående konfigurationen forekomster er angivet til 10000000 så hver gang en ny node føjes til klyngen Datadog automatisk installere en agent til denne node. Dette er en midlertidig løsning. Når du har installeret pakken, skal du gå tilbage til webstedet Datadog og finde "Dashboards." Derfra kan du se brugerdefineret og Integration Dashboards. Dashboardet Docker Integration har alle de objektbeholder før mailadvarslen sendes du har brug for til at overvåge din klynge. 
