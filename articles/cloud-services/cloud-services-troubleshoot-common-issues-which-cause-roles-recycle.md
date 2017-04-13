<properties
   pageTitle="Almindelige årsager til skybaseret tjeneste roller genbrug | Microsoft Azure"
   description="En skybaseret tjeneste rolle, der pludselig genbruger kan medføre længere nedetid. Her er nogle almindelige problemer, der kan medføre roller genbruges, der kan hjælpe dig med at reducere nedetid."
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
   ms.date="09/02/2016"
   ms.author="v-six" />

# <a name="common-issues-that-cause-roles-to-recycle"></a>Almindelige problemer, der kan medføre roller til Papirkurv

I denne artikel beskrives nogle af de almindelige årsager til problemer med installation og giver tip til fejlfinding kan hjælpe dig med at løse problemerne. Angivelse, er et problem med et program er, når den rolle forekomst ikke kan startes, eller den F4 mellem initialiseringen af, optaget og standse tilstand.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Manglende runtime afhængigheder

Hvis en rolle i dit program er baseret på en samling, der ikke er en del af .NET Framework eller Azure administrerede bibliotek, skal du eksplicit medtage denne samling i pakken programmet på computeren. Husk på, at andre Microsoft-strukturer ikke er tilgængelige på Azure som standard. Hvis din rolle er afhængig af disse en ramme, skal du føje de pågældende assemblies til pakken programmet på computeren.

Før du opretter og pakke dit program, skal du kontrollere følgende:

- Hvis ved hjælp af Visual studio, skal du kontrollere egenskaben **Lokale kopi** er indstillet til **Sand** for hver der refereres til samling i dit projekt, der ikke er en del af Azure SDK eller .NET Framework.

- Kontrollér, at filen web.config ikke refererer til en hvilken som helst ubrugte assemblies i elementet kompilering.

- **Opbygge handling** for alle filer, der .cshtml er angivet til **indhold**. Dette sikrer, at filerne vises korrekt i pakken, og gør det muligt for andre der refereres til filer, der skal vises i pakken.

## <a name="assembly-targets-wrong-platform"></a>Samling destinationer forkerte platform

Azure er et 64-bit-miljø. Derfor fungerer .NET assemblies kompileret for en 32-bit destination ikke på Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Rolle viser ikke-afviklet undtagelser under initialisering af eller stoppe

Eventuelle undtagelser, der er opstået ved hjælp af metoder til klassen [RoleEntryPoint] , som indeholder den [OnStart], [OnStop]og [køre] metoder, er ikke-afviklet undtagelser. Hvis der opstår en ikke-afviklet undtagelse i en af disse metoder, vil rollen Papirkurv. Hvis rolle genbrug flere gange, kan det udløses en ikke-afviklet undtagelse hver gang den forsøger at starte.

## <a name="role-returns-from-run-method"></a>Rolle returnerer fra Run-metoden

Metoden [køre] er beregnet til at køre på ubestemt tid. Hvis din kode tilsidesætter metoden [køre] , skal den dvale på ubestemt tid. Hvis metoden [køre] returnerer, genbruger rollen.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Forkert DiagnosticsConnectionString indstilling

Hvis programmet bruger Azure diagnosticering, din tjeneste konfigurationsfil skal angive den `DiagnosticsConnectionString` denne indstilling. Denne indstilling skal angive en HTTPS-forbindelse til kontoen lagerplads i Azure.

At sikre, at din `DiagnosticsConnectionString` indstilling er korrekte, inden du installerer programmet pakken til Azure, skal du kontrollere følgende:  

- Den `DiagnosticsConnectionString` angive punkter til en gyldig lagerplads konto i Azure.  
  Som standard punkter denne indstilling til kontoen emuleret lagerplads, så du skal eksplicit ændrer denne indstilling, før du installerer programmet pakken. Hvis du ikke ændrer denne indstilling, udløst en undtagelse, når den rolle forekomst forsøger at starte den diagnosticering skærm. Dette kan medføre forekomsten rolle til Papirkurv på ubestemt tid.

- Forbindelsesstrengen er angivet i følgende [format](../storage/storage-configure-connection-string.md). (Protokollen skal være angivet som HTTPS). Erstat *MyAccountName* med navnet på din lagerplads konto og *MyAccountKey* med din access-nøgle:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Hvis du udvikler dit program ved hjælp af Azure værktøjer til Microsoft Visual Studio, kan du bruge [egenskabssider](https://msdn.microsoft.com/library/ee405486) til at angive denne værdi.

## <a name="exported-certificate-does-not-include-private-key"></a>Eksporterede certifikat omfatter ikke privat nøgle

Hvis du vil køre en web rolle under SSL, skal du sikre dig, at dit eksporterede management certifikat omfatter privat nøgle. Hvis du bruger *Windows Certifikatstyring* til at eksportere certifikatet, skal du vælge **Ja** for indstillingen **Eksporter privat nøgle** . Certifikatet, der skal eksporteres i formatet PFX, som er det eneste format, der understøttes i øjeblikket.

## <a name="next-steps"></a>Næste trin

Få vist flere [foretage fejlfinding af artikler](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) til skytjenester.

Få vist flere rolle genbrug scenarier på [Kevin Williamson blogserier](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Kør]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
