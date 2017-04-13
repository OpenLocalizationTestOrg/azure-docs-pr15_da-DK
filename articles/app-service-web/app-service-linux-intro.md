<properties 
    pageTitle="Introduktion til App-tjenesten på Linux | Microsoft Azure" 
    description="Få mere at vide om App Service på Linux." 
    keywords="Azure-app-tjenesten, linux, oss"
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="naziml"/>

# <a name="introduction-to-app-service-on-linux"></a>Introduktion til App-tjenesten på Linux
App Service på Linux er i øjeblikket i offentlige Preview og understøtter igangværende Onlines oprindeligt på Linux. 

## <a name="overview"></a>Oversigt ##
Kunder kan bruge App Service på Linux til host webapps oprindeligt på Linux til understøttede programmet stakke. Følgende funktioner afsnit viser de aktuelt understøttede programmet stakke.

## <a name="features"></a>Funktioner ##
App Service på Linux understøtter i øjeblikket de følgende programmet stakke

- Node.js
- PHP

Du kan implementere deres programmer, der bruger

- FTP.
- Lokale ciffer.
- GitHub eller BitBucket.

For programmet skalering


- Kunder kan skalere deres online op og ned ved at ændre niveauet i planlægge deres App-tjenesten. 
- Kunder kan skalere deres programmer ud ud og køre deres app på tværs af flere forekomster inden for rammerne af deres SKU.

For Kudu fungerer nogle af de grundlæggende funktioner

- Miljø.
- Installationer.
- Grundlæggende console.

## <a name="limitations"></a>Begrænsninger ##

Portalen Azure management kan kun vise aktuelt understøttede funktioner til App-tjenesten på Linux og skjule resten. Som vores team aktivere flere funktioner vil vi beholde reflektere over dette portalen management. Nogle funktioner som VNET integration og AAD / tredjeparters godkendelse eller Kudu websted filtypenavne virker aktuelt ikke. Men som vi få disse arbejde vi opdaterer vores dokumentation og blog om ændringer.

Denne offentlige preview er tilgængelig i øjeblikket kun i følgende områder

-   Vest USA.
-   Vest Europe.
-   Sydøstasien.

Online på Linux understøttes kun i dedikeret App serviceplaner og har ikke et gratis eller delt trin. Desuden udelukker app tjenesteplanerne for almindelige og Linux Onlines, så du ikke kan oprette en Linux WebApp i en ikke-Linux app-serviceaftale.

Online på Linux skal oprettes i en ressourcegruppe, ikke som indeholder ikke Linux webapps i det samme område.

På grund af manglende overlappede genbrug af de forskellige webapps, bør kunder forventer en lille nedetid i tilfælde af en WebApp har genstartet. 

## <a name="next-steps"></a>Næste trin ##

Følge linkene nedenfor for at komme i gang med App-tjenesten på Linux. Sende spørgsmål og problemer på [vores-forummet](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Oprette Onlines i App-tjenesten på Linux](./app-service-linux-how-to-create-a-web-app.md)
* [Ved hjælp af PM2 konfiguration til Node.js i Webapps på Linux](./app-service-linux-using-nodejs-pm2.md)

