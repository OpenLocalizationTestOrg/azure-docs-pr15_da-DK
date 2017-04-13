<properties
   pageTitle="Standard TEMP mappestørrelse er for lille til en rolle | Microsoft Azure"
   description="En skybaseret tjeneste rolle har en begrænset mængde plads til mappen TEMP. Denne artikel indeholder nogle forslag til, hvordan du undgå at løbe tør for plads."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/12/2016"
   ms.author="v-six" />

# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Standard TEMP mappestørrelse er for lille til en skybaseret tjeneste web/arbejder rolle

Midlertidige standardmappen for en skybaseret tjeneste arbejder eller web rolle har en maksimumstørrelse på 100 MB, som kan blive fuld på et tidspunkt. I denne artikel beskrives, hvordan du undgår at løbe tør for plads til den midlertidige mappe.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Hvorfor kan jeg køre tør for plads?

Standard Windows-miljøvariabler TEMP og TMP er tilgængelige for kode, der kører i programmet. Både TEMP og TMP pege på en enkelt mappe, der har en maksimumstørrelse på 100 MB. De data, der er gemt i denne mappe bevares ikke på tværs af skytjenesten; livscyklus Hvis rolle forekomster i en skybaseret tjeneste er flyttet til papirkurven, ryddes kataloget.

## <a name="suggestion-to-fix-the-problem"></a>Forslag til at løse problemet

Implementere en af følgende muligheder:

- Konfigurere et lokalt lager ressource, og få adgang til den direkte i stedet for ved hjælp af TEMP- eller TMP. Kalde metoden [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) for at få adgang til et lokalt lager ressource fra kode, der kører i dit program. 

- Konfigurere et lokalt lager ressource, og peg TEMP og TMP mapper til at pege på stien til den lokale lager ressource. Denne ændring skal udføres i metoden [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) .

Følgende kodeeksempel viser hvordan du ændrer mapperne mål for TEMP og TMP fra i metoden OnStart:


```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Næste trin

Læse en blog, der beskriver, [hvordan du kan øge størrelsen på Azure Web rolle ASP.NET midlertidige mappe](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Få vist flere [foretage fejlfinding af artikler](/?tag=top-support-issue&product=cloud-services) til skytjenester.

For at lære at foretage fejlfinding af problemer med skyen tjenesten rolle ved hjælp af Azure PaaS computer diagnosticering data, skal du se [Kevin Williamson blogserier](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
