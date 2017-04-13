<properties
    pageTitle="Sådan konfigureres en App Service miljø | Microsoft Azure"
    description="Konfiguration, administration og overvågning af App Service miljøer"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="ccompy"/>


# <a name="configuring-an-app-service-environment"></a>Konfigurere en App Service-miljø #

## <a name="overview"></a>Oversigt ##

På et højt niveau består et Azure App servicemiljø af flere vigtigste komponenter:

- Beregne ressourcer, der kører i tjenesten App servicemiljø hostet
- Lagerplads
- En database
- Et Classic(V1) eller ressource Manager(V2) Azure virtuelt netværk (VNet) 
- Et undernet med App-tjenesten miljø hostet tjenesten kører i den

### <a name="compute-resources"></a>Beregne ressourcer

Du kan bruge Beregn ressourcer for dine fire ressourcepuljer.  Hver App Service miljø (ASE) har et sæt af front ender og tre mulige arbejder grupper. Du behøver ikke at bruge alle tre arbejder grupper – Hvis du vil, kan du kun bruge én eller to.

Hosts i ressourcepuljer (front ender og medarbejdere) er ikke direkte adgang til lejere. Du kan ikke bruge RDP Remote Desktop Protocol () til at oprette forbindelse til dem, ændre deres klargøring, eller du kan fungere som administrator for dem.

Du kan angive ressource puljen antal og størrelse. I en ASE har du fire størrelsesindstillinger for, som er mærket P1 gennem P4. Få mere at vide om disse størrelser og deres priser se [App Service priser](../app-service/app-service-value-prop-what-is.md).
Ændre det antal eller størrelse, kaldes en handling af skala.  Kun én skala operation kan være i gang ad gangen.

**Forside slutter**: forside enderne er HTTP/HTTPS-slutpunkter for dine apps, der afholdes i din ASE. Du løber ikke arbejdsmængder i front enderne.

- En ASE starter med to P2s, som er tilstrækkelige til Udviklingscenter/afprøve arbejdsmængder og lavt niveau fremstilling arbejdsmængder. Vi anbefaler P3s for moderat til tunge fremstilling arbejdsbelastninger.
- Vi anbefaler, at du har mindst fire P3s at sikre, at der er tilstrækkeligt forside ender kører, når der udsendes planlagt vedligeholdelse for moderat til tunge fremstilling arbejdsbelastninger. Planlagt vedligeholdelsesaktiviteter sænkes én front-end ad gangen. Dette reducerer de samlede tilgængelige front end kapacitet under vedligeholdelsesaktiviteter.
- Du kan ikke tilføje en ny forekomst af front end med det samme. De kan tage mellem 2 til 3 timer at blive klargjort.
- For yderligere skala finjustere, skal du overvåge CPU procentdel, hukommelse procentdel og aktive anmodninger omfanget af front end-puljen. Hvis CPU eller hukommelse procenter er over 70%, når du kører P3s, kan du tilføje flere forside ender. Hvis værdien aktive anmodninger beregner gennemsnittet til 15.000 på 20.000 anmodninger per front-end, bør du også tilføje flere forside ender. Det overordnede mål er at forhindre CPU og hukommelse procentdele nedenfor 70%, og aktive anmodninger gennemsnittet til under 15.000 anmodninger per foran afslutte, når du kører P3s.  

**Medarbejdere**: medarbejderne er, hvor dine apps faktisk køres. Når du skalere af din App Service-planer, bruger, der op medarbejdere i gruppen tilknyttet arbejder.

- Du kan ikke tilføje medarbejdere med det samme. De kan udføre fra 2 til 3 timer at blive klargjort, uanset hvor mange der tilføjes.
- Skalering størrelsen på en Beregn ressource til en hvilken som helst puljen tager 2 til 3 timer hver opdatering domæne. Der er 20 opdatering domæner i en ASE. Hvis du skaleres Beregn størrelsen på en kollega pulje med 10 forekomster, kan det tage mellem 20-30 timer at gennemføre.
- Hvis du ændrer størrelsen på de Beregn ressourcer, der bruges i en kollega puljen, medfører kolde starter Apps, der kører i puljen, der arbejder.

Den hurtigste måde at ændre Beregn ressource størrelsen på en kollega pulje, der ikke kører nogen apps er at:

- Indskrænke forekomst antallet til 0. Det tager om 30 minutter for at deallokere din forekomster.
- Vælg ny Beregn størrelse og antallet af forekomster. Det tager herfra mellem 2 til 3 timer at gennemføre.

