<properties
    pageTitle="Styre Azure web app trafik med Azure trafik Manager"
    description="Denne artikel indeholder oversigtsoplysningerne til Azure trafik Manager, som relaterer til Azure webapps."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    writer="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/25/2016"
    ms.author="cephalin"/>

# <a name="controlling-azure-web-app-traffic-with-azure-traffic-manager"></a>Styre Azure web app trafik med Azure trafik Manager

> [AZURE.NOTE] Denne artikel indeholder oversigtsoplysningerne til Microsoft Azure trafik Manager, som relaterer til Azure App Service Web Apps. Flere oplysninger om Azure trafik Manager selve kan findes ved at besøge linkene i slutningen af denne artikel.

## <a name="introduction"></a>Introduktion
Du kan bruge Azure trafik Manager til at styre, hvordan anmodninger fra Webklienter fordeles på webapps i Azure App Service. Når web app slutpunkter er føjet til en Azure trafik Manager profil, Azure trafik Manager holder styr på status for dine webapps (kører, holdt op med at eller slettede) så den kan bestemme, hvilken af disse slutpunkter skal modtage trafik.

## <a name="load-balancing-methods"></a>Indlæse justering af belastning metoder
Azure trafik Manager bruger tre forskellige Indlæs justering af belastning metoder. Disse er beskrevet i følgende liste, som de vedrører Azure webapps.

* **Failover**: Hvis du har web app Kopier i forskellige områder, du kan bruge denne metode til at konfigurere en WebApp, hvis du vil vedligeholde al web klienttrafik og konfigurere en anden WebApp i et andet område til at vedligeholde pågældende trafik i tilfælde af den første WebApp bliver utilgængeligt.

* **Afrund Robin**: Hvis du har web app Kopier i forskellige områder, kan du bruge denne metode til at distribuere trafik lige på tværs af webapps i forskellige områder.

* **Ydeevnen**: ydeevnen i feltet metode spredes trafik baseret på den kortest returkørsel til klienter. Metoden ydeevnen kan bruges til webapps i det samme område eller i forskellige områder.

##<a name="web-apps-and-traffic-manager-profiles"></a>Webapps og trafik Manager profiler
For at konfigurere kontrol af web app trafik skal du oprette en profil i Azure trafik Manager, der, bruger en af tre indlæse justering af belastning metoder er beskrevet tidligere, og derefter tilføje de slutpunkter (i dette tilfælde online), du vil styre trafik til profilen. Web app status (kører, ikke længere eller slettet) er regelmæssigt meddeles profilen, så Azure trafik Manager kan dirigere trafik i overensstemmelse hermed.

Når du bruger Azure trafik Manager med Azure, skal du huske følgende punkter:

* Web app kun installationer inden for samme region indeholder Onlines allerede for failover og round robin-funktionalitet uden hensyn til web app-tilstand.

* For installationer i samme område, der bruger Web Apps sammen med en anden Azure skybaseret tjeneste, kan du kombinere begge typer slutpunkter for at aktivere hybrid scenarier.

* Du kan kun angive et web app-slutpunkt per område i en profil. Når du vælger en online som et slutpunkt for ét område, bliver de resterende webapps i det pågældende område ikke tilgængelig for markerede for profilen.

* Web app-slutpunkter, du angiver i en Azure trafik Manager profil, vises den under afsnittet **Domain Names** på siden Konfigurer for online i profilen, men kan ikke konfigureres der.

* Når du har tilføjet en web-app til en profil, vises **Webstedets URL-adresse** på dashboardet til af det online portalen siden eget domæne URL-adressen af WebApp, hvis du har konfigureret et. Ellers vises trafik Manager profil URL-adressen (for eksempel `contoso.trafficmgr.com`). Begge direkte domænenavnet på WebApp og trafik Manager URL-adressen kan ses på den online Konfigurer side under afsnittet **Domain Names** .

* Din brugerdefinerede domænenavne, fungerer som forventet, men ud over at føje dem til din online, skal du også konfigurere DNS-kortet til at pege på trafik Manager URL-adressen. Oplysninger om, hvordan du konfigurerer et brugerdefineret domæne til en Azure web-app, kan du se [konfigurere et brugerdefineret domænenavn til en Azure-webstedet](web-sites-custom-domain-name.md).

* Du kan kun tilføje webapps, der er i standardtilstand til en Azure trafik Manager profil.

## <a name="next-steps"></a>Næste trin

Du kan finde en oversigt over og en teknisk oversigt over Azure trafik Manager, [Trafik Manager oversigt](../traffic-manager/traffic-manager-overview.md).

Du kan finde flere oplysninger om at bruge trafik Manager med Web Apps, kan du se blogindlæg [Ved hjælp af Azure trafik Manager med Azure websteder](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) og [Azure trafik Manager kan nu integrere med Azure websteder](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).
