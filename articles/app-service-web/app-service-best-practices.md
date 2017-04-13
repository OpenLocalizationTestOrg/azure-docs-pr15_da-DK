<properties
    pageTitle="Bedste fremgangsmåder til Azure App Service"
    description="Lære om bedste praksis og fejlfinding for Azure App Service."
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/30/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="best-practices-for-azure-app-service"></a>Bedste fremgangsmåder til Azure App Service

I denne artikel opsummeres de bedste fremgangsmåder til brug af [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Outsourcing
Når Azure ressourcer ved at oprette en løsning som en WebApp og en database er placeret i forskellige områder kan effekterne omfatter følgende:

*  Øget ventetid i kommunikation mellem ressourcer
*  Valuta gebyrer for udgående data overføre kryds-område som angivet på [Azure priser side](https://azure.microsoft.com/pricing/details/data-transfers).

Outsourcing i samme område, der passer bedst til Azure ressourcer ved at oprette en løsning som en WebApp og en database eller lagerplads konto, der bruges til at holde indhold eller data. Når du opretter ressourcer bør du sørge for er de i samme Azure område, medmindre du har bestemte business eller designe årsag til dem ikke til at være. Du kan flytte en App Service-app til det samme område som databasen ved at udnytte [App Service klone funktion](app-service-web-app-cloning-portal.md) tilgængelige for øjeblikket til Premium App Service planlægge apps.   

## <a name="memoryresources"></a>Når apps forbruge mere hukommelse end forventet
Når du opdager en app forbruger mere hukommelse end forventet som angivet via overvågning eller tjeneste anbefalinger overveje [App Service automatisk reparation funktion](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). En af indstillingerne for funktionen automatisk reparation tager brugerdefinerede handlinger, der er baseret på en grænseværdi for hukommelse. Handlinger dække spektret fra besked via mail til undersøgelsen via lagring af hukommelse til på stedet afhjælpning ved at genbruge arbejdsprocessen. Automatisk reparation kan konfigureres via web.config og via en god brugergrænseflade, som beskrevet på i dette blogindlæg om [App Support websted udvidelse](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Når apps forbruge flere CPU end forventet
Når du ser en app forbruger flere CPU end forventet, eller der opstår gentages CPU spidser, som angivet via overvågning eller tjeneste anbefalinger Overvej skalering eller skalering af App-serviceaftale. Hvis dit program er statefull, er skalering den eneste mulighed, mens Hvis dit program er uden status, skalering ud kan give dig større fleksibilitet og højere skala potentielle. 

Du kan finde flere oplysninger om "statefull" vs "uden status" programmer kan du se denne video: [planlægge et SVG - til-slut med flere lag program på Microsoft Azure Web App](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Finde flere oplysninger om App skalering og Autoskalering Tjenesteindstillinger: [skala en Web App i Azure App Service](web-sites-scale.md).  

## <a name="socketresources"></a>Når socket ressourcer er opbrugt
En almindelig årsag for både er udmattende udgående TCP-forbindelser er brugen af klientbiblioteker, der ikke er implementeret genbruge TCP-forbindelser, eller hvis det protokol på et højere niveau som HTTP - heller ikke blive opgraderede. Gennemgå dokumentation for hver af de biblioteker, der refereres til af apps i din App Service planlægger at sikre, at de er konfigureret eller åbnet i din kode til effektiv genbrug af udgående forbindelser. Også følge vejledningen, bibliotek dokumentation til stort oprettelse og version eller oprydning for at undgå lækker forbindelser. Mens sådanne klient biblioteker undersøgelser er i kan gang virkning mindskes ved skalering af flere forekomster.  

## <a name="appbackup"></a>Når din app Sikkerhedskopiér starter ned
De to mest almindelige årsager hvorfor app sikkerhedskopiering opstår er: indstillinger for ugyldige lager og konfiguration af ugyldige database. Disse fejl sker typisk, når der er ændringer til lagerplads eller database ressourcer eller ændringer til at få adgang til disse ressourcer (fx legitimationsoplysninger opdateret til den database, der er valgt i indstillingerne for sikkerhedskopiering). Sikkerhedskopier typisk kører på en tidsplan og kræver adgang til lager (for skriver den sikkerhedskopierede filer) og databaser (for kopiering og læse indholdet skal medtages i sikkerhedskopien). Resultatet af ned til at få adgang til en af disse ressourcer, der ville være ensartet sikkerhedskopiering mislykkedes. 

Gennemse seneste resultater for at forstå, hvilken type fejl der sker, når sikkerhedskopiering mislykkede forsøg sker skal. I forbindelse med lagerplads access fejl, skal du gennemse og opdatere lagerplads indstillingerne i konfigurationen af sikkerhedskopien. Hvis det er database access fejl, skal du gennemse og opdatere dine forbindelser strenge som en del af indstillinger for; derefter fortsætte med at opdatere din sikkerhedskopi af en konfiguration for at medtage de nødvendige databaser korrekt. Du kan finde flere oplysninger om sikkerhedskopiering af app dokumentationen [sikkerhedskopiere en WebApp i Azure App Service](web-sites-backup.md) .

## <a name="nodejs"></a>Når nye Node.js apps er installeret på Azure App Service
Azure App standard tjenestekonfiguration for Node.js apps er beregnet på at bedst passer til behovet hos de mest almindelige apps. Hvis konfiguration for din Node.js app ville få glæde af tilpassede tilpasning for at forbedre ydeevnen eller optimere Ressourceforbrug for hukommelse-CPU/netværk ressourcer, kan du gennemse vores bedste praksis og fejlfindingstrin. I denne artikel dokumentation beskrives indstillingerne iisnode du måske brug at konfigurere for din Node.js app, beskrives de forskellige scenarier eller problemer, din app kan være modstående, og viser, hvordan du kan løse problemerne: [bedste praksis og fejlfinding i forbindelse med vejledning til Node programmer på Azure App Service](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md).   


