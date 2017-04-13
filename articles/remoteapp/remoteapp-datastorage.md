
<properties
    pageTitle="Aldrig gemme følsomme data på brugerdefinerede billeder til Azure RemoteApp | Microsoft Azure"
    description="Få mere at vide om retningslinjerne for lagring af data i brugerdefinerede billeder i Azure RemoteApp"
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


# <a name="never-store-sensitive-data-on-custom-images"></a>Aldrig gemme følsomme data på brugerdefinerede billeder

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Når du hoster dine egne programmet i Azure RemoteApp, skal er det første trin at oprette et brugerdefineret billede. Vi bruger brugerdefinerede billedet til at oprette VM forekomster, fungerer dine apps til brugerne. Brugerdefineret billedet skal indeholde kun programmer og aldrig følsomme data, der kan gå tabt, såsom SQL-databaser, personalefiler eller speciel datafiler som QuickBooks virksomhed filer. Alle følsomme data skal være placeret i forhold til Azure RemoteApp på en filserver, en anden Azure VM, eller i SQL Azure. Billedet skal bare være vært for det program, der opretter forbindelse til datakilden og præsenterer dataene. Gennemse [krav til Azure RemoteApp billeder](remoteapp-imagereqs.md) kan finde flere oplysninger. 

For at forstå, hvorfor du ikke gemme følsomme data, du har brug at forstå, hvordan Azure RemoteApp fungerer. Når en samling oprettes eller opdateres i baggrunden flere kopier eller kopier af billedet er oprettet. Alle disse VM forekomster er nøjagtige kopier af det brugerdefinerede billede Når brugerne starter programmer, som de er forbundet til en af disse VM forekomster. Men den samme forekomst er ingen garanti og skal ikke noget, fordi de er ikke-fast. VM forekomster vært for programmerne, der er ikke-permanente og kan være ødelagt eller slettet baseret, for eksempel under opdatering af websteder. 

Når der er klargjort samlingen, og brugerne begynder at oprette forbindelse til FOS, brugerdata er fast og beskyttet, fordi den er gemt på separat lager i en virtuel harddisk, vi kalder en [bruger profil disk (hvorved UPD)](remoteapp-upd.md), som er brugerprofilen i c:\users\<brugerprofil >. Når et program startes, er hvorved UPD tilsluttet og behandles ligesom en lokal brugerprofil af operativsystemet. Læs mere om, hvordan [Azure RemoteApp gemmer brugerdata og indstillinger](remoteapp-upd.md).

På eksempeldata, der ikke skal er placeret i billedet:

- Delte data for brugerne at få adgang til
- SQL DB eller QuickBooks DB
- Alle data i D:\

På eksempeldata, der kan være placeret i standardprofil der skal kopieres til alle brugere hvorved UPD:

- Af konfigurationsfiler per bruger
- Scripts, som brugerne skal bevares i deres hvorved UPD

Vigtige punkter:

- Aldrig store følsomme data, der kan gå tabt på billedet, når du opretter et brugerdefineret billede.
- Følsomme data skal altid være placeret på en separat filserver separat Azure VM, i skyen, og altid ekstern i forhold til de VM forekomster, der er vært for dine programmer i Azure RemoteApp. 
- Brugerdata gemmes og fortsætter i bruger profil disken (hvorved UPD)