Hvis dine apps kræver en ressource større Beregn, kan du drage fordel af de forrige retningslinjer. I stedet for at ændre størrelsen på arbejder puljen, der vært for disse apps, kan du udfylde en anden arbejder gruppe med medarbejdere på den ønskede størrelse og flytte dine apps over til denne gruppe.

- Oprette flere forekomster af den nødvendige Beregn størrelse i en anden arbejder gruppe. Dette vil tage fra 2 til 3 timer at gennemføre.
- Tildele din App Service-planer, der er vært for de apps, der har brug for en større størrelse til lige er blevet konfigureret arbejder puljen. Dette er en hurtig handling, der fører mindre end et minut at fuldføre.  
- Indskrænke første arbejder puljen, hvis du ikke længere har brug for disse ubrugte forekomster. Denne handling tager om 30 minutter for at gennemføre.

**Autoskalering**: en af de værktøjer, der kan hjælpe dig med at administrere din Beregn Ressourceforbrug er Autoskalering. Du kan bruge Autoskalering til front end- eller arbejder grupper. Du kan udføre ting som øge din forekomster af enhver puljetype morgen og reducere det i aftenen. Eller måske kan du tilføje forekomster, når antallet medarbejdere, der er tilgængelige i en kollega pulje falder under en bestemt grænse.

Hvis du vil angive Autoskalering regler omkring Beregn ressource puljen målepunkter, og klik derefter huske på det tidspunkt, hvor klargøringen kræver. Få mere at vide om Autoskalering App Service-miljøer, se, [hvordan du konfigurere Autoskalering i en App servicemiljø][ASEAutoscale].

### <a name="storage"></a>Lagerplads

Hver ASE er konfigureret med 500 GB lagerplads. Dette område bruges på tværs af alle programmer i ASE. Denne lagerplads er en del af ASE og i øjeblikket ikke kan ændres for at bruge din lagerplads. Hvis du vil foretage justeringer til distribution af virtuelt netværk eller sikkerhed, skal du stadig tillade adgang til Azure-lager – eller ASE kan ikke fungere.

### <a name="database"></a>Database

Databasen indeholder oplysninger, der definerer miljøet samt oplysninger om de apps, der kører i modellen. Dette er også en del af abonnementet Azure-opbevares. Det er ikke noget, du har en direkte muligheden for at ændre. Hvis du vil foretage justeringer til distribution af virtuelt netværk eller sikkerhed, skal du stadig tillade adgang til SQL Azure - eller ASE kan ikke fungere.

### <a name="network"></a>Netværk

Den VNet, der bruges sammen med din ASE kan være et, du har foretaget, da du oprettede ASE eller et, du har foretaget forvejen. Når du opretter undernettet under oprettelse af ASE, tvinger ASE skal være i samme ressourcegruppe som det virtuelle netværk. Hvis du har brug for den ressourcegruppe, der anvendes af din ASE til at være anderledes end din VNet, skal du oprette din ASE ved hjælp af en ressource manager skabelon.

Er der nogle begrænsninger for det virtuelle netværk, der bruges til en ASE:
 
- Det virtuelle netværk skal være et internationale virtuelt netværk.
- Der skal være et undernet med 8 eller flere adresser, hvor ASE er installeret.
- Når et undernet bruges til at være vært for en ASE, kan ikke ændres adresseområde på undernettet. Derfor anbefaler vi, at undernettet indeholder mindst 64-adresser for at tage højde for en hvilken som helst fremtidig vækst af ASE.
- Der kan du ikke noget mere i undernettet, men ASE.

I modsætning til tjenesten hostet, der indeholder ASE, [virtuelt netværk] [ virtualnetwork] og undernet er under brugerens kontrol.  Du kan administrere netværket virtuel via virtuelle netværk brugergrænseflade eller PowerShell.  En ASE kan installeres i en Classic eller ressource Manager VNet.  Portalen og API oplevelser er en smule anderledes mellem klassisk og ressourcestyring VNets, men ASE-oplevelsen er den samme.

Den VNet, der bruges til at hoste en ASE kan bruge enten private RFC1918 IP-adresser eller den kan bruge offentlige IP-adresser.  Hvis du vil bruge en IP-område, der ikke er omfattet af RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) og derefter skal du oprette din VNet og undernet, der skal bruges i din ASE forud for oprettelse af ASE.

