<properties
   pageTitle="Administrere roller i Azure skyen services projekter med Visual Studio | Microsoft Azure"
   description="Få mere at vide, hvordan du tilføjer nye roller til projektet Azure skyen tjenesten eller fjerne eksisterende roller fra den ved hjælp af Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="managing-roles-in-the-azure-cloud-services-projects-with-visual-studio"></a>Administrere roller i Azure cloud services-projekter med Visual Studio

Når du har oprettet projektet Azure skybaseret tjeneste, kan du føje nye roller til den eller fjerne eksisterende roller fra den. Du kan også importere et eksisterende projekt og konvertere den til en rolle. Du kan for eksempel importere en ASP.NET web application og angive den som en web rolle.

## <a name="adding-or-removing-roles"></a>Tilføje eller fjerne roller

**Tilføje en rolle**

Åbn genvejsmenuen for noden **roller** i projektet skybaseret tjeneste i **Solution Explorer**, og vælg **Tilføj**. Du kan vælge en eksisterende web rolle eller arbejder rolle fra den aktuelle løsning eller oprette et nyt web eller arbejder rolle projekt. Eller du kan vælge et relevant projekt, som et ASP.NET web application-projekt, og knytte den til en rolle projekt.

**Fjerne en rolletilknytning**

Åbn genvejsmenuen for rollen for at fjerne, og vælg **Fjern**i noden **roller** i skyen service projektet i Solution Explorer.

## <a name="removing-and-adding-roles-in-your-cloud-service"></a>Fjerne og tilføje roller i skyen-tjeneste

Hvis du fjerner en rolle fra projektet skybaseret tjeneste, men senere beslutter at føje rollen tilbage til projektet, tilføjes kun rolle erklæringen og grundlæggende attributter, som slutpunkter og diagnosticering oplysninger. Ingen yderligere ressourcer eller referencer føjes til fil, ServiceDefinition.csdef eller ServiceConfiguration.cscfg fil. Hvis du vil tilføje disse oplysninger, har du vil tilføje den manuelt i disse filer igen.

For eksempel du kan fjerne en web service rolle og senere du beslutter dig for at tilføje denne rolle i din løsning igen. Hvis du gør dette, opstår der en fejl. For at undgå denne fejl, du skal tilføje den `<LocalResources>` elementer, der vises i følgende XML i filen ServiceDefinition.csdef igen. Brug navnet på den web service rolle, du har tilføjet i projektet igen som en del af name-attributten for de **<LocalStorage>** element. I dette eksempel er navnet på rollen web **WCFServiceWebRole1**.

    <WebRole name="WCFServiceWebRole1">
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
        </Imports>
       <LocalResources>
          <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
       </LocalResources>
    </WebRole>

## <a name="next-steps"></a>Næste trin

Få mere at vide om, hvordan du konfigurerer roller i Visual Studio ved at læse [konfigurere rollerne for en Azure skybaseret tjeneste med Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).
