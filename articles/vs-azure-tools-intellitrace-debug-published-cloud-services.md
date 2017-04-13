<properties 
   pageTitle="Fejlfinding af en publiceret skybaseret tjeneste med IntelliTrace og Visual Studio | Microsoft Azure"
   description="Fejlfinding af en publiceret skybaseret tjeneste med IntelliTrace og Visual Studio"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="debugging-a-published-cloud-service-with-intellitrace-and-visual-studio"></a>Fejlfinding af en publiceret skybaseret tjeneste med IntelliTrace og Visual Studio

##<a name="overview"></a>Oversigt

Du kan logge omfattende fejlfindingsoplysninger for en forekomst af rolle med IntelliTrace, når der køres i Azure. Hvis du har brug at finde årsagen til et problem, kan du bruge loggene IntelliTrace til at gennemgå din kode fra Visual Studio, som om den kørte i Azure. I kraft nøgle IntelliTrace poster programkode og miljø data, når dit Azure program kører som en skybaseret tjeneste i Azure, og kan du afspille de registrerede data fra Visual Studio. Som et alternativ kan du bruge ekstern fejlfinding vedhæfte direkte til en skybaseret tjeneste, der kører på Azure. Se [fejlfinding Cloud Services](http://go.microsoft.com/fwlink/p/?LinkId=623041).

>[AZURE.IMPORTANT] IntelliTrace er beregnet til kun fejlfinding scenarier, og der ikke bør benyttes til et produktionsmiljø.

>[AZURE.NOTE] Du kan bruge IntelliTrace, hvis du har Visual Studio Enterprise installeret og din Azure-program destinationer .NET Framework 4 eller nyere. IntelliTrace indsamler oplysninger til dine Azure roller. Virtuelle maskiner til disse roller køre altid 64-bit operativsystemer.

## <a name="to-configure-an-azure-application-for-intellitrace"></a>Konfigurere et Azure-program til IntelliTrace

Hvis du vil aktivere IntelliTrace for et Azure-program, skal du oprette og publicere programmet fra et Visual Studio Azure-projekt. Du skal konfigurere IntelliTrace til Azure-program, før du udgiver billedet til Azure. Hvis du publicerer dit program uden at konfigurere IntelliTrace, men derefter beslutter, at du vil gøre det, har du publicere programmet igen fra Visual Studio. Se [publicere en skybaseret tjeneste, ved hjælp af værktøjerne Azure](http://go.microsoft.com/fwlink/p/?LinkId=623012)kan finde flere oplysninger.

1. Når du er klar til at installere Azure-program skal du kontrollere, at er din project build mål sat til **fejlfinding**.

1. Åbne genvejsmenuen for Azure projektet i Solution Explorer, og vælg **Publicer**.
 
    Guiden publicere Azure program vises.

1. For at samle IntelliTrace logfiler for dit program, når den er blevet publiceret i skyen, skal du markere afkrydsningsfeltet **Aktivér IntelliTrace** .

    >[AZURE.NOTE] Du kan aktivere enten IntelliTrace eller profiler, når du publicerer dit Azure-program. Du kan ikke aktivere begge.

1. Vælg linket **Indstillinger** for at tilpasse den grundlæggende IntelliTrace konfiguration.

    Dialogboksen med IntelliTrace indstillinger vises, som vist i følgende figur. Du kan angive, hvilke hændelser log, om du vil indsamle opkaldsoplysninger, hvilke moduler og processer til at indsamle logger og hvor meget plads der skal allokeres til optagelsen. Du kan finde flere oplysninger om IntelliTrace, [fejlfinding med IntelliTrace](http://go.microsoft.com/fwlink/?LinkId=214468).

    ![VST_IntelliTraceSettings](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

IntelliTrace loggen er en cirkulær logfil over den maksimale størrelse, der er angivet i indstillingerne IntelliTrace (standardstørrelsen er 250 MB). Indsamling af logfiler for IntelliTrace til en fil i filsystemet på den virtuelle maskine. Når du anmoder om logfiler, er et øjebliksbillede taget på dette tidspunkt og overført til din lokale computer.

Når programmet Azure er udgivet til Azure, kan du se Hvis IntelliTrace er blevet aktiveret fra noden Azure beregne i Server Explorer, som vist på følgende billede:

![VST_DeployComputeNode](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## <a name="downloading-intellitrace-logs-for-a-role-instance"></a>Hente IntelliTrace logfiler for en forekomst af rolle

Du kan hente IntelliTrace logfiler for en forekomst af rolle fra noden **Cloud Services** i **Server Explorer**. Udvid noden **Cloud Services** , indtil du finder den forekomst, du er interesseret i, Åbn genvejsmenuen for denne forekomst, og vælg **Vis IntelliTrace logfiler**. Loggene IntelliTrace overføres til en fil i en mappe på din lokale computer. Hver gang, du anmode om IntelliTrace logger, oprettes der et nyt øjebliksbillede.

Når loggene hentes, viser status for handlingen i vinduet Azure aktivitetslog i Visual Studio. Som vist i følgende figur, kan du udvide elementet linje for at få vist flere detaljer handlingen.

![VST_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

Du kan fortsætte med at arbejde i Visual Studio, mens loggene IntelliTrace hentes. Når loggen er hentet, åbnes det automatisk i Visual Studio.

>[AZURE.NOTE] Loggene IntelliTrace kan indeholde undtagelser, rammerne genererer og efterfølgende heltal. Interne framework kode genererer disse undtagelser som et normalt led i at starte en rolle, så du kan ignorere dem.

## <a name="see-also"></a>Se også

[Fejlfinding Cloud Services](https://msdn.microsoft.com/library/ee405479.aspx)

