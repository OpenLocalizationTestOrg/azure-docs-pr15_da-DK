
<properties 
    pageTitle="Hvordan du bruger Office 365-abonnement med Azure RemoteApp | Microsoft Azure"
    description="Få mere at vide, hvordan du kan bruge dit Office 365-abonnement i Azure RemoteApp til at dele Office-apps."
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-to-use-your-office-365-subscription-with-azure-remoteapp"></a>Hvordan du bruger Office 365-abonnement med Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Vidste du, at du kan bruge dit eksisterende Office 365-abonnement i Azure RemoteApp til at dele Office-apps fra skyen? Læs videre for at finde oplysninger om din Office 365 + Azure RemoteApp indstillinger, få herunder links til artikler om Office 365, der hjælper dig med at mest muligt ud af dit abonnement.

## <a name="how-do-i-use-office-365-accounts-for-azure-remoteapp"></a>Hvordan bruger jeg Office 365-konti til Azure RemoteApp?
Se Peters nye artikel for at få alle oplysninger: [Sådan bruger du Azure RemoteApp med Office 365-brugerkonti](remoteapp-o365user.md)

## <a name="can-i-use-my-office-365-subscription-to-run-office-applications-in-azure-remoteapp"></a>Kan jeg bruge min Office 365-abonnement til at køre Office-programmer i Azure RemoteApp?

Ja! Faktisk er med dit Office 365-abonnement den eneste måde til at hente dine Office-programmer til Azure RemoteApp.

