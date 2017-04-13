<properties
 pageTitle="Fejlfinding i forbindelse med installation serviceproblemer til skyen | Microsoft Azure"
 description="Der er et par almindelige problemer, du kan støde på, når du installerer en skybaseret tjeneste på Azure. Denne artikel indeholder løsninger på nogle af dem."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/02/2016"
   ms.author="v-six" />

# <a name="troubleshoot-cloud-service-deployment-problems"></a>Fejlfinding i forbindelse med installation serviceproblemer til skyen

Når du installerer en skybaseret tjeneste programpakke til Azure, kan du få oplysninger om installationen fra ruden **Egenskaber** i portalen Azure. Du kan bruge oplysningerne i denne rude kan hjælpe dig med at foretage fejlfinding af problemer med tjenesten skyen, og du kan angive disse oplysninger til at understøtte Azure, når du åbner en ny supportanmodning.

Du kan finde ruden **Egenskaber** på følgende måde:

* I portalen Azure, skal du klikke på installation af skybaseret tjeneste, skal du klikke på **alle indstillinger**og klik derefter på **Egenskaber**.
* Klik på installation af skybaseret tjeneste, **DASHBOARD**, findes i det nederste højre hjørne af siden (under **Oversigt over**) i Azure klassisk portalen. Vær opmærksom på, at der er ingen "Egenskaber" etiket i ruden.

> [AZURE.NOTE] Du kan kopiere indholdet af ruden **Egenskaber** til Udklipsholder ved at klikke på ikonet i øverste højre hjørne af ruden.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problem: Jeg kan ikke få adgang til mit websted, men min installation er i gang og alle forekomster af rolle er klar

URL-adresse til linket til websted vises i portalen omfatter ikke port. Standardporten for websteder er 80. Hvis dit program er konfigureret til at køre i en anden port, skal du føje det korrekte portnummer til URL-adressen, når du åbner det offentlige websted.

1. Klik på installation af skybaseret tjeneste i Azure-portalen.
2. Markér portene efter rolle forekomster (under **Input slutpunkter**) i ruden **Egenskaber** i portalen Azure.
3. Hvis port ikke er 80, kan du tilføje den korrekte portværdi til URL-adressen, når du får adgang til programmet. For at angive en ikke-standardport, skal du skrive URL-adressen, efterfulgt af et kolon (:), efterfulgt af portnummeret, som ikke indeholder mellemrum.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problem: Min rolle forekomster genbruges uden mig at gøre noget

Tjenesten reparation sker automatisk, når Azure registrerer problemet noder og derfor flytter rolle forekomster til nye noder. Når dette sker, kan du muligvis vist din rolle forekomster genbrug automatisk. At finde ud af, om tjenesten reparation foretaget:

1. Klik på installation af skybaseret tjeneste i Azure-portalen.
2. Gennemse oplysningerne i ruden **Egenskaber** i portalen Azure og finde ud af, om tjenesten reparation opstod under det tidspunkt, hvor du observeret rollerne genbrug.

Roller vil også Papirkurv omkring én gang for hver måned under host OS og Gæst OS opdateringer.  
Du kan finde flere oplysninger, kan du se blogindlægget [Rolle forekomst genstarter forfaldne til OS opgraderinger](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problem: Jeg kan ikke gøre en VIP Byt om og modtage en fejl

Udskift en VIP er ikke tilladt, hvis en installation opdatering er i gang. Installation opdateringer kan sker automatisk, når:

* Et nyt gæst operativsystem er tilgængelig, og du er konfigureret for Automatiske opdateringer.
* Service reparation opstår.

Hvis du vil finde ud af, om en automatisk forhindrer opdatering dig i at gøre en VIP Byt om:

1. Klik på installation af skybaseret tjeneste i Azure-portalen.
2. I ruden **Egenskaber** i portalen Azure skal se på værdien af **Status**. Hvis det er **klar**, skal du kontrollere **seneste handling** , der skal se, hvis en senest er der sket, der kan forhindre VIP Udskift.
3. Gentag trin 1 og 2 for installationen i et produktionsmiljø.
4. Hvis en automatisk opdatering er i gang, vente på at afsluttes, før du forsøger at gøre VIP Udskift.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problem: En forekomst af rolle løkker mellem Introduktion, initialiserer, optaget og stoppet

Denne betingelse kan skyldes et problem med programmet kode, pakke eller konfiguration af filen. Det er tilfældet, du skal kunne se status ændre alle par minutter, og portalen Azure kan sig noget som **genbrug**, **optaget**eller **initialiserer**. Dette angiver, at der er noget galt med det program, der holder forekomsten rolle i at køre.

Du kan finde flere oplysninger om, hvordan du udfører fejlfinding på dette problem, kan du se blogindlægget [Azure PaaS beregne diagnosticering Data](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) og [almindelige problemer, der forårsager roller til Papirkurv](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problem: Mit program, der ikke længere arbejder

1. Klik på forekomsten rolle i Azure-portalen.
2. I ruden **Egenskaber** i portalen Azure, skal du overveje følgende betingelser til at løse problemet:
   * Hvis den rolle forekomst for nylig er stoppet (du kan kontrollere værdien af **Afbryd optælling**), installationen kan opdateres. Vente på at se, hvis rolle forekomst genoptages fungerer på sin egen.
   * Hvis forekomsten rolle er **optaget**, kan du kontrollere din programkode for at få vist Hvis hændelsen [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck) håndteres. Du muligvis tilføje eller rette nogle kode, der håndterer begivenheden.
   * Gå igennem diagnosticering data og fejlfinding scenarier i blogindlægget [Azure PaaS beregne diagnosticering Data](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

>[AZURE.WARNING] Hvis du Papirkurv skybaseret tjeneste, nulstille du egenskaberne for installationen, effektivt slette oplysningerne til det oprindelige problem.

## <a name="next-steps"></a>Næste trin

Få vist flere [foretage fejlfinding af artikler](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) til skytjenester.

Hvis du vil lære at foretage fejlfinding af problemer med skyen tjenesten rolle ved hjælp af Azure PaaS computer diagnosticering data, skal du se [Kevin Williamson blogserier](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
