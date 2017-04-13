<properties
   pageTitle="Sådan administreres service konfigurationer og profiler | Microsoft Azure"
   description="Få mere at vide om at arbejde med tjenesten konfigurationer og profiler konfigurationsfiler | som gemme indstillingerne for installation miljøer og publicere indstillinger for skytjenester."
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

# <a name="how-to-manage-service-configurations-and-profiles"></a>Sådan administreres service konfigurationer og profiler

## <a name="overview"></a>Oversigt

Når du publicerer en skybaseret tjeneste, Visual Studio gemmer konfigurationsoplysninger i to typer af konfigurationsfiler: konfigurationer og profiler. Tjenesten konfigurationer (.cscfg filer) indeholder indstillinger for installation miljøer til en Azure skybaseret tjeneste. Azure bruger konfigurationsfilerne, når den administrerer din skytjenester. På den anden side profiler (.azurePubxml filer) store publicere indstillinger for skytjenester. Disse indstillinger er en oversigt over, hvad du vælger, når du bruger guiden Udgiv og anvendes lokalt af Visual Studio. Dette emne forklares, hvordan du arbejder med begge typer konfigurationsfiler.

## <a name="service-configurations"></a>Tjenesten konfigurationer

Du kan oprette flere service konfigurationer skal bruges for hver af din installation miljøer. For eksempel kan du oprette en tjenestekonfiguration for det lokale miljø, som du kan bruge til at køre og teste et Azure-program og en anden tjenestekonfiguration for dit produktionsmiljø.

Du kan tilføje, slette, omdøbe og ændre disse service konfigurationer efter dine behov. Du kan administrere disse service konfigurationer fra Visual Studio, som vist i nedenstående illustration.

