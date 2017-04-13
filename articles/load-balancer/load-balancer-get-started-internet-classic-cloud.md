<properties
   pageTitle="Få en introduktion til en via justering af belastning i klassisk installation model ved hjælp af for-skytjenester internettet | Microsoft Azure"
   description="Lær at oprette en modstående justering af belastning i klassisk implementeringsmodel til skytjenester på internettet"
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
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-for-cloud-services"></a>Få en introduktion til en via justering af belastning til skytjenester internettet

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Denne artikel omhandler klassisk implementeringsmodel. Du kan også [se, hvordan du opretter en via justering af belastning ved hjælp af Azure ressourcestyring internettet](load-balancer-get-started-internet-arm-cli.md).

Skytjenester er automatisk konfigureret med en justering af belastning og kan tilpasses via tjenesten modellen.

## <a name="create-a-load-balancer-using-the-service-definition-file"></a>Oprette belastningsjustering ved hjælp af tjenesten definitionsfil

Du kan udnytte Azure SDK til .NET 2.5 til at opdatere din skybaseret tjeneste. Slutpunkt indstillinger for skytjenester er foretaget i filen [tjenestedefinitionen](https://msdn.microsoft.com/library/azure/gg557553.aspx).csdef.

I følgende eksempel viser, hvordan en servicedefinition.csdef fil til en skybaseret installation er konfigureret:

Kontrollere snipet for filen .csdef genereres af en sky-installation, kan du se det eksterne slutpunkt, der er konfigureret til at bruge portene HTTP port 10000, 10001 og 10002.


    <ServiceDefinition name=“Tenant“>
    <WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
    </WorkerRole>
    </ServiceDefinition>




## <a name="check-load-balancer-health-status-for-cloud-services"></a>Kontrollere status for indlæsning belastningsjusteringstjenesten tilstand for skytjenester


Følgende er et eksempel på en tilstand efterprøvning af af:

        <LoadBalancerProbes>
        <LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
        </LoadBalancerProbes>

Justering af belastning kombinerer oplysningerne på slutpunktet og af oplysninger for efterprøvning af af til at oprette en URL-adresse i form af http://{DIP af VM}:80/Probe.aspx, der kan bruges til at forespørge tilstanden for tjenesten.

Tjenesten registrerer periodiske sonder fra den samme IP-adresse. Dette er sundhed efterprøvning af af anmodningen kommer fra værten for noden, hvor den virtuelle maskine kører.
Tjenesten har svare med en HTTP 200 statuskode for justering af belastning skal have, at tjenesten er sund. Alle andre HTTP-statuskode (for eksempel 503) direkte tager virtuelt af rotation.

Efterprøvning af af definitionen styrer også hyppigheden for efterprøvning af af. I dette tilfælde ovenfor, er justering af belastning test slutpunktet hver 5 sekunder. Hvis intet positive svar er modtaget i 10 sekunder (to efterprøvning af af intervaller), antages efterprøvning af af ned, og den virtuelle maskine, benyttes af rotation. På samme måde, hvis tjenesten er uden for rotation og et positivt svar er modtaget, tjenesten indsættes tilbage til det samme rotation. Hvis tjenesten udsving mellem sund og beskadiget, kan justering af belastning vil forsinke fornyet indførelsen af tjenesten tilbage til rotation, indtil det har været sund for et antal sonder.

Markér tjenesten definition skemaet for [systemtilstand efterprøvning af af](https://msdn.microsoft.com/library/azure/jj151530.aspx) kan finde flere oplysninger.

## <a name="next-steps"></a>Næste trin

[Introduktion til konfiguration af en intern justering af belastning](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurere en Indlæs belastningsjusteringstjenesten fordeling tilstand](load-balancer-distribution-mode.md)

[Konfigurere inaktive TCP timeoutindstillinger for din justering af belastning](load-balancer-tcp-idle-timeout.md)

