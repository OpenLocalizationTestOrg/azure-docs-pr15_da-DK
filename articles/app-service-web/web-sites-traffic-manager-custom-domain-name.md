<properties
    pageTitle="Konfigurere et brugerdefineret domænenavn til en WebApp i Azure App-tjeneste, som bruger trafik Manager til justering af belastning."
    description="Bruge et brugerdefineret domænenavn til en en WebApp i Azure App Service, der omfatter trafik Manager for justering af belastning."
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Konfiguration af et brugerdefineret domænenavn til en WebApp i Azure App Service ved hjælp af trafik Manager

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

I denne artikel indeholder en generel vejledning til brug af et brugerdefineret domænenavn med Azure App Service, bruger trafik Manager til justering af belastning.

[AZURE.INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
## <a name="understanding-dns-records"></a>Forstå DNS-poster

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>
## <a name="configure-your-web-apps-for-standard-mode"></a>Konfigurere din webapps til standardtilstand

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>
## <a name="add-a-dns-record-for-your-custom-domain"></a>Tilføje en DNS-post til dit eget domæne

> [AZURE.NOTE] Hvis du har købt domæne via Azure App Service Web Apps springe følge trin, og refererer til det sidste trin af [Købe domæne til online](custom-dns-web-site-buydomains-web-app.md) -artiklen.

Hvis du vil knytte dit eget domæne med en WebApp i Azure App Service, skal du tilføje en ny post i tabellen DNS for dit brugerdefinerede domæne ved hjælp af værktøjer, der leveres af domæneregistratoren, du har købt dit domænenavn fra. Brug følgende trin til at finde og bruge værktøjerne i DNS.

1. Log på din konto hos din domæneregistrator, og se efter en side til administration af DNS-poster. Se efter links eller områder på webstedet mærket **Domænenavn**, **DNS**eller **Name Server Management**. Ofte et link til denne side kan findes være visning af dine kontooplysninger, og derefter leder du efter et link som **My domains**.

1. Når du har fundet siden til administration af til dit domænenavn, skal du se efter et link, hvor du kan redigere DNS-poster. Det kan være angivet som en **Zone file** **DNS-poster**, eller som et link af typen **Avanceret** konfiguration.

    * Siden sandsynligvis har nogle poster, der allerede har oprettet, som en post at tilknytte '**@**'eller'\*' med en 'domæne parkering' side. Det kan også indeholde poster for almindelige underdomæner som **www**.
    * Siden omtale **CNAME-poster**, eller angive en rullemenu for at vælge en posttype. Det kan også angives andre poster som **en poster** og **MX-poster**. I nogle tilfælde vil blive ringet CNAME-poster af andre navne som et **Alias Record**.
    * Siden får også felter, der gør det muligt at **kort** fra et **værtsnavn** eller **domænenavn** til et andet domænenavn.

1. Mens detaljerne for hver registrator, varierer generelt du tilknytte *fra* dit brugerdefinerede domænenavn (såsom **contoso.com**,) *til* trafik Manager domænenavnet (**contoso.trafficmanager.net**), der bruges til din online.

    > [AZURE.NOTE] Hvis en post er allerede i brug, og du vil binde overskrides dine apps til det, kan du også oprette en ekstra CNAME-post. Eksempelvis hvis du vil binde overskrides **www.contoso.com** til din online, oprette en CNAME-post fra **awverify.www** til **contoso.trafficmanager.net**. Du kan derefter føje "www.contoso.com" til din online uden at ændre "www" CNAME-posten. Få mere at vide under [oprette DNS-poster til en WebApp i et brugerdefineret domæne][CREATEDNS].

1. Når du er færdig med at tilføje eller redigere DNS-poster hos din domæneregistrator, kan du gemme ændringerne.

<a name="enabledomain"></a>
## <a name="enable-traffic-manager"></a>Aktivere trafik Manager

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Næste trin

Du kan finde yderligere oplysninger finder [Node.js Developer Center](/develop/nodejs/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
