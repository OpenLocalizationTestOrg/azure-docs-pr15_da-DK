<properties 
    pageTitle="Hvordan omdirigerer du USB-enheder i Azure RemoteApp? | Microsoft Azure" 
    description="Lær at bruge omdirigering til USB-enheder i Azure RemoteApp." 
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



# <a name="how-do-you-redirect-usb-devices-in-azure-remoteapp"></a>Hvordan omdirigerer du USB-enheder i Azure RemoteApp?

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Omdirigering af kan brugere kan bruge de USB-enheder, der er knyttet til deres computer eller tablet med apps i Azure RemoteApp. Eksempelvis hvis du har delt Skype via Azure RemoteApp, skal brugerne kunne bruge deres enhed kameraer.

Før du går videre, skal du kontrollere du læse USB-omdirigering oplysninger i [Brug af omdirigering i Azure RemoteApp](remoteapp-redirection.md). Men det anbefales nusbdevicestoredirect:s: * ikke fungerer, USB-webkameraer og fungerer muligvis ikke for nogle USB-printere eller USB-multifunktionelle enheder. Ved design og af sikkerhedsmæssige årsager skal Azure RemoteApp administratoren aktivere omdirigering ved enhedsklasse-GUID eller ved at enheds forekomst-ID, før dine brugere kan bruge disse enheder.

Selvom denne artikel omhandler web kamera omdirigering, kan du bruge en lignende fremgangsmåde til at omdirigere USB-printere og andre multifunktionelle USB-enheder, der ikke er omdirigeret af den **nusbdevicestoredirect:s:*** kommandoen.

## <a name="redirection-options-for-usb-devices"></a>Omdirigering af indstillinger for USB-enheder
Azure RemoteApp bruger minder meget om mekanismer til at omdirigere USB-enheder som dem, der findes til Remote Desktop Services. Den underliggende teknologi kan du vælge den korrekte omdirigering metode til en given enhed, for at få bedst fra begge overordnet og RemoteFX USB-enhed omdirigering ved hjælp af den **usbdevicestoredirect:s:** kommandoen. Der er fire elementer til denne kommando:

| Behandlingsrækkefølgen | Parameter           | Beskrivelse                                                                                                                |
|------------------|---------------------|----------------------------------------------------------------------------------------------------------------------------|
| 1                | *                   | Markerer alle enheder, der ikke er valgte ved overordnet omdirigering. Bemærk: ved design * fungerer ikke for USB-webkameraer.  |
|                  | {Enhedsklasse-GUID} | Markerer alle enheder, der svarer til den angivne enhed konfiguration klasse.                                                           |
|                  | USB\InstanceID      | Markerer en USB-enhed, der er angivet for den angivne forekomst-ID.                                                                  |
| 2                | -USB\Instance-ID    | Fjerner indstillinger for omdirigering for den angivne enhed.                                                                 |

## <a name="redirecting-a-usb-device-by-using-the-device-class-guid"></a>Omdirigere en USB-enhed ved hjælp af enhedsklassen GUID
Der er to måder at finde enhedsklassen GUID, der kan bruges til omdirigering. 

Den første indstilling er at bruge de [Systemdefinerede enhed konfiguration klasser tilgængelige for leverandører](https://msdn.microsoft.com/library/windows/hardware/ff553426.aspx). Vælg den klasse, der passer bedst til den enhed, der er knyttet til den lokale computer. Det kan være en billedenhed klasse eller registrere videoenhed klasse for digitale kameraer. Du skal gøre eksperimentere med enhedsklasser til at finde den korrekte klasse-GUID, der fungerer sammen med den lokalt tilknyttede USB-enhed (i dette tilfælde webkameraet).

En bedre måde eller den anden mulighed er at følge disse trin for at finde den ønskede enhedsklasse-GUID:

1. Åbn Enhedshåndtering, Find den enhed, bliver brugeren omdirigeret og højreklik på den, og Åbn derefter på Egenskaber.
![Åbn Enhedshåndtering](./media/remoteapp-usbredir/ra-devicemanager.png)
2. Vælg egenskaben **Class Guid**på fanen **Detaljer** . Den værdi, som vises er klasse-GUID for den pågældende enhed.
![Egenskaber for kamera](./media/remoteapp-usbredir/ra-classguid.png)
3. Brug værdien fra klasse Guid til at omdirigere enheder, der svarer til den.

Eksempel:

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s:<Class Guid value>"

Du kan kombinere flere enhed omdirigeringer i samme cmdlet. For eksempel: Hvis du vil omdirigere lokale lager og et USB-web-kamera, cmdlet ser sådan ud:

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:<Class Guid value>"

Når du indstiller enhedsomdirigering af klasse GUID omdirigeres alle enheder, der svarer til klassen-GUID i den angivne samling. Hvis der er flere computere i det lokale netværk, der har samme USB-webkameraer, kan du køre en enkelt cmdlet for at omdirigere alle webkameraer.

## <a name="redirecting-a-usb-device-by-using-the-device-instance-id"></a>Omdirigere en USB-enhed ved hjælp af enhed forekomst-ID

Hvis du vil have mere detaljerede kontrol og vil styre omdirigering per enhed, kan du bruge parameteren **USB\InstanceID** omdirigering.

Den sværeste del af denne metode er at finde id USB-enhed forekomst. Du skal have adgang til computeren og den ønskede USB-enhed. Følg disse trin:

1. Aktivere enhedsomdirigering i skrivebord fjernsession, som beskrevet i [hvordan kan jeg bruge mine enheder og ressourcer i en session med Fjernskrivebord?](http://windows.microsoft.com/en-us/windows7/How-can-I-use-my-devices-and-resources-in-a-Remote-Desktop-session)
2. Åbn en forbindelse til Fjernskrivebord, og klik på **Vis indstillinger**.
3. Klik på **Gem som** for at gemme de aktuelle forbindelsesindstillinger i en RDP-fil.  
    ![Gemme indstillingerne som en RDP-fil](./media/remoteapp-usbredir/ra-saveasrdp.png)
4. Vælg et filnavn og en placering, for eksempel "MyConnection.rdp" og "Denne PC\Documents", og Gem filen.
5. Åbn filen MyConnection.rdp ved hjælp af en teksteditor, og find den enhed, du vil omdirigere forekomst ID.

Nu, bruge forekomst-ID i følgende cmdlet:

    Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s: USB\<Device InstanceID value>"



### <a name="help-us-help-you"></a>Hjælp os med at hjælpe dig med at 
Vidste du, at ud over vurdering i denne artikel og foretage kommentarer ned under, du kan foretage ændringer i artiklen selve? Noget gå glip af? Noget galt? Jeg skrive noget, som er lige forvirrende? Rulle op og klikke på **Rediger på GitHub** for at foretage ændringer – dem, der kommer til os til gennemsyn, og klik derefter, når vi logge af dem, får du vist dine ændringer og forbedringer her.