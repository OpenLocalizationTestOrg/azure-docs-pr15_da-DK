<properties
   pageTitle="Azure Sikkerhedscenter fejlfindingsvejledningen | Microsoft Azure"
   description="Dette dokument hjælper med at foretage fejlfinding af problemer i Azure Sikkerhedscenter."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-troubleshooting-guide"></a>Azure Sikkerhedscenter Fejlfindingsvejledning
Denne vejledning er beregnet til oplysninger diverse IT-medarbejdere, oplysninger sikkerhed analytikere og skyen administratorer Hvis organisationer bruger Azure Sikkerhedscenter og har brug at foretage fejlfinding af Sikkerhedscenter relaterede problemer.

## <a name="troubleshooting-guide"></a>Vejledning til fejlfinding
Denne vejledning forklarer, hvordan du udfører fejlfinding af Sikkerhedscenter relaterede problemer. De fleste af fejlfinding færdig i Sikkerhedscenter skal foregå ved først at se på [Overvågningsloggen](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) -posterne for komponenten mislykkedes. Gennem overvågningslogge, kan du se:

- Hvilke handlinger blev fundet sted
- Hvem der startede handlingen
- Når handlingen blev udført
- Status for handlingen
- Værdierne i andre egenskaber, der kan hjælpe dig med at undersøge handlingen

Overvågningsloggen indeholder alle skrivehandlinger (læg, INDLÆG, slette), der udføres på dine ressourcer, men det ikke indeholder læst handlinger (Hent).

## <a name="troubleshooting-monitoring-agent-installation-in-windows"></a>Fejlfinding i forbindelse med overvågning agent-installation i Windows

Sikkerhedscenter overvågning agent bruges til at udføre dataindsamling. Når dataindsamling er aktiveret, og agenten er installeret korrekt i target maskine, skal disse processer være i udførelse af:

- ASMAgentLauncher.exe - Azure overvågning Agent 
- ASMMonitoringAgent.exe - Azure sikkerhed overvågning lokalnummer
- ASMSoftwareScanner.exe – Azure Scan Manager

Filtypenavnet Azure sikkerhed overvågning scanner for forskellige sikkerhed relevante konfiguration og indsamler sikkerhedslogfiler fra den virtuelle maskine. Scanning manager bruges som en programrettelse scanner.

Hvis installationen udføres korrekt skal du se en post, der ligner, under i overvågningslogge for destination VM:

![Overvågningslogge](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig1.png)

Du kan også få flere oplysninger om installationsprocessen ved at læse loggene agent, findes i *%systemdrive%\windowsazure\logs* (eksempel: C:\WindowsAzure\Logs).

> [AZURE.NOTE] Hvis Azure Security Center Agent fejl, skal du genstarte destinationen VM, da der er ingen kommando til at stoppe og start agent.

## <a name="troubleshooting-monitoring-agent-installation-in-linux"></a>Fejlfinding i forbindelse med overvågning agent-installation i Linux
Når fejlfinding VM Agent-installation i et Linux-system skal du sikre dig, at filtypenavnet blev overført til/varians/bibliotek/waagent /. Du kan køre kommandoen nedenfor til at kontrollere, om den er installeret:

`cat /var/log/waagent.log` 

De andre logfiler, kan du gennemgå til fejlfinding formål er: 

- /var/log/mdsd.ERR
- / varians/log/azure /

I et arbejde system skal du se en forbindelse til mdsd processen på TCP 29130. Dette er den syslog, kommunikere med mdsd processen. Du kan validere dette problem ved at køre kommandoen nedenfor:

`netstat -plantu | grep 29130`

## <a name="contacting-microsoft-support"></a>Kontakte Microsoft Support

Nogle problemer kan identificeres ved hjælp af retningslinjerne, forudsat i denne artikel andre du kan også finde dokumenteret på det offentlige Sikkerhedscenter- [forummet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter). Men hvis du har brug for yderligere fejlfinding, kan du åbne en ny supportanmodning portalen Azure, som vist nedenfor: 

![Microsoft Support](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)


## <a name="see-also"></a>Se også

I dette dokument, du har lært at konfigurere sikkerhedspolitikker i Azure Sikkerhedscenter. Hvis du vil vide mere om Azure Sikkerhedscenter, skal du se følgende:

- [Azure Security Center planlægning og Operations Guide](security-center-planning-and-operations-guide.md) – Lær, hvordan du planlægger og forstå Designovervejelser at indføre Azure Sikkerhedscenter.
- [Sikkerhed sundhed overvågning i Azure Sikkerhedscenter](security-center-monitoring.md) – Lær, hvordan du overvåge tilstanden for ressourcerne Azure
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed
- [Overvågning partnerløsninger med Azure Security Center](security-center-partner-solutions.md) – Lær, hvordan du overvåge systemtilstand status for din partnerløsninger.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten
- [Azure sikkerhed Blog](http://blogs.msdn.com/b/azuresecurity/) – finde blogindlæg om Azure sikkerhed og overholdelse
