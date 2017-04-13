<properties
    pageTitle="Udfyldt indlæse Aktiver på Azure CDN ark | Microsoft Azure"
    description="Lær at indlæse allerede cachelagrede indhold på et CDN slutpunkt."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Foreløbig Indlæs Aktiver på Azure CDN ark

[AZURE.INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Som standard cachelagrede Aktiver først, når der anmodes om. Det betyder, at den første anmodning i hver region tage længere tid, da edge-servere ikke vil have indholdet cachelagret og skal videresende anmodningen til den oprindelige server. Udfyldt indlæses indhold undgår denne første besøg ventetid.

Ud over at give en bedre oplevelse, kan før indlæsning af dine cachelagrede Aktiver også formindske netværkstrafik på den oprindelige server.

> [AZURE.NOTE] Før indlæsning af aktiver er nyttig til store begivenheder eller indhold, der bliver samtidigt tilgængelige for et stort antal brugere, som en ny version af film eller en softwareopdatering.

Dette selvstudium vejleder dig gennem før indlæsning af cachelagret indhold på alle Azure CDN kant noder.

## <a name="walkthrough"></a>Gennemgang

1. Gå til den CDN profil, der indeholder det slutpunkt, du vil indlæse udfyldt i [Azure-portalen](https://portal.azure.com).  Bladet profil åbnes.

2. Klik på slutpunktet på listen.  Bladet slutpunkt åbnes.

3. Klik på knappen Indlæs bladet CDN slutpunkt.

    ![LEVERANDØR(er) slutpunkt blade](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)

    Bladet Indlæs åbnes.

    ![LEVERANDØR(er) Indlæs blade](./media/cdn-preload-endpoint/cdn-load-blade.png)

4. Angiv den fulde sti for hver aktiv, du vil indlæse (f.eks., `/pictures/kitten.png`) i tekstboksen **sti** .

    > [AZURE.TIP] Flere **sti** tekstbokse vises, når du har indtastet tekst, der gør det muligt at oprette en liste over flere aktiver.  Du kan slette Aktiver på listen ved at klikke på ellipsen (...).
    >
    > Stier skal være en relativ URL-adresse, der passer til følgende [regulære udtryk](https://msdn.microsoft.com/library/az24scfc.aspx): `^(?:\/[a-zA-Z0-9-_.\u0020]+)+$`.  Hver aktiv skal have sin egen sti.  Der er ikke nogen jokertegn funktion for udfyldt indlæse aktiver.

    ![Indlæse knappen](./media/cdn-preload-endpoint/cdn-load-paths.png)

5. Klik på knappen **Indlæs** .

    ![Indlæse knappen](./media/cdn-preload-endpoint/cdn-load-button.png)

> [AZURE.NOTE] Er der en begrænsning af 10 Indlæs forespørgsler i minuttet per CDN profil.

## <a name="see-also"></a>Se også
- [Fjern Azure CDN ark](cdn-purge-endpoint.md)
- [Azure CDN REST-API reference - tømme eller allerede indlæse et slutpunkt](https://msdn.microsoft.com/library/mt634451.aspx)
