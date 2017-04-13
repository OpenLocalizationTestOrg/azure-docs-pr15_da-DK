<properties 
   pageTitle="Ved hjælp af Fjernskrivebord med Azure roller | Microsoft Azure"
   description="Ved hjælp af Fjernskrivebord med Azure roller"
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

# <a name="using-remote-desktop-with-azure-roles"></a>Ved hjælp af Fjernskrivebord med Azure roller

Ved hjælp af Azure SDK og Fjernskrivebord-tjenester, du kan få adgang til Azure roller og virtuelle maskiner, der er hostet af Azure. Du kan konfigurere Remote Desktop Services fra et Azure projekt i Visual Studio. Hvis du vil aktivere Remote Desktop Services, skal du oprette et projekt i arbejde, der indeholder en eller flere roller og derefter publicere den til Azure.

>[AZURE.IMPORTANT] Du skal åbne en Azure rolle til fejlfinding eller udvikling kun. Formålet med hver virtuelt er til at køre en bestemt rolle i dit Azure-program, ikke til at køre andre klientprogrammer. Hvis du vil bruge Azure vært for en virtuel maskine, som du kan bruge til formål, kan du se få adgang til Azure virtuelle maskiner fra Server Explorer.

## <a name="to-enable-and-use-remote-desktop-for-an-azure-role"></a>Du aktiverer og bruger Remote Desktop til en rolle til Azure

1. I Solution Explorer skal du åbne genvejsmenuen for dit projekt og derefter vælge **Publicer**.

    Guiden **Publicere Azure program** vises.

    ![Publicere kommando til en skybaseret tjeneste project](./media/vs-azure-tools-remote-desktop-roles/IC799161.png)

1. Vælg **Aktivere Remote Desktop** til alle roller i bunden af **Microsoft Azure publiceringsindstillinger** side i guiden. 

    Dialogboksen **Remote Desktop konfiguration** vises.

1. Vælg knappen **Flere indstillinger** nederst i dialogboksen **Remote Desktop konfiguration** . 
 
    Dette viser en rullemenuen, hvor du kan oprette eller vælge et certifikat, så du kan kryptere legitimationsoplysninger, når der oprettes forbindelse via Fjernskrivebord.

1. På rullelisten Vælg ** &lt;Opret >**, eller Vælg et eksisterende dokument på listen. 

    Hvis du vælger et eksisterende certifikat, kan du springe de følgende trin over.

    >[AZURE.NOTE] Certifikater, som du har brug for en forbindelse til Fjernskrivebord er forskellige fra de certifikater, som du bruger til andre Azure handlinger. Fjernadgang certifikat skal have en privat nøgle.

    Dialogboksen **Opret et certifikat** vises.

    1. Angiv et brugervenligt navn til det nye certifikat, og vælg knappen **OK** . Det nye certifikat vises i rullemenuen.

    1. Angive et brugernavn og en adgangskode i dialogboksen **Remote Desktop konfiguration** .
    
        Du kan ikke bruge en eksisterende konto. Administratoren ikke angive som brugernavnet for den nye konto.

        >[AZURE.NOTE] Hvis adgangskoden ikke opfylder kompleksitetskravene, vises et rødt ikon ud for tekstfeltet adgangskode. Adgangskoden skal indeholde store bogstaver, små bogstaver og tal eller symboler.

    1. Vælg en dato på kontoen udløber og efter hvilke forbindelser til Fjernskrivebord, blokeres.

    1. Vælg knappen **OK** , når du har angivet alle de nødvendige oplysninger.
    
        Flere indstillinger, der aktiverer Remote Access Services er føjet til .cscfg og .csdef filerne.

1. I guiden **Microsoft Azure publiceringsindstillinger** skal du vælge knappen **OK** , når du er klar til at publicere din skybaseret tjeneste.

    Hvis du ikke er klar til at publicere, kan du vælge knappen **Annuller** . Indstillinger for søgekonfiguration gemmes, og du kan udgive din skytjeneste senere.

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Oprette forbindelse til en Azure rolle ved hjælp af Remote Desktop

Når du publicerer dit skybaseret tjeneste på Azure, kan du bruge Server Explorer til at logge ind på virtuelle maskiner, der er Azure vært. 

1. Udvid noden **Azure** i Server Explorer, og udvid derefter noden til en skybaseret tjeneste, og én af dens roller for at få vist en liste over de forekomster af.

1. Åbne genvejsmenuen for en forekomst node, og vælg derefter **Opret forbindelse ved hjælp af Remote Desktop**.

    ![Oprette forbindelse via Fjernskrivebord](./media/vs-azure-tools-remote-desktop-roles/IC799162.png)

1. Angiv brugernavn og adgangskode, du tidligere har oprettet. Du er nu logget ind i din fjernsession.


