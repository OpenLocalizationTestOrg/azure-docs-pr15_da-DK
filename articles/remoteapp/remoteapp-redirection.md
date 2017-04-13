<properties
    pageTitle="Brug af omdirigering i Azure RemoteApp | Microsoft Azure"
    description="Lær, hvordan du konfigurerer og bruger omdirigering i RemoteApp"
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

# <a name="using-redirection-in-azure-remoteapp"></a>Brug af omdirigering i Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Omdirigering af kan brugerne interagere med remote apps ved hjælp af de enheder, der er knyttet til deres lokale computer, telefon eller tablet. Eksempelvis hvis du har angivet Skype via Azure RemoteApp, skal din bruger kameraet installeret på deres PC til at fungere sammen med Skype. Dette gælder også for printere, højttalere, skærme og et område af USB-tilsluttet enheder.

RemoteApp anvender den RDP Remote Desktop Protocol () og RemoteFX for at give omdirigering.

## <a name="what-redirection-is-enabled-by-default"></a>Hvilke omdirigering er aktiveret som standard?
Når du bruger RemoteApp, er følgende omdirigeringer aktiveret som standard. Oplysningerne i parenteser viser indstillingen RDP.

- Afspil lyde på den lokale computer (**afspilles på denne computer**). (audiomode:i:0)
- Registrere lyd fra den lokale computer, og send til fjerncomputeren (**post fra denne computer**). (audiocapturemode:i:1)
- Udskriv til lokale printere (redirectprinters:i:1)
- COM-porte (redirectcomports:i:1)
- Chipkort enhed (redirectsmartcards:i:1)
- Udklipsholder (mulighed for at kopiere og indsætte) (redirectclipboard:i:1)
- Fjern markeringen i type skrifttype udglatning (Tillad Skrifttype-udjævning: i:1)
- Omdirigere alle understøttede Plug and Play-enheder. (devicestoredirect:s: *)

## <a name="what-other-redirection-is-available"></a>Hvilke andre omdirigering er tilgængelig?
To omdirigeringsindstillinger er deaktiveret som standard:

- Omdirigering af drev (drevtilknytning): computerens lokale drev bliver tilknyttede drev i den fjernsession. Dette kan du gemme eller åbne filer fra dit lokale drev, mens du arbejder i den fjernsession.
- USB-omdirigering: Du kan bruge de USB-enheder, der er knyttet til din lokale computer i den fjernsession.

## <a name="change-your-redirection-settings-in-remoteapp"></a>Ændre indstillingerne for omdirigering i RemoteApp
Du kan ændre indstillinger for enhed omdirigering for en samling ved hjælp af Microsoft Azure PowerShell med SDK. Når du har installeret den nye PowerShell og SDK, først konfigureres til at administrere dit abonnement, som beskrevet i [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

Brug derefter en stil med følgende kommando til at angive brugerdefinerede RDP egenskaber:

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

(Bemærk pågældende *n* bruges som en afgrænser mellem individuelle egenskaber).

For at få en liste over de brugerdefinerede RDP-egenskaber er konfigureret, skal du køre følgende cmdlet. Bemærk, at kun brugerdefinerede egenskaber vises som outputresultater og ikke standardegenskaberne:  

    Get-AzureRemoteAppCollection -CollectionName <collection name>

Når du indstiller brugerdefinerede egenskaber skal du angive alle de brugerdefinerede egenskaber hver gang Ellers igen indstillingen til deaktiveret.   

### <a name="common-examples"></a>Almindelige eksempler
Brug følgende cmdlet til at aktivere omdirigering af drev:  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*”

Brug denne cmdlet til at aktivere både USB- og drev omdirigering:

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

Brug denne cmdlet til at deaktivere Udklipsholder deling:  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "redirectclipboard:i:0”

> [AZURE.IMPORTANT] Sørg for at helt log alle brugere i gruppen af websteder (og ikke blot afbryde dem) før du tester ændringen. Gå til fanen **sessioner** i gruppen af websteder i portalen Azure for at sikre, at brugere er helt logget af, og log af alle brugere, der er afbrudt eller logget på. Nogle gange kan det tage nogle sekunder, inden de lokale drev til at vise i Stifinder i sessionen.

## <a name="change-usb-redirection-settings-on-your-windows-client"></a>Ændre indstillinger for USB-omdirigering på din Windows-klient

Hvis du vil bruge USB-omdirigering på en computer, der opretter forbinder til RemoteApp, er der 2 handlinger, der skal ske. 1 – din administrator skal aktivere USB-omdirigering på niveauet af websteder ved hjælp af Azure PowerShell. 2 – på hver enhed, hvor du vil bruge USB-omdirigering, skal du aktivere en gruppepolitik, der tillader, at den. Dette trin skal udføres for hver bruger, som ønsker at bruge USB-omdirigering.

> [AZURE.NOTE] USB-omdirigering med Azure RemoteApp understøttes kun til Windows-computere.

### <a name="enable-usb-redirection-for-the-remoteapp-collection"></a>Aktivere USB-omdirigering til samlingen RemoteApp
Brug følgende cmdlet til at aktivere USB-omdirigering på niveauet af websteder:

    Set-AzureRemoteAppCollection -CollectionName <collection_name> -CustomRdpProperty "nusbdevicestoredirect:s:*"

### <a name="enable-usb-redirection-for-the-client-computer"></a>Aktivere USB-omdirigering for klientcomputeren

Sådan konfigureres USB-omdirigeringsindstillinger på din computer:

1. Åbn den gruppepolitikken (GPEDIT. MSC). (Køre gpedit.msc fra en kommandoprompt).
2. Åbn **Computer Configuration\Policies\Administrative brugerne Components\Remote computeren Services\Remote Desktop Connection Client\RemoteFX USB-enhedsomdirigering**.
3. Dobbeltklik på **Tillad RDP omdirigering af andre understøttede RemoteFX USB-enheder fra denne computer**.
4. Vælg **aktiveret**, og vælg derefter **Administratorer og brugere i RemoteFX USB-omdirigering adgangsrettigheder**.
5. Åbn en kommandoprompt med administratorrettigheder og køre følgende kommando:

        gpupdate /force
6. Genstart computeren.

Du kan også bruge værktøjet administration af gruppepolitik til at oprette og anvende politikken USB-omdirigering for alle computere på dit domæne:

1. Log på domænecontrolleren som domæneadministrator.
2. Åbn Group Policy Management Console. (Klik på **Start > Administration > Administration af gruppepolitik**.)
3. Gå til det domæne eller organisationsenhed, du vil oprette politikken.
4. Højreklik på **Politik for standarddomæne**, og klik derefter på **Rediger**.
5. Åbn **Computer Configuration\Policies\Administrative brugerne Components\Remote computeren Services\Remote Desktop Connection Client\RemoteFX USB-enhedsomdirigering**.
6. Dobbeltklik på **Tillad RDP omdirigering af andre understøttede RemoteFX USB-enheder fra denne computer**.
7. Vælg **aktiveret**, og vælg derefter **Administratorer og brugere i RemoteFX USB-omdirigering adgangsrettigheder**.
8. Klik på **OK**.  
