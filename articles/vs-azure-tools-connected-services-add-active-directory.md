<properties 
   pageTitle="Tilføje en Azure Active Directory ved hjælp af forbundne tjenester i Visual Studio | Microsoft Azure"
   description="Tilføje en Azure Active Directory ved hjælp af dialogboksen Visual Studio tilføje forbundne tjenester"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="active-directory"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Tilføje en Azure Active Directory ved hjælp af forbundne tjenester i Visual Studio 

##<a name="overview"></a>Oversigt
Ved hjælp af Azure Active Directory (Azure AD), kan du yder support enkelt enkeltlogon (SSO) til ASP.NET MVC webprogrammer eller AD godkendelse i Web API-tjenester. Brugerne kan bruge deres konti fra Azure AD at oprette forbindelse til dine webprogrammer med Azure AD-godkendelse. Fordelene ved at Azure AD-godkendelse med Web API er forbedret datasikkerhed, når udsætte en API fra et webprogram. Med Azure AD har du ikke til at administrere et separat godkendelsessystem med sin egen konto og bruger management.

## <a name="supported-project-types"></a>Understøttede projekttyper

Du kan bruge dialogboksen forbundne tjenester for at oprette forbindelse til Azure AD i følgende projekttyper.

- ASP.NET MVC projekter

- ASP.NET Web API projekter


### <a name="connect-to-azure-ad-using-the-connected-services-dialog"></a>Oprette forbindelse til Azure AD ved hjælp af dialogboksen forbundne tjenester

1. Sørg for, at du har en Azure-konto. Hvis du ikke har en Azure-konto, kan du tilmelde dig en [gratis prøveversion](http://go.microsoft.com/fwlink/?LinkId=518146).

1. Åbne genvejsmenuen for noden **referencer** i dit projekt i Visual Studio, og vælg **Tilføj forbundne tjenester**.
1. Vælg **Azure AD-godkendelse** , og vælg derefter **Konfigurer**.

    ![Vælg Tilføj Azure AD-godkendelse](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. Se **konfigurere Single Sign-on ved hjælp af Azure AD**på den første side i **konfigurere Azure AD-godkendelse**.

    Hvis dit projekt er konfigureret med en anden godkendelse konfiguration, advarer guiden dig, fortsætter deaktiverer den tidligere konfiguration.

    ![Konfigurere Azure AD i guiden](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1.  På den anden side skal du vælge et domæne fra rullelisten **domæne** . På listen over domæner indeholder alle domæner, der er tilgængelige ved de konti, der er angivet i dialogboksen Kontoindstillinger. Som alternativ kan angive du et domænenavn, hvis du ikke finde den fil, du leder efter, som mydomain.onmicrosoft.com. Du kan vælge muligheden for at oprette en ny Azure AD-app eller bruge indstillinger fra en eksisterende Azure AD-app. 

    ![Konfigurere Azure AD i guiden](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)


1. Sørg for, at **læse directory data** er markeret på den tredje side i guiden. Guiden udfylder **klient hemmeligt**. 

    ![Konfigurere Azure AD i guiden](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. Vælg knappen **Udfør** . Dialogboksen tilføjer nødvendige konfigurationskode og referencer til at aktivere dit projekt til Azure AD-godkendelse. Du kan se AD domænet på [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Gennemgå siden Introduktion, der vises i din browser for at få ideer på næste trin, og hvad er der sket siden for at se, hvordan projektet blev ændret. De indstillinger, der er nævnt i Hvad er der sket er der, hvis du vil kontrollere, at alt lykkedes, Åbn den ene af de ændrede konfigurationsfiler, og Kontrollér, at. For eksempel har primære web.config i en ASP.NET MVC project disse indstillinger, der er tilføjet:

        <appSettings> 
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:AADInstance" value="https://login.windows.net/" />
            <add key="ida:Domain" value="Your selected domain" />
            <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
            <add key="ida:PostLogoutRedirectUri" value="The default redirect URI from the project" />
        </appSettings>

## <a name="how-your-project-is-modified"></a>Hvordan dit projekt er ændret

Når du kører guiden, tilføjer Azure AD Visual Studio, og der er knyttet referencer til projektet. Af konfigurationsfiler og kodefiler i dit projekt er også ændret for at tilføje understøttelse af Azure AD. De bestemte ændringer, der gør det Visual Studio, afhænger af projekttypen. Du kan finde detaljerede oplysninger om, hvordan ASP.NET MVC projekter er ændret, [Hvad skete der med – MVC projekter](http://go.microsoft.com/fwlink/p/?LinkID=513809). Se [Hvad er der sket – Web API projekter](http://go.microsoft.com/fwlink/p/?LinkId=513810)for Web API projekter.

##<a name="next-steps"></a>Næste trin

Stille spørgsmål og få hjælp.

 - [MSDN-Forum: Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)

 - [Azure AD dokumentation](https://azure.microsoft.com/documentation/services/active-directory/)

 - [Blogindlæg: Introduktion til Azure AD](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