Da denne egenskab placerer tjenesten Azure-App til netværket virtuel, betyder det, at dine apps, der er placeret i din ASE har nu adgang til ressourcer, der er foretaget gennem ExpressRoute eller websted til websted virtuelt privat netværk (VPN) direkte. De apps, der er i dit App servicemiljø kræver ikke yderligere netværksfunktioner til at få adgang til ressourcer, der er tilgængelige for det virtuelle netværk, der vært for din App Service-miljø. Det betyder, at du ikke behøver at bruge VNET Integration eller Hybrid forbindelser til at få adgang til ressourcer i eller have forbindelse til netværket virtuel. Du kan stadig bruge begge disse funktioner selvom få adgang til ressourcer i netværk, der ikke har forbindelse til netværket virtuel.

Du kan for eksempel bruge VNET Integration du integrere systemet med et virtuelt netværk, der er i dit abonnement, men ikke er forbundet med det virtuelle netværk, som din ASE er i. Du kan også stadig bruge Hybrid forbindelser til at få adgang til ressourcer, der findes i andre netværk, ligesom du kan normalt.  

Hvis du har netværket virtuel konfigureret med en ExpressRoute VPN, skal du være opmærksom på nogle af de routing behov, der indeholder en ASE. Der er nogle brugerdefinerede rute (UDR) konfigurationer, der er inkompatible med en ASE. Yderligere oplysninger om at køre en ASE i et virtuelt netværk med ExpressRoute, du [kører en App servicemiljø i et virtuelt netværk med ExpressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Sikring af indgående trafik

Der findes to primære metoder til at styre indgående trafik til dit ASE.  Du kan bruge netværk sikkerhed Groups(NSGs) til at styre, hvilke IP adresser kan få adgang til din ASE, som beskrevet her, [hvordan du kan styre indgående trafik i en App Service-miljø](app-service-app-service-environment-control-inbound-traffic.md) , og du kan også konfigurere din ASE med en intern Indlæs Balancer(ILB).  Disse funktioner kan også bruges sammen, hvis du vil begrænse adgangen ved hjælp af NSGs til din ILB ASE.

Når du opretter en ASE, oprettes der en VIP i din VNet.  Der findes to typer af VIP, eksterne og interne.  Når du opretter en ASE med en ekstern VIP vil dine apps i din ASE være tilgængelige via en internet distribueret IP-adresse. Når du vælger interne din ASE vil blive konfigureret med en ILB og vil ikke være direkte internet, der er tilgængelige.  En ILB ASE stadig kræver en ekstern VIP, men den bruges kun til Azure administration og vedligeholdelse adgang.  

Under oprettelse af ILB ASE du giver det underdomæne, der bruges af ILB ASE og nødt til at administrere dine egne DNS-Posterne for det underdomæne, du angiver.  Da du angive underdomænenavnet skal du også administrere certifikatet, der bruges til HTTPS-adgang.  Du bliver bedt om at angive certifikatet efter oprettelse af ASE.  For at lære mere om oprettelse og brug af en ILB ASE læsevenlig [ved hjælp af en intern justering af belastning, miljøet App Service][ILBASE]. 


## <a name="portal"></a>Portal

Du kan administrere og overvåge dit App Service-miljø ved hjælp af Brugergrænsefladen i portalen Azure. Hvis du har en ASE, er du sandsynligvis at se symbolet App Service på dit margentekst. Dette symbol bruges til at repræsentere App Service miljøer i portalen Azure:

![App-tjenesten miljø symbol][1]

For at åbne den brugergrænseflade, der indeholder alle dine App Service-miljøer, kan du bruge ikonet eller vælge vinklen (">" symbol) nederst i sidepanelet til at vælge App Service miljøer. Ved at vælge en af de ASEs, der er angivet, kan du åbne den brugergrænseflade, der bruges til at overvåge og administrere den.

![Brugergrænseflade til overvågning og administration af din App Service-miljø][2]

Denne første blade viser nogle egenskaber for din ASE sammen med et metriske diagram per ressourcepulje. Nogle af de egenskaber, der vises i **grundlæggende om** blokering er også hyperlinks, der åbnes af bladet, der er knyttet til den. For eksempel kan du vælge navnet på det **Virtuelle netværk** til at åbne den brugergrænseflade, der er knyttet til det virtuelle netværk, der kører din ASE i. Åbne **App Service planer** og **Apps** blade, hvor der står disse elementer, der findes i din ASE.  

### <a name="monitoring"></a>Overvågning

Diagrammerne gør det muligt at se et udvalg af ydeevne målepunkter i hver enkelt ressourcepulje. Du kan overvåge gennemsnitlige CPU'EN og hukommelsen til front end-puljen. Du kan overvåge det antal, der bruges og det antal, der er tilgængelige for arbejder grupper.

Flere App Service planer kan gøre brug af medarbejdere i en kollega gruppe. Arbejdsbelastningen distribueres ikke på samme måde som med front end-servere, så brugen af CPU og hukommelse giver ikke nogen meget forstyrrende nyttige oplysninger. Det er vigtigere til at registrere hvor mange medarbejdere, du har brugt og er tilgængelige –, især hvis du selv administrerer dette system for andre til brug.  

Du kan også bruge alle målene, der skal følges i diagrammerne til at konfigurere beskeder. Konfigurere beskeder her fungerer på samme måde som et andet sted i App-tjeneste. Du kan angive en besked fra **beskeder** Brugergrænsefladen del eller analysere i en hvilken som helst målepunkter Brugergrænsefladen og vælge **Tilføj påmindelse**.

![Målepunkter brugergrænseflade][3]

Målene, der lige blev beskrevet er App servicemiljø målepunkter. Der er også målepunkter, der er tilgængelige på niveauet App Service plan. Dette er hvor overvågning CPU og hukommelse gør en masse mening.

I en ASE er alle App Service planerne dedikeret App Service planer. Det betyder, at de kun apps, der kører på hosts tildeles, App-serviceaftale er apps i serviceaftale, som App. For at se oplysninger i din App Service-plan, skal du få vist din App-serviceaftale, fra en af listerne i ASE Brugergrænsefladen eller **Gennemse App Service plan** (som viser dem alle).   

### <a name="settings"></a>Indstillinger

I bladet ASE er der en **Indstillinger** sektion, der indeholder flere vigtige funktioner:

**Indstillinger for** > **Egenskaber**: bladet **Indstillinger** åbnes automatisk, når du få vist din ASE blade. Er **Egenskaber**øverst. Der findes en antallet af elementer i her, der er overflødige hvad du kan få vist i **Essentials**, men hvad er meget nyttig er **Virtuelle IP-adresse**, samt **Udgående IP-adresser**.

![Indstillinger for blade og egenskaber][4]

**Indstillinger for** > **IP-adresser**: Når du opretter en IP-Secure Sockets Layer (SSL)-app i dit ASE, du har brug for en SSL IP-adresse. For at få et, skal din ASE SSL IP-adresser, som den ejer, som kan allokeres. Når der oprettes en ASE, der er én SSL IP-adresse til dette formål, men du kan tilføje mere. Der er et gebyr for ekstra IP SSL-adresser, som vist i [App Service priser] [ AppServicePricing] (i sektionen på SSL-forbindelser). Den ekstra pris er IP SSL pris.

**Indstillinger for** > **Front End-puljen** / **Arbejder grupper**: hver af disse ressource puljen blade giver mulighed for at se oplysninger kun på ressourcepuljen, ud over at give kontrolelementer for at tilpasse fuldt pågældende ressourcepulje.  

Bladet base for hver enkelt ressourcepulje indeholder et diagram med målepunkter til pågældende ressourcepulje. Ligesom med diagrammer fra bladet ASE, kan du gå til diagrammet og konfigurere beskeder efter behov. Indstille en besked fra bladet ASE for en bestemt ressourcepulje gør det samme som at gøre det fra ressourcepuljen. Du har adgang til alle Apps eller App Service-planer, der kører i denne arbejder pulje bladet arbejder puljen **Indstillinger** .

![Arbejder puljen indstillinger for Brugergrænsefladen][5]

### <a name="portal-scale-capabilities"></a>Portalen skala funktioner  

Der er tre skala handlinger:

- Ændre antallet af IP-adresser i ASE, der er tilgængelige for at se IP SSL-formatet.
- Ændre størrelsen på Beregn ressourcen, der bruges i en ressourcepulje.
- Ændre antallet af Beregn ressourcer, der bruges i en ressourcepulje, enten manuelt eller via Autoskalering.

I portalen er der tre måder til at styre, hvor mange servere, som du har i din ressourcepuljer:

- Handlingen skala fra den primære ASE blade øverst. Du kan foretage flere skala ændringer i konfigurationen til front end- og arbejder grupper. De anvendes alle som en enkelt handling.
- En Manuel skala handling fra enkelt ressource puljen **skala** blade, som er under **Indstillinger**.
- Autoskalering, som du har angivet fra bladet enkelt ressource puljen **skala** .

Træk i skyderen til det antal, du vil, og Gem for at bruge handlingen skala på bladet ASE skal. Denne brugergrænseflade understøtter også ændring af størrelsen.  

![Skala brugergrænseflade][6]

Hvis du vil bruge funktionerne i manualen eller Autoskalering i en bestemt ressourcepulje, skal du gå til **Indstillinger** > **Front End-puljen** / **Arbejder grupper** efter behov. Åbn derefter op puljen, du vil ændre. Gå til **Indstillinger** > **Skaler ud** eller **Indstillinger for** > **skalere op**. Bladet **Skala ud** gør det muligt at styre forekomst antal. Gør det muligt at kontrollere ressourcen størrelse **Skala op** .  

![Tilpasse indstillinger for Brugergrænsefladen][7]

## <a name="fault-tolerance-considerations"></a>Fejltolerance overvejelser i forbindelse med

Du kan konfigurere et App-tjenesten miljø for at bruge op til 55 samlede Beregn ressourcer. Kun 50 kan bruges til at host arbejdsbelastninger af ressourcerne 55 Beregn. Årsagen til dette er der to årsager. Der findes et minimum af 2 front end Beregn ressourcer.  Der lader op til 53 til at understøtte arbejder puljen allokeringen. For at få vist fejltolerance, skal du have en ekstra Beregn ressource, som tildeles efter følgende regler:

- Hver arbejder gruppe skal have mindst 1 yderligere Beregn ressource, der ikke er tilgængelig skal tildeles en arbejdsbyrde.
- Når antallet af Beregn ressourcer i en kollega pulje går over en bestemt værdi, derefter er en anden Beregn ressource påkrævet for fejltolerance. Det er ikke tilfældet i front end-puljen.

I en enkelt arbejder puljen er fejltolerance kravene, for en given værdi af X ressourcer, der er tildelt til en kollega gruppe:

- Hvis X er mellem 2 og 20, er mængden brugbar Beregn ressourcer, som du kan bruge til arbejdsbelastninger X-1.
- Hvis X er mellem 21 og 40, er mængden brugbar Beregn ressourcer, som du kan bruge til arbejdsbelastninger X-2.
- Hvis X er mellem 41 og 53, er mængden brugbar Beregn ressourcer, som du kan bruge til arbejdsbelastninger X-3.

Den mindste miljøet har 2 front end-servere og 2 medarbejdere.  Med de ovenfor sætninger derefter er her nogle eksempler for at gøre det klart:  

- Hvis du har 30 medarbejdere i en enkelt gruppe, kan 28 i dem bruges til at arbejdsbelastninger, som vært.
- Hvis du har 2 medarbejdere i en enkelt gruppe, kan 1 bruges til at arbejdsbelastninger, som vært.
- Hvis du har 20 medarbejdere i en enkelt gruppe, kan 19 bruges til at arbejdsbelastninger, som vært.  
- Hvis du har 21 medarbejdere i en enkelt gruppe, derefter stadig kun 19 kan bruges til at arbejdsbelastninger, som vært.  

Fejltolerance højde er vigtige, men du skal huske på, som du skalere over visse tærskler. Hvis du vil tilføje flere kapacitet, der går fra 20 forekomster, derefter gå til 22 eller nyere fordi 21 ikke tilføje en større kapacitet. På samme måde gælder gå over 40, hvor det næste nummer, der tilføjer kapacitet er 42.  

## <a name="deleting-an-app-service-environment"></a>Slette en App Service-miljø ##

Hvis du vil slette en App Service-miljø, derefter blot bruge handlingen **Slet** øverst i bladet App Service-miljø. Når du gør dette, bliver du bedt om at angive navnet på dit miljø til App-tjenesten for at bekræfte, at du virkelig vil gøre dette. Bemærk, at når du sletter et App-tjenesten miljø, du sletter alt indholdet i den samt.  

![Slette en App Service miljø brugergrænseflade][9]  

## <a name="getting-started"></a>Kom godt i gang

For at komme i gang med App-tjenesten miljøer, se, [hvordan du opretter en App Service-miljø](app-service-web-how-to-create-an-app-service-environment.md).

Du kan finde flere oplysninger om Azure App Service platformen, [Azure App Service](../app-service/app-service-value-prop-what-is.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
