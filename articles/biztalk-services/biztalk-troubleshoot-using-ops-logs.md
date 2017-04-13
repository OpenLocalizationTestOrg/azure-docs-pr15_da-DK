<properties 
    pageTitle="Fejlfinding i forbindelse med BizTalk-tjenester ved hjælp af handlingen logfiler | Microsoft Azure" 
    description="Foretage fejlfinding af BizTalk-tjenester ved hjælp af handlingen logfiler. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="mandia"/>


# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>BizTalk-tjenester: Fejlfinding ved hjælp af handlingen logfiler

## <a name="what-are-the-operation-logs"></a>Hvad er loggene handling
Logfilerne for handlingerne Management Services funktionen er tilgængelig i Azure klassisk portalen, hvor du kan få vist historiske logfiler af handlinger, der udføres på din Azure tjenesterne, herunder BizTalk-tjenester. Dette gør det muligt at få vist historiske data, der er relateret til administration af handlinger på abonnementet BizTalk Service helt 180 dage.

> [AZURE.NOTE]Denne funktion registrerer kun logfiler til administration af handlinger på BizTalk-tjenester, som når tjenesten er blevet startet, sikkerhedskopier op, og så videre. Disse handlinger registreres uanset om de udføres fra portalen Azure klassisk eller ved at bruge [BizTalk-tjenesten REST API'er](http://msdn.microsoft.com/library/azure/dn232347.aspx). Du kan finde en komplet liste over handlinger, der registreres ved hjælp af Management Services [Handlinger registrerede ved hjælp af Azure Management Services](#bizops).<br/><br/>
Dette ikke registrere loggene til aktiviteter i forbindelse med BizTalk Service runtime (såsom meddelelse behandlet af broer osv.). For at få vist disse logfiler, anvende visningen Opfølgning fra portalen BizTalk-tjenester. Du kan finde flere oplysninger [Sporing af meddelelser](http://msdn.microsoft.com/library/azure/hh949805.aspx).

## <a name="view-biztalk-services-operation-logs"></a>Få vist BizTalk Services handlingen logfiler
1. Vælg **Management Services**i Azure klassisk portalen, og vælg derefter fanen **Handlingen logføring** .
2. Du kan filtrere loggene på baggrund af forskellige parametre som abonnement, datointerval, tjenestetype (fx BizTalk Services), navnet på tjenesten eller status for handlingen (lykkedes, mislykkedes).
3. Vælg markering til at få vist den filtrerede liste. Følgende billede viser aktiviteter, der er relateret til testbiztalkservice:  ![få vist handlingen logfiler][ViewLogs] 
4. Du kan se mere om en bestemt operation under markere rækken og klikke på **Detaljer** i proceslinjen nederst.


## <a name="bizops"></a>Handlinger spores ved hjælp af Azure Management Services
I følgende tabel vises de handlinger, der registreres ved hjælp af Azure Management Services:

Handlingsnavn | Opgave
--- | ---
CreateBizTalkService | Handling til at oprette en ny BizTalk Service
DeleteBizTalkService | Handling til at slette en BizTalk Service
RestartBizTalkService | Handlingen genstarte en BizTalk Service
StartBizTalkService | Handling til at starte en BizTalk Service
StopBizTalkService | Handlingen stoppe en BizTalk Service
DisableBizTalkService | Handling til at deaktivere en BizTalk Service
EnableBizTalkService | Operationen for at aktivere et BizTalk Service
BackupBizTalkService | Handling til at sikkerhedskopiere en BizTalk Service
RestoreBizTalkService | Handling til at gendanne en BizTalk Service fra angivne sikkerhedskopi
SuspendBizTalkService | Handlingen at annullere en BizTalk Service
ResumeBizTalkService | Handling til at fortsætte en BizTalk Service
ScaleBizTalkService | Handlingen skalere et BizTalk Service, op eller ned
ConfigUpdateBizTalkService | Handling til at opdatere konfigurationen af en BizTalk Service
ServiceUpdateBizTalkService | Operator, der skal opgradere eller nedgradere en BizTalk Service til en anden version
PurgeBackupBizTalkService | Handlingen at tømme sikkerhedskopier af BizTalk Service uden for opbevaringsperioden


## <a name="see-also"></a>Se også
- [Sikkerhedskopiér BizTalk Service](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Gendanne BizTalk Service fra sikkerhedskopi](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [BizTalk-tjenester: Udvikler, Basic, Standard og Premium udgaver diagram](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [BizTalk-tjenester: Ved hjælp af Azure klassisk portal klargøring](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [BizTalk-tjenester: Klargøring Status diagram](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [BizTalk-tjenester: Dashboard, overvåge og skala faner](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [BizTalk-tjenester: (throttling)](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [BizTalk-tjenester: Udstedernavn og udsteder nøgle](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Hvordan får jeg Start med at bruge Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png
 
