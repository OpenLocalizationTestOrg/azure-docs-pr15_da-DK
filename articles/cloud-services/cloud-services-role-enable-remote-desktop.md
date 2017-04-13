<properties 
pageTitle="Aktivere Remote Desktop Connection til en rolle i Azure Cloud Services" 
description="Sådan konfigureres din azure skyen tjenesteprogrammet for at tillade forbindelser til Fjernskrivebord" 
services="cloud-services" 
documentationCenter="" 
authors="sbtron" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="02/17/2016" 
ms.author="saurabh"/>

# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Aktivere Remote Desktop Connection til en rolle i Azure Cloud Services

>[AZURE.SELECTOR]
- [Azure klassisk portal](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)


Remote Desktop gør det muligt at få adgang til skrivebordet af en rolle, der kører i Azure. Du kan bruge funktionen Fjernskrivebord til fejlfinding i forbindelse med og diagnosticere problemer med programmet, mens den kører. 

Du kan aktivere en forbindelse til Fjernskrivebord i din rolle under udviklingen ved at medtage Fjernskrivebord modulerne i tjenestedefinitionen for din, eller du kan vælge at aktivere Fjernskrivebord gennem filtypenavnet Remote Desktop. Den foretrukne metode egner sig til at bruge filtypenavnet Fjernskrivebord, som du kan aktivere Fjernskrivebord, selv efter at programmet installeres uden at skulle Geninstaller dit program. 


## <a name="configure-remote-desktop-from-the-azure-classic-portal"></a>Konfigurere Fjernskrivebord fra portalen Azure klassisk
Azure klassisk portalen bruger Remote Desktop lokalnummer fremgangsmåde, så du kan aktivere Fjernskrivebord, selv efter at programmet installeres. Siden **Konfigurer** for skybaseret tjeneste, kan du aktivere Fjernskrivebord, skal du ændre den lokale administratorkonto, der bruges til at oprette forbindelse til de virtuelle maskiner, certifikatet, der bruges til godkendelse og angive udløbsdatoen. 


1. Klik på **Cloud Services**, klik på navnet på skytjenesten og derefter klikke på **Konfigurer**.

