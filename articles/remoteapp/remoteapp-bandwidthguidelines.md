<properties 
    pageTitle="Azure RemoteApp netværksbåndbredde - generelle retningslinjer | Microsoft Azure"
    description="Forstå nogle standardnetværk båndbredde retningslinjer for din Azure RemoteApp af websteder og apps."
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
    
# <a name="azure-remoteapp-network-bandwidth---general-guidelines-if-you-cant-test-your-own"></a>Azure RemoteApp netværksbåndbredde - generelle retningslinjer (Hvis du ikke kan teste din egen)

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Hvis du ikke har tid eller muligheden for at køre [netværksbåndbredde test](remoteapp-bandwidthtests.md) til Azure RemoteApp, er her nogle ret generisk retningslinjer, der kan hjælpe dig med at vurdere netværksbåndbredde per bruger.

Hvis du har en blanding af disse scenarier, vi anbefaler ikke at gøre noget mindre end (eller lig med) 10 MB/s som MINIMUM netværksbåndbredde til moderne internetforbindelse apps i en ekstern miljø. (Selvom som beskrevet, dette ikke sikrer en bedre end gennemsnitlige brugeroplevelsen.)

## <a name="complex-powerpoint-simple-powerpoint"></a>Kompleks PowerPoint enkel PowerPoint

Azure RemoteApp fungerer bedst på 100 MB LAN. Når formindskelse over 120 ms er mere end 5%, på den 10 MB/s netværksprofil, og se brugeren en middel oplevelse. På 1 MB/s de forskellige stirre – for eksempel i et diasshow, kan brugeren ikke se animerede overgange overhovedet fordi rammer er ignoreret.

## <a name="internet-explorer-mixed-pdf-pdf-text"></a>Internet Explorer, blandet PDF-fil, PDF-fil, tekst

10 MB/s netværksprofil er tæt på LAN i de fleste aspekter. 1 MB/s give en OK oplevelse, selvom der opstår muligvis nogle formindskelse, når en bruger ruller, mens der ikke er billeder på skærmen.

## <a name="flash-video-youtube"></a>Flash-video (YouTube)

100 MB/s LAN giver den bedste oplevelse, mens 10 MB/s er acceptable (betydning vi holde med den ramme rente, men jitter øges). I 1 MB/s er formindskelse meget høj og synlige.

## <a name="word-typing-word-remote-input"></a>Word at skrive (Word remote input)
Dette er et scenarie med lav båndbredde brugen. Vi giver dig så godt af en oplevelse som LAN på 256 KB/s.

## <a name="learn-more"></a>Lær mere
- [Anslå Azure RemoteApp netværk båndbredden](remoteapp-bandwidth.md)

- [Azure RemoteApp - hvordan netværksbåndbredde og kvaliteten af oplever arbejde sammen?](remoteapp-bandwidthexperience.md)

- [Azure RemoteApp - tseting dit netværk båndbredden med nogle almindelige scenarier](remoteapp-bandwidthtests.md)