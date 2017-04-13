
<properties 
    pageTitle="Hvordan gemmer Azure RemoteApp brugerdata og indstillinger for? | Microsoft Azure"
    description="Få mere at vide, hvordan Azure RemoteApp gemmer brugerdata ved hjælp af brugeren profil disken."
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

# <a name="how-does-azure-remoteapp-save-user-data-and-settings"></a>Hvordan gemmer Azure RemoteApp brugerdata og indstillinger for?

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Azure RemoteApp gemmer bruger-id og tilpasninger på tværs af enheder og sessioner. Denne brugerdata er gemt i en per bruger per samling disk, kendt som en bruger profil disk (hvorved UPD). Disken følger brugeren og sikrer, at brugeren har en ensartet oplevelse, uanset hvor de logge på. gemmer 

Bruger profil er helt gennemsigtig til brugeren – brugere gemme dokumenter i mappen dokumenter (på det ser ud til at være et lokalt drev) og ændre deres app indstillinger som normalt. Alle personlige indstillinger fastholdes på samme tid, når du opretter forbindelse til Azure RemoteApp fra enhver enhed. Alle brugeren ser er deres data på samme sted.

Hver hvorved UPD indeholder 50GB fast lager og den indeholder begge data og programmet brugerindstillinger. 

Læs videre for at få specifikke oplysninger på brugerdata profil.

>[AZURE.NOTE] Brug for at deaktivere hvorved UPD? Du kan gøre nu - udtjekningen Pavithras blogindlæg, [Deaktivere bruger profil diske (UPDs) i Azure RemoteApp](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/)kan få mere at vide.


## <a name="how-can-an-admin-get-to-the-data"></a>Hvordan kan en administrator til at komme til dataene?

Hvis du har brug at få adgang til dataene til en af dine brugere (for nedbrud, eller hvis brugeren forlader virksomheden), kan du kontakte Support til Azure og angive abonnementsoplysninger for gruppen af websteder og bruger-id. Azure RemoteApp teamet giver dig en URL-adressen til den virtuelle harddisk. Hent denne Virtuelle og hente alle dokumenter eller filer, du har brug for. Bemærk, at den virtuelle harddisk 50GB, så det kan tage nogle bit at hente den.


## <a name="is-the-data-backed-up"></a>Dataene, der sikkerhedskopieres?

Ja, vi gemme en sikkerhedskopi af brugerdataene per geografiske placering. Dataene er skrivebeskyttet og kan åbnes på samme måde, som de normale data ville være (kontakt Azure RemoteApp at få det), hvis den primære datacenter ned. Dataene er kopieret realtid til placeringen af sikkerhedskopien, og vi beholde ikke kopier af forskellige versioner. Vi vil ikke kunne gendanne den til en tidligere kendte gode version så på databeskadigelse, men hvis den primære datacenter er nede, du vil kunne få brugerdata fra anden placeringen.

## <a name="how-do-users-see-the-upd-on-the-server-side"></a>Hvordan kan brugere se hvorved UPD på serversiden?

Hver bruger skal være deres egen mappe på den server, der er tilknyttet deres hvorved UPD: c:\Users\username.

## <a name="whats-the-best-way-to-use-outlook-and-upd"></a>Hvad er den bedste måde at bruge Outlook og UPD?

Azure RemoteApp gemmer Outlook-tilstand (postkasser, pst-filer) mellem forskellige sessioner. Hvis du vil aktivere, vi har brug for pst-filen skal gemmes i profil brugerdata (c:\users\<brugernavn >). Dette er standardplaceringen for de data, så så længe du ikke ændre placeringen, som dataene bevares mellem forskellige sessioner.

Vi anbefaler, at du bruger "cachelagrede" tilstand i Outlook og bruger "server/online"-tilstand til søgning.

Se [denne artikel](remoteapp-outlook.md) for at få flere oplysninger om brug af Outlook og Azure RemoteApp.

## <a name="what-about-redirection"></a>Hvad med omdirigering?
Du kan konfigurere Azure RemoteApp for at lade brugerne adgang til lokale enheder ved at konfigurere [omdirigering](remoteapp-redirection.md). Lokale enheder vil derefter kunne få adgang til dataene på hvorved UPD.

