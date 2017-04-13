
<properties
    pageTitle="Hvad er nyt i Azure RemoteApp? | Microsoft Azure"
    description="Få mere at vide om ændringer og forbedringer i Azure RemoteApp"
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



# <a name="whats-new-in-azure-remoteapp"></a>Hvad er nyt i Azure RemoteApp?

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

En af fordelene ved Azure RemoteApp er, at vi arbejder altid for at forbedre den. Hver gang vi gør, kan vi vil give besked om ændringerne her.

## <a name="future-updates"></a>Fremtidige opdateringer
Hey - Vidste du, Azure RemoteApp teamet indlæg månedlige opdateringer til RDS blog? Du kan finde ikke blot Hvad er ændring i Azure RemoteApp, men andre oplysninger om, hvordan du bruger RDS. Se deres blog, [Remote Desktop Services Blog](https://blogs.msdn.microsoft.com/rds/), du kan finde oplysninger. For eksempel et par uger gamle, som brugeren har offentliggjort en indtastning om [løfte og forskyde arbejdsmængder med Azure RemoteApp og Azure AD](https://blogs.msdn.microsoft.com/rds/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services/).
 
## <a name="september-2015"></a>September 2015
- Tilføjede Infopath til Microsoft Office 365 skabelon og galleriet billede. Hvis du vil dele Infopath, skal du sørge for at opdatere dine samlinger med den seneste billede.
- Klientopdateringer:
    - Windows-klient opdateret for at gøre det muligt for brugerne at dele feedback, især omkring forbindelsesproblemer.
    - iOS klient opdateret til at løse fejlen messaging og løse et problem, hvor dine legitimationsoplysninger udløbet tidligere end forventet.
- Vi arbejder på Sådan får du Office 2016 support testet. Når, der er færdig, skal du se efter opdaterede billeder.
- Publiceret en ny artikel om [forskellene mellem skyen og hybrid samlinger](remoteapp-collections.md) - dette hjælper dig med at vælge typen af websteder, der passer bedst til dine apps - kun skyen, skyen + VNET eller hybrid.
- Vil dele QuickBooks ved hjælp af Azure RemoteApp, men ikke sikker på, over trinnene? Se [Erics nye artikel](remoteapp-quickbooks.md) fortæller nøjagtigt, hvad du skal gøre.

## <a name="august-2015"></a>August 2015
Store ændringer, der er foregået i August - her er de vigtige punkter:

- Du kan nu bruge en Azure VNET med en skyen samling! Se [skyen oprettelse af instruktioner](remoteapp-create-cloud-deployment.md) til de nye trin.
- Gør det muligt at føje apps til menuen **Start **for Windows RemoteApp-klienten. Apps vises på listen program, og du kan fastgøre dem til menuen **Start **i Windows.
- Føjes et nyt billede til galleriet Azure VM - Windows Server Remote Desktop sessionsværten med Microsoft Office 365 ProPlus.
- Fast Mac-klienten, så apps med modale windows stopper fryse.
- Dokumenteret, hvordan du kan bruge dit [Office 365 Pro Plus-abonnement](remoteapp-officesubscription.md) med Azure RemoteApp.
- Detaljeret, hvordan du kan [sikre apps og data](remoteapp-secure.md) i Azure RemoteApp af websteder.

## <a name="july-2015"></a>Juli 2015

Juli lægge stemningen for ændringer i August, så der ikke er meget at tale om nu, men ellers primært dokument opdateringer. Her er de seneste ændringer:

- Føjes en **understøtter** fane til portalen, så du nemmere kan få adgang til supportressourcer som forummerne.
- Ændret de oplysninger om fejlfinding til oprettelse af en hybrid af websteder. Tjek ud [det seneste og bedste](remoteapp-hybridtrouble.md) tip til fejlfinding kan lide, hvordan du identificerer de korrekte porte til at konfigurere til din VNET.
- Dokumenteret hvordan [brugerdata](remoteapp-upd.md) er oprettet og gemt i Azure RemoteApp.
- Dokumenteret Sådan [låsning apps](remoteapp-secure.md).
- Publiceret [Azure RemoteApp-cmdletter](https://msdn.microsoft.com/library/mt428031.aspx).
- Og til sidst skal vi gang en samtale med nogle Azure RemoteApp brugere om terminologi. Se efter ændringer af, hvordan vi refererer til indstillingerne for forskellige af websteder.

## <a name="june-2015"></a>Juni 2015

Så mange ændringer! Teamet er optaget i juni:

- Nyt design på Azure RemoteApp [landingssiden](https://www.remoteapp.windowsazure.com/) - tjekke filen ud!
- Opdateret softwaren i alle de tilgængelige billeder som en del af dit abonnement.
- Foretaget forbedringer til hybrid websteder, herunder tvunget tunnelføring support og kontrollere IP-undernet størrelse før du forsøger at oprette gruppen af websteder.
- Har opdaget, som den * jokertegn fungerer ikke for webcam. I stedet skal du angive den forekomst-ID eller GUID. Vi opdaterer skal Omdirigeringsoplysningerne om for at afspejle.
- Gjort det, så du kan føje brugerdefinerede antivirussoftware til dit billede, når du opretter et billede af designskabelon fra galleriet Azure.

Vi har flere ændringer rullende i juli, så vi kan tilbage med en anden opdatering snart.

## <a name="may-2015"></a>Maj 2015

Der er gået et antal tilføjelser (og måneder) siden vi først har oprettet i dette emne, så denne liste cheats lidt og er fra begyndelsen af marts til og med maj. Se disse nye funktioner:

- Automatisere alt - Azure RemoteApp har nu [-cmdletter i Azure PowerShell-modulet](remoteapp-tutorial-arawithpowershell.md).
- [Opret et Azure RemoteApp billede fra en Azure virtuel maskine](remoteapp-image-on-azurevm.md). Gør overførsel af dit eget billede til Azure meget hurtigere.
- Bruge en Azure VNET i stedet for en RemoteApp VNET til at forbinde dine ressourcer til virksomhedens netværk til Azure. Vi har opdateret [hybrid samling instruktioner](remoteapp-create-hybrid-deployment.md) for at vejlede dig gennem oprettelse af en Azure VNET (det er trin 1).
- Taler af VNETs, se [den nye vejledning](remoteapp-vnetsizing.md) omkring VNET størrelse grænser og begrænsninger.
- Og nu vi taler om begrænsninger - lige hvad er [grænser og -standarder](../azure-subscription-service-limits.md)?

Vil du vide mere om Azure RemoteApp? RemoteApp teamet blev ud i kraft på Ignite et par uger gamle. Se Erics video, [feltet grundlæggende af Microsoft Azure RemoteApp styring og Administration](http://channel9.msdn.com/Events/Ignite/2015/BRK3868).

Brug for at se Azure RemoteApp i virkeligheden? Se [køre en app på en hvilken som helst enhed hvor som helst](remoteapp-anyapp.md) selvstudiet – det viser, hvordan du deler adgang med dine brugere, herunder deling databasefilerne. Vi har også en selvstudium på, [at Office 365](remoteapp-tutorial-o365anywhere.md) køre den samme på en hvilken som helst enhed.

Tak for at holde dig til os - tilbage næste måned med flere opdateringer.


### <a name="help-us-help-you"></a>Hjælp os med at hjælpe dig med at
Vidste du, at ud over vurdering i denne artikel og foretage kommentarer ned under, du kan foretage ændringer i artiklen selve? Noget gå glip af? Noget galt? Jeg skrive noget, som er lige forvirrende? Rulle op og klikke på **Rediger på GitHub** for at foretage ændringer – dem, der kommer til os til gennemsyn, og klik derefter, når vi logge af dem, får du vist dine ændringer og forbedringer her.
