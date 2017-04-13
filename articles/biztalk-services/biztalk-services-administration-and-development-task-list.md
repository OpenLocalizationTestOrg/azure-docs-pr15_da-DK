<properties
    pageTitle="Administration og udvikling opgave liste i BizTalk Services | Microsoft Azure"
    description="Planlægge og job støtte til installation af Azure BizTalk Services."
    services="biztalk-services"
    documentationCenter=""
    authors="msftman"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="deonhe"/>

# <a name="administration-and-development-task-list-in-biztalk-services"></a>Administration og udvikling opgaveliste i BizTalk-tjenester  

## <a name="getting-started"></a>Kom godt i gang
Når du arbejder med Microsoft Azure BizTalk-tjenester, er der flere lokale og skybaserede komponenter skal du tænke på. For at komme i gang skal du overveje følgende procesforløb:  

|Trin|Hvem har ansvaret|Opgave|Relaterede Links|
|----|----|----|----|
|1.|Administrator|Oprette Microsoft Azure abonnementet ved hjælp af en Microsoft-konto eller en organisationskonto|[Azure klassisk portal](http://go.microsoft.com/fwlink/p/?LinkID=213885)|
|2.|Administrator|Oprette eller klargøre en BizTalk Service.|[Oprette en BizTalk Service Azure klassisk portalen](http://go.microsoft.com/fwlink/p/?LinkID=302280)|
|3.|Administrator|Registrere du eller dit firmas BizTalk Services installation|[Registrere og opdatere en BizTalk Service-installation på portalen BizTalk-tjenester](https://msdn.microsoft.com/library/azure/hh689837.aspx)|
|4.|Administrator|Anvender Hvis programmet bruger BizTalk Adapter Service til at oprette forbindelse til et lokalt Line of Business (LOB) system, eller bruger en kø eller emne Destination.  Oprette Azure Service Bus Namespace. Giv denne navneområde, Service Bus udstedernavn og Service Bus udsteder nøgle-værdier i udvikleren.|[Sådan: oprette eller ændre en tjeneste Bus Service Namespace](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) og [få udstedernavn og udsteder nøgle-værdier](biztalk-issuer-name-issuer-key.md)|
|5.|Udvikler|Installere SDK og oprette BizTalk Service projektet i Visual Studio.|[Installer Azure BizTalk Services SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx) og [oprette omfattende SMS slutpunkterne på Azure](https://msdn.microsoft.com/library/azure/hh689766.aspx)|
|6.|Udvikler|Installer din BizTalk Service projekt for BizTalk Service hostes på Azure.|[Installation og opdatering af BizTalk Services-projekt](https://msdn.microsoft.com/library/azure/hh689881.aspx)|
|7.|Administrator|Gælder, hvis du bruger eller-Brugergrupper.  Du kan tilføje partnere og oprette aftaler på portalen Microsoft Azure BizTalk-tjenester. Når du opretter en aftale, kan du tilføje bro og/eller transformeringer, der er oprettet af udvikleren til indstillingerne for aftale.|[Konfigurere eller-Brugergrupper, AS2 og EDIFACT på BizTalk Services-portalen](https://msdn.microsoft.com/library/azure/hh689853.aspx)|
|8.|Administrator|Ved hjælp af portalen Azure klassisk, overvåge tilstanden for din BizTalk Service, herunder ydeevne målepunkter.|[BizTalk-tjenester: Dashboard, overvåge og skala faner](http://go.microsoft.com/fwlink/p/?LinkID=302281)|
|9.|Administrator|De elementer, der bruges af BizTalk-tjenesterne, og Registrer meddelelser, som de er behandlet af bro filer ved hjælp af portalen Microsoft Azure BizTalk-tjenester, administrere.|[Ved hjælp af portalen BizTalk-tjenester](https://msdn.microsoft.com/library/azure/dn874043.aspx)|
|10.|Administrator|Opret en plan for sikkerhedskopiering til at sikkerhedskopiere BizTalk Service.|[Forretningskontinuitet og nedbrud i BizTalk-tjenester](https://msdn.microsoft.com/library/azure/dn509557.aspx) |  
## <a name="next-steps"></a>Næste trin
[Selvstudier og eksempler](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Opret projektet i Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Installer Azure BizTalk Services SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>Begreber
[Opret projektet i Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
[Eller-Brugergrupper, AS2 og EDIFACT Messaging (Business to Business)](https://msdn.microsoft.com/library/azure/hh689898.aspx)  
## <a name="other-resources"></a>Andre ressourcer  
[Tilføj kilde, Destination og bro Messaging slutpunkter](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[Få mere at vide og oprette meddelelsen kort og transformationer](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[Ved hjælp af tjenesten BizTalk-Adapter (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[Azure BizTalk-tjenester](http://go.microsoft.com/fwlink/p/?LinkID=303664)
