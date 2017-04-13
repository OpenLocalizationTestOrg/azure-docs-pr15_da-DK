<properties
    pageTitle="Slette et Azure CDN slutpunkt | Microsoft Azure"
    description="Lær at rydde alle cachelagrede indhold fra et CDN slutpunkt."
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

# <a name="purge-an-azure-cdn-endpoint"></a>Fjern Azure CDN ark

## <a name="overview"></a>Oversigt

Azure CDN kant noder cachelagres Aktiver indtil aktivets time to live (TTL) udløber.  Når aktivets TTL udløber, når en klient anmoder om aktivet fra noden kant, noden kant vil hente en ny opdateret kopi af aktiv at kunne levere klientanmodningen og store opdatere cache.

Nogle gange kan du rydde cachelagret indhold fra alle kant noder og Tving dem alle til at hente nye opdaterede aktiver.  Dette skyldes opdateringer til dit webprogram eller til hurtigt at opdatere aktiver, der indeholder forkerte oplysninger.

> [AZURE.TIP] Bemærk, at kun sletning rydder cachelagrede indholdet på CDN edge-servere.  Alle efterfølgende cache, som proxyservere og lokale browser cache kan stadig indeholde en cachelagret kopi af filen.  Det er vigtigt at huske dette, når du indstiller en fils time to live.  Du kan gennemtvinge en efterfølgende klient til at anmode om den seneste version af filen ved at give den et entydigt navn, hver gang du opdaterer dem eller ved at drage fordel af [cachelagring af forespørgsel streng](cdn-query-string.md).  

Dette selvstudium vejleder dig gennem sletning aktiver fra alle kant knudepunkter på et slutpunkt.

## <a name="walkthrough"></a>Gennemgang

1. [Azure-portalen](https://portal.azure.com), Find den CDN profil, der indeholder det slutpunkt, du vil slette.

2. Klik på knappen Fjern fra bladet CDN profil.

    ![LEVERANDØR(er) profil blade](./media/cdn-purge-endpoint/cdn-profile-blade.png)

    Fjern blade åbnes.

    ![LEVERANDØR(er) Rens blade](./media/cdn-purge-endpoint/cdn-purge-blade.png)

3. Vælg den tjeneste-adresse, du vil fjerne fra rullemenuen URL-adressen på bladet Fjern.

    ![Fjern formular](./media/cdn-purge-endpoint/cdn-purge-form.png)

    > [AZURE.NOTE] Du kan også finde bladet Rens ved at klikke på knappen **Fjern** på bladet CDN slutpunkt.  I så fald er feltet **URL-adressen** udfyldt med tjenesten adressen for den specifikke slutpunkt.

4. Vælg hvilken aktiver, du vil fjerne fra noderne kant.  Hvis du vil fjerne alle aktiver, skal du klikke på afkrydsningsfeltet **Fjern alle** .  Ellers skal skrive den fulde sti for hver aktiv, du vil slette (f.eks., `/pictures/kitten.png`) i tekstboksen **sti** .

    > [AZURE.TIP] Flere **sti** tekstbokse vises, når du har indtastet tekst, der gør det muligt at oprette en liste over flere aktiver.  Du kan slette Aktiver på listen ved at klikke på ellipsen (...).
    >
    > Stier skal være en relativ URL-adresse, der passer til følgende [regulære udtryk](https://msdn.microsoft.com/library/az24scfc.aspx): `^\/(?:[a-zA-Z0-9-_.\u0020]+\/)*\*$";`.  **Azure CDN fra Verizon** (Standard og Premium), stjerne (\*) kan bruges som et jokertegn (f.eks., `/music/*`).  Jokertegn og **slette alle** er ikke tilladt med **Azure CDN fra Akamai**.
    
5. Klik på knappen **Fjern** .

    ![Fjern knappen](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [AZURE.IMPORTANT] Fjern anmodninger tage cirka 2-3 minutter til at behandle med **Azure CDN fra Verizon** (Standard og Premium) og cirka 7 minutter med **Azure CDN fra Akamai**.  Azure CDN har en begrænsning på 50 samtidige tømme anmodninger til enhver tid. 

## <a name="see-also"></a>Se også
- [Foreløbig Indlæs Aktiver på Azure CDN ark](cdn-preload-endpoint.md)
- [Azure CDN REST-API reference - tømme eller allerede indlæse et slutpunkt](https://msdn.microsoft.com/library/mt634451.aspx)