## <a name="can-i-use-my-upd-as-a-network-share"></a>Kan jeg bruge min hvorved UPD som et netværksshare?
Nej. UPDs kan ikke bruges som et netværksshare. En hvorved UPD er kun tilgængelig for brugeren, når brugeren er aktivt tilsluttet Azure RemoteApp.

## <a name="if-i-delete-a-user-from-a-collection-is-their-upd-deleted"></a>Hvis jeg sletter en bruger fra en samling, hvorved deres UPD slettes?

Nej, når du sletter en bruger, vi Slet ikke automatisk på hvorved UPD - i stedet vi gemme dataene, indtil du slette gruppen af websteder. 90 dage, når du har slettet samlingen, slette vi alle UPDs. 

Hvis du vil slette en hvorved UPD fra en samling, ved at kontakte Azure RemoteApp - kan vi slette hvorved UPD vores side.

## <a name="can-i-access-my-users-upds-either-current-or-deleted-users"></a>Kan jeg få adgang til brugernes UPDs (aktuelle eller slettede brugere)?

Ja, hvis du kontakter [Azure RemoteApp](mailto:remoteappforum@microsoft.com), vi kan konfigurere du med en URL-adresse til at få adgang til dataene. Har du om 10 timer at hente data eller filer fra hvorved UPD før adgang til det udløber.

## <a name="are-upds-available-offline"></a>Er UPDs tilgængelige offline?

Nu er vi ikke yder offlineadgang til UPDs, ud over 10 time access-vinduet er beskrevet ovenfor. Det betyder, at vi ikke har en metode til at give dig adgang til langt nok til at udføre mere komplicerede opgaver, som kører antivirussoftware på UPDs eller få adgang til data til en revision.

## <a name="do-registry-key-settings-persist"></a>Vigtige Registreringsindstillingerne fastholdes?
Ja, noget skrevet til HKEY_Current_User er en del af hvorved UPD.

## <a name="can-i-disable-upds-for-a-collection"></a>Kan jeg deaktivere UPDs for en af websteder?

Ja, du kan stille Azure RemoteApp at deaktivere UPDs for et abonnement, men du kan gøre den selv. Det betyder, at UPDs deaktiveres for alle websteder i abonnement.

Du kan vælge at slå UPDs i en af følgende situationer: 

- Du skal fuldføre adgang og kontrol af brugerdata (for overvåge og gennemse formål som finansielle institutioner).
- Du har 3 tredjepart bruger profil management-løsninger, der i det lokale miljø og vil fortsætte med at bruge dem i dit domæne, der er joinforbundne Azure RemoteApp-installation. Dette kræver profil agent ind i gold billedet. 
- Du ikke har brug for en hvilken som helst lokale datalager, eller du har alle data i skyen eller et filshare og gerne vil kontrolelementet lagring af data lokalt ved hjælp af Azure RemoteApp.

Du kan få flere oplysninger i [Deaktivere bruger profil diske (UPDs) i Azure RemoteApp](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/) .

## <a name="can-i-restrict-users-from-saving-data-to-the-system-drive"></a>Kan jeg forhindre brugere i at gemme data på systemdrevet?

Ja, men du er nødt til at konfigurere, på i skabelon billedet, før du opretter samlingen. Brug følgende trin til at blokere adgang til systemdrevet:

1. Køre **gpedit.msc** på skabelon billedet.
2. Gå til **Brugerkonfiguration > Administrative skabeloner > Windows-komponenter > Explorer**.
3. Vælg følgende indstillinger:
    - **Skjul disse angivne drev i denne Computer**
    - **Forhindre adgang til drev fra denne Computer**

## <a name="can-i-seed-upds-i-want-to-put-some-data-in-the-upd-thats-available-the-first-time-the-user-signs-in"></a>Kan jeg podes UPDs? Jeg vil placere nogle data i den hvorved UPD, der er tilgængelig, første gang brugeren logger ind.

Ja, når du opretter skabelon billedet, kan du føje oplysninger til standardprofilen. Disse oplysninger føjes derefter til hvorved UPD.

