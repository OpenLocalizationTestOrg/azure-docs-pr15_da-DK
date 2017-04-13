<properties
   pageTitle="Logik Apps lokale gateway dataforbindelse | Microsoft Azure"
   description="Oplysninger om, hvordan du opretter en forbindelse til gatewayen lokale data fra en logik app."
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# <a name="connect-to-the-on-premises-data-gateway-for-logic-apps"></a>Oprette forbindelse til den lokale datastyringsgateway for logik Apps

Understøttede logik apps forbindelser gør det muligt at konfigurere forbindelsen til access lokale data via gatewayen lokale data.  Følgende trin fører dig gennem hvordan du installerer og konfigurerer gatewayen lokale data til at arbejde med en logik app.

## <a name="prerequisites"></a>Forudsætninger

* Skal ved hjælp af en arbejde eller skole mailadresse i Azure knytte gatewayen lokale data til din konto (Azure Active Directory baseret konto)
    * Hvis du bruger en Microsoft-Account (fx @outlook.com, @live.com) du kan bruge din Azure-konto til at oprette en arbejds- eller skolemailadresse ved at [følge trinnene her](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal)

> [AZURE.WARNING] Er der en begrænsning i øjeblikket, lokale gateway Installer fuldfører kun, når du bruger en konto, der er registreret med Power BI.  I mellemtiden Registrer en konto med "Power BI gratis" installationen er fuldført.

* Være lokale datastyringsgateway [installeret på en lokal computer](app-service-logic-gateway-install.md).
* Gatewayen skal ikke er blevet krav ved en anden Azure lokale datastyringsgateway ([gøre krav på sker der med oprettelse af trin 2 under](#2-create-an-azure-on-premises-data-gateway-resource)) – en installation kan kun knyttes til én gateway ressource.

## <a name="installing-and-configuring-the-connection"></a>Installation og konfiguration af forbindelsen

### <a name="1-install-the-on-premises-data-gateway"></a>1. installere gatewayen lokale data

Oplysninger om installation af gatewayen lokale data kan findes [i denne artikel](app-service-logic-gateway-install.md).  Gatewayen skal være installeret på en lokal maskine, før du kan fortsætte med resten af trinnene.

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2. Opret en Azure lokale datastyringsgateway ressource

Når installeret, skal du knytte abonnementet Azure til gatewayen lokale data.

1. Logge på ved hjælp af den samme arbejds- eller skolemailadresse, der blev brugt under installationen af gatewayen Azure
1. Klik på knappen **Ny** ressource
1. Søg og vælg den **lokale datastyringsgateway**
1. Udfyld derefter oplysningerne for at knytte gatewayen til din konto – herunder at vælge den relevante **Installation navn**

    ![Lokal Gateway dataforbindelse][1]
1. Klik på knappen **Opret** for at oprette ressourcen

### <a name="3-create-a-logic-app-connection-in-the-designer"></a>3. oprette en logik app forbindelse i designer

Nu, hvor abonnementet Azure er knyttet til en forekomst af gatewayen lokale data, kan du oprette en forbindelse til den fra i en logik app.

1. Åbn en logik app og vælge en forbindelse, der understøtter lokal forbindelse (og denne skriver SQL Server)
1. Markér afkrydsningsfeltet for **Opret forbindelse via lokale datastyringsgateway**

    ![Oprettelse af logik App Designer Gateway][2]
1. Vælg **Gateway** til at oprette forbindelse til og fuldføre eventuelle nødvendige forbindelsesoplysninger
1. Klik på **Opret** for at oprette forbindelsen

Forbindelsen skal nu være konfigureret til brug i din logik app.  

## <a name="next-steps"></a>Næste trin
- [Almindelige eksempler og scenarier, hvor logik apps](app-service-logic-examples-and-scenarios.md)
- [Enterprise funktioner for klientintegration](app-service-logic-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/app-service-logic-gateway-connection/createblade.PNG
[2]: ./media/app-service-logic-gateway-connection/blankconnection.PNG
[3]: ./media/app-service-logic-gateway-connection/checkbox.PNG