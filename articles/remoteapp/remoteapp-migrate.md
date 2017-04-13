
<properties
    pageTitle="Overføre brugerdata fra Azure RemoteApp | Microsoft Azure"
    description="Lær at overføre dine brugerdata og Azure RemoteApp."
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



# <a name="how-to-migrate-data-into-and-out-of-azure-remoteapp"></a>Sådan overføres data til og fra Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Du kan bruge mange forskellige værktøjer og metoder til at overføre [brugerdata](remoteapp-upd.md) til og fra Azure RemoteApp. Her er nogle metoder:

- Kopiere og indsætte ved hjælp af Udklipsholder Deling
- Kopiere filer og data på en filserver
- Kopiere filer til OneDrive for Business via en browser
- Kopiere filer ved hjælp af omdirigering

>[AZURE.NOTE] 
> Du kan ikke aktivere OneDrive for Business eller forbruger Synkroniser supportmedarbejdere - de [understøttes ikke](remoteapp-onedrive.md) i Azure RemoteApp.

## <a name="use-copy-and-paste-in-file-explorer"></a>Brug Kopiér og sæt ind i Stifinder

Kopiere og indsætte ved hjælp af Udklipsholder er aktiveret i RemoteApp installationer [som standard](remoteapp-redirection.md). Dette gør det muligt at kopiere filer mellem deres lokale PC og RemoteApp apps. Ofte, i normal løbet af brug af apps i RemoteApp har brugere gemt filer til deres UPDs - flytning af RemoteApp der nemt:

1. [Publicere Stifinder som en app](remoteapp-publish.md) i en RemoteApp af websteder. (Bemærk, at dette er en administrativ opgave).
2. Få dine brugere at starte appen Stifinder, du publicerede og for at bruge den til at kopiere og indsætte filer, både i deres hvorved UPD og ud af det.

## <a name="upload-files-and-data-to-a-file-server-by-using-standard-network-file-copy"></a>Overføre filer og data på en filserver ved hjælp af almindeligt netværk kopiering af fil

Ofte organisationer bruger filservere til at gemme generelle data. Hvis du kender servernavnet eller placering, kan brugerne gå det lokale netværk til serveren til og derefter kopiere deres filer, der, samme måde, som de gjorde ovenfor. Igen skal du vil publicere Stifinder på RemoteApp og derefter dele den med dine brugere.

>[AZURE.NOTE] 
> Filserveren skal være på distribueret netværket, der blev installeret RemoteApp til.

## <a name="copy-files-to-onedrive-for-business"></a>Kopiere filer til OneDrive for Business
Selvom du ikke aktiverer OneDrive for Business Synkroniser agent i RemoteApp, kan du stadig kopiere filer fra din hvorved UPD til OneDrive for Business via en browser. 

1. Udgive Stifinder på RemoteApp og derefter fortælle brugerne adgang til filerne via denne app. 
2. Det er lettest at overføre filer, hvis de er komprimeret, så brugerne skal oprette en .zip-fil, der indeholder alle filerne til at flytte til OneDrive for Business.
3. Bed brugerne om at gå til Office 365-portalen, og gå til OneDrive, og Overfør .zip-filen.

## <a name="copy-files-by-using-drive-redirection"></a>Kopiere filer ved hjælp af omdirigering af drev

Hvis du har aktiveret [omdirigering af drev](remoteapp-redirection.md), har du allerede oprettet et tilknyttet drev for dine brugere. I dette tilfælde kan de zip deres filer på omdirigeret drev og derefter gemme dem til deres lokale PC.