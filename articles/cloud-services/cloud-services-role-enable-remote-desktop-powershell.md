<properties
pageTitle="Aktivere Fjernskrivebord til en rolle i Azure Cloud Services ved hjælp af PowerShell"
description="Sådan konfigureres din azure skyen tjenesteprogram ved hjælp af PowerShell til at tillade forbindelser til Fjernskrivebord"
services="cloud-services"
documentationCenter=""
authors="thraka"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="08/05/2016"
ms.author="adegeo"/>

# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Aktivere Fjernskrivebord til en rolle i Azure Cloud Services ved hjælp af PowerShell

>[AZURE.SELECTOR]
- [Azure klassisk portal](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)


Remote Desktop gør det muligt at få adgang til skrivebordet af en rolle, der kører i Azure. Du kan bruge funktionen Fjernskrivebord til fejlfinding i forbindelse med og diagnosticere problemer med programmet, mens den kører.

I denne artikel beskrives, hvordan du aktiverer Fjernskrivebord på dine skyen Service roller, ved hjælp af PowerShell. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) til forudsætninger, der kræves for denne artikel. PowerShell anvender filtypenavnet Remote Desktop, så du kan aktivere Fjernskrivebord, når programmet er installeret.


## <a name="configure-remote-desktop-from-powershell"></a>Konfigurere Fjernskrivebord fra PowerShell

Cmdletten [Sæt AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) kan du aktivere Fjernskrivebord på angivne roller eller alle roller i skyen service installationen. Cmdletten kan du angive det brugernavn og adgangskoden til brugerens remote til computeren via parameteren *legitimationsoplysninger* , der accepterer et PSCredential objekt.

Hvis du bruger PowerShell interaktivt, kan du nemt indstille objektet PSCredential ved at ringe til Cmdletten [Get-legitimationsoplysninger](https://technet.microsoft.com/library/hh849815.aspx) .

```
$remoteusercredentials = Get-Credential
```

Denne kommando viser en dialogboks, så du kan angive det brugernavn og adgangskoden for den eksterne bruger på en sikker måde.

Da PowerShell hjælper i scenarier med automation, kan du også konfigurere objektet **PSCredential** på en måde, som ikke kræver brugerinput. Først skal du oprette en sikker adgangskode. Du starte med at angive en adgangskode til almindelig tekst konverteres til en sikker streng, der bruger [ConvertTo SecureString](https://technet.microsoft.com/library/hh849818.aspx). Derefter skal du konvertere denne sikker streng til en krypteret standard streng, der bruger [ConvertFrom SecureString](https://technet.microsoft.com/library/hh849814.aspx). Nu kan du gemme denne krypterede standard streng til en fil ved hjælp af [Sæt indhold](https://technet.microsoft.com/library/ee176959.aspx).

Du kan også oprette en sikker adgangskode-fil, så du ikke behøver at skrive i feltet adgangskode, hver gang. En sikker adgangskode fil er også bedre end en almindelig tekstfil. Brug følgende PowerShell til at oprette en sikker adgangskode fil:

```
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

>[AZURE.IMPORTANT] Når angive adgangskoden, skal du kontrollere, at du opfylder [kravene til kompleksitet](https://technet.microsoft.com/library/cc786468.aspx).

Hvis du vil oprette objektet legitimationsoplysninger fra filen sikker adgangskode, skal du læse filindholdet og konvertere dem tilbage til en sikker streng, der bruger [ConvertTo SecureString](https://technet.microsoft.com/library/hh849818.aspx).

[Angiv AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) cmdlet accepterer også en *udløb* parameter, der angiver en **dato/klokkeslæt** , hvor brugerkontoen udløber. For eksempel kan du angive kontoen, der udløber et par dage fra dags dato og klokkeslæt.

Dette PowerShell-eksempel viser, hvordan du angive filtypenavnet Remote Desktop på en skybaseret tjeneste:

```
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Du kan eventuelt også angive installation slot og roller, du vil aktivere Fjernskrivebord på. Hvis disse parametre ikke er angivet, aktiverer cmdlet Fjernskrivebord på alle roller i **fremstilling** installation slot.

Filtypenavnet Fjernskrivebord er knyttet til en installation. Hvis du opretter en ny installation for tjenesten, er det nødvendigt at aktivere Fjernskrivebord på denne installation. Hvis du altid vil have Fjernskrivebord aktiveret, skal derefter du overveje integrere PowerShell-scripts i arbejdsprocessen installation.


## <a name="remote-desktop-into-a-role-instance"></a>Remote Desktop til en forekomst af rolle
Cmdletten [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) bruges til at Fjernskrivebord til en bestemt rolle forekomst af skybaseret tjeneste. Du kan bruge parameteren *LocalPath* til at hente filen RDP lokalt. Eller du kan bruge parameteren *Start* for at åbne dialogboksen Fjernskrivebord for at få adgang til forekomsten af skyen rolle direkte.

```
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Kontrollere, om Fjernskrivebord lokalnummer er aktiveret på en tjeneste
Cmdletten [Get-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495261.aspx) viser, Fjernskrivebord er aktiveret eller deaktiveret på en tjeneste-installation. Cmdlet returnerer brugernavn for remote desktop brugeren og de roller, der er aktiveret i remote desktop udvidelse til. Dette sker på installation slotten som standard, og du kan vælge at bruge den midlertidige slot i stedet.

```
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Fjerne Fjernskrivebord filtypenavn fra en tjeneste
Hvis du allerede har aktiveret filtypenavnet remote til computeren på en installation og brug for at opdatere de indstillinger for Fjernskrivebord, først fjerne filtypenavnet. Og aktivere den igen med de nye indstillinger. For eksempel, hvis du vil angive en ny adgangskode for kontoen ekstern bruger, eller kontoen udløbet. Dette er påkrævet af eksisterende installationer, der har aktiveret remote skrivebordet filtypenavnet. For nye installationer, kan du anvende filtypenavnet direkte.

Hvis du vil fjerne filtypenavnet remote desktop fra installationen, kan du bruge [Fjern AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495280.aspx) cmdlet. Du kan eventuelt også angive installation slot og rolle, hvorfra du vil fjerne filtypenavnet remote desktop.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

>[AZURE.NOTE] For helt at fjerne lokalnummer konfigurationen, skal du ringe til Cmdletten *fjerne* med parameteren **UninstallConfiguration** .
>
>Parameteren **UninstallConfiguration** fjerner et lokalnummer konfiguration, der anvendes til tjenesten. Hver lokalnummer konfiguration er knyttet til konfigurationen af tjenesten. Filtypenavnet fjerne opkald Cmdletten *fjerne* uden **UninstallConfiguration** tilknytningen mellem <mark>installationen</mark> fra konfigurationen lokalnummer, og som derfor effektivt. Filtypenavn konfigurationen forbliver dog knyttet til tjenesten.



## <a name="additional-resources"></a>Yderligere ressourcer

[Sådan konfigureres Cloud Services](cloud-services-how-to-configure.md)
