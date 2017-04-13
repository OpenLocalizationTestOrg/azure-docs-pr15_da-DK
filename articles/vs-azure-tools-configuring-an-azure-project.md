<properties
   pageTitle="Konfigurere et Azure skyen Service projekt med Visual Studio | Microsoft Azure"
   description="Lær, hvordan du konfigurerer et Azure skyen service projekt i Visual Studio, afhængigt af dine behov for det pågældende projekt."
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

# <a name="configure-an-azure-cloud-service-project-with-visual-studio"></a>Konfigurere et Azure skyen Service projekt med Visual Studio

Du kan konfigurere et Azure skyen service projekt, afhængigt af dine behov for det pågældende projekt. Du kan angive egenskaber for projektet for følgende kategorier:

- **Publicere en skybaseret tjeneste til Azure**

  Du kan angive en egenskab for at sikre dig, at en eksisterende skybaseret tjeneste, der er installeret på Azure ikke slettes ved et uheld.

- **Køre eller foretage fejlfinding af en skybaseret tjeneste på den lokale computer**

  Du kan vælge en tjenestekonfiguration bruge, og Angiv, om du vil starte emulatoren Azure-lager.

- **Validere en skybaseret tjeneste pakke, når den er oprettet**

  Du kan vælge at behandle eventuelle advarsler som fejl, så du kan sikre dig, at skyen servicepakke vil installere uden problemer. Dette reducerer din ventetid, hvis du installerer og derefter opdager, at der opstod en fejl.

Følgende illustration viser, hvordan du vælger en konfiguration skal bruges, når du kører eller foretage fejlfinding af din skytjeneste lokalt. Du kan angive egenskaberne for projektet, som du har brug for i dette vindue, som vist i illustrationen.

![Konfigurere et Microsoft Azure-projekt](./media/vs-azure-tools-configuring-an-azure-project/IC713462.png)

## <a name="to-configure-an-azure-cloud-service-project"></a>Konfigurere et Azure skyen service projekt

1. For at konfigurere en skybaseret tjeneste projektet fra **Solution Explorer**skal du åbne genvejsmenuen for skyen service projektet og derefter vælge **Egenskaber**.

  Der vises en side med navnet på skyen service projektet i Visual Studio editor.

1. Vælg fanen **udvikling** .

1. For at sikre, at du ikke kommer til at slette en eksisterende installation i Azure, i prompt før du sletter en eksisterende liste installation, skal du vælge **Sand**.

1. For at vælge Vælg konfigurationen af tjenesten, som du vil bruge, når du kører eller foretage fejlfinding af din skytjeneste lokalt, på listen **tjenestekonfiguration** konfigurationen af tjenesten.

  >[AZURE.NOTE] Hvis du vil oprette en tjenestekonfiguration bruge, skal du se sådan: administrere tjenesten konfigurationer og profiler. Hvis du vil redigere tjenestekonfigurationen af en for en rolle, skal du se, [hvordan du konfigurerer roller for en Azure skybaseret tjeneste med Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

1. Hvis du vil starte emulatoren Azure-lager, når du kører eller foretage fejlfinding af din skytjeneste lokalt, i **starte Azure lagerplads emulator**, Vælg **Sand**.

1. For at sikre, at du ikke kan udgive, hvis der er pakke valideringsfejl i **Opfat advarsler som fejl**, skal du vælge **Sand**.

1. For at sikre, at din web rolle bruger den samme port hver gang programmet starter lokalt i IIS Express, i **Brug web project porte**, vælge **Sand**. Hvis du vil bruge en bestemt port for et bestemt webprojekt, åbne genvejsmenuen for webprojektet, Vælg fanen **Egenskaber** , Vælg fanen **Web** og ændre portnummeret i indstillingen **Project URL-adressen** i sektionen **IIS Express** . For eksempel angive `http://localhost:14020` som project URL-adresse.

1. For at gemme de ændringer, du har foretaget i egenskaberne for skyen tjenesten project skal du vælge knappen **Gem** på værktøjslinjen.

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om, hvordan du kan konfigurere Azure skyen service projekter i Visual Studio, skal du se [konfiguration af din Azure projekt ved hjælp af flere service konfigurationer](vs-azure-tools-multiple-services-project-configurations.md).
