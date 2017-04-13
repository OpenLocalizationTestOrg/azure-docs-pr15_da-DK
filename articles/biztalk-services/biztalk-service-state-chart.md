<properties 
    pageTitle="Opgaver, der er tilladt i forskellige tilstande eller statusser i BizTalk Services | Microsoft Azure" 
    description="De handlinger/handlinger, der er tilladt i forskellige MABS status: stop, start, genstart, suspendere, genoptage, slette, skalere, opdatere konfiguration og sikkerhedskopiering op" 
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



# <a name="biztalk-services-service-state-chart"></a>BizTalk-tjenester: Tjenesten tilstand diagram
Afhængigt af den aktuelle status for tjenesten BizTalk er der handlinger, som du kan eller ikke kan udføre på BizTalk-tjenesten.

For eksempel klargøre du en ny BizTalk-tjeneste i portalen Azure klassisk. Når den er fuldført, er tjenesten BizTalk i aktive tilstand. Du kan stoppe tjenesten BizTalk i tilstanden aktiv. Hvis Stop er gået igennem, går BizTalk-tjenesten til tilstanden showet afbrydes. Hvis Stop mislykkes, går BizTalk-tjenesten til tilstanden StopFailed. Du kan genstarte tjenesten BizTalk i tilstanden StopFailed. Hvis du prøver en handling, der ikke er tilladt, som CV BizTalk-tjenesten, der opstår følgende fejl:

**Handlingen er ikke tilladt**

Hvis du vil klargøre en BizTalk Service, gå til [BizTalk-tjenester: klargøring ved hjælp af Azure klassisk portal](http://go.microsoft.com/fwlink/p/?LinkID=302280).

De følgende tabeller viser handlinger eller handlinger, der kan udføres, når BizTalk Service er i en bestemt tilstand. En ✔ betyder, at handlingen er tilladt i denne tilstand. En tom post betyder, at denne handling ikke kan udføres i denne tilstand.

## <a name="start-stop-restart-suspend-resume-and-delete-operations"></a>Starte, stoppe, genstart, afbryde, CV, og slette handlinger
<table border="1">
<tr>
        <th colspan="15">Handlingen eller handling</th>
</tr>

<tr>
        <th rowspan="18">BizTalk-tjenestetilstand</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Start</th>
        <th>Stop</th>
        <th>Genstart</th>
        <th>Suspender</th>
        <th>CV</th>
        <th>Slet</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Aktive</b></td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Deaktiveret</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Suspenderet</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Ikke længere</b></td>
<td><center>✔</center></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Tjenesteopdatering mislykkedes</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
</table>
<br/>

## <a name="scale-update-configuration-and-backup-operations"></a>Skala, Opdater konfiguration og sikkerhedskopiering
<table border="1">
<tr>
        <th colspan="15">Handlingen eller handling</th>
</tr>

<tr>
        <th rowspan="18">BizTalk-tjenestetilstand</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Skala</th>
        <th>Opdatere konfigurationen</th>
        <th>Sikkerhedskopi</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Aktive</b></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Deaktiveret</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Suspenderet</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Ikke længere</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Tjenesteopdatering mislykkedes</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
</tr>
</table>

## <a name="see-also"></a>Se også
- [BizTalk-tjenester: Ved hjælp af Azure klassisk portal klargøring](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk-tjenester: Dashboard, overvåge og skala faner](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk-tjenester: Udvikler, Basic, Standard og Premium udgaver diagram](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk-tjenester: Sikkerhedskopiering og gendannelse](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk-tjenester: (throttling)](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [BizTalk-tjenester: Udstedernavn og udsteder nøgle](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Hvordan får jeg Start med at bruge Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)


 