2. Klik på **Fjern**.
    
    ![Remote-skytjenester](./media/cloud-services-role-enable-remote-desktop/CloudServices_Remote.png)
    
    > [AZURE.WARNING] Alle forekomster af rolle genstartes, når du først aktivere Fjernskrivebord, og klik på OK (markering). Det certifikat, der bruges til at kryptere adgangskoden skal være installeret på rollen, for at forhindre en genstart. Du vil forhindre genstart, [overføre et certifikat til skytjenesten](cloud-services-how-to-create-deploy/#how-to-upload-a-certificate-for-a-cloud-service) , og vend derefter tilbage til denne dialogboks.
    

3. Vælg den rolle, du vil opdatere eller vælge **alle** for alle roller i **roller**.

4. Foretag en af følgende ændringer:
    
    - For at aktivere Fjernskrivebord skal du markere afkrydsningsfeltet **Aktivér Fjernskrivebord** . Fjern markeringen i afkrydsningsfeltet for at deaktivere Fjernskrivebord.
    
    - Oprette en konto, der skal bruges i Fjernskrivebord forbindelser til rolle forekomster.
    
    - Opdatere adgangskoden for den eksisterende konto.
    
    - Vælg en overførte certifikat skal bruges for godkendelse (upload det certifikat, der bruger **overføre** på siden **certifikaterne** ) eller oprette et nyt certifikat. 
    
    - Ændre udløbsdatoen for konfigurationen af Fjernskrivebord.

5. Når du er færdig med din konfiguration opdateringer, skal du klikke på **OK** (markering).


## <a name="remote-into-role-instances"></a>Remote til rolle forekomster
Når Fjernskrivebord er aktiveret på rollerne, der kan du remote til en rolle forekomst gennem forskellige værktøjer.

Oprette forbindelse til en rolle forekomst fra Azure klassisk portalen:
    
  1.   Klik på **forekomster** for at åbne siden **forekomster** .
  2.   Vælg en rolle-forekomst, der indeholder Fjernskrivebord, der er konfigureret.
  3.   Klik på **Opret forbindelse**, og følg vejledningen for at åbne på skrivebordet. 
  4.   Klik på **Åbn** og derefter **Opret forbindelse** for at starte Fjernskrivebord forbindelsen. 


### <a name="use-visual-studio-to-remote-into-a-role-instance"></a>Brug Visual Studio til at remote i en forekomst af rolle

I Visual Studio, Server Explorer:

1. Udvid den **Azure\\Skytjenester\\[skyen service navn]** node.
2. Udvid **midlertidige** eller **fremstilling**.
3. Udvid den enkelte rolle.
4. Højreklik på en af de rolle forekomster, skal du klikke på **Opret forbindelse ved hjælp af Fjernskrivebord...**, og derefter angive det brugernavn og adgangskode. 

![Server explorer Fjernskrivebord](./media/cloud-services-role-enable-remote-desktop/ServerExplorer_RemoteDesktop.png)


### <a name="use-powershell-to-get-the-rdp-file"></a>Bruge PowerShell til at få RDP-fil
Du kan bruge cmdlet'en [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) til at hente RDP-fil. Du kan derefter bruge RDP-fil med Fjernskrivebord til at få adgang til tjenesten skyen.

### <a name="programmatically-download-the-rdp-file-through-the-service-management-rest-api"></a>Fra et program hente filen RDP gennem Service Management REST-API
Du kan bruge handlingen [Overførselsfilen RDP](https://msdn.microsoft.com/library/jj157183.aspx) RESTEN til at downloade RDP-fil. 



## <a name="to-configure-remote-desktop-in-the-service-definition-file"></a>Du kan konfigurere Fjernskrivebord i definitionsfil service

Denne metode kan du aktivere Fjernskrivebord for programmet under udvikling. Denne fremgangsmåde kræver krypterede adgangskoder gemmes i konfigurationen af tjenesten fil og eventuelle opdateringer af remote desktop konfigurationen vil kræver en geninstallation af programmet. Hvis du vil undgå disse downsides skal du bruge metoden remote desktop lokalnummer baseret er beskrevet ovenfor.  

Du kan bruge Visual Studio til at [aktivere en forbindelse til Fjernskrivebord](../vs-azure-tools-remote-desktop-roles.md) ved hjælp af tjenesten definition fil fremgangsmåde.  
Følgende fremgangsmåde beskriver de ændringer, der er behov for at modelfiler tjeneste til at aktivere Fjernskrivebord. Visual Studio opretter automatisk ændringerne, når du udgiver.

### <a name="set-up-the-connection-in-the-service-model"></a>Konfigurere forbindelsen i modellen, service 
Bruge elementet **importen** til at importere modulet **Remote Access** og modulet **RemoteForwarder** til filen [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef) .

Tjenesten definitionsfil skal ligne eksemplet nedenfor med den `<Imports>` element, der er tilføjet.

```xml
<ServiceDefinition name="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2013-03.2.0">
    <WebRole name="WebRole1" vmsize="Small">
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="Endpoint1" endpointName="Endpoint1" />
                </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
            <Import moduleName="Diagnostics" />
            <Import moduleName="RemoteAccess" />
            <Import moduleName="RemoteForwarder" />
        </Imports>
    </WebRole>
</ServiceDefinition>
```
Filen [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg) skal ligne eksemplet nedenfor, Bemærk de `<ConfigurationSettings>` og `<Certificates>` elementer. Det certifikat, der er angivet skal [overføres til skytjenesten](../cloud-services-how-to-create-deploy.md#how-to-upload-a-certificate-for-a-cloud-service).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2013-03.2.0">
    <Role name="WebRole1">
        <Instances count="2" />
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="[name-of-user-account]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="[base-64-encrypted-user-password]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="[certificate-expiration]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
        </ConfigurationSettings>
        <Certificates>
            <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="[certificate-thumbprint]" thumbprintAlgorithm="sha1" />
        </Certificates>
    </Role>
</ServiceConfiguration>
```


## <a name="additional-resources"></a>Yderligere ressourcer

[Sådan konfigureres Cloud Services](cloud-services-how-to-configure.md)