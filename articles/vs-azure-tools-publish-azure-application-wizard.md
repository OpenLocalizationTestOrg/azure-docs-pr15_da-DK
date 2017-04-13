<properties 
   pageTitle="Publiceringsguide for Azure-program | Microsoft Azure"
   description="Publiceringsguide for Azure-program"
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

# <a name="publish-azure-application-wizard"></a>Publiceringsguide for Azure-program

## <a name="overview"></a>Oversigt

Når du udvikler et webprogram i Visual Studio, kan du publicere programmet igen for nemmere at Azure-skytjeneste ved hjælp af guiden **Publicere Azure-program** . Det første afsnit forklares trinnene, du skal fuldføre, før du bruger guiden, og de resterende afsnit beskriver funktionerne i guiden.

>[AZURE.NOTE] Dette emne handler om installation af til skytjenester, ikke til websteder. Oplysninger om installation af til websteder, se, [hvordan du installerer et Azure-websted](https://social.msdn.microsoft.com/Search/windowsazure?query=How%20to%20Deploy%20an%20Azure%20Web%20Site&Refinement=138&ac=4#refinementChanges=117&pageNumber=1&showMore=false).

## <a name="prerequisites"></a>Forudsætninger

Før du kan udgive dit webprogram til Azure, du vil have en Microsoft-konto og et Azure-abonnement, og du behøver at tilknytte dit webprogram med en Azure skybaseret tjeneste. Hvis du allerede har fuldført disse opgaver, kan du gå videre til næste afsnit.

1. Få en Microsoft-konto og et Azure-abonnement. Du kan prøve et gratis en måned gratis abonnement til Azure [her](https://azure.microsoft.com/pricing/free-trial/)

1. Oprette en skybaseret tjeneste og en lagerplads konto på Azure. Du kan gøre dette fra Server Explorer i Visual Studio eller ved at bruge [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885).

1. Aktivere webprogrammet for Azure. Hvis du vil aktivere web-programmet skal publiceres til Azure fra Visual Studio, skal du knytte den til et Azure skyen service projekt i Visual Studio. Åbne genvejsmenuen for projektet til dit webprogram for at oprette tilknyttede skyen service projektet, og vælg derefter Konverter, **Konverter til Azure skyen Service projekt**.

1. Når skyen service projektet er føjet til din løsning, åbne den samme genvejsmenu igen, og vælg derefter **Publicer**. Finde flere oplysninger om, hvordan du kan programmer til Azure [Sådan: overføre og publicere et webprogram til en skybaseret tjeneste til Azure fra Visual Studio](https://msdn.microsoft.com/library/azure/hh420322.aspx).

>[AZURE.NOTE] Sørg for at starte Visual Studio med administratorrettigheder (Kør som Administrator).

1. Når du er klar til at udgive dit program, Åbn genvejsmenuen for projektets Azure skybaseret tjeneste, og vælg derefter **Publicer**. I nedenstående fremgangsmåde vises guiden publicere Azure-program.

## <a name="choosing-your-subscription"></a>Vælge dit abonnement

### <a name="to-choose-a-subscription"></a>Vælge et abonnement

1. Før du bruger guiden for første gang, skal du logge på. Vælg linket **Log på** . Log på portalen Azure, når du bliver spurgt, og Giv dit Azure brugernavn og din adgangskode. 

    ![Dette er en af de publicering skærmbilleder](./media/vs-azure-tools-publish-azure-application-wizard/IC799159.png)

    På listen over abonnementer udfyldes med de abonnementer, der er knyttet til din konto. Du kan også se abonnementer fra en hvilken som helst abonnementsfiler, som du har importeret tidligere.

1. Vælg abonnementet, der skal bruges til denne installation på listen **Vælg dit abonnement** .

   Hvis du vælger **<... Administrer >**, vises i dialogboksen **Administrer abonnementer** , og du kan vælge den abonnement og bruger konto, du vil bruge. Under fanen **konti** viser alle dine konti, og fanen **abonnementer** viser alle de abonnementer, der er knyttet til kontiene. Du kan også vælge et område, du vil bruge Azure ressourcer, samt oprette eller importere certifikater til dit abonnement fra portalen Azure fra. Hvis du har importeret nogen abonnementer fra et abonnement-fil, vises de tilknyttede certifikater under fanen **certifikater** . Når du er færdig, skal du vælge knappen **Luk** .

    ![Administrere abonnementer](./media/vs-azure-tools-publish-azure-application-wizard/IC799160.png)

    >[AZURE.NOTE] Abonnementsfilen kan indeholde mere end ét abonnement.

1. Klik på **Næste** for at fortsætte. 

    Hvis der ikke er en hvilken som helst skytjenester i dit abonnement, skal du oprette en skybaseret tjeneste i Azure vært for dit projekt. Dialogboksen **Opret skybaseret tjeneste og lagerplads konto** vises.

    Angiv et nyt navn til skytjenesten. Navnet skal være entydigt i Azure. Derefter skal du angive et område eller en gruppe af forbindelse til et datacenter, der er i nærheden af du eller mest muligt ud af dine kunder. Dette navn bruges også til en ny lagerplads-konto, som opretter Azure for skybaseret tjeneste.

1. Ændre nogen indstillinger, du vil bruge til denne installation og derefter publicere den ved at vælge knappen **Udgiv** (næste afsnit indeholder flere oplysninger om de forskellige indstillinger). Vælg knappen **Næste** for at få vist indstillingerne for før publicering.

    >[AZURE.NOTE] Hvis du vælger Udgiv i dette trin, kan du overvåge statussen for denne installation i Visual Studio.

Du kan ændre både almindelige og avancerede indstillinger for en installation ved hjælp af guiden **Publicere Azure-program** . For eksempel kan du vælge en indstilling til at udrulle dit program til et testmiljø, før du slipper den. Følgende illustration viser fanen **Almindelige indstillinger** for en Azure-installation.

![Almindelige indstillinger](./media/vs-azure-tools-publish-azure-application-wizard/IC749013.png)

## <a name="configuring-your-publish-settings"></a>Konfiguration af dine indstillinger for publicering

### <a name="to-configure-the-publish-settings"></a>Konfigurere indstillingerne for Udgiv

1. På listen **skybaseret tjeneste** skal du udføre én af følgende sæt trin:

   1. I rullemenuen, vælge en eksisterende skybaseret tjeneste. Data center placeringen for tjenesten vises. Du skal noterer denne placering, og Sørg for, at din konto lagerplacering er i den samme datacenter.

    1. Vælg **Opret ny** til at oprette en skybaseret tjeneste, der er Azure vært. Angiv et navn til tjenesten i dialogboksen **Opret skybaseret tjeneste** , og derefter angive et område eller en gruppe af forbindelse til at angive placeringen af det datacenter, som du ønsker at hoste denne skybaseret tjeneste. Navnet skal være entydigt i Azure.

1. Vælg enten **fremstilling** eller **midlertidige**på listen **miljø** . Vælg det midlertidige miljø, hvis du vil installere programmet til et testmiljø. Du kan flytte dit program til produktionsmiljøet senere.

1. Vælg enten **fejlfinding** eller **Release**på listen **opbygge konfiguration** .

1. Vælg enten **skyen** eller **lokale**på listen **tjenestekonfiguration** .

    Markér afkrydsningsfeltet **Aktivér Fjernskrivebord for alle roller** , hvis du vil kunne oprette forbindelse fra en fjernplacering til tjenesten. Denne indstilling er primært bruges til fejlfinding. Når du markerer dette afkrydsningsfelt, vises dialogboksen **Remote Desktop konfiguration** . Vælg linket indstillinger for at ændre konfigurationen.

    Markér afkrydsningsfeltet **Aktivér Web installere for alle web roller** for at aktivere webinstallation til tjenesten. Du skal aktivere Remote Desktop til at bruge denne funktion. Se [[publicere en skybaseret tjeneste, ved hjælp af værktøjerne Azure](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx)kan finde flere oplysninger. Du kan finde flere oplysninger om Web installere se [[publicere en skybaseret tjeneste, ved hjælp af værktøjerne Azure](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx).

1. Vælg fanen **Avancerede indstillinger** . Acceptere standardnavnet i feltet **installation etiket** , eller skriv et navn efter eget valg. Hvis du vil føje datoen til etiketten installation, du ikke markerer afkrydsningsfeltet markeret.

    ![Tredje skærmbillede i guiden Udgivelse](./media/vs-azure-tools-publish-azure-application-wizard/IC749014.png)

1. På listen **lagerplads konto** skal du vælge lagerplads konto skal bruges til denne installation. Sammenligne placeringer af datacentre til din skytjeneste og kontoen lagerplads. Ideelt være disse placeringer den samme.

    >[AZURE.NOTE] Kontoen Azure lagerplads gemmer pakken til programmet installation. Når programmet er installeret, fjernes pakken fra kontoen lagerplads.

1. Markér afkrydsningsfeltet **Opdater installation** , hvis du vil installere kun opdaterede komponenter. Denne type installation kan være hurtigere end en komplet installation. Vælg linket **Indstillinger** for at åbne dialogboksen **Installation opdatere indstillinger for** , vises i følgende illustration. 

    ![Indstillinger for indholdsinstallation](./media/vs-azure-tools-publish-azure-application-wizard/IC617060.png)

    Du kan vælge en af to muligheder for installation af opdateringer, stigende eller samtidig. En trinvis installation opdaterer en udløst forekomst ad gangen, så dit program er online og tilgængelige for brugerne. En samtidig installation opdaterer alle installerede forekomster på én gang. Samtidig opdatering er hurtigere end trinvis opdatering, men hvis du vælger denne indstilling, dit program muligvis ikke tilgængelig under opdateringsprocessen.

    Du skal markere afkrydsningsfeltet for hvis installation ikke kan opdateres, gøre en komplet installation, hvis du vil have den fulde installation skal finde sted automatisk, hvis en opdatering installation mislykkes. En komplet installation nulstiller den virtuelle IP-adresse (VIP) adresse for skytjenesten. Du kan finde flere oplysninger, se [Sådan: bevare en konstant virtuelle IP-adresse til en skybaseret tjeneste](https://msdn.microsoft.com/library/azure/jj614593.aspx).


1. Markér afkrydsningsfeltet **Aktivér IntelliTrace** for at fejlfinde din tjeneste, eller hvis du installerer en **fejlfinding af** konfiguration og vil foretage fejlfinding af din skybaseret tjeneste i Azure, markere afkrydsningsfeltet **Aktivér Remote fejlfinding for alle roller** til at udrulle remote fejlfinding tjenesterne.

2. Hvis du vil profil programmet, Markér afkrydsningsfeltet **aktivere oprettelse af profiler** og derefter vælge linket **Indstillinger** for at få vist indstillingerne for status. 


    >[AZURE.NOTE] Du skal bruge Visual Studio Ultimate til at aktivere IntelliTrace eller niveau interaktion profiler (TIP), og du kan ikke aktivere både på samme tid.

    Du kan finde flere oplysninger, se [fejlfinding en publiceret skybaseret tjeneste med IntelliTrace og Visual Studio](https://msdn.microsoft.com/library/azure/ff683671.aspx) og [test af ydeevnen for en skybaseret tjeneste](https://msdn.microsoft.com/library/azure/hh369930.aspx).

1. Klik på **Næste** til at få vist siden Oversigt for programmet.

## <a name="publishing-your-application"></a>Publicere dit program

1. Du kan vælge at oprette en publicering profil på de indstillinger, du har valgt. For eksempel kan du oprette en profil for at få et testmiljø og et andet til fremstilling. For at gemme denne profil skal du vælge ikonet **Gem** . Guiden opretter profilen og gemmer den i Visual Studio-projekt. Hvis du vil ændre profilnavnet, Åbn listen **Target profil** , og vælg derefter **<... Administrer >**.

    ![Oversigt over skærmbillede i guiden Udgivelse](./media/vs-azure-tools-publish-azure-application-wizard/IC749015.png)

    >[AZURE.NOTE] Publicering profilen vises i Solution Explorer i Visual Studio, og profilindstillingerne skrives til en fil med filtypenavnet .azurePubxml. Indstillinger for gemmes som attributter i XML-filer.

1. Vælg **Publicer** for at publicere dit program. Du kan overvåge processtatus i vinduet **Output** i Visual Studio.

## <a name="see-also"></a>Se også

[Sådan: overføre og publicere et webprogram til en Azure skybaseret tjeneste fra Visual Studio](https://msdn.microsoft.com/library/azure/hh420322.aspx)

[Publicere en skybaseret tjeneste, ved hjælp af værktøjerne Azure](https://msdn.microsoft.com/library/azure/ff683672.aspx)

[Fejlfinding af en publiceret skybaseret tjeneste med IntelliTrace og Visual Studio](https://msdn.microsoft.com/library/azure/ff683671.aspx)

[Test af ydeevnen for en skybaseret tjeneste](https://msdn.microsoft.com/library/azure/hh369930.aspx)

