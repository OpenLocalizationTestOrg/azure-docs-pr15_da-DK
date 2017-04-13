<properties
   pageTitle="Få en introduktion til en intern justering af belastning i ressourcestyring ved hjælp af portalen Azure | Microsoft Azure"
   description="Lær, hvordan du opretter en intern justering af belastning i ressourcestyring ved hjælp af portalen Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="create-an-internal-load-balancer-in-the-azure-portal"></a>Oprette en intern justering af belastning på portalen Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][Klassisk implementeringsmodel](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="get-started-creating-an-internal-load-balancer-using-azure-portal"></a>Få en introduktion til en intern justering af belastning Azure portalen

Brug følgende trin til at oprette en intern justering af belastning fra Azure-portalen.

1. Åbne en browser, gå til [Azure portal](http://portal.azure.com)og logge på med din Azure-konto.
2. Klik på **Ny**i øverste venstre side af skærmen, > **netværk** > **justering af belastning**.
3. Angiv et **navn** til din justering af belastning i bladet **Opret justering af belastning** .
4. Klik på **interne**under **farveskema**.
5. Klik på **virtuelle netværk**, og vælg derefter det virtuelle netværk, hvor du vil oprette justering af belastning.

    >[AZURE.NOTE] Hvis du ikke kan se det virtuelle netværk, du vil bruge, kan du se **placering** , du bruger til justering af belastning og ændre den tilsvarende.

6. Klik på **undernet**, og vælg derefter det undernet, hvor du vil oprette justering af belastning.
7. Under **tildeling af IP-adresser**, skal du klikke på enten **dynamisk** eller **statisk**, afhængigt af om du vil IP-adressen for justering af belastning rettes (statisk) eller ej.

    >[AZURE.NOTE] Hvis du vælger for at bruge en statisk IP-adresse, har du giver dig en adresse til justering af belastning.

8. Under **ressourcegruppe** angive navnet på en ny ressourcegruppe for justering af belastning, enten klikke på **Vælg eksisterende** og vælge en eksisterende ressourcegruppe.
9. Klik på **Opret**.

## <a name="configure-load-balancing-rules"></a>Konfigurere regler for justering af belastning

Gå til Indlæs belastningsjusteringstjenesten ressourcen konfigurere det efter oprettelse af belastning belastningsjusteringstjenesten.
Du skal konfigurere først en adresse til back end-puljen og et rør inden du konfigurerer en regel til justering af belastning.

### <a name="step-1-configure-a-back-end-pool"></a>Trin 1: Konfigurere en back end-puljen

1. Klik på **Gennemse**i portalen Azure > **indlæse balancere**, og klik derefter på den justering af belastning du oprettede ovenfor.
2. Klik på **back end-grupper**i bladet **Indstillinger** .
3. Klik på **Tilføj**i bladet **back end-adresse grupper** .
4. Angiv et **navn** til back end-puljen i bladet **Tilføj back end-puljen** , og klik derefter på **OK**.

### <a name="step-2-configure-a-probe"></a>Trin 2: Konfigurere en efterprøvning af af

1. Klik på **Gennemse**i portalen Azure > **indlæse balancere**, og klik derefter på den justering af belastning du oprettede ovenfor.
2. Klik på **sonder**i bladet **Indstillinger** .
3. Klik på **Tilføj**i bladet **sonder** .
4. Angiv et **navn** til efterprøvning af af i bladet **Tilføj efterprøvning af af** .
5. Vælg **HTTP** (for websteder) eller **TCP** (for andre TCP-baserede programmer) under **Protocol**.
6. Angiv porten, der skal bruges, når du åbner efterprøvning af af under **Port**.
7. Angiv stien til brug som en efterprøvning af af under **sti** (for kun HTTP sonder).
8. Angiv, hvor ofte at teste programmet under **Interval** .
9. Angiv under **grænseværdi for beskadiget**, hvor mange forsøg går ned, før den back end-virtuelle maskine er markeret som sikre.
10. Klik på **OK** for at oprette efterprøvning af af.

### <a name="step-3-configure-load-balancing-rules"></a>Trin 3: Konfigurere regler for justering af belastning

1. Klik på **Gennemse**i portalen Azure > **indlæse balancere**, og klik derefter på den justering af belastning du oprettede ovenfor.
2. Klik på **belastning regler**i bladet **Indstillinger** .
3. Klik på **Tilføj**i bladet **belastning regler** .
4. Angiv et **navn** til reglen i bladet **Tilføj indlæse justering af belastning regel** .
5. Vælg **HTTP** (for websteder) eller **TCP** (for andre TCP-baserede programmer) under **Protocol**.
6. Angiv port klienter opretter forbindelse til i justering af belastning under **Port**.
7. Angiv port der skal bruges i back end-puljen under **back end-port**(som regel, Indlæs belastningsjusteringstjenesten port og backend-porten er den samme).
8. Vælg back end-puljen, du oprettede ovenfor, under **back end-puljen**.
9. Vælg, hvordan du vil sessioner bevares under **Session brugerdata**.
10. Angiv den inaktive timeout under **inaktiv timeout (minutter)**.
11. Klik på **deaktiveret** eller **aktiveret**under **Flydende IP-(returnerede direkte server)**.
12. Klik på **OK**.

## <a name="next-steps"></a>Næste trin

[Konfigurere en Indlæs belastningsjusteringstjenesten fordeling tilstand](load-balancer-distribution-mode.md)

[Konfigurere inaktive TCP timeoutindstillinger for din justering af belastning](load-balancer-tcp-idle-timeout.md)