<properties 
pageTitle="Cloud Services-rolle config XPath snyde ark | Microsoft Azure" 
description="De forskellige XPath indstillinger du kan bruge i skyen service rolle config at få vist indstillinger som en miljøvariablen." 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="08/10/2016" 
ms.author="adegeo"/>

# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Få vist indstillinger for søgekonfiguration rolle som en miljøvariablen med XPath

I skyen tjenesten arbejder eller web rolle service definitionsfil, kan du fremvise runtime konfigurationsværdier som miljøvariabler. Følgende XPath værdier understøttes (som svarer til API værdier).

Disse XPath-værdier er også tilgængelig via biblioteket [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) . 

## <a name="app-running-in-emulator"></a>APP'en kører i emulator

Angiver, at app'en kører i emulatoren.

| Type  | Eksempel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@emulated" |
| Kode  | varians x = RoleEnvironment.IsEmulated; |


## <a name="deployment-id"></a>Installation-ID

Henter installation-ID'ET for forekomsten.

| Type  | Eksempel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@id" |
| Kode  | varians deploymentId = RoleEnvironment.DeploymentId; |


## <a name="role-id"></a>Rolle-ID 

Henter den aktuelle rolle-ID'ET for forekomsten.

| Type  | Eksempel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@id" |
| Kode  | varians id = RoleEnvironment.CurrentRoleInstance.Id; |


## <a name="update-domain"></a>Opdatere domæne

Henter opdateringen domænet for forekomsten.

| Type  | Eksempel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Kode  | varians ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |


## <a name="fault-domain"></a>Et domæne

Henter fejl domænet for forekomsten.

| Type  | Eksempel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Kode  | varians fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |


## <a name="role-name"></a>Rollenavn

Henter rollenavnet på forekomsterne.

| Type  | Eksempel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Kode  | varians rname = RoleEnvironment.CurrentRoleInstance.Role.Name;  |


## <a name="config-setting"></a>Config indstilling

Henter værdien af den angivne indstilling.

| Type  | Eksempel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Kode  | varians indstilling = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |
 
## <a name="local-storage-path"></a>Lokale lager sti

Henter lokale lagerstien til forekomsten.

| Type  | Eksempel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Kode  | varians localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1"). RootPath; |


## <a name="local-storage-size"></a>Lokale lagerstørrelse

Henter størrelsen på det lokale lager til forekomsten.

| Type  | Eksempel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Kode  | varians localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1"). MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Slutpunkt protokol 

Henter den første eller sidste ark protokol for forekomsten.

| Type  | Eksempel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Kode  | varians prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. Protokol |

## <a name="endpoint-ip"></a>Slutpunkt IP-adresse

Bliver det angivne slutpunkt IP-adresse.

| Type | Eksempel |
| ----- | ---- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Kode  | varians adresse = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Address |

## <a name="endpoint-port"></a>Slutpunkt port 

Henter den første eller sidste ark port for forekomsten.

| Type  | Eksempel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Kode  | varians port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Port; |





## <a name="example"></a>Eksempel

Her er et eksempel på en kollega rolle, der opretter en Start-opgave med en miljøvariablen med navnet `TestIsEmulated` indstillet til den [ @emulated xpath værdi](#app-running-in-emulator). 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>Næste trin

Lær mere om filen [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) .

Oprette en [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) pakke.

Aktivere [Fjernskrivebord](cloud-services-role-enable-remote-desktop.md) til en rolle.
