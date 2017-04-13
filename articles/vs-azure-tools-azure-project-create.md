<properties
   pageTitle="Oprette et Azure projekt med Visual Studio | Microsoft Azure"
   description="Oprette et Azure projekt med Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="creating-an-azure-project-with-visual-studio"></a>Oprette et Azure projekt med Visual Studio

Azure-værktøjer til Visual Studio giver en skabelon, kan du oprette en skybaseret tjeneste til Azure. Værktøjerne også hjælpe dig med at konfigurere, teste og installere skytjenesten til Azure.

En Azure skyen serviceløsning indeholder følgende typer projekter:

- **Azure project**

    Azure projektet har tilknytninger til rolle projekter i løsningen. Den indeholder også tjenestedefinitionen og tjenesten konfigurationsfiler. Tjenesten definitionsfil definerer runtime indstillingerne for dit program, herunder hvilke roller, der kræves, slutpunkter og virtuelt størrelse. Konfigurationsfil service konfigurerer, hvor mange forekomster af en rolle køres og værdierne af de indstillinger, der er defineret for en rolle. Finde flere oplysninger om disse indstillinger, [Sådan: konfigurere rollerne for en Azure skybaseret tjeneste med Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

- **Web rolle project**

    En kollega rolle udfører behandler i baggrunden. En kollega rolle kan kommunikere med lagerplads tjenester og med andre internetbaserede tjenester. En kollega rolle kan have en hvilken som helst antal HTTP, HTTPS eller TCP slutpunkter.

    - **ASP.NET Web rolle**, opbygning af en ASP.NET-program med en web front-end
    - **ASP.NET MVC5 Web rolle**
    - **ASP.NET MVC4 Web rolle**
    - **ASP.NET MVC3 Web rolle**
    - **WCF Service Web rolle**, opbygning af en WCF-webtjeneste
    - **Silverlight-program Web forretningsrolle** (kræver Visual Studio 2012)

- **Cache arbejder rolle**

    En rolle, der indeholder en dedikeret cache til dit program.

- **Arbejder rolle med Service Bus kø**

    En service bus kø, som indeholder meddelelse kø funktioner til at kommunikere med arbejdsprocessen. Se, [hvordan du bruge Service Bus køer](http://go.microsoft.com/fwlink/?LinkId=260560)kan finde flere oplysninger.

## <a name="to-create-an-azure-cloud-service-project-in-visual-studio"></a>Oprette et Azure skyen service projekt i Visual Studio

1. Start Microsoft Visual Studio som administrator.

1. Vælg **filer**, **Ny** **projekt**på menulinjen.

1. I ruden **Projekttyper** skal du vælge **skyen** fra Visual C# eller Visual Basic-projekt skabelon noder.

1. I ruden **skabeloner** skal du vælge **Azure skybaseret tjeneste**.

1. Angive, hvilken version af .NET Framework, du vil bruge til at udvikle dit projekt.

1. Angiv et navn og placering for projektet og et navn til løsningen. Vælg knappen **OK** .

1. Vælg de roller, du vil tilføje i dialogboksen **Nyt Azure-projekt** , og klik på højre pil for at føje dem til din løsning. Du kan tilføje lige så mange roller, som du vil have.

1. Hvis du vil omdøbe en rolle, du har føjet til projektet, pege på rollen i dialogboksen **Ny Azure-projekt** , og vælg ikonet **Omdøb** til højre for rollen. Du kan også omdøbe en rolle i din løsning, når den er blevet tilføjet.