(Bemærk: Hvis installationen af Azure RemoteApp leveres som en hosting-partner, kan de muligvis give dig Office licenser, der er baseret på en [Service Provider licensaftale](http://www.microsoft.com/en-us/Licensing/licensing-programs/spla-program.aspx))


Smarte om dit Office 365-abonnement er, at det kan du bruge den samme brugerlicens på mange forskellige platforme og -miljøer, herunder Azure skyen. Når du bruger Office-programmer i Azure RemoteApp kan du ikke behøver at købe flere licenser eller konfigurere dine eksisterende licenser i en speciel måde. Du skal blot et Office 365-abonnement, der omfatter [Office 365 ProPlus](https://technet.microsoft.com/library/Gg702619.aspx).

Office 365 ProPlus gør det muligt for [delt computeraktivering](https://technet.microsoft.com/library/Dn782860.aspx) – med denne funktion kan midlertidige bruger-baseret aktivering af Office i virtuelle og skyen miljøer som Azure RemoteApp (og Remote Desktop Services).

Hvilke Office 365-planer indeholder Office 365 ProPlus? Se tabellen [tjenesten kører i hver enkelt plan](https://technet.microsoft.com/library/office-365-plan-options.aspx) . Bemærk, at ikke alle planer omfatter Office 365 ProPlus (for eksempel Office 365 Business-plan). Hvis din plan ikke er tilfældet, kan du overveje at opgradere til en plan, der indeholder (for eksempel, Office 365 Education E3).

## <a name="ok-so-how-are-my-office-365-proplus-licenses-used-with-azure-remoteapp"></a>OK, så hvordan er mit Office 365 ProPlus licenser, der bruges med Azure RemoteApp?

Hver brugerlicens til Office 365 Pro plus kan en enkelt bruger aktivere Office-programmer på op til 5 computere plus tabletter og telefoner. Hver enkelt aktivering er registreret med brugeren, indtil de har deaktiveret Office på enheden. (Brugere kan administrere deres enheder i [Office 365-portalen](https://portal.office365.com/)).

Med Azure RemoteApp kan en enkelt bruger logge på flere computere på den samme ugedag uden at vide det. Det skyldes, at tjenesten automatisk administrerer og skaleres ressourcer i skyen, mens brugeren ser de tilhørende apps og programmer, du har delt. For dette scenario Office 365 ProPlus tilbyder en delt computer aktivering tilstand – det betyder, at brugeren ikke skal gøre en hvilken som helst håndtering af licenser for at få adgang til disse ressourcer og, der tæller de enkelte computere ikke mod grænsen på 5 computer aktivering.

Så længe du (admin) tildeler Office 365 ProPlus licenser til brugerne, kan de bruge Office på deres personlige enheder, samt gennem din Azure RemoteApp af websteder.

## <a name="which-office-applications-can-i-use-with-office-365-and-azure-remoteapp"></a>Hvilke Office-programmer kan jeg bruge sammen med Office 365 og Azure RemoteApp?

Du kan bruge dit Office 365-abonnement til at aktivere og dele Office 2013 i Azure RemoteApp installationer. Vi understøtter i øjeblikket ikke brugen af andre versioner af Office med Azure RemoteApp. Dette omfatter Office 2003, Office 2007, Office 2010 og Office 2016.

## <a name="what-about-visio-pro-or-project-pro"></a>Hvad med Visio Pro eller Project Pro?

Office 365 ProPlus billedet inkluderet i din RemoteApp-abonnement omfatter både Visio Pro og Project Pro. Men du kan ikke bruge dit Office 365 Pro Plus-abonnement til at aktivere disse programmer – de hver har deres egne licens. Du kan aktivere dem i [Office 365-portalen](https://portal.office365.com/). 

Du har ikke licens disse programmer, hvis du ikke vil bruge dem. Aktivér kun de programmer, du vil bruge - og springe de andre. Du kan stadig se dem i billedet, men du kan ikke bruge dem. 

## <a name="how-do-i-get-started-with-office-365-and-azure-remoteapp"></a>Hvordan kommer jeg i gang med Office 365 og Azure RemoteApp?

Nu hvor du kender detaljerne for Office 365-licensering, Lad os komme i gang med at bruge det i Azure RemoteApp - det er meget nemt:

Når du opretter din Azure RemoteApp af websteder, kan du bruge **Office 365 ProPlus (abonnement kræves)** billedet.

![Azure RemoteApp billede med Office 365 Pro Plus](./media/remoteapp-officesubscription/remoteapp-officeimage.png)


Dette billede viser den seneste version af Windows Server og Office 365 ProPlus. Når du har konfigureret din samling (herunder publicering apps), skal brugerne blot log på Azure RemoteApp (ved hjælp af deres RemoteApp-klienten) og angive deres Office 365-legitimationsoplysninger for en hvilken som helst Office-apps. Licenser leveres automatisk uden et sæt eller management påkrævet.

## <a name="can-i-create-a-custom-image-with-office-365-proplus"></a>Kan jeg oprette et brugerdefineret billede med Office 365 ProPlus?

Du kan oprette et brugerdefineret billede for dine websteder, der indeholder Office 365 ProPlus. Der er 2 valgmuligheder – bruge Azure galleribilledet vi giver dig eller du kan oprette din egen brugerdefinerede billede og installere Office 365 ProPlus der.

### <a name="use-the-azure-gallery-image"></a>Brug Azure galleriet Hurtige

Den nemmeste måde at installere Office 365 ProPlus til en samling er ved at [starte med et af billederne Azure galleriet](remoteapp-image-on-azurevm.md) inkluderet i abonnementet Azure RemoteApp. Kontrollér, at du vælger **Windows Server Remote Desktop sessionsværten med Office 365 ProPlus forudinstalleret** billedet. Installer derefter eventuelle andre ønskede-apps på billedet, og du er klar.

### <a name="use-a-custom-image"></a>Brug et brugerdefineret billede

Du kan altid oprette et brugerdefineret billede – du kan oprette en [Azure VM](remoteapp-image-on-azurevm.md) eller [oprette-billedet lokalt](remoteapp-create-custom-image.md) og overføre den til Azure. Sørg for, at du installerer Office 365 ProPlus ved hjælp af noden delt computer aktivering i begge tilfælde. Bruge [Office Udrulningsværktøj](http://blogs.technet.com/b/odsupport/archive/2014/07/11/using-the-office-deployment-tool.aspx) , og følg [instruktionerne](https://technet.microsoft.com/library/Dn782858.aspx) til installation.  

### <a name="disable-automatic-updates-for-office-365-proplus-in-your-custom-image---important"></a>Deaktivere Automatiske opdateringer til Office 365 Pro plus i den brugerdefinerede afbildning - vigtige

Den brugerdefinerede afbildning bruges af Azure RemoteApp som en skabelon for at tilføje yderligere ressourcer som behovet fra dine brugere øges. For at undgå forsinkelser og forbindelsesproblemer skal du deaktivere Automatiske opdateringer til Office i billedet. Hvis du ikke, og derefter hver ressource, der er oprettet med skabelonen opdateres automatisk, når den er startet. I stedet bruge Azure RemoteApp standardprocedure for at opdatere den brugerdefinerede afbildning. Den måde, du opdatere Office-programmerne én gang på skabelon billedet og derefter lade Azure RemoteApp tager sig for at få opdateringerne til brugerne.

Tilføj følgende konfigurationsfil Office Udrulningsværktøj for at deaktivere Automatiske opdateringer skal:

        <Updates Enabled="FALSE" />

Din konfigurationsfil bør nu indeholde disse linjer:
    
        <Display Level="NONE" AcceptEULA="TRUE" />
        <Property Name="SharedComputerLicensing" Value="1" />
        <Updates Enabled="FALSE" />

## <a name="so-how-can-i-update-an-image-with-office-365-proplus"></a>Så hvordan kan jeg opdatere et billede med Office 365 ProPlus?

Der findes mange grunde til at opdatere billedet i din samling. Her er blot nogle få:

- Få de seneste opdateringer til Windows 
- Få de nyeste opdateringer til Office 365 ProPlus
- Opdatere din brugerdefinerede app
- Ændre andre indstillinger for søgekonfiguration til selve billedet

Gå til slut trinnene til at opdatere din samling for at bruge det billede, du har opdateret, [her](remoteapp-update.md). Men se følgende oplysninger for at få oplysninger om, hvordan du opdaterer billedet og Office 365 ProPlus.

Du har to muligheder for at opdatere dit billede - erstatte billedet med et helt nyt kort eller manuelt opdatere din nuværende billede.

### <a name="replace-your-image-with-the-latest-azure-gallery-image--add-customizations"></a>Erstatte billedet med den seneste Azure galleriet Hurtige + Tilføj tilpasninger
Med denne indstilling, kan du lade Microsoft tager sig af Windows Server og Office 365 ProPlus opdateringer. I stedet for at opdatere din nuværende billede, vil du oprette et helt nyt billede, der er baseret på den seneste galleriet Hurtige. Gentag derefter de trin, som du gjorde tidligere du tilpasse billedet – installere brugerdefinerede apps, ændre billede konfiguration osv.

Galleriet billederne opdateres regelmæssigt, så du kan være nemt viden om, at dine Windows Server og Office 365 ProPlus apps er opdaterede. At tage er naturligvis, er du nødt til at anvende dine tilpasninger, hver gang du får et nyt billede. Du kan oprette scripts til at automatisere angive dine tilpasninger.

### <a name="manually-update-your-existing-image"></a>Manuelt opdatere din nuværende billede

Med denne indstilling, kan du bruge standard Windowsværktøjer til at anvende opdateringer til billedet. Bruge Office udrulningsværktøj til at downloade og installere de seneste opdateringer eller versioner af Office 365 ProPlus til Office 365 ProPlus.

> [AZURE.IMPORTANT] Husk – Deaktiver Office 365 ProPlus automatiske opdateringer.

Brug for yderligere oplysninger om brug af Office Udrulningsværktøj til opdateringer?

- [Installere Klik og Kør til Office 365-produkter ved hjælp af Office Udrulningsværktøj](https://technet.microsoft.com/library/JJ219423.aspx)
- [Installation og opdatering af Office 365 ProPlus ved hjælp af Office Udrulningsværktøj til](https://channel9.msdn.com/Events/Ignite/2015/BRK3168) (video)
- [Konfigurere indstillinger for opdatering til Office 365 Pro plus](https://technet.microsoft.com/library/dn761708.aspx)