![Administrere tjenesten konfigurationer](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

Du kan også åbne dialogboksen **Administrer konfigurationer** fra den rolle egenskabssider. Åbne genvejsmenuen for rollen, for at åbne egenskaberne for en rolle i projektet Azure, og vælg derefter **Egenskaber**. Udvid listen **Tjenestekonfiguration** under fanen **Indstillinger** , og vælg derefter **Administrer** for at åbne dialogboksen **Administrer konfigurationer** .

### <a name="to-add-a-service-configuration"></a>Tilføje en tjenestekonfiguration

1. Åbne genvejsmenuen for Azure projektet i Solution Explorer og derefter vælge **Administrer konfigurationer**.

    Dialogboksen **Administrer Service konfigurationer** vises.

1. Hvis du vil tilføje en tjenestekonfiguration, skal du oprette en kopi af en eksisterende konfiguration. Vælg den konfiguration, som du vil kopiere på listen navn på og derefter vælge **Opret kopi**for at gøre dette.

1. (Valgfrit) Vælg den nye tjenestekonfiguration på listen navn for at give konfigurationen af et andet navn, og vælg derefter **Omdøb**. I tekstfeltet **navn** skal du skrive det navn, du vil bruge til denne konfiguration af tjenesten, og klik derefter på **OK**.

    En ny tjeneste konfigurationsfil, der hedder ServiceConfiguration. [Navn på nyt] .cscfg er føjet til Azure projektet i Solution Explorer.


### <a name="to-delete-a-service-configuration"></a>Slette en tjenestekonfiguration

1. I Solution Explorer skal du åbne genvejsmenuen for Azure projektet og derefter vælge **Administrere konfigurationer**.

    Dialogboksen **Administrer Service konfigurationer** vises.

1. For at slette en tjenestekonfiguration, skal du vælge den konfiguration, som du vil slette fra listen **navn** , og vælg derefter **Fjern**. Der vises en dialogboks til at kontrollere, at du vil slette denne konfiguration.

1. Vælg **Slet**.

     Konfigurationsfil tjenesten fjernes fra Azure projektet i Solution Explorer.


### <a name="to-rename-a-service-configuration"></a>Omdøbe en tjenestekonfiguration

1. Åbn genvejsmenuen for Azure projektet i Solution Explorer, og vælg derefter **Administrer konfigurationer**.

    Dialogboksen **Administrer Service konfigurationer** vises.

1. Hvis du vil omdøbe en tjenestekonfiguration, Vælg den nye tjenestekonfiguration på listen **navn** , og vælg derefter **Omdøb**. Skriv det navn, du vil bruge til denne tjenestekonfiguration i tekstboksen **navn** , og klik derefter på **OK**.

    Navnet på tjenesten konfigurationsfil er ændret i Azure projektet i Solution Explorer.

### <a name="to-change-a-service-configuration"></a>Ændre en tjenestekonfiguration

- Hvis du vil ændre en tjenestekonfiguration, åbne genvejsmenuen for den rolle, du vil ændre i Azure projektet, og klik derefter på **Egenskaber**. Se [Sådan: konfigurere rollerne for en Azure skybaseret tjeneste med Visual Studio](https://msdn.microsoft.com/library/azure/hh369931.aspx) kan finde flere oplysninger.

## <a name="make-different-setting-combinations-by-using-profiles"></a>Gør indstillingen speciel kombinationer ved at bruge profiler

Ved hjælp af en profil, kan du automatisk udfylde **Guiden Udgivelse** med forskellige kombinationer af indstillinger for forskellige formål. For eksempel kan du have en profil for fejlfinding og et andet til release opbygger. I så fald profilen **fejlfinding** ville have **IntelliTrace** aktiveret og **foretage fejlfinding af** konfigurationen markeret, og din **Release** profil ville have **IntelliTrace** deaktiveret og **Release** konfigurationen markeret. Du kan også bruge forskellige profiler til at implementere en tjeneste, der bruger en anden lagerplads-konto.

Når du kører guiden for første gang, oprettes en standardprofil. Visual Studio gemmer profilen i en fil, der har filtypenavnet .azurePubXml, som er føjet til projektet Azure under mappen **profiler** . Hvis du manuelt angive forskellige indstillinger, når du kører guiden senere, opdaterer filen automatisk. Før du kører den følgende procedure, bør du allerede har udgivet skybaseret tjeneste mindst én gang.

### <a name="to-add-a-profile"></a>Sådan tilføjes en profil

1. Åbn genvejsmenuen for projektet Azure, og vælg derefter **Publicer**.

1. Vælg knappen **Gem profil** , som i følgende illustration vises ud for listen **målprofil** . Dette opretter en profil for dig.

    ![Oprette en ny profil](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)

1. Efter profilen, der er oprettet, skal du vælge **<... Administrer >** på listen **Target profil** .

    Dialogboksen **Administrer profiler** vises som i følgende illustration vises.

    ![Administrer profiler dialogboksen](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)

1. Vælge en profil på listen **navn** , og vælg derefter **Opret kopi**.

1. Vælg knappen **Luk** .

    Den nye profil vises på mållisten profil.

1. Markér den profil, som du lige har oprettet, på listen **Target profil** . Indstillingerne for guiden Udgivelse er udfyldt med valgmuligheder fra den profil, du har valgt.

1. Vælg knapperne **forrige** og **Næste** for at få vist hver enkelt side i guiden publicere, og tilpas derefter indstillingerne for denne profil. Du kan finde oplysninger i [Publicere Azure-guiden](http://go.microsoft.com/fwlink/p/?LinkID=623085) .

1. Når du er færdig med at tilpasse indstillingerne, klik på **Næste** at vende tilbage til siden Indstillinger. Profilen, der er gemt, når du publicerer tjenesten ved hjælp af disse indstillinger, eller hvis du vælger **Gem** ud for listen over profiler.

### <a name="to-rename-or-delete-a-profile"></a>Omdøb eller Slet en profil

1. Åbn genvejsmenuen for projektet Azure, og vælg derefter **Publicer**.

1. Klik på **Administrer**på listen **Target profil** .

1. Vælg den profil, du vil slette, og vælg derefter **Fjern**i dialogboksen **Administrer profiler** .

1. Vælg **OK**i bekræftelsesdialogboksen.

1. Vælg **Luk**.

### <a name="to-change-a-profile"></a>Ændre en profil

1. Åbn genvejsmenuen for projektet Azure, og vælg derefter **Publicer**.

1. Markér den profil, du vil ændre, på listen **Target profil** .

1. Vælg knapperne **forrige** og **Næste** for at få vist hver enkelt side i guiden publicere, og rediger derefter de ønskede indstillinger. Du kan finde oplysninger i [Publicere Azure-guiden](http://go.microsoft.com/fwlink/p/?LinkID=623085) .

1. Når du er færdig med at ændre indstillingerne, klik på **Næste** at vende tilbage til siden **Indstillinger** .

1. (Valgfrit) Vælg **Publicer** publicere skytjenesten ved hjælp af de nye indstillinger. Hvis du ikke vil publicere din skytjeneste på nuværende tidspunkt, og du lukke guiden publicere, spørger Visual Studio, hvis du vil gemme ændringerne til profilen.

## <a name="next-steps"></a>Næste trin

For at få mere for at vide om konfiguration af andre dele af projektet Azure fra Visual Studio, se [konfigurere et Azure-projekt](http://go.microsoft.com/fwlink/p/?LinkID=623075)
