<properties
   pageTitle="Konfiguration af navngivne legitimationsoplysninger | Microsoft Azure"
   description="Lær, hvordan til at angive legitimationsoplysninger, Visual Studio kan bruge til at godkende anmodninger om til Azure udgive et program til Azure fra Visual Studio eller til at overvåge en eksisterende skytjeneste.. "
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

# <a name="setting-up-named-authentication-credentials"></a>Konfiguration af navngivne legitimationsoplysninger

Udgive et program til Azure fra Visual Studio eller til at overvåge en eksisterende skybaseret tjeneste, skal du angive legitimationsoplysninger, Visual Studio kan bruge til at godkende anmodninger om til Azure. Der findes flere steder i Visual Studio, hvor du kan logge på angive disse legitimationsoplysninger. For eksempel fra Stifinder Server, kan du åbne genvejsmenuen for noden **Azure** og vælge **Opret forbindelse til Azure**. Når du logger på, om abonnementsoplysninger, der er knyttet til kontoen Azure er tilgængelig i Visual Studio, og der er ikke noget mere, skal du gøre.

Azure værktøjer understøtter også en ældre måde til at formidle legitimationsoplysninger, ved hjælp af abonnement-fil (.publishsettings-fil). Dette emne beskrives denne metode, der stadig understøttes i Azure SDK 2.2.

Følgende elementer, der kræves til godkendelse til Azure.

- Dit abonnement-ID

- Et gyldigt X.509 v3 certifikat

>[AZURE.NOTE] Længden af X.509 v3 certifikatets nøgle skal være mindst 2048 bit. Azure afviser et certifikat, der ikke opfylder dette krav eller, der ikke er gyldig.

Visual Studio bruger dit abonnement-ID sammen med certifikatets data som legitimationsoplysninger. Abonnementsfilen (.publishsettings fil), som indeholder en offentlig nøgle for certifikatet, der er henvist til de korrekte legitimationsoplysninger. Abonnementsfilen kan indeholde legitimationsoplysninger for mere end ét abonnement.

Du kan redigere abonnementsoplysninger i dialogboksen **Ny/Edit abonnement** , som beskrevet senere i dette emne.

Hvis du vil oprette et certifikat dig selv, kan du referere til vejledningen i [oprette og overføre et Management certifikat til Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) og overfør derefter manuelt certifikatet til [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885).

>[AZURE.NOTE] Disse legitimationsoplysninger, der kræver Visual Studio til at administrere din skytjenester ikke de samme legitimationsoplysninger, der kræves til at godkende en anmodning om mod Azure lagerplads tjenesterne.

## <a name="modify-or-export-authentication-credentials-in-visual-studio"></a>Ændre eller eksportere legitimationsoplysninger i Visual Studio

Du kan også angive, ændre eller eksportere dine legitimationsoplysninger i dialogboksen **Nyt abonnement** , som vises, hvis du udfører en af følgende handlinger:

- I **Server Explorer**, åbne genvejsmenuen for noden **Azure** , vælge **Administrer abonnementer**, Vælg fanen **certifikater** og vælge knappen **Ny** eller **redigere** .

- Når du publicerer en Azure skybaseret tjeneste fra guiden **Publicere Azure-program** , på listen **Vælg dit abonnement** skal du klikke på **Administrer** og derefter vælge fanen certifikater, og vælg derefter knappen **Ny** eller **redigere** .

Følgende fremgangsmåde forudsætter, at dialogboksen **Nyt abonnement** er åbent.

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Konfigurere legitimationsoplysninger i Visual Studio

1. **Vælg et eksisterende certifikat** godkendelse liste, ved at vælge et certifikat.

1. Vælg knappen **Kopiér den fulde sti** . Stien til certifikatet (.cer-fil) er kopieret til Udklipsholder.

    >[AZURE.IMPORTANT] Hvis du vil publicere dit Azure program fra Visual Studio, skal du overføre dette certifikat til [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885).

1. Sådan overfører du certifikatet til [Azure klassisk portalen](http://go.microsoft.com/fwlink/?LinkID=213885):

    1. Vælg linket Azure-portalen.

         [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885) åbnes.

    1. Log på [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885), og vælg derefter knappen **Cloud Services** .

    1. Vælg skytjenesten, der interesserer dig.

        Siden til tjenesten åbnes.

    1. Vælg knappen **Overfør** under fanen **certifikater** .

    1. Indsæt den fulde sti til den .cer-fil, du lige har oprettet, og derefter angive den adgangskode, du har angivet.
