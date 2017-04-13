<properties
    pageTitle="Azure-lager konto liste"
    description="Administrere dine lagerplads kontoindstillinger med Azure-værktøjskassen til Eklipse"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn205108.aspx -->

# <a name="azure-storage-account-list"></a>Azure-lager konto liste #

Azure-lager konti aktivere downloadplaceringer, der skal bruges til JDK, programserver og vilkårlig komponenter samt til lagring af tilstand, når du bruger cachelagring. Eklipse fører en liste over kendte lagerplads konti, der er tilgængelige for dine projekter i arbejdsområdet Eklipse. For at åbne dialogboksen **Lagerplads konti** , som bruges til at administrere denne liste i Eklipse, skal du klikke på **vinduet**, klik på **Indstillinger**, udvide **Azure**og derefter klikke på **Lagerplads konti**.

Følgende viser dialogboksen **Lagerplads konti** .

![][ic719496]

Også du kan åbne denne dialogboks fra et link til **konti** på dialogbokse, der bruger lagerplads konti, som følgende:

* Fanen **JDK** af dialogboksen **Serverkonfiguration** .
* Fanen **Server** i dialogboksen **Serverkonfiguration** .
* Dialogboksen **Tilføj komponent** .
* Egenskabsdialogboksen for **cachelagring** .

## <a name="to-import-your-storage-accounts-using-a-publish-settings-file"></a>Importere kontiene lagerplads ved hjælp af en fil med Publicer indstillinger ##

1. Klik på **Importer fra fil med PUBLICER-indstillinger**i dialogboksen **Lagerplads konti** .
2. (Springe dette trin, hvis du allerede har gemt en fil med Publicer indstillinger til din lokale computer.) Klik på **Hent PUBLICER – indstillinger fil**i dialogboksen **Importér abonnementsoplysninger** . Hvis du endnu ikke er logget på din Azure-konto, bliver du bedt om at logge på. Og du bliver bedt om at publicere indstillingsfil Gem en Azure. (Du kan ignorere resulterende instruktionerne, der vises på siderne logon - de der leveres af portalen Azure og er beregnet til Visual Studio brugere.) Gemme den på din lokale computer.
3. Stadig i dialogboksen **Importér abonnementsoplysninger** , skal du klikke på knappen **Gennemse** , Vælg Publicer indstillinger-fil, du har gemt lokalt tidligere og klik derefter på **Åbn**.
4. Klik på **OK** for at lukke dialogboksen **Importér abonnementsoplysninger** .

## <a name="to-create-a-new-storage-account"></a>Oprette en ny konto lagerplads ##

1. Klik på **Tilføj**i dialogboksen **Lagerplads konti** .
2. Klik på **Ny**i dialogboksen **Tilføj lagerplads konto** .
3. Angiv værdier for følgende i dialogboksen **Ny lagerplads konto** :
    * Kontonavn-lager.
    * Placeringen af kontoen lagerplads.
    * Beskrivelse af kontoen lagerplads.
    * Det abonnement, som kontoen lagerplads tilhører.
4. Klik på **OK** for at lukke dialogboksen **Ny lagerplads konto** .

Det kan tage flere minutter, før kontoen lagerplads skal oprettes. Klik på **OK** for at lukke dialogboksen **Tilføj lagerplads konto** , når den er oprettet, og din nye lagerplads-konto, føjes til listen over tilgængelige lagerplads konti.

## <a name="to-add-an-existing-storage-account-to-the-list"></a>Tilføje en eksisterende konto lagerplads til listen ##

1. Hvis du ikke allerede har en Azure-lager-konto, kan du oprette en ved at følge trinnene i feltet **til at oprette en ny lagerplads konto sektion** over. (, Du kan også oprette en ny konto lagerplads på [Azure Management Portal][].)
2. Klik på **Tilføj**i dialogboksen **Lagerplads konti** .
3. Angiv værdier for **navn** og **Hurtigtast**i dialogboksen **Tilføj lagerplads konto** . Kontonøgle navn og adgang skal være til en eksisterende konto Azure-lager. Brug sektionen **lagerplads** på [Azure Management Portal][] til at få vist din lagerplads kontonavne og taster. Dialogboksen **Tilføj lagerplads konto** ser nogenlunde sådan følgende.

    ![][ic719497]

4. Klik på **OK** for at lukke dialogboksen **Tilføj lagerplads konto** .

## <a name="to-modify-a-storage-account-to-use-a-new-access-key"></a>Ændre en lagerplads konto for at bruge en ny nøgle i access ##

1. Klik på kontoen lagerplads, du vil redigere, og klik derefter på **Rediger**i dialogboksen **Lagerplads konti** .
2. Ændre værdien **Hurtigtast** i dialogboksen **Rediger lagerplads konto-adgangsnøgle** .
3. Klik på **OK** for at lukke dialogboksen **Rediger lagerplads konto-adgangsnøgle** .

## <a name="to-remove-a-storage-account-from-the-list-maintained-in-eclipse"></a>Du fjerner en lagerplads konto på listen, vedligeholdes i Eklipse ##

1. Klik på kontoen lagerplads, du vil redigere, og klik derefter på **Fjern**i dialogboksen **Lagerplads konti** .
2. Klik på **OK** , når du bliver bedt om at fjerne kontoen lagerplads.

>[AZURE.NOTE] Fjernelse af kontoen lagerplads via dialogboksen **Lagerplads konti** til kun fjerne den fra listen over lagerplads konti kan ses i Eklipse. Det fjerner ikke kontoen lagerplads fra dit Azure abonnement. Desuden kan kontoen lagerplads vises igen på listen over når Eklipse genindlæser detaljerne for dit abonnement.

## <a name="see-also"></a>Se også ##

[Azure-værktøjskassen til Eklipse][]

[Installation af Azure værktøjerne til Eklipse][] 

[Oprette et Hej verden program til Azure i Eklipse][]

Du kan finde flere oplysninger om brug af Azure med Java, [Azure Java Developer Center][].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure-værktøjskassen til Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure Management-portalen]: http://go.microsoft.com/fwlink/?LinkID=512959
[Oprette et Hej verden program til Azure i Eklipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installation af Azure værktøjerne til Eklipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[What's New in the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic719496]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719496.png
[ic719497]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719497.png