## <a name="can-i-change-the-size-of-the-upd-depending-on-how-much-data-i-want-to-store"></a>Kan jeg ændre størrelsen på hvorved UPD afhængigt af hvor meget data, jeg vil gemme?

Nej, alle UPDs har 50 GB lagerplads. Hvis du vil gemme forskellige mængde data, skal du prøve følgende:

1. Deaktivere UPDs for gruppen af websteder.
2. Konfigurere et filshare for brugerne at få adgang til.
3. Indlæse det pågældende filshare ved hjælp af et script til start. Se under få at vide om scripts til start i Azure RemoteApp.
4. Give brugerne adgang til at gemme alle data i filen dele.


## <a name="how-do-i-run-a-startup-script-in-azure-remoteapp"></a>Hvordan kan jeg køre et script til start i Azure RemoteApp?

Hvis du vil køre et script til Start, skal du starte med at oprette en planlagt opgave på skabelonbillede, du skal bruge til samlingen. (Gør denne *før* du kører sysprep.) 

![Oprette en systemopgave](./media/remoteapp-upd/upd1.png)

![Oprette et systemopgave, som afspilles, når en bruger logger på](./media/remoteapp-upd/upd2.png)

På fanen **Generelt** skal du sørge for at ændre den **Brugerkonto** , under sikkerhed for at "BUILTIN\Users."

![Ændre brugerkontoen til en gruppe](./media/remoteapp-upd/upd4.png)

Den planlagte opgave åbne din startscript ved hjælp af brugerens legitimationsoplysninger. Planlægge opgaven skal køre alle på en gang en bruger logger på.

![Angive udløser til opgaven som "ved logon"](./media/remoteapp-upd/upd3.png)

Du kan også bruge [scripts til baseret på gruppepolitik Start](https://technet.microsoft.com/library/cc779329%28v=ws.10%29.aspx). 

## <a name="what-about-placing-a-startup-script-in-the-start-menu-would-that-work"></a>Hvad med placere et script til start i menuen Start? Vil det virker?

Det vil sige, kan jeg oprette en .bat-fil, der kører et config vinduet script og gemme den til mappen c:\ProgramData\Microsoft\Windows\Start Start\Programmer\Start, og derefter køre, når en bruger starter en session med RemoteApp scriptet?

Nej, der ikke understøttes med Azure RemoteApp, som bruger RDSH, som også ikke understøtter scripts til start af i menuen Start.

## <a name="can-i-use-mstscexe-the-remote-desktop-program-to-configure-logon-scripts"></a>Kan jeg bruge mstsc.exe (Remote Desktop-program) til at konfigurere logon-scripts?

Nej, understøttes ikke af Azure RemoteApp.

## <a name="can-i-store-data-on-the-vm-locally"></a>Kan jeg gemme data på VM lokalt?

Nej, mistes data, der gemmes et vilkårligt sted på VM bortset fra i hvorved UPD. Der er stor risiko for brugeren ikke er den samme VM næste gang de logger ind i Azure RemoteApp. Vi opretholde ikke bruger VM brugerdata, så brugeren ikke kan logge på den samme VM og dataene, går tabt. Desuden, når vi opdaterer samlingen, erstattes de eksisterende FOS med et nyt sæt FOS -, der betyder data er gemt på VM selve går tabt. Det anbefales, at gemme data i hvorved UPD, delt storage som Azure-filer, en filserver i en VNET eller på skyen ved hjælp af et cloud storage-system som DropBox.

## <a name="how-do-i-mount-an-azure-file-share-on-a-vm-using-powershell"></a>Hvordan tilslutte en Azure filshare på en VM, ved hjælp af PowerShell?

Du kan bruge cmdlet'en redskaber PSDrive til at tilslutte drevet, på følgende måde:

    New-PSDrive -Name <drive-name> -PSProvider FileSystem -Root \\<storage-account-name>.file.core.windows.net\<share-name> -Credential :<storage-account-name>


Du kan også gemme dine legitimationsoplysninger ved at køre følgende:

    cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>


Kan du springe - legitimationsoplysninger for parameteren i ny PSDrive cmdlet.
