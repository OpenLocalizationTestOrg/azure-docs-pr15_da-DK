<properties
    pageTitle="Hvad er Azure RemoteApp skabelon billeder? | Microsoft Azure"
    description="Få mere at vide om skabelonbillederne inkluderet med Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="what-is-in-the-azure-remoteapp-template-images"></a>Hvad er Azure RemoteApp skabelon billeder?

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Abonnementet Azure RemoteApp indeholder tre billeder af webstedsskabeloner:


- Windows Server 2012
- Microsoft Office 365 ProPlus (Office 365-abonnement påkrævet)
- Microsoft Office 2013 Professional Plus (kun prøveversion)

> [AZURE.IMPORTANT]Abonnementet Azure RemoteApp giver dig adgang til softwaren i billeder, med undtagelse af Office 365 Pro plus, der kræver en separat abonnement, og Office 2013, hvilket ikke kan bruges i fremstilling. Det betyder, at du kan dele programmer eller apps på skabelonbillederne med dine brugere. Hvis du opretter en samling, der bruger Windows Server 2012 R2 billedet, kan du publicere System Center Endpoint Protection for brugerne at få adgang til via RemoteApp.
>
> Se [RemoteApp licensering detaljer](remoteapp-licensing.md) kan finde flere oplysninger. Og [Brug af Office med Azure RemoteApp](remoteapp-o365.md) til Office-licensering oplysninger.

Læs videre for at få at vide om, hvad hvert billede indeholder.

## <a name="windows-server-2012-r2--the-vanilla-image"></a>Windows Server 2012 R2 ("vanille billedet")
Dette billede er baseret på Microsoft Windows Server 2012 R2 Datacenter operativsystem og har følgende roller og funktioner, der er installeret, så det opfylder kravene til Azure RemoteApp billeder af webstedsskabeloner:


- .NET framework 4.5 3.5.1 til, 3.5
- Skrivebord
- Håndskrift og håndskrift tjenester
- Media Foundation
- Remote Desktop sessionsværten
- Windows PowerShell 4.0
- Windows PowerShell ISE
- WoW64 Support

Dette billede har også følgende programmer være installeret:

- Adobe Flash Player
- Microsoft Silverlight
- Microsoft System Center 2012 Endpoint Protection
- Microsoft Windows Media Player


## <a name="microsoft-office-365-proplus-subscription-required"></a>Microsoft Office 365 ProPlus (abonnement påkrævet)
Office 365 er mest brugte programmet, så vi oprettede et "Brugerdefineret" billede for dig at arbejde med.

Dette billede er en udvidelse af vanille billedet og har følgende komponenter af Microsoft Office 365 ProPlus installeret ud over de komponenter, der er beskrevet i Windows Server 2012 R2 billedet:


- Access
- Excel
- Lync
- OneNote
- OneDrive for Business (Bemærk, at synkronisere agent ikke understøttes til brug sammen med Azure RemoteApp)
- Outlook
- PowerPoint
- Word
- Microsoft Office Korrekturredskaber

Billedet indeholder også Visio Pro og Project Pro.

Og følgende programmer, samt:

- SQL Native client
- ODBC-Driver
- SQL Server Data Mining klient
- MasterDataServices klient
- Microsoft Publisher
- PowerQuery
- Power-plotning


Fuld funktionalitet på Office 365 ProPlus apps er kun tilgængelig for brugere, der har en Office 365 ProPlus plan. Få mere at vide på Office 365-abonnementsplaner, skal du se [Office 365-tjenesten planer](http://technet.microsoft.com/library/office-365-plan-options.aspx). Har du stadig spørgsmål? Se oplysninger om [Office 365 + RemoteApp](remoteapp-o365.md) . Se også nye artikel, [hvordan du bruger Office 365-abonnement med Azure RemoteApp](remoteapp-officesubscription.md).

Bemærk, at du har brug at give licens Office 365 ProPlus, Visio Pro og Project Pro separat – de hver har deres egne licens.

## <a name="microsoft-office-2013-professional-plus-trial-only"></a>Microsoft Office 2013 Professional Plus (kun prøveversion)
Du kan teste tjenesten med Office 2013 billede under den gratis prøveperiode.

Dette billede er en udvidelse af vanille billedet og har følgende komponenter af Microsoft Office 2013 Professional Plus installeret ud over de komponenter, der er beskrevet i Windows Server 2012 R2 billedet:


- Access
- Excel
- Lync
- OneNote
- OneDrive for Business (Bemærk, at synkronisere agent ikke understøttes til brug sammen med Azure RemoteApp)
- Outlook
- PowerPoint
- Project
- Visio
- Word
- Microsoft Office Korrekturredskaber

> [AZURE.IMPORTANT]**Juridiske oplysninger:** Dette billede inkluderer ikke en Microsoft Office-licens og *kan ikke bruges til fremstilling*. Billedet er beregnet til prøveabonnement Brug kun Office 2013 Professional Plus. Hvis du vil bruge Office-apps i Azure RemoteApp til fremstilling, skal du bruge Office 365 ProPlus billedet. Du kan finde flere oplysninger om licenser Office, se [ved hjælp af Office 365 med Azure RemoteApp](remoteapp-o365.md)
