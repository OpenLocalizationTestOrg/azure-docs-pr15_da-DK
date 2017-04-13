
<properties
    pageTitle="Overføre et brugerdefineret billede på Azure RemoteApp | Microsoft Azure"
    description="Lær at overføre et brugerdefineret billede på Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="ericorman"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="ericor" />



# <a name="upload-a-custom-image-for-azure-remoteapp"></a>Overføre et brugerdefineret billede på Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Nu, hvor du har oprettet din brugerdefinerede skabelonbillede eller har opdateret med ændringer, skal du overføre billedet til biblioteket Azure RemoteApp billede. Følg disse trin.


## <a name="before-you-start"></a>Før du starter

1.      Bekræft dit brugerdefinerede billede opfylder [billede krav](remoteapp-imagereqs.md) og [programmet krav](remoteapp-appreqs.md).
2.      Installer [Azure PowerShell-modulet](../powershell-install-configure.md).

## <a name="step-by-step-on-how-to-upload-custom-image"></a>Step by step om, hvordan du overføre brugerdefinerede billede

1.      Åbn Azure Management-portalen, og gå til siden RemoteApp.
2.      Klik på fanen **billeder af webstedsskabeloner** **overføre** nederst på siden.
4.      Angiv et fuldt navn for dit billede og angive lagerplaceringen konto. Sørg for placeringen er den samme placering, som din RemoteApp af websteder eller en placering, hvor du vil oprette en.
5.      Når du bliver bedt om det, kan du hente scriptet til din lokale computer.
6.      Kopiere parametrene kommandoen i tekstfeltet til Udklipsholder.
7.      Åbn et øgede Windows PowerShell-vindue.
8.      Gå til den samme mappe, hvor du har hentet scriptet fra vinduet øgede Windows PowerShell.
9.      Indsætte den kopierede kommando, og tryk på **Enter**.

    Begynder overførslen og varighed kan afhænge af mange faktorer, herunder dine netværkets hastighed og størrelsen på billedet

11.    Hvis overførslen mislykkes på grund af netværk blev afbrudt eller ting, du ønsker det, kan du altid fortsætte overførslen du startede. Kør scriptet igen ved hjælp af den samme kommando for at fortsætte en overførsel.

> [AZURE.WARNING] Ændre aldrig scriptet Overfør. Kontroller, der er implementeret for at sikre, at billedet opfylder billede krav og programmet krav.

## <a name="common-problems"></a>Almindelige problemer

- Kontrollér, at du bruger Windows PowerShell, ikke Azure PowerShell. Du skal installere Azure PowerShell-modulet, fordi du skal bruge visse moduler under overførselsprocessen.
- Ændre aldrig scriptet, valideringer er tilgængelige, så passer dig.
- Hvis filen virtuelle bliver låst ude under Overfør, kopiere filen eller flytte den til en ny placering og forsøg upload igen. Der kan være en Windows-proces, der forhindrer overførsel.  
