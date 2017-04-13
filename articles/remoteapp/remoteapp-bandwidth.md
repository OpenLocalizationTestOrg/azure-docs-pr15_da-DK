
<properties 
    pageTitle="Anslå Azure RemoteApp netværk båndbredden | Microsoft Azure"
    description="Få mere at vide om kravene til netværksbåndbredde til Azure RemoteApp af websteder og apps."
    services="remoteapp"
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />

# <a name="estimate-azure-remoteapp-network-bandwidth-usage"></a>Anslå Azure RemoteApp netværk båndbredden 

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Azure RemoteApp bruger RDP Remote Desktop Protocol () til at kommunikere mellem programmer, der kører i Azure skyen og dine brugere. I denne artikel får du nogle grundlæggende retningslinjer, som du kan bruge til at anslå netværk brugen og potentielt evaluerer netværk båndbredden per Azure RemoteApp bruger.

Beregning af internetbåndbredde per bruger er meget komplekse og kræver kørsel af flere programmer samtidigt i multitasking scenarier hvor programmer kan påvirke hinandens ydeevne baseret på deres behov for netværksbåndbredde. Selv typen af Fjernskrivebord-klienten (såsom Mac-klienten kontra HTML5-klienten) kan føre til forskellige båndbredderesultater. For at hjælpe dig med at gennemgå disse komplikationer, får vi inddele brugsscenarier i flere af de almindelige kategorier for at gentage reale scenarier. (Hvis det virkelige scenario er selvfølgelig en blanding af kategorier og afviger bruger.)

Før vi gå videre - skal Bemærk, at vi antager, at RDP giver et godt at fremragende oplevelse for de fleste brugsscenarier på netværk ventetid under 120 ms og båndbredde mere end 5 MB – dette er baseret på RDPS mulighed for at justere dynamisk ved hjælp af den tilgængelige netværksbåndbredde og anslåede programmet båndbredde. I denne artikel, der går ud over dem, der er "de fleste brugsscenarier" at kigge på den kant, hvor scenarier begynder at unwind og brugeroplevelsen begynder at forringe.

Nu se følgende artikler for at få de oplysninger, herunder faktorer, du skal overveje, Oprindelig anbefalinger, og hvad blev ikke medtaget i vores estimater.

- [Hvordan netværksbåndbredde og kvaliteten af oplever arbejde sammen?](remoteapp-bandwidthexperience.md)
- [Test dit netværk båndbredden med nogle almindelige scenarier](remoteapp-bandwidthtests.md)
- [Hurtig retningslinjer, hvis du ikke har tid eller mulighed for at teste](remoteapp-bandwidthguidelines.md)


## <a name="what-are-we-not-including"></a>Hvad vi ikke herunder?

Når du gennemser de foreslåede test og vores overordnede (og admittedly generisk) anbefalinger, kan du være opmærksom på, at der er flere faktorer, som vi ikke kan. For eksempel hente bruger oplevelse komplikationer leveres af hvilke asymmetrisk Overfør kontra båndbredde. De fleste Wi-Fi-netværk asymmetrisk art påvirker desuden ydeevnen og bruger oplevelse opfattelsen. For interaktive scenarier kan den efterfølgende trafik prioriteres lavere end den tidligere, der kan øge antallet af tabt video eller lyd rammer og derfor påvirke bruger opfattelsen af streaming-oplevelsen. Du kan køre din egen forsøg for at se, hvad er en god ide til bestemte Brug store og små bogstaver og netværk.

Selvom vi diskutere enhedsomdirigering, vi ikke tager højde båndbreddepåvirkning netværkstrafikken skyldes vedhæftede enheder, som lager, printere, scannere, webkameraer og andre USB-enheder. Effekten af enhederne, som regel er højest midlertidigt båndbredde behov og forsvinder, når opgaven er fuldført. Men hvis gjort ofte, båndbredde demand kan være helt chatanmodning.

Vi diskuterer også ikke hvordan en bruger kan påvirke andre brugere i det samme netværk. En bruger forbrug 4K video på et 100 MB/s netværk kan for eksempel betydeligt påvirke andre brugere på samme netværket forsøger at gøre den samme opgave. Det får desværre gradvist sværere at finde ud af, hvordan samtidige brugen at give et almindelige eller alle omfatter anbefaling om, hvordan systemet udfører på aggregat. Alle vi kan sige er, at den underliggende teknologi protokol får den bedste anvendelse af den tilgængelige netværksbåndbredde, men det har sine begrænsninger.